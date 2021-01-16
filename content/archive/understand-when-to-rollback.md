---
title: 'Understand when to rollback'
toc: true
date: "2016-03-04T10:45:25+00:00"
---

# Prepare

At first, I create a table:



```sql

CREATE TABLE "SYSTEM"."TEST" 
(	
  "ID" NUMBER(*,0), 
  "NAME" VARCHAR2(200 BYTE), 
  CONSTRAINT "TEST_PK" PRIMARY KEY ("ID")
);

```


Then, create a stored procedure:



```sql

CREATE PROCEDURE sp_insert (x NUMBER) IS
BEGIN
  -- Do some inserts here.
  INSERT INTO test VALUES (x, 'insert by stored procedure');
  -- Sometimes there might be an error.
  IF x = 2 THEN
    RAISE_APPLICATION_ERROR(-20000, 'Wooops...');
  END IF;
EXCEPTION
  WHEN OTHERS THEN
    --Rollback all the changes and then raise the error again.
    ROLLBACK;
    RAISE;
END sp_insert; 

```


To understand how to handle the rollback, I wrote a test to demonstrate the rule.

Before each test, we insert a record with the id of 2.



```java

public class TransactionTest {

    // JDBC driver name and database URL
    static final String DB_URL = "jdbc:oracle:thin:@10.10.10.100:1521:ENG11R2";
    //  Database credentials
    static final String USER = "system";
    static final String PASS = "oracle";

    @Before
    public void setUp() throws SQLException {
        Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);

        conn.setAutoCommit(false);
        PreparedStatement statement = conn.prepareStatement("truncate table test");
        statement.execute();
        
        String sql = "insert into test values (2, 'insert in advance')";
        statement = conn.prepareStatement(sql);
        statement.execute();

        conn.commit();
    }

    @After
    public void tearDown() throws SQLException {
        Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);
        PreparedStatement statement = conn.prepareStatement("select * from test");
        ResultSet rs = statement.executeQuery();
        while(rs.next()) {
            System.out.printf("%d | %s %n", rs.getInt(1), rs.getString(2));
        }
        conn.close();
    }

    @Test
    public void clientExitDueToException() throws SQLException {
        System.out.printf("-----------Now start run clientExitDueToException test -----------%n");
        Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);

        conn.setAutoCommit(false);
        String sql = "insert into test values (?, 'insert by clientExitDueToException')";
        PreparedStatement statement = conn.prepareStatement(sql);

        statement.setInt(1, 1);
        statement.execute();

        statement.setInt(1, 2); // expected throw exception here
        statement.execute();

        statement.setInt(1, 3);
        statement.execute();

        conn.commit();
    }

    @Test
    public void clientIgnoreError() throws SQLException {
        System.out.printf("-----------Now start run clientIgnoreError test -----------%n");
        Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);

        conn.setAutoCommit(false);
        String sql = "insert into test values (?, 'insert by clientIgnoreError')";
        PreparedStatement statement = conn.prepareStatement(sql);

        statement.setInt(1, 1);
        statement.execute();

        try {
            statement.setInt(1, 2);
            statement.execute();
        } catch (SQLException ex) {
            // silently ignore unique key violation to prevent rollback,
            // but statement-level rollback still happen implicitly
        }

        statement.setInt(1, 3);
        statement.execute();

        conn.commit();
    }

    @Test
    public void clientNoErrorButProcedureRollback() throws SQLException {
        System.out.printf("-----------Now start run clientNoErrorButProcedureRollback test -----------%n");
        Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);

        conn.setAutoCommit(false);
        PreparedStatement statement = conn.prepareStatement("insert into test (id, name) values (?, 'clientNoErrorButProcedureRollback')");

        statement.setInt(1, 1);
        statement.execute();

        // rollback within stored procedure
        try {
            PreparedStatement sp = conn.prepareCall("{ call sp_insert(2) }"); // 2
            sp.execute();
        } catch (Exception ex) {
            // ignore the exception within the stored procedure
        }

        statement.setInt(1, 3);
        statement.execute();

        conn.commit();
    }
}

```


