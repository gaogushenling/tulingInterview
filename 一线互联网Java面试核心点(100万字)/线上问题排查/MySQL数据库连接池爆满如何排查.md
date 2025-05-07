# MySQL数据库连接池爆满如何排查

<font style="color:rgba(0, 0, 0, 0.82);">当Java应用程序面临MySQL数据库连接池爆满的问题时，通常会导致应用程序性能下降或请求被拒绝。解决这个问题需要深入分析程序的数据库连接管理。以下是具体的排查步骤和过程：</font>

### <font style="color:rgba(0, 0, 0, 0.82);">确认问题</font>
<font style="color:rgba(0, 0, 0, 0.82);">首先，我们需要确认连接池确实爆满了。通常可以通过以下方式：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">检查应用日志，查找与数据库连接相关的错误信息，如"无法获取连接"等。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">查看数据库连接池的监控面板（如果有的话）。</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">使用数据库管理工具查看当前活跃连接数。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">收集信息</font>
<font style="color:rgba(0, 0, 0, 0.82);">收集以下信息：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">连接池配置（最大连接数、最小连接数、超时时间等）</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">当前活跃连接数</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">数据库服务器资源使用情况（CPU、内存、磁盘I/O）</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">应用服务器资源使用情况</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">近期是否有代码变更或流量激增</font>

### <font style="color:rgba(0, 0, 0, 0.82);">分析连接使用情况</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用MySQL命令查看当前连接：</font>

```java
SHOW PROCESSLIST;
```

<font style="color:rgba(0, 0, 0, 0.82);">这会显示所有当前连接，包括它们的状态、执行的查询等。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">检查慢查询</font>
<font style="color:rgba(0, 0, 0, 0.82);">查看是否有长时间运行的查询占用连接：</font>

```java
SHOW FULL PROCESSLIST;
```

<font style="color:rgba(0, 0, 0, 0.82);">关注"Time"列，看是否有查询执行时间过长。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">分析应用代码</font>
<font style="color:rgba(0, 0, 0, 0.82);">检查应用代码中的连接使用方式：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">是否正确关闭连接</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">是否有连接泄露</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">是否有不必要的长连接</font>

<font style="color:rgba(0, 0, 0, 0.82);">例如，以下代码可能导致连接泄露：</font>

```java
public void leakyMethod() {  
    Connection conn = null;  
    try {  
        conn = dataSource.getConnection();  
        // 使用连接进行操作  
    } catch (SQLException e) {  
        e.printStackTrace();  
    }  
    // 没有在 finally 块中关闭连接  
}
```

<font style="color:rgba(0, 0, 0, 0.82);">正确的做法应该是：</font>

```java
public void leakyMethod() {  
    Connection conn = null;  
    try {  
        conn = dataSource.getConnection();  
        // 使用连接进行操作  
    } catch (SQLException e) {  
        e.printStackTrace();  
    }  
    finally {
        conn.close();
    }
}
```

### <font style="color:rgba(0, 0, 0, 0.82);">检查连接池配置</font>
<font style="color:rgba(0, 0, 0, 0.82);">检查连接池的配置是否合理。以HikariCP为例：</font>

```java
HikariConfig config = new HikariConfig();  
config.setMaximumPoolSize(10);  
config.setConnectionTimeout(30000);  
config.setIdleTimeout(600000);  
config.setMaxLifetime(1800000);
```

<font style="color:rgba(0, 0, 0, 0.82);">确保这些参数设置合理：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">maximumPoolSize：最大连接数</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">connectionTimeout：等待连接的最大毫秒数</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">idleTimeout：连接允许在池中闲置的最长时间</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">maxLifetime：连接最长生命周期</font>

### <font style="color:rgba(0, 0, 0, 0.82);">使用监控工具</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用如JConsole或VisualVM等Java监控工具，观察连接池的使用情况。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">数据库性能分析</font>
<font style="color:rgba(0, 0, 0, 0.82);">使用MySQL的性能模式（Performance Schema）来分析数据库性能：</font>

```java
SELECT * FROM performance_schema.events_waits_summary_global_by_event_name  
WHERE event_name LIKE '%wait/synch/mutex/innodb/%'  
ORDER BY sum_timer_wait DESC;
```

<font style="color:rgba(0, 0, 0, 0.82);">这可以帮助识别数据库层面的瓶颈。</font>

### <font style="color:rgba(0, 0, 0, 0.82);">解决方案</font>
<font style="color:rgba(0, 0, 0, 0.82);">根据分析结果，可能的解决方案包括：</font>

