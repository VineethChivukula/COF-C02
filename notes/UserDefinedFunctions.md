# User Defined Functions (UDFs)

- They allow you to extend the native functionality of Snowflake by writing reusable custom code that performs operations not covered by built-in system functions.
- The logic can be written in SQL and the custom application logic can be written in Java, JavaScript, Python, or Scala.
- A Java UDF can be used to extract the content of PDF files stored in Snowflake stages.
- UDFs written in JavaScript or SQL are sharable using the Snowflake Secure Data Sharing feature while UDFs written in Java, Python, or Scala are generally not sharable.
- If the UDFs are written with some unqualified object names then the Snowflake will only check the schema the UDF belongs to.
- UDFs cannot execute database operations.
- If there are multiple SQL UDFs with the same names but with a different number of arguments or argument types, then it is called Overloading.
- A single executable statement can call multiple UDFs.
- UDFs come in several variations depending on the required input and output:
  - **Scalar UDF**: It returns one output row (a single column/value) for each input row.
  - **User-Defined Aggregate Function (UDAF)**: It operates on values across multiple rows to perform calculations like sum, average, or counting.
  - **User-Defined Table Function (UDTF)**: It returns a tabular value (multiple columns/rows) for each input row.
  - **Vectorized UDF/UDTF**: It receives batches of input rows as Pandas DataFrames and returns batches of results as Pandas arrays/Series (UDF) or tabular results (UDTF).

## Scalar UDF Example:

Let us say you want to write a function which returns a value multiplied by 2, you can do so as follows:

```SQL
CREATE OR REPLACE FUNCTION multiply(num NUMBER)
RETURNS INTEGER
LANGUAGE SQL
AS
$$
    num * 2
$$;
```

To execute this function do so as follows:

```SQL
SELECT MULTIPLY(4) AS multiply_value;
```

## UDAF Example:

Let us say you want to create your own aggregation function that finds the average of the provided numbers, you can do so as follows:

```SQL
CREATE OR REPLACE AGGREGATE FUNCTION custom_avg(num INT)
RETURNS FLOAT
LANGUAGE PYTHON
RUNTIME_VERSION = '3.12'
HANDLER = 'CustomAvg'
AS $$
class CustomAvg:
    def __init__(self):
        self.sum = 0.0
        self.count = 0

    @property
    def aggregate_state(self):
        return (self.sum, self.count)

    def accumulate(self, num):
        if num is not None:
            self.sum += num
            self.count += 1

    def merge(self, other):
        other_sum, other_count = other
        self.sum += other_sum
        self.count += other_count

    def finish(self):
        if self.count == 0:
            return None
        return self.sum / self.count
$$;
```

To execute this function do so as follows:

```SQL
SELECT custom_avg(column1)
FROM VALUES (1), (2), (3), (4), (5);
```

## UDTF Example:

Let us say you have a table called `employees` which has columns id, name, department, and salary. Assume that there is data present in that table. If want to retrieve the employee names in UPPERCASE, you can do so as follows:

```SQL
CREATE OR REPLACE FUNCTION get_upper_names(name VARCHAR)
RETURNS TABLE (uppercase_name VARCHAR)
LANGUAGE PYTHON
RUNTIME_VERSION = '3.12'
HANDLER = 'UpperCaseProcessor'
AS $$
class UpperCaseProcessor:
    def process(self, name):
        if name is not None:
            yield (name.upper(),)
$$;
```

To execute this function do so as follows:

```SQL
SELECT
    e.id,
    u.uppercase_name,
    e.department
FROM employees e,
    TABLE(get_upper_names(e.name)) u;
```

## Vectorized UDTF Example:

Let us say you want to perform the same as in the Example of UDTF, do as follows:

```SQL
CREATE OR REPLACE FUNCTION get_upper_names_vectorized(name VARCHAR)
RETURNS TABLE (uppercase_name VARCHAR)
LANGUAGE PYTHON
RUNTIME_VERSION = '3.12'
HANDLER = 'UpperCaseVectorized'
PACKAGES = ('pandas')
AS $$
import pandas as pd

class UpperCaseVectorized:
    def process(self, input_df: pd.DataFrame):
        input_df['UPPERCASE_NAME'] = input_df['NAME'].str.upper()

        return input_df[['UPPERCASE_NAME']]
$$;
```
