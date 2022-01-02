### 一、什么是JDBC

​		JDBC（Java Database Connectivity）Java和数据库的连接技术是sun公司推出的一个**独立于特定数据库管理系统、通用的SQL数据库存取和操作的公共接口**，定义了用来访问数据库的标准Java类库，使用这个类库可以以一种标准的方法、方便地访问数据库资源。

​	规范：抽象类或接口

​	java.sql包下的一组接口或抽象类

​	java.sql包下的一组接口

### 二、JDBC的好处

1.开发者不需要记多套API，减轻了开发压力

2.提高了代码的可维护性

没有使用JDBC访问数据库：

> ![image-20220101135733844](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101135733844.png)

使用JDBC访问数据库

![image-20220101140533774](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101140533774.png)







### 三、通过案例演示JDBC使用步骤

```java
public class JDBCConnectionPlay {

    public static void main(String[] args) throws SQLException {
        // 1.加载驱动
        DriverManager.registerDriver(new Driver());
        // 2.获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://1.15.72.181:3306/bigData", "6623", "6623");
        System.out.println("连接成功");

        // 3.执行CRUD
        // 3-1编写SQL
//        String sql = "delete from dept where id = 11";
//        String sql = "update  dept set dname='IT' where id = 10";
//        String sql = "INSERT INTO `bigData`.`dept`(`id`, `deptno`, `dname`, `loc`) VALUES (11, 120, 'fdfsdf', 'sdfdf')";
        String sql = "select name, password from user where id = 1 ";
        // 3-2获取执行SQL语句的命令对象
        Statement statement = connection.createStatement();
        // 3-3使用命令对象执行SQL语句
        ResultSet resultSet = statement.executeQuery(sql);
        // 3-4处理执行结果
        while(resultSet.next()) {
            String username = resultSet.getString(1);
            String pwd = resultSet.getString(2);
            System.out.println("username: " + username + ", pwd: " + pwd);
        }
        // 4.关闭连接
        resultSet.close();
        statement.close();
        connection.close();
    }
    
}
```

### 四、介绍JDBC相关API

#### DriverManager驱动管理类

​	registerDriver(Driver对象):注册驱动，不推荐使用

​	getConnection(url, user, pwd):获取连接

#### Connection连接对象接口

​	createStatement():生成命令对象

​	prepareStatement():生成预编译命令对象

#### Statement命令对象接口

​	executeUpdate(sql):执行增删改语句，返回受影响的行数

​	executeQuery(sql):执行查询语句，返回结果集

​	execute(sql):执行任意sql语句，返回boolean

#### PreparedStatement预编译命令对象接口

​	executeUpdate(sql):执行增删改语句，返回受影响的行数

​	executeQuery(sql):执行查询语句，返回结果集

​	execute(sql):执行任意sql语句，返回boolean

​	setXX(占位符索引，占位符的值)：设置对应索引的占位符的值，类型为XX类型

​	setObject(占位符索引，占位符的值)：设置对应索引的占位符的值，类型为Object

#### ResultSet结果集对象接口

​	next():下移一行，返回当前行是否有值

​	previous():上移一行，返回当前行是否有值

​	getXX(列索引 || 列名 || 别名):返回对应列的值，接收类型为XX

​	getObject(列索引 || 列名 || 别名):返回对应列的值，接收类型为Object

### 五、PreparedStatement和Statement

#### 相同点

​	都属于执行SQL语句的命令接口，都提供了一系列执行SQL语法的方法

​	PreparedStatement继承了Statement

#### 区别

​	PreparedStatement不再使用“+”拼接SQL语句，减少语法错误

​	PreparedStatement将SQL和参数部分实现了分离，提高语义性和维护性

​	PreparedStatement有效避免了SQL注入问题

​	PreparedStatement减少了编译次数，提高了效率；比如10条SQL语句，仅仅是参数不同，使用PreparedStatement只需要编译一次，而使用Statement则需要编译10次



### 六、封装JDBCUtils类，

#### 用于获取连接和关闭连接

