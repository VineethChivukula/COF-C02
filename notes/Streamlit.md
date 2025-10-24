# Streamlit in Snowflake

- Snowflake integrates the popular open-source Python library, **Streamlit**, directly.
- Interactive data applications process and utilize data residing within Snowflake i.e., no data or code is moved to external systems.
- Streamlit applications are managed as standard Snowflake objects.
- Access is secured using Role-based Access Control (RBAC).
- It requires a selected virtual warehouse to run the application itself and execute queries.
- The active virtual warehouse consumes Snowflake credits and the connection expires (auto-suspends) approximately 15 minutes after the app's last use to conserve credits.
