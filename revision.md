**JDBC Full Revision Guide (Interview-Ready)**

### 1. What is JDBC?

* JDBC (Java Database Connectivity) is an API in Java that enables Java programs to connect and execute SQL statements on relational databases.
* It is a part of Java SE and provides a standard interface for database interaction.
* JDBC is **low-level**, giving precise control over SQL and connections.

### 2. JDBC Architecture

* **JDBC API** (Java Application)
* **JDBC Driver Manager** (Loads the driver class)
* **JDBC Driver** (Vendor-specific implementation)
* **Database (DBMS)**

Flow:
Java App → JDBC API → DriverManager → JDBC Driver → DBMS

### 3. JDBC Driver Types

| Type | Name             | Description                                 |
| ---- | ---------------- | ------------------------------------------- |
| 1    | JDBC-ODBC Bridge | Uses ODBC driver, requires native libraries |
| 2    | Native-API       | Converts JDBC to DB-native calls            |
| 3    | Network Protocol | Middleware translates JDBC to DB calls      |
| 4    | Thin Driver      | Pure Java; connects directly via protocol   |

✅ **Type 4 is the most commonly used in production**

### 4. JDBC Core Interfaces

* `DriverManager`: Establishes database connections.
* `Connection`: Represents a session with a database.
* `Statement`: Used to execute static SQL queries.
* `PreparedStatement`: Precompiled SQL, supports parameters.
* `CallableStatement`: Used to call stored procedures.
* `ResultSet`: Cursor to iterate over query results.

### 5. Basic Connection and Query Example

```java
String url = "jdbc:mysql://localhost:3306/test";
Connection conn = DriverManager.getConnection(url, "root", "pass");
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM user");

while (rs.next()) {
    System.out.println(rs.getString("username"));
}
rs.close();
stmt.close();
conn.close();
```

### 6. PreparedStatement Example (Prevents SQL Injection)

```java
String query = "SELECT * FROM user WHERE id = ?";
PreparedStatement ps = conn.prepareStatement(query);
ps.setInt(1, 10);
ResultSet rs = ps.executeQuery();
```

### 7. CallableStatement Example (Stored Procedures)

```java
CallableStatement cs = conn.prepareCall("{call getSalary(?)}");
cs.setInt(1, 101);
ResultSet rs = cs.executeQuery();
```

### 8. ResultSet Navigation

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM emp");
while (rs.next()) {
    System.out.println(rs.getString("name"));
}
```

* `next()`: Moves to next row
* `first()`, `last()`, `absolute(n)`, `beforeFirst()`, `afterLast()` (requires scrollable `ResultSet`)

### 9. Transaction Management in JDBC

```java
conn.setAutoCommit(false);
try {
    // execute queries
    conn.commit();
} catch (SQLException e) {
    conn.rollback();
}
```

* Transactions are **auto-committed by default**.
* Set `autoCommit(false)` to manage manually.

### 10. Batch Processing

```java
PreparedStatement ps = conn.prepareStatement("INSERT INTO product VALUES (?, ?)");
for (int i = 0; i < 1000; i++) {
    ps.setInt(1, i);
    ps.setString(2, "Product" + i);
    ps.addBatch();
}
ps.executeBatch();
```

### 11. Connection Pooling

* JDBC doesn't include pooling; use:

  * HikariCP
  * Apache DBCP
  * C3P0

```java
HikariDataSource ds = new HikariDataSource();
ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
ds.setUsername("user");
ds.setPassword("pass");
Connection conn = ds.getConnection();
```

### 12. Error Handling

```java
try {
    // db operations
} catch (SQLException e) {
    System.out.println(e.getMessage());
    System.out.println(e.getSQLState());
    System.out.println(e.getErrorCode());
}
```

### 13. Advanced ResultSet Features

* `TYPE_FORWARD_ONLY` (default)
* `TYPE_SCROLL_INSENSITIVE` (scrollable, read-only)
* `TYPE_SCROLL_SENSITIVE` (scrollable, updates visible)
* `CONCUR_READ_ONLY`, `CONCUR_UPDATABLE`

```java
Statement stmt = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,
    ResultSet.CONCUR_READ_ONLY
);
```

### 14. JDBC vs Hibernate

| Feature            | JDBC                       | Hibernate                 |
| ------------------ | -------------------------- | ------------------------- |
| Abstraction        | Low-level SQL              | High-level ORM            |
| Learning Curve     | Lower                      | Higher                    |
| Boilerplate Code   | High                       | Low                       |
| Performance Tuning | Manual                     | Auto via caching/fetching |
| Caching            | Manual (none by default)   | L1 & L2 cache supported   |
| DB Portability     | Manual SQL tuning required | Automatic via Dialect     |

### 15. Interview Questions (with Answers)

1. **What is JDBC and why is it used?**

   * It is a Java API that enables database connectivity and SQL execution.

2. **What are the main steps in JDBC?**

   * Load driver, create connection, create statement, execute query, process result, close.

3. **Difference between `Statement` and `PreparedStatement`?**

   * `PreparedStatement` is precompiled and secure against SQL injection.

4. **How do you manage transactions in JDBC?**

   * Use `conn.setAutoCommit(false)`, `commit()`, and `rollback()` manually.

5. **How do you perform batch updates?**

   * Use `addBatch()` and `executeBatch()` in `PreparedStatement`.

6. **How do you call a stored procedure?**

   * Using `CallableStatement` and curly-brace syntax `{ call proc_name(?) }`

7. **How do you enable connection pooling?**

   * Use external libraries like HikariCP or frameworks like Spring.

8. **How to retrieve generated keys after insert?**

```java
PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
ps.executeUpdate();
ResultSet rs = ps.getGeneratedKeys();
```

### 16. Flashcards (Quick Recall)

* **Q:** Interface to execute parameterized SQL?
  **A:** `PreparedStatement`
* **Q:** Default transaction mode in JDBC?
  **A:** `autoCommit = true`
* **Q:** Class used for stored procedure calls?
  **A:** `CallableStatement`
* **Q:** Statement to disable auto commit?
  **A:** `conn.setAutoCommit(false);`
* **Q:** Class that manages DB drivers?
  **A:** `DriverManager`
* **Q:** Method to fetch next row in ResultSet?
  **A:** `rs.next()`

---

Let me know if you'd like to generate mock interview drills or Spring JDBC revision too.