```java
public class JDBCUtils {

    private static String user;
    private static String password;
    private static String url;
    private static String driver;

    static {
        try {
            Properties info = new Properties();
            info.load(new FileInputStream("src/main/resources/jdbc.properties"));
            //info.list(System.out);
            user = info.getProperty("user");
            password = info.getProperty("password");
            url = info.getProperty("url");
            driver = info.getProperty("driver");
            Class.forName(driver);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }

    }

    /**
     * 获取连接
     * @return
     * @throws IOException
     * @throws ClassNotFoundException
     * @throws SQLException
     */
    public static Connection getConnection(){
        try {
            return DriverManager.getConnection(url, user, password);
        }catch (Exception e) {
            throw new RuntimeException(e); // 将编译异常转为运行异常，使得调用方不用try catch
        }
    }


    /**
     * 释放资源
     * @param set
     * @param statement
     * @param connection
     * @throws SQLException
     */
    public static void close(ResultSet set, Statement statement, Connection connection) {
        try {
            if (set != null) {
                set.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        }catch (SQLException e) {
            throw new RuntimeException(e); // 将编译异常转为运行异常，使得调用方不用try catch
        }
    }
    
    
     /**
     * 使用JDBCUtils
     * @throws Exception
     */
    @Test
    public void test03() throws Exception {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = JDBCUtils.getConnection();
            String sql = "select id, deptno, dname  from  dept where id = 10";
            statement = connection.createStatement();
            resultSet = statement.executeQuery(sql);
            while (resultSet.next()) {
                long id = resultSet.getLong(1);
                int deptno = resultSet.getInt(2);
                String dname = resultSet.getString(3);
                System.out.println("id: " + id + ", deptno: " + deptno + ", dname: " + dname);
            }
        }catch (SQLException e){
            e.printStackTrace();
        }finally {
            JDBCUtils.close(resultSet, statement, connection);
        }
        
    }
    
}

```

#### 封装通用的增删改查【了解】

```java

/**
 * @Description 用于封装通用的增删改查方法
 * 功能
 * 1.执行增删改
 * 2.执行查询
 * @Author HeZhipeng
 * @Date 2022/1/2 10:09
 **/
public class CRUDUtils {


    /**
     * 功能：增删改
     * 针对于任何表的任何增删改语句
     *
     * @return
     */
    public static int update(String sql, Object... params) {
        Connection connection = null;
        PreparedStatement statement = null;
        try {
            //1.获取连接
            connection = JDBCByDruidUtils.getConnection();

            //2.执行sql语句
            statement = connection.prepareStatement(sql);
            // 设置占位符的值
            for (int i = 0; i < params.length; i++) {
                statement.setObject(i + 1, params[i]);
            }
            int i = statement.executeUpdate();
            return i;
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCByDruidUtils.close(null, statement, connection);
        }

    }


    @Test
    public void testUpdate() {
//        String sql = "update dept set dname=? where id = ?";
//        int hr = update(sql, "HR",11);
//        System.out.println(hr > 0 ? "update success," : "update failure");

        int update = update("delete from dept where id = ?", 5);
        System.out.println(update > 0 ? "delete success" : "delete failure");

    }


    /**
     * 查询一条记录
     *
     * @param sql
     * @param params
     * @return
     */
    public static Dept selectOne(String sql, Object... params) {

        Connection connection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;
        try {
            //1.获取连接
            connection = JDBCByDruidUtils.getConnection();

            //2.执行sql语句
            statement = connection.prepareStatement(sql);
            // 设置占位符的值
            for (int i = 0; i < params.length; i++) {
                statement.setObject(i + 1, params[i]);
            }

            resultSet = statement.executeQuery();
            if (resultSet.next()) {
                long id = resultSet.getLong(1);
                int deptno = resultSet.getInt(2);
                String dname = resultSet.getString(3);
                String loc = resultSet.getString(4);
                return new Dept(id, deptno, dname, loc);
            }
            return null;
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCByDruidUtils.close(resultSet, statement, connection);
        }

    }

    @Test
    public void testSelectOne() {
        Dept dept = selectOne("select * from dept where id = ?", 10);
        System.out.println(dept);
    }


    public static List<Dept> list(String sql, Object... params) {

        Connection connection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;
        try {
            //1.获取连接
            connection = JDBCByDruidUtils.getConnection();

            //2.执行sql语句
            statement = connection.prepareStatement(sql);
            // 设置占位符的值
            for (int i = 0; i < params.length; i++) {
                statement.setObject(i + 1, params[i]);
            }

            resultSet = statement.executeQuery();
            List<Dept> depts = new ArrayList<>();
            while (resultSet.next()) {
                long id = resultSet.getLong(1);
                int deptno = resultSet.getInt(2);
                String dname = resultSet.getString(3);
                String loc = resultSet.getString(4);
                depts.add(new Dept(id, deptno, dname, loc));
            }
            return depts;
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            JDBCByDruidUtils.close(resultSet, statement, connection);
        }

    }

    @Test
    public void testList() {
        List<Dept> depts = list("select * from dept where id > ?", 5);
        System.out.println(depts);
    }


}

```