The results of the code:



```log

-----------Now start run clientExitDueToException test -----------
2 | insert in advance 

java.sql.SQLIntegrityConstraintViolationException: ORA-00001: unique constraint (SYSTEM.TEST_PK) violated


	at oracle.jdbc.driver.T4CTTIoer.processError(T4CTTIoer.java:450)
	at oracle.jdbc.driver.T4CTTIoer.processError(T4CTTIoer.java:399)
	at oracle.jdbc.driver.T4C8Oall.processError(T4C8Oall.java:1059)
	at oracle.jdbc.driver.T4CTTIfun.receive(T4CTTIfun.java:522)
	at oracle.jdbc.driver.T4CTTIfun.doRPC(T4CTTIfun.java:257)
	at oracle.jdbc.driver.T4C8Oall.doOALL(T4C8Oall.java:587)
	at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:225)
	at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:53)
	at oracle.jdbc.driver.T4CPreparedStatement.executeForRows(T4CPreparedStatement.java:943)
	at oracle.jdbc.driver.OracleStatement.doExecuteWithTimeout(OracleStatement.java:1150)
	at oracle.jdbc.driver.OraclePreparedStatement.executeInternal(OraclePreparedStatement.java:4798)
	at oracle.jdbc.driver.OraclePreparedStatement.execute(OraclePreparedStatement.java:4901)
	at oracle.jdbc.driver.OraclePreparedStatementWrapper.execute(OraclePreparedStatementWrapper.java:1385)
	at TransactionTest.clientExitDueToException(TransactionTest.java:56)
	
	
-----------Now start run clientIgnoreError test -----------
2 | insert in advance 
1 | insert by clientIgnoreError 
3 | insert by clientIgnoreError 

-----------Now start run clientNoErrorButProcedureRollback test -----------
2 | insert in advance 
3 | clientNoErrorButProcedureRollback 

```


# Case 1

The java code didn't catch the `SQLException`, so the jvm will exit before the commit line. 

For this case, the equivalent script the database received is like this:



```sql

BEGIN
INSERT INTO TEST VALUES(1, 'insert by clientExitDueToException');
INSERT INTO TEST VALUES(2, 'insert by clientExitDueToException'); 
-- the above statement will be rollbacked implicitly due to unique key violation

```


Because the database think the client exited, it rollback the transaction.

# Case 2

The java code caught the exception and silently supressed it, the code can run to the commit line. So the transaction is committed normally. 

For this case, the equivalent script the database received is like this:



```sql

BEGIN
INSERT INTO TEST VALUES(1, 'insert by clientIgnoreError');
INSERT INTO TEST VALUES(2, 'insert by clientIgnoreError'); 
-- the above statement will be rollbacked implicitly due to unique key violation
INSERT INTO TEST VALUES(3, 'insert by clientIgnoreError';
COMMIT;

```


Although the transaction committed, the second statement will be rollbacked. This demonstrated the support of statement-level rollback.

# Case 3

The java code caught the exception and silently supressed it, the code can run to the commit line. So the transaction is committed normally. 

But in the stored procedure, it rollbacked the transaction. So it caused the records before the error statement to rollback.

For this case, the equivalent script the database received is like this:



```sql

BEGIN
INSERT INTO TEST VALUES(1, 'insert by clientNoErrorButProcedureRollback');
-- the above statement will be rollbacked due to the rollback statement within the procedure
INSERT INTO TEST VALUES(2, 'insert by clientNoErrorButProcedureRollback'); 
-- the above statement will be rollbacked implicitly due to unique key violation
ROLLBACK;
INSERT INTO TEST VALUES(3, 'insert by clientNoErrorButProcedureRollback'
COMMIT;

```



