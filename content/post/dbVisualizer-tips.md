---
title: 'DbVisualizer Tips'
toc: true
date: "2016-04-21T18:08:25+00:00"
---

# DbVisualizer Tips

1. How do we execute stored procedure?
use `@call stored_procedure_name;`

2. Parameterize SQL with variables
Write your SQL as `select * from table1 where a = ${param}$`
Execute it and the "Enter data for variables" window will pop up.

3. Parameterize SQL with markers
Write your SQL as `select * from table1 where a = :marker`
Execute it and the "Enter data for markers" window will pop up.

4. How do we render the query result as chart?
In the result panel, there is a button "show as chat", click it and there is also a setting window from where you can configure the way the data is shown.

5. DbVis can get the query Explain Plan.