### 七、批处理

当需要成批插入或者更新记录时，可以采用Java的批量更新机制，它可以允许多条语句一次性提交给数据库批量处理。通常比单独提交更高效。

JDBC的批处理语句包括下面两个方法：

​	addBatch()：添加需要批量处理的SQL语句或参数

​	executeBatch()：执行批量处理语句

​	clearBatch()：清空批处理语句

通常我们会遇到两种批量执行SQL语句的情况：

​	多条SQL语句的批量处理；

​	![image-20220101233122659](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101233122659.png)

​	一个SQL语句的批量传参

![image-20220101233202589](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101233202589.png)

>JDBC连接MySQL时，如果要使用批处理功能，请再url中加参数?rewriteBatchedStatements=true
>
>PreparedStatement作批处理插入时使用values（使用value没效果）

```java
// 演示没有批处理，需要很长时间才能处理完
@Test
public void testNoBatch() throws SQLException {
    //1.获取连接
    Connection connection = JDBCUtils.getConnection();
    //2.执行插入
    PreparedStatement statement = connection.prepareStatement("insert into user values(null, ?, ?)");
    for (int i = 1; i <= 50000; i++) {
        statement.setString(1, "mark" + i);
        statement.setString(2, "000000");
        statement.executeUpdate();//执行
    }
    //3.释放资源
    JDBCUtils.close(null, statement, connection);
}
```

```java
/**
 * 使用批处理，很快就能处理完
 * @throws SQLException
 */
@Test
public void testBatch() throws SQLException {
    //1.获取连接
    Connection connection = JDBCUtils.getConnection();
    //2.执行插入
    PreparedStatement statement = connection.prepareStatement("insert into user values(null, ?, ?)");
    for (int i = 0; i < 50000; i++) {
        statement.setString(1, "mark" + i);
        statement.setString(2, "000000");
        statement.addBatch(); // 添加sql语句到批处理包中
        if (i % 1000 == 0) {
           statement.executeBatch(); // 执行批处理包中的SQL语句
           statement.clearBatch(); // 清空批处理包中的SQL语句
        }
    }
    //3.释放资源
    JDBCUtils.close(null, statement, connection);

}
```

### 八、演示事务

JDBC程序中**一个连接对象**被创建时，默认情况下是自动提交事务，每次执行一个SQL语句时，如果执行成功，就会向数据库自动提交，而不能回滚。

JDBC程序中为了让多个SQL语句作为一个事务执行：

​	调用Connection对象的setAutoCommit(false)，以取消自动提交事务

​	在所有的SQL语句都执行成功后，调用commit()，提交事务

​	在其中某个操作失败或出现异常时，调用rollback()，回滚事务

​	若此时Connection没有被关闭，则需要恢复其自动提交状态，setAutoCommit(true)

tips:如果多个操作，每个操作使用的是自己单独的连接，则无法保证事务。即**同一个事务的多个操作必须在同一个连接下**。