+ <font style="color:rgba(0, 0, 0, 0.82);">优化慢查询</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">增加连接池大小（如果服务器资源允许）</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">修复连接泄露的代码</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">使用读写分离或分库分表来分散负载</font>
+ <font style="color:rgba(0, 0, 0, 0.82);">添加连接池监控和告警机制</font>

### <font style="color:rgba(0, 0, 0, 0.82);">实施和验证</font>
<font style="color:rgba(0, 0, 0, 0.82);">实施解决方案后，持续监控连接池使用情况，确保问题得到解决。</font>

<font style="color:rgba(0, 0, 0, 0.82);">示例：  
</font><font style="color:rgba(0, 0, 0, 0.82);">假设我们发现了一个导致连接池爆满的问题，原因是某个查询长时间运行导致连接无法释放。</font>

1. <font style="color:rgba(0, 0, 0, 0.82);">首先，我们通过</font>`<font style="color:rgba(0, 0, 0, 0.82);">SHOW FULL PROCESSLIST</font>`<font style="color:rgba(0, 0, 0, 0.82);">发现了一个长时间运行的查询：</font>

```java
+----+------+-----------------+------+---------+------+---------------+------------------+  
| Id | User | Host            | db   | Command | Time | State         | Info             |  
+----+------+-----------------+------+---------+------+---------------+------------------+  
| 1  | root | localhost:3306  | mydb | Query   | 3600 | Sending data  | SELECT * FROM ... |  
+----+------+-----------------+------+---------+------+---------------+------------------+
```

2. <font style="color:rgba(0, 0, 0, 0.82);">分析该查询，发现是一个全表扫描的大查询：</font>

```java
SELECT * FROM large_table WHERE non_indexed_column = 'some_value';
```

3. <font style="color:rgba(0, 0, 0, 0.82);">优化这个查询：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">添加适当的索引</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">限制返回的行数</font>

```java
ALTER TABLE large_table ADD INDEX idx_non_indexed_column (non_indexed_column);  
SELECT * FROM large_table WHERE non_indexed_column = 'some_value' LIMIT 1000;
```

4. <font style="color:rgba(0, 0, 0, 0.82);">在应用代码中，我们发现了以下问题：</font>

```java
public List<Data> fetchData(String value) {  
Connection conn = dataSource.getConnection();  
try {  
    PreparedStatement stmt = conn.prepareStatement("SELECT * FROM large_table WHERE non_indexed_column = ?");  
    stmt.setString(1, value);  
    ResultSet rs = stmt.executeQuery();  
    // 处理结果集  
} catch (SQLException e) {  
    e.printStackTrace();  
}  
// 连接未关闭  
return dataList;  
}
```

5. <font style="color:rgba(0, 0, 0, 0.82);">修改代码以正确管理连接：</font>

```java
public List<Data> fetchData(String value) {  
List<Data> dataList = new ArrayList<>();  
try {Connection conn = dataSource.getConnection();  
     PreparedStatement stmt = conn.prepareStatement("SELECT * FROM large_table WHERE non_indexed_column = ? LIMIT 1000")) {  
    stmt.setString(1, value);  
    try (ResultSet rs = stmt.executeQuery()) {  
        while (rs.next()) {  
            // 处理结果集  
            dataList.add(new Data(rs.getString("column1"), rs.getInt("column2")));  
        }  
    }  
} catch (SQLException e) {  
    logger.error("Error fetching data", e);  
} finally{
    conn.close();
} 
return dataList;  
}
```

6. <font style="color:rgba(0, 0, 0, 0.82);">调整连接池配置：</font>

```java
HikariConfig config = new HikariConfig();  
config.setMaximumPoolSize(20); // 增加最大连接数  
config.setConnectionTimeout(30000);  
config.setIdleTimeout(600000);  
config.setMaxLifetime(1800000);
```

7. <font style="color:rgba(0, 0, 0, 0.82);">添加监控：</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">使用Spring Actuator或自定义监控来跟踪连接池使用情况</font>
    - <font style="color:rgba(0, 0, 0, 0.82);">设置告警，当连接使用率接近最大值时通知开发团队</font>

<font style="color:rgba(0, 0, 0, 0.82);">通过这些步骤，我们解决了导致连接池爆满的主要问题，优化了数据库查询，修复了连接泄露，并增强了监控能力。在实施这些更改后，我们会持续监控系统，确保连接池使用正常，并在必要时进行进一步的优化。</font>



> 更新: 2024-08-26 14:30:18  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/gn5d62blxrrrftko>