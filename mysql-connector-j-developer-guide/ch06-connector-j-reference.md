# Chapter 6 Connector/J Reference

## Keywords

### 6.3 Configuration Properties

- Configuration properties, how Connector/J will make a connection to a MySQL server

- set for `DataSource` object, `Connection` object

- `set*()` methods, MySQL implementations of `java.sql.DataSource`

    - `com.mysql.cj.jdbc.MysqlDataSource`

    - `com.mysql.cj`jdbc.MysqlConnectionPoolDataSource`

- key-value pair in the `java.util.Properties` instance, `DriverManager.getConnection()`, `Driver.connect()`

- JDBC URL parameter, `java.sql.DriverManager.getConnection()`, `java.sql.Driver.connect()`, MySQL implementations of the `javax.sql.DataSource setURL()` method

#### 6.3.3 Session

- `characterEncoding`

    - server, session system variables `character_set_client`and `character_set_connection`

    - `collation_connection`, default collation

    - if neither this property nor the property `connectionCollation` is set:

        - Connector/J 8.0.25 and earlier, driver use the server's default character set

        - Connector/J 8.0.26 and later, driver use utf8mb4

    - since version 1.1g

#### 6.3.5 Security

- `allowLoadLocalInfile`

    - `LOAD DATA LOCAL INFILE ...`

    - default value false, since version 3.0.3

#### 6.3.11 Datetime types processing

- `connectionTimeZone`

    - connection time zone, Connector/J

    - conversion between JVM default and a target time zone

    - geographic time zone name, time zon offset from Greenwich/UTC, syntax `java.time.ZoneId`, `LOCAL`, `SERVER`

    - `LOCAL`, default, JVM default time zone

    - `SERVER`, session time zone, MySQL server session variables `time_zone` or `system_time_zone`

    - MySQL server session variable `time_zone`, `forceConnectionTimeZoneToSession` connection option `true`

    - `forceConnectionTimeZoneToSession=false`, `preserveInstants=false`, no effect

    - former connection option `serverTimezone`, valid, alias

    - see `forceConnectionTimeZoneToSession`, `preserveInstants`

    - since version 3.0.2