>使用步骤
>
>1.开启事务：setAutoCommit(false);
>
>2.编写组成事务的一组sql语句
>
>3.结束事务：
>
>​	commit();//提交
>
>​	rollback();//回滚

```mysql
#演示的SQL脚本
CREATE TABLE `account` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(64) DEFAULT NULL,
  `balance` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

INSERT INTO `bigData`.`account`(`id`, `name`, `balance`) VALUES (1, '张三丰', 10000);
INSERT INTO `bigData`.`account`(`id`, `name`, `balance`) VALUES (2, '灭绝师太', 10000);
```

![image-20220101225118540](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101225118540.png)



```java
/**
 * 不用事务，模拟转账
 * @throws Exception
 */
@Test
public void test1() throws Exception {
    //1.获取连接
    Connection connection = JDBCUtils.getConnection();
    //2.执行SQL语句
    PreparedStatement preparedStatement = connection.prepareStatement("update account set balance = ? where name = ?");
    // 操作1：张三丰的钱减少5000
    preparedStatement.setInt(1, 5000);
    preparedStatement.setString(2, "张三丰");
    preparedStatement.executeUpdate();

    // 模拟异常
    int i = 10/0;

    // 操作2：灭绝师太的钱加5000
    preparedStatement.setInt(1, 15000);
    preparedStatement.setString(2, "灭绝师太");
    preparedStatement.executeUpdate();

    //3.释放资源
    JDBCUtils.close(null, preparedStatement, connection);
    
}
```

>出现异常，没有回滚事务

![image-20220101225450749](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101225450749.png)



```MYSQL
#把balance还原为10000，10000，然后用事务的方式来模拟转账
```

```java
/**
 * 使用事务：配合try-catch使用，
 * 正常结束执行commit()，出现异常执行rollback()
 * 结论：出现异常，事务回滚了。
 * @throws Exception
 */
@Test
public void test2() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    try {
        //1.获取连接

        connection = JDBCUtils.getConnection();
        // 事务的使用步骤1：开启事务
        connection.setAutoCommit(false);

        //2.事务的使用步骤2：编写SQL并执行
        preparedStatement = connection.prepareStatement("update account set balance = ? where id = ?");
        // 操作1：张三丰的钱减少5000
        preparedStatement.setInt(1, 5000);
        preparedStatement.setString(2, "1");
        preparedStatement.executeUpdate();

        // 模拟异常
        int i = 10/0;

        // 操作2：灭绝师太的钱加5000
        preparedStatement.setInt(1, 15000);
        preparedStatement.setString(2, "2");
        preparedStatement.executeUpdate();
        // 事务的使用步骤3：提交事务
        connection.commit();
    }catch (SQLException e) {
        // 事务的使用步骤3：回滚事务
        try {
            connection.rollback();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }finally {
        //3.释放资源
        JDBCUtils.close(null, preparedStatement, connection);
    }

}   
```

>出现异常，回滚了事务。

![image-20220101230724325](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220101230724325.png)



### 9、数据库连接池

#### （1）数据库连接池的必要性

##### 不使用数据库连接池存在的问题

* 普通的JDBC数据库连接使用DriverManger来获取，每次向数据库建立连接的时候都要将Connection加载到内存中，再验证IP地址、用户名和密码（花费0.05~1s时间）。需要数据库连接的时候，就向数据库要求一个，执行完后再断开连接。这样的方式将会消耗大量的资源和时间。数据库的连接资源并没有得到很好的重复利用。若同时有几百甚至几千个人在线，频繁的进行数据库连接操作将占用很多的系统资源，严重的甚至会造成服务器崩溃。
* 对于每一次数据库的连接，使用完后都得断开。否则，如果程序出现异常而未能关闭，将会导致数据库系统中的内存泄漏，最终导致重启数据库。
* 这种开发不能控制被创建的连接对象数，系统资源会被毫无顾忌地分配出去，如连接过多，也可能导致内存泄漏，服务器崩溃。

