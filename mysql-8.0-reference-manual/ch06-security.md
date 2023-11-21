# Ch6 Security

## Keywords

### 6.1 General Security Issues

#### [6.1.6 Security Considerations for LOAD DATA LOCAL](https://dev.mysql.com/doc/refman/8.0/en/load-data-local-security.html)

- `load data`, server host, `local`, client host

- `local`, security issue

    - server side parsing, MySQL server initiate, server access any file on the client host

    - web environment, webserver use `load data local` to read any file that the Web server process has read access to

- client, secure connection, server identity, `--ssl-mod=VERIFY_IDENTITY` option, CA cerficate

    - see Command Options for Encrypted Connections

##### Enabling or Disabling Local Data Loading Capability

- server side

    - `local_infile`, system variable, server-side `local` capability, default desable

    - start `mysqld` with `local_infile`

    - set `local_infile` at runtime

- client side

    - `ENABLED_LOCAL_INFILE` CMake option, compiled-in default `local` capability for the MySQL client library

        - see Section 2.8.7 MySQL Source-Configuration Options

    - MySQL binary distribution, `ENABLED_LOCAL_INFILE` default disable

    - C API, `mysql_options()` C API function, `MYSQL_OPT_LOCAL_INFILE`

        - see mysql_options()

    - mysql client, `--local-infile=0`, `local-infile[=1]`

    - mysqlimport client, `--local=0`, `--local[=1]`

    - Perl scripts, other programs

        ```perl
        [client]
        loose-local-infile=0
        ```

        ```perl
        [client]
        loose-local-infile=1
        ```

- error message

    ```
    ERROR 3950 (42000): Loading local data is disabled; this must be
    enabled on both the client and server side
    ```
