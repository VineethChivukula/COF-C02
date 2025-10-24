# Stored Procedures

- They allow you to write procedural code that extends Snowflake's capabilities like branching and looping that go beyond standard SQL.
- The custom application logic can be written in SQL Scripting, Java, JavaScript, Python, or Scala.
- The JavaScript portion of the stored procedure code must be enclosed within either single quotes (') or double dollar signs ($$).
- One key feature is that they can be created to run with a caller's rights or an owner's rights.
- A stored procedure can be created with an owner's rights when it needs to run with the privileges of the role that owns the stored procedure and the owner will inherit the caller's current virtual warehouse.
- A stored procedure can be created with a caller's rights when it needs to run with the privileges of the role that called the stored procedure.
- They are used to automate tasks and can execute database operations.
- A single executable statement can call only one stored procedure.

## Example:

Let us say you have a table called `employees` which has columns id, name, department, and salary. Assume that there is data present in that table. If you want to find out the count of employees who belong to a particular department using stored procedure, you can do it as follows:

### 1. SQL Scripting

```SQL
CREATE OR REPLACE PROCEDURE count_department_employees(dept_name VARCHAR)
RETURNS INTEGER
LANGUAGE SQL
AS
$$
DECLARE
    count_result INTEGER;
BEGIN
    SELECT COUNT(*) INTO count_result
    FROM employees
    WHERE department = :dept_name;

    RETURN count_result;
END;
$$;
```

### 2. Java

```SQL
CREATE OR REPLACE PROCEDURE count_department_employees(dept_name STRING)
RETURNS INTEGER
LANGUAGE JAVA
RUNTIME_VERSION = '11'
PACKAGES = ('com.snowflake:snowpark:latest')
HANDLER = 'EmployeeCounter.run'
AS
$$
import com.snowflake.snowpark_java.Session;
import com.snowflake.snowpark_java.Row;
import com.snowflake.snowpark_java.DataFrame;

public class EmployeeCounter {
    public int run(Session session, String dept_name) {
        String sqlQuery = "SELECT COUNT(*) FROM employees WHERE department = ?";
        long count_result = session.sql(sqlQuery, new Object[]{dept_name}).collect()[0].getLong(0);

        return (int) count_result;
    }
}
$$;
```

### 3. JavaScript

```SQL
CREATE OR REPLACE PROCEDURE count_department_employees(dept_name STRING)
RETURNS FLOAT
LANGUAGE JAVASCRIPT
AS
$$
  var sql_query = "SELECT COUNT(*) AS COUNT_RESULT FROM employees WHERE department = ?";
  var stmt = snowflake.createStatement({
      sqlText: sql_query,
      binds: [DEPT_NAME]
  });
  var result = stmt.execute();
  result.next();
  var count_result = result.getColumnValue(1);

  return count_result;
$$;
```

### 4. Python

```SQL
CREATE OR REPLACE PROCEDURE count_department_employees(dept_name STRING)
RETURNS INTEGER
LANGUAGE PYTHON
RUNTIME_VERSION = '3.12'
PACKAGES = ('snowflake-snowpark-python')
HANDLER = 'run'
AS
$$
def run(session, dept_name):
    sql_query = """
    SELECT COUNT(*)
    FROM employees
    WHERE department = ?
    """
    count_result = session.sql(sql_query, params=[dept_name]).collect()[0][0]

    return count_result
$$;
```

### 5. Scala

```SQL
CREATE OR REPLACE PROCEDURE count_department_employees(dept_name STRING)
RETURNS INTEGER
LANGUAGE SCALA
RUNTIME_VERSION = '2.12'
PACKAGES = ('com.snowflake:snowpark:latest')
HANDLER = 'EmployeeCounter.run'
AS
$$
import com.snowflake.snowpark.Session

class EmployeeCounter {
  def run(session: Session, dept_name: String): Int = {
    val sql_query = "SELECT COUNT(*) AS COUNT_RESULT FROM employees WHERE department = ?"
    val count_result = session.sql(sql_query, Seq(dept_name)).collect()(0).getLong(0).toInt

    count_result
  }
}
$$;
```

To call a stored procedures we simply do it as follows:

```SQL
CALL count_department_employees('Engineering');
```