为了解决上述问题，出现了数据库连接池技术（connection pool）。

它就是为数据库建立一个“缓冲池”。预先在缓冲池中放入一定数量地连接，当需要建立数据库连接时，只需从“缓冲池”中取出一个，使用完毕后再放回去。数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有地数据库连接，而不是重新建立一个。连接池地最大数据库连接数量限定了这个连接池占有地最大连接数，当应用程序向连接池请求地连接数超过最大数量时，这些请求将被加入到等待队列中。

![image-20220102010354310](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220102010354310.png)

##### 数据库连接池技术的优点

* 资源重用
* 更快的系统反应速度
* 新的资源分配手段
* 统一的连接管理，避免数据库连接泄漏

#### （2）多种开源的数据库连接池

JDBC的数据库连接池使用java.sql.DataSource来表示，DataSource只是一个接口，该接口通常由服务器（Weblogic，WebSphere，Tomcat）提供实现，也有一些开源组织提供实现

* DBCP：是apache提供的，速度相对C3P0较快，但因自身有bug，hibernate3已不再提供支持
* C3P0：速度较慢，稳定性还可以
* Proxool：是sourceforge下的，有监控连接池状态的功能，稳定性比C3P0差一点
* BoneCP：
* Druid：是阿里提供的，它集DBCP、C3P0和Proxool优点于一身的数据库连接池。

DataSource通常被称作数据源，它包含连接池和连接池管理两个部分。习惯上也把DataSource称为连接池

>数据源和数据库连接不同，数据源无需创建多个，它是产生数据库连接的工厂，因此整个应用只需要一个数据源即可。
>
>当数据库访问结束后，**程序还是像以前一样关闭数据库连接，但它并没有关闭数据库的物理连接，它仅仅是把数据库连接释放，归还给了数据库连接池。**

#### （3）Druid（德鲁伊）数据源

```properties
username=6623
password=6623
url=jdbc:mysql://1.15.72.181:3306/bigData
driverClassName=com.mysql.jdbc.Driver
#初始个数
initialSize=10
#最小空闲量
minIdle=5
#最大活跃量
maxActive=20
maxWait=5000
```

```java
/**
 * 演示获取德鲁伊连接
 * @throws Exception
 */
@Test
public void testDruid() throws Exception {
    Properties properties = new Properties();
    properties.load(new FileInputStream("src/main/resources/druid.properties"));
    properties.list(System.out);
    //1.创建一个指定参数的数据库连接池
    DataSource ds = DruidDataSourceFactory.createDataSource(properties);
    //2.从数据库连接池中获取连接对象
    Connection connection = ds.getConnection();
    System.out.println("连接成功");
    //3.关闭连接
    connection.close();
}
```

```java
/**
 * @Description JDBC-Druid工具类
 * @Author HeZhipeng
 * @Date 2022/1/2 1:39
 **/
public class JDBCByDruidUtils {

    private static  DataSource ds;

    static {
        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("src/main/resources/druid.properties"));
            properties.list(System.out);
            //1.创建一个指定参数的数据库连接池
            ds = DruidDataSourceFactory.createDataSource(properties);

        } catch (Exception e) {
            throw new RuntimeException(e);
        }

    }

    /**
     * 获取连接
     * @return
     * @throws ClassNotFoundException
     * @throws SQLException
     */
    public static Connection getConnection(){
        try {
            return ds.getConnection();
        }catch (Exception e) {
            throw new RuntimeException(e); // 将编译异常转为运行异常，使得调用方不用try catch
        }
    }


    /**
     * 释放资源
     * @param set
     * @param statement
     * @param connection
     * @throws SQLException
     */
    public static void close(ResultSet set, Statement statement, Connection connection) {
        try {
            if (set != null) {
                set.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        }catch (SQLException e) {
            throw new RuntimeException(e); // 将编译异常转为运行异常，使得调用方不用try catch
        }
    }
}

```

### 10、Apache-DBUtils开源框架的学习

