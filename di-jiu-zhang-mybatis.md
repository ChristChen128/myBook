# 九、 MyBatis

**9.1 MyBatis的工作原理：**

MyBatis是一个轻量级的ORM框架，开发者可以使用XML或注解编写SQL，这样SQL更便于维护，与Java代码完全隔离。

1. 加载配置：配置来源于两个地方，一是配置文件，一是Java代码的注解，将SQL的配置信息加载成为一个个MappedStatement对象（包括了传入参数映射配置、执行的SQL语句、结果映射配置），存储在内存中。
2. SQL解析：当API接口层接收到调用请求时，会接收到传入SQL的ID和传入对象（可以是Map、JavaBean或者基本数据类型），Mybatis会根据SQL的ID找到对应的MappedStatement，然后根据传入参数对象对MappedStatement进行解析，解析后可以得到最终要执行的SQL语句和参数。
3. SQL执行：将最终得到的SQL和参数拿到数据库进行执行，得到操作数据库的结果，
4. 结果映射：将操作数据库的结果按照映射的配置进行转换，可以转换成HashMap、JavaBean或者基本数据类型，并将最终结果返回。

   ![MyBatis&#x5DE5;&#x4F5C;&#x539F;&#x7406;](http://study.christchen.top/img/MyBatis工作原理.jpg)

注意的是MyBatis中用到了建造者模式，建造者模式最简单的理解就是不手动new对象，而是由其他类来进行对象的创建。

**9.2 MyBatis的优缺点**

* 优点：SQL写在XML里面，便于统一管理和优化；提供映射标签，支持对象和数据库的ORM字段关系映射；可以对SQL进行优化
* 缺点：SQL工作量大；MyBatis的移植性不好；不支持级联

**9.3 MyBatis中常用的标签**

| 标签 | 说明 |
| :---: | :---: |
| `<select></select>` | 定义sql语句——查询 |
| `<insert></insert>` | 定义sql语句——插入 |
| `<update></update>` | 定义sql语句——更新 |
| `<delete></delete>` | 定义sql语句——删除 |
| `<resultMap></resultMap>` | 配置java对象属性和查询结果集的映射关系 |
| `<resultType></resultType>` | 定义返回值的数据类型 |
| `<parameterType></parameterType>` | 定义入参的数据类型 |
| `<if></if>` | 条件判定，一般用在Where语句中 |
| `<foreach></foreach>` | 循环，一般用于批量插入或修改 |
| `<choose></choose>` | 条件判定，一般用在Where语句中 |
| `<where></where>` | 格式化输出，用于Where语句 |
| `<set></set>` | 格式化输出，用于update语句 |
| `<trim></trim>` | 格式化，自动去除空格 |
| `<collection></collection>` | 配置关联关系，用于映射定义 |
| `<sql></sql>` | 定义常量——字段名 |
| `<include></include>` | 引用常量 |

涉及到的面试题：

* resultType和resultMap有什么区别？

参考：两者都是用于处理SQL语句的返回结果与JavaBean的映射关系；

​ 当使用resultType时，对于SQL语句查询出来的字段要与接收的JavaBean中的字段一一对应（变量类型和名称），常见于对应关系是一对一的查询，resultType的值是JavaBean在项目中的位置（包名+类名）；

​ 当使用resultMap时，SQL查出来的字段，可以和JavaBean的字段不对应，因为会在mapper.xml中定义resultMap进行SQL字段和JavaBean字段对应。

* 变量引用方式\#{}和${}有什么区别？什么是sql注入？

参考：

​ \#{} 代表占位符，可以接受简单类型或pojo的属性值，会自动进行java类型和jdbc类型转换，可以有效防止SQL注入；

​ ${} 代表sql串拼接，只会把传入内容拼接在sql中，不会进行jdbc转化；

​ 二者的主要差异在预编译的时候处理方式不一样，因而使用场景不同，\#{} 用来传递参数，${} 用来拼接数据库表名

**9.4 MyBatis的事务管理**

* 什么是事务？事务有四个特性：ACID

​ 原子性：事务是一个原子操作，不可分割，要么全完成，要么全不完成；

​ 一致性：一旦事务完成（不管成功还是失败），事务中的业务状态一致，不能是部分成功部分失败；

​ 隔离性：可能会有几个事务同时进行（比如多人一起取钱），事务之间应该是互不影响的；

​ 持久性：一旦事务完成，无论发生什么，这个事务的结果都不会受到影响。

* SpringBoot项目如何配置事务？

声明式事务管理基于AOP，在启动类Application中开启事务管理，只需要添加一个注解即可（@EnableTransactionManagement），在Service层需要事务操作的方法上加上@Transactional注解；springboot就是这么简单，不需要做额外的配置，就可以开启一个普通的事务。

@Transactional注解有几个参数：

| 参数 | 类型 | 描述 |
| :---: | :---: | :---: |
| value | String | 可选的限定描述符，指定使用的事务管理器 |
| propagation | enum: Propagation | 可选的事务传播行为设置 |
| isolation | enum: Isolation | 可选的事务隔离级别设置 |
| readOnly | boolean | 读写或只读事务，默认读写 |
| timeout | int \(in seconds granularity\) | 事务超时时间设置 |
| rollbackFor | Class对象数组，必须继承自Throwable | 导致事务回滚的异常类数组 |
| rollbackForClassName | 类名数组，必须继承自Throwable | 导致事务回滚的异常类名字数组 |
| noRollbackFor | Class对象数组，必须继承自Throwable | 不会导致事务回滚的异常类数组 |
| noRollbackForClassName | 类名数组，必须继承自Throwable | 不会导致事务回滚的异常类名字数组 |

