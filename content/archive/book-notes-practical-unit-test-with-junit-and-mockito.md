---
title: Book notes - Practical Unit Test with JUnit and Mockito
toc: true
id: 1298
categories:
  - 'Building, Testing &amp; CI'
date: "2015-05-25T14:46:18+00:00"
---

# Developers' Tests

## On Tests and Tools

**SUT** (aka. System Under Test) – test target being tested

**DOC** (aka. Dependency of Components) – collaborators and dependencies when testing

### Types of Developers' Tests

*   Unit Test
*   Integration Test
*   End-to-End Test
Every type of test has different scope. Basically, unit test is in class level, integration test is in module level and end-to-end test is in application level.

Other types of tests:
- **System Integration Test** – test with external systems/applications, services etc
- **User Acceptation Test** – final user verification test
- **Load Test** – verify system performance under pressure / load

<table border="1" width="635">
<tbody>
<tr>
<td>**type of test**</td>
<td>**SUT example**</td>
<td>**DOC example**</td>
</tr>
<tr>
<td rowspan="3">unit test</td>
<td>UserService</td>
<td>UserDAO</td>
</tr>
<tr>
<td>Invoice</td>
<td>Product</td>
</tr>
<tr>
<td>Client</td>
<td>Account</td>
</tr>
<tr>
<td rowspan="3">integration test</td>
<td>DAO layer (ORM based)</td>
<td>Hibernate</td>
</tr>
<tr>
<td>DAO layer (JDBC based)</td>
<td>MySQL 5</td>
</tr>
<tr>
<td>FullTextIndexer module</td>
<td>FileStorage module</td>
</tr>
<tr>
<td>end-to-end test</td>
<td>Whole application</td>
<td>LDAP repository</td>
</tr>
</tbody>
</table>

### Should Developers Test Their Own Code?

Yes!

## Unit Tests

### what is a Unit Test?

It's to make sure the class you are working on right now works correctly.

You should test your classes** in isolation**. When writing unit tests it's important to test a single class and nothing more. Forget about databases, Spring configuration files, and external web services.

Concentrate on the logic of your class!

### Interactions in Unit Tests

Types of collaboration with an SUT

![collaboration-of-SUT](/media/collaboration-of-SUT.png)

Test class has direct operations with SUT, but its collaborations have indirect operations on the SUT state.

We use direct inputs and indirect **inputs** (SUT receiveing some message) to set the SUT in a required state and to invoke its methods.

The direct and indirect outputs (SUT sending a message) of the SUT are expressions of the SUT's behaviours; we use them to verify whether the SUT is working properly.

<table border="1">
<tbody>
<tr>
<td>**type of interaction**</td>
<td>**involved parties**</td>
<td>**description**</td>
</tr>
<tr>
<td>direct input</td>
<td rowspan="2">Test class &amp; SUT</td>
<td>calls the SUT methods</td>
</tr>
<tr>
<td>direct output</td>
<td>values returned by the SUT to the test class after calling some SUT method</td>
</tr>
<tr>
<td>indirect output</td>
<td rowspan="2">SUT &amp; DOCs</td>
<td>arguments passed by the SUT to a method of one of its collaborators</td>
</tr>
<tr>
<td>indirect input</td>
<td>value returned (or an exception thrown) to the SUT by collaborators, after it called some methods of its collaborators</td>
</tr>
</tbody>
</table>

### State Testing vs. Interaction Testing

*   State Testing - uses direct inputs and outputs for what the results of actions.
*   Interaction Testing - concentrates on how messages are passed between collaborators
With State Testing, the SUT is a black box. Interaction testing looks inside the SUT and verifies its internal parts.

# Writing Unit Testing

## Unit tests with no collaborator

### parameterized



```java
public class Money {
    private final int amount;
    private final String currency;
    public Money(int amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public int getAmount() {
        return amount;
    }

    public String getCurrency() {
        return currency;
    }

    public boolean equals(Object anObject) {
        if (anObject instanceof Money) {
            Money money = (Money) anObject;
            return money.getCurrency().equals(getCurrency())
                    &amp;&amp; getAmount() == money.getAmount();
        }
        return false;
    }
}
```




```java
import org.junit.Test;
import static org.junit.Assert.*;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;

@RunWith(Parameterized.class)
public class MoneyParameterizedTest {
    @Parameterized.Parameters(name = "{index}: money({0}, {1})")
    public static final Object[] data() {
        return new Object[] {
                new Object[] {10, "USD"},
                new Object[] {20, "EUR"}
        };
    }

    @Parameterized.Parameter(value = 0) // first data value (0) is default
    public int amount;

    @Parameterized.Parameter(value = 1) // value is the index in parameter tuple
    public String currency;

    @Test
    public void constructorShouldSetAmountAndCurrency() {
        Money money = new Money(amount, currency);
        assertEquals(amount, money.getAmount());
        assertEquals(currency, money.getCurrency());
    }
}
```


### test fixture

A **test fixture** is a fixed state of a set of objects used as a baseline for running tests. The purpose of a test fixture is to ensure that there is a well known and fixed environment in which tests are run so that results are repeatable. Examples of fixtures:

*   Preparation of input data and setup/creation of fake or mock objects
*   Loading a database with a specific, known set of data
*   Copying a specific known set of files creating a test fixture will create a set of objects initialized to certain states.
*   
JUnit supports test fixture by using:



```java
@BeforeClass
public static void setUpClass() {}

@AfterClass
public static void tearDownClass() {}

@Before
public void setUp() {}

@After
public void tearDown() {}
```