commons-dbutils 是 Apache 组织提供的一个开源 JDBC工具类库，它是对JDBC的简单封装，学习成本极低，并且使用dbutils能极大简化jdbc编码的工作量，同时也不会影响程序的性能。

　commons-dbutilsAPI介绍：

- #### org.apache.commons.dbutils.QueryRunner

  - execute（执行SQL语句）
  - batch（批量处理语句）
  - insert（执行INSERT语句）
  - insertBatch（批量处理INSERT语句）
  - query（SQL中 **SELECT** 语句）
  - update（SQL中 **INSERT**, **UPDATE**, 或 **DELETE** 语句）

- #### org.apache.commons.dbutils.ResultSetHandler

  该接口用于处理java.sql.ResultSet，将数据按要求转换为另一种形式。

  ResultSetHandler接口提供了一个单独的方法：Object handle(java.sql.ResultSet.rs)。

  ResultSetHandler接口的实现类

  * ArrayHandler：把结果集中的第一行数据转换成对象数组。
  * ArrayListHandler：把结果集中的每一行数据都转换成一个对象数组，再存放到List中。
  * BeanHandler：将结果集中的第一行数据封装到一个对应的JavaBean实例中。
  * BeanListHandler：将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里。
  * MapHandler：将结果集中的第一行数据封装到一个Map里，key是列名，value就是对应的值。
  * MapListHandler：将结果集中的每一行数据都封装到一个Map里，然后再存放到List。
  * ColumnListHandler：将结果集中某一列的数据存放到List中。
  * KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map里(List)，再把这些map再存到一个map里，其key为指定的列。
  * ScalarHandler:获取结果集中第一行数据指定列的值,常用来进行单值查询

- #### org.apache.commons.dbutils.DbUtils

```java
/**
 * @Description apache common-dbutils的使用演示
 * @Author HeZhipeng
 * @Date 2022/1/2 11:07
 **/
public class DBUtilsPlay {


    /**
     * 测试增删改
     * @throws Exception
     */
    @Test
    public void testUpdate() throws Exception {
        //1.获取连接
        Connection connection = JDBCByDruidUtils.getConnection();
        //2.执行操作
        QueryRunner qr = new QueryRunner();
        int update = qr.update(connection, "update dept set dname=? where id = 10", "IT PLUS");
        System.out.println(update > 0 ? "success" : "failure");
        //3.关闭连接
        JDBCByDruidUtils.close(null, null, connection);

    }

    /**
     * 测试查询单条记录
     * @throws Exception
     */
    @Test
    public void testQuerySingle() throws Exception {
        //1.获取连接
        Connection connection = JDBCByDruidUtils.getConnection();
        //2.执行操作
        QueryRunner qr = new QueryRunner();
        Dept dept = qr.query(connection, "select * from dept where id = ?", new BeanHandler<>(Dept.class), "10");
        System.out.println(dept);

        //3.关闭连接
        JDBCByDruidUtils.close(null, null, connection);

    }
    
    /**
     * 测试查询多个
     * @throws Exception
     */
    @Test
    public void testQueryMulti() throws Exception {
        //1.获取连接
        Connection connection = JDBCByDruidUtils.getConnection();
        //2.执行操作
        QueryRunner qr = new QueryRunner();
        List<Dept> depts = qr.query(connection, "select * from dept where id > ?", new BeanListHandler<>(Dept.class), "1");
        System.out.println(depts);

        //3.关闭连接
        JDBCByDruidUtils.close(null, null, connection);

    }
    
    /**
     * 测试查询单个值
     * @throws Exception
     */
    @Test
    public void testScalar() throws Exception {
        //1.获取连接
        Connection connection = JDBCByDruidUtils.getConnection();
        //2.执行操作
        QueryRunner qr = new QueryRunner();
        Long count = qr.query(connection, "select count(*) from dept", new ScalarHandler<>());
        System.out.println(count);
        //3.关闭连接
        JDBCByDruidUtils.close(null, null, connection);

    }

}
```

### 11、封装dao

