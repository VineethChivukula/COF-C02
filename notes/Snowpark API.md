# Snowpark API

- It offers an intuitive library designed for querying and processing data at scale within Snowflake, allowing developers to build applications using native language constructs in Python, Java, and Scala.
- Data processing is performed directly within the Snowflake data cloud, eliminating the need to move data to an external system where the application code runs.
- So it does not require that a separate cluster be running outside of Snowflake.
- It executes as much work as possible in the source databases for all operations including UDFs.
- Developers can create UDFs inline in their code, and Snowpark automatically pushes this custom logic to the server to execute on data at scale.