### Phases of a unit test

<table border="1"><colgroup> <col /> <col /> </colgroup>
<tbody>
<tr>
<td>
<div class="layoutArea">
<div class="column">

**phase**

</div>
</div></td>
<td>
<div class="layoutArea">
<div class="column">

**explanation**

</div>
</div></td>
</tr>
<tr>
<td colspan="1" rowspan="2">
<div class="layoutArea">
<div class="column">

arrange

</div>
</div></td>
<td>
<div class="layoutArea">
<div class="column">

creation of all objects (except for the SUT) that are necessary for test execution

</div>
</div></td>
</tr>
<tr>
<td>
<div class="layoutArea">
<div class="column">

creation of the object whose functionality will be tested, and setting it in some initial state

</div>
</div></td>
</tr>
<tr>
<td>
<div class="layoutArea">
<div class="column">

act

</div>
</div></td>
<td>
<div class="layoutArea">
<div class="column">

execution of SUT methods to be tested

</div>
</div></td>
</tr>
<tr>
<td>
<div class="layoutArea">
<div class="column">

assert

</div>
</div></td>
<td>
<div class="layoutArea">
<div class="column">

verification of the test results

</div>
</div></td>
</tr>
</tbody>
</table>
</div>
The first phase relates to the preparation of a test fixture.

## TDD (Test-Driven Development)

when to write test?

*   Test Last (aka. Code First) development
*   Test First development

How TDD do?

*   write a test that fails ( <span style="background-color: #ff0000;">     </span> )
*   make the code work ( <span style="background-color: #00ff00;">     </span> )
*   eliminate redundancy ( <span style="background-color: #0000ff;">     </span> )

![tdd](/media/tdd.png)

Note: refractor can be done for classes to test or the test code.

TDD should be applied to every level: unit test, integration test, end-to-end test.
![tdd-on-different-levels](/media/tdd-on-different-levels.png)

#  test double (stub, test spy, mock)

<table border="1" width="644"><caption>Types of test doubles</caption>
<tbody>
<tr>
<td>test double type</td>
<td>also known as (aka.)</td>
<td>description</td>
</tr>
<tr>
<td>dummy object</td>
<td>dummy</td>
<td>needs to exist, no real collaboration needed</td>
</tr>
<tr>
<td>test stub</td>
<td>stub</td>
<td>used for passing some values to the SUT ("indirect inputs")</td>
</tr>
<tr>
<td>test spy</td>
<td>spy</td>
<td rowspan="2">used to verify if the SUT calls specific method of the collaborator ("indirect outputs")</td>
</tr>
<tr>
<td>mock object</td>
<td>mock</td>
</tr>
<tr>
<td>fake</td>
<td>fake</td>
<td></td>
</tr>
</tbody>
</table>
![test-doubles](/media/test-doubles.png)

*   Dummies and stubs are used to prepare the environment for testing (test-fixture setting). They are not used for verification.
*   Test spies and mocks are to verify the correctness of the communication between the SUT and DOCs.  Bothe can also participate in test-fixture setting.
**Fake**
For the sake of completeness, let us describe another type of test double: a fake. Fake works almost as good as the real collaborator, but is somehow simpler and/or weaker (which makes it not suitable for production use). It is also usually "cheaper" in use (i.e. faster or simpler to set up), which makes it suited to tests (which should run as fast as possible). A typical example is an in-memory database that is used instead of a full-blown database server. It can be used for some tests, as it serves SQL requests pretty well; however, you would not want to use it in a production environment. In tests, fake plays a similar role to dummy and stub: it is a part of the environment (test fixture), not an object of verification. Fakes are used in integration tests rather than in unit tests, so we will not be discussing them any further.



```java
package messenger;

/**
 * Created by Richard on 24/05/15.
 */
public class Messenger {
    private TemplateEngine templateEngine;
    private MailServer mailServer;
    public Messenger(MailServer mailServer, TemplateEngine templateEngine) {
        this.mailServer = mailServer;
        this.templateEngine = templateEngine;
    }
    public void sendMessage(Client client, Template template) {
        String msgContent = templateEngine.prepareMessage(template, client);
        mailServer.send(client.getEmail(), msgContent);
    }
}

interface MailServer {
    void send(Object email, String msgContent);
}

interface Template {
}

interface TemplateEngine {
    String prepareMessage(Template template, Client client);
}
```




```java
package messenger;

import org.junit.Before;
import org.junit.Test;
import static org.mockito.Mockito.*;

/**
 * Created by Richard on 24/05/15.
 */
public class MessengerTest {
    // SUT
    Messenger messenger;

    // dummy
    Client client;
    Template template;

    // stub
    TemplateEngine templateEngine;

    // spy
    MailServer mailServer;

    final String CLIENT_EMAIL = "some@email.com";
    final String MSG_CONTENT = "Dear John! You are fired.";

    @Before
    public void setUp() {
        // dummy
        template = mock(Template.class);

        // stubs
        client = mock(Client.class);
        when(client.getEmail()).thenReturn(CLIENT_EMAIL);

        templateEngine = mock(TemplateEngine.class);
        when(templateEngine.prepareMessage(template, client)).thenReturn(MSG_CONTENT);

        // spy
        mailServer = mock(MailServer.class);

        // SUT
        messenger = new Messenger(mailServer, templateEngine);

    }

    @Test
    public void test1() {
        messenger.sendMessage(client, template);

        // verify interaction
        verify(mailServer).send(CLIENT_EMAIL, MSG_CONTENT);
    }
}
```