```java
/**
 * @Description 
 * 优点：使用了泛型
 * 缺点：模板代码比较多，spring对其进行了改造
 * @Author HeZhipeng
 * @Date 2022/1/2 13:02
 **/
public class BasicDao <T> {

    /**
     * 增删改操作接口
     * @throws Exception
     */
    public int update(String sql, Object... param) throws Exception {
        //1.获取连接
        Connection connection = null;
        try {
            connection = JDBCByDruidUtils.getConnection();
            //2.执行操作
            QueryRunner qr = new QueryRunner();
            int update = qr.update(connection, sql, param);
            System.out.println(update > 0 ? "success" : "failure");
            return update;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            //3.关闭连接
            JDBCByDruidUtils.close(null, null, connection);
        }



    }

    /**
     * 查询单条记录
     * @throws Exception
     */
    public T selectOne(String sql,Class<T> clazz, Object... param)  {
        //1.获取连接
        Connection connection = null;
        try {
            //1.获取连接
            connection = JDBCByDruidUtils.getConnection();
            //2.执行操作
            QueryRunner qr = new QueryRunner();
            return qr.query(connection, sql, new BeanHandler<>(clazz), param);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            //3.关闭连接
            JDBCByDruidUtils.close(null, null, connection);
        }


    }

    /**
     * 查询多个
     * @throws Exception
     */
    public List<T> selectList(String sql,Class<T> clazz, Object... param) {
        //1.获取连接
        Connection connection = null;
        try {
            connection = JDBCByDruidUtils.getConnection();
            QueryRunner qr = new QueryRunner();
            return qr.query(connection, sql, new BeanListHandler<>(clazz), param);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            //3.关闭连接
            JDBCByDruidUtils.close(null, null, connection);
        }
    }

    /**
     * 查询单个值
     * @throws Exception
     */
    public Object scalar(String sql,Class<T> clazz, Object... param)  {

        //1.获取连接
        Connection connection = null;
        try {
            connection = JDBCByDruidUtils.getConnection();
            QueryRunner qr = new QueryRunner();
            return qr.query(connection, sql, new ScalarHandler<>(), param);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            //3.关闭连接
            JDBCByDruidUtils.close(null, null, connection);
        }
        
    }
    
}

```

### 12、Blob类型数据的读写【选学】

Blob是一个二进制大型对象，是一个可以存储大量数据的容器，它能容纳不同大小的数据。

MySQL有四种BLOB类型（除了在存储的最大信息量上不同外，它们是等同的）

实际使用中根据需要存入的数据大小定义不同的Blob类型。需要注意的是，如果存储的文件过大，数据库的性能会下降

```java
/**
 * 存图片
 */
@Test
public void testSave() throws SQLException, FileNotFoundException {
    //1.获取连接
    Connection connection = JDBCUtils.getConnection();
    //2.执行插入
    PreparedStatement statement = connection.prepareStatement("update user set photo = ? where id = ?");
    statement.setBlob(1, new FileInputStream("src/main/resources/liuyan.png"));
    statement.setInt(2, 1);
    statement.executeUpdate();
    JDBCUtils.close(null, statement, connection);
}
```

```java
/**
 * 读取blob文件
 * @throws SQLException
 * @throws IOException
 */
@Test
public void testGet() throws SQLException, IOException {
    //1.获取连接
    Connection connection = JDBCUtils.getConnection();
    //2.执行插入
    PreparedStatement statement = connection.prepareStatement("select photo from user where id = ?");
    statement.setInt(1, 1);

    ResultSet resultSet = statement.executeQuery();
    if (resultSet.next()) {
        Blob blob = resultSet.getBlob(1);
        InputStream inputStream = blob.getBinaryStream();
        FileOutputStream fos = new FileOutputStream("src/main/resources/liuyan_copy.png");
        int len;
        byte[] b = new byte[1024];
        while((len = inputStream.read(b)) != -1) {
            fos.write(b, 0, len);
        }
        fos.close();
        inputStream.close();
    }
    JDBCUtils.close(null, statement, connection);
}
```



