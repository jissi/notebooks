### JPA

---

[TOC]

---

```yml
spring:
  application:
    name: jpa
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    type: com.zaxxer.hikari.HikariDataSource
    url: jdbc:mysql://192.168.1.103:3306/jpa?characterEncoding=utf-8
    username: jissi
    password: jissi
  jpa:
    hibernate:
      ddl-auto: update # 有必要时更新数据表，其他类型见 xml版hibernate
    show-sql: true
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect #指定使用innodb，否则默认Myisam
```

<font color=red>注解都是使用JPA的规范提供的注解，一定要导入javax.persistence下的</font>

### 1. 实体类映射

```java
@Entity //该类是实体类
@Table(name="sys_user") //与数据库 sys_user表对应 , 不写该注解或name时，类名SysUser 对应 sys_user表
@DynamicInsert // 插入数据时，没有给定值的字段使用数据库默认字段
@DynamicUpdate
public class SysUser {
    @Id //标注该属性为主键，必须要有
    @GenericGenerator(name = "myuuid",strategy = "uuid") //自定义主键生成方式为uuid，该方法的名称为myuuid
    @GeneratedValue(generator = "uuid") //使用指定的主键生成策略
    private String uuid;
    
    /**
    *自增主键
    * @Id
    * @GenercatedValue(strategy=GenerationType.IDENTITY)
    * private Integer id;
    */
    
    //该属性映射到 username字段 ，不写该注解 userName 对应 user_name
    // 可以指定 unique=true等属性
    @Column(name="username",unique = true,nullable = false) 
    //@Type(type = "java.lang.String")
    private String userName;
    
    @Temporal(TemporalType.TIMESTAMP)//定义日期类型
    private Date createtime;
    
    @Transient //标注该字段不做持久化（不保存到数据库）
    private String balabala;
}
```



### 2. 关联关系

#### 2.1 相关注解

```java
// ------- 一对一
@OneToOne
// ------- 一对多
@OneToMany //用在一方的 private Collection<?> coll  = new XXX; 属性上
    targetEntiry=多方.class, 关联多方,泛型集合声明类型时可以不写
    mappedBy="多方中的一方成员变量", 外键交由一方维护，hibernate3.5.3以上该属性与@JoinColum冲突，二选一
    casecade=CaseCadeType.ALL, 指定使用的级联操作类型，默认不级联
    fetch=FetchType.LAZY, 使用延迟加载
    orphanRemoval=false ,级联删除，孤儿删除
        
@ManyToOne //用在多方的 private 一方 属性名; 无需实例化
     targetEntiry=一方.class , 可以不写
     ....
     optional=true ,是否允许为空，设为false则必须始终存在非空关系

@JoinColumn  // 与 @ManyToOne一起使用，@OneToMany的mappedBy与@JoinColumn\@JoinTable冲突冲突
        name="外键名",
	    referencedColumnName="参考的键名", // 默认主键可以不写，该名称要么在数据库已经存在要么 主表@Colum声明
        unique.....
// ------ 多对多
@ManyToMany //两个多方使用
         targetEntiry=对方.class, 泛型指定可以不写
         casecade,fetch
         mappedBy="对方中的集合",放弃外键维护，与@JoinColum和 @JoinTable冲突
@JoinTable //写在没出现mappedBy的多方中
         name="xx",中间表名
         joinColumns={ @JoinColumn(name="中间表关联本实体类的外键")}, 
         inverseJoinColumns={@JoinColumn(name="中间表关联对方的外键")}
```



#### 2.2 jpa cascade值

````java
CascadeType.PERSIST 级联保存
    		REMOVE 级联删除 ：慎用
    		MERGE 级联更新
    		DETACH 撤销所有相关的外键关联
    		REFRESH 级联刷新
    		ALL
````



#### 2.3 一对多：公司 - 部门

一方(公司)为主表，多方(部门)为从表，从表设置外键(从表中的一列参照主表的主键)

```java
//-------模板
@Entity
@Table(name="company")
......{
    ....;
    //绑定与Orders的一对多关系，mappedBy= Orders中的Customers属性，表示由多方维护外键
    //级联操作(操作该方时一对多方进行同样的操作):所有
    @OneToMany(targetEntity=Orders.class,mappedBy="customer")
    private Set<Orders> orders = new HashSet<>();//需要实例化
}

@Entity
@Table(name="orders")
.....{
    .....;
    @JoinColumn(name="cust_id")//在orders表中加入外键cust_id
    @ManyToOne(cascade = {CascadeType.PERSIST,CascadeType.MERGE},fetch = FetchType.LAZY)
    private Customers customer;//不必实例化
}
// 删除一方时，如果有多方与之对应则无法删除，除非级联删除
```

#### 2.4 多对多

一个员工对应多个部门，一个部门对应多个员工

想要向一个已经存在的部门中添加新的员工，需要通过部门来操作

```java
@Entity
@Table(name="dept")
...
    @ManyToMany(fetch=FetchType.EARGE,mappedBy="depts")
    private List<Emp> emps = new ArrayList<>();

@Entity
@Table(name="emp")
...
    @JoinTable(name="dept_emp",
               joinColumns={@JoinColumn(name="emp_id")},
               inverseJoinColumns={@JoinColumn(name="dept_id")}
              )
    @ManyToMany
    private List<Dpet> depts = new ArrayList<>();
```



### 3 自定义SQL

####  3.1 JPQL  @Query()

```java
原则：
    1.不能出现表名,列名,只能出现java的类名,属性名，区分大小写
    2.不能写select * 要写select 别名，或不写select *
---------------查询语句 @Query
@Query("from Customers")// 查询所有
List<Customers> selectAll();

@Query("from Customers c where c.name = ?1 and c.age > ?2")//别名、参数
Customers selectByName(String name,Integer age);

@Query("from Customers c where c.name = :name")//别名参数
Customers selectByName(@Param("name") String name);

"from Customers order by age desc";//排序查询
"from Customers c where name like %:name%";//like查询
"from Customers c where level in :list";// in查询，传入参数为Collection
------------- 更新语句 @Transactional +  @Query + @Modifying
@Transactional //service层有事务时无需添加
@Query("update Customers set custLevel = ?1 where custId = ?2")
@Modifying
void updateLevel(String newLevel, Long id);
```

#### 3.2 原生SQL

```java
@Query(value="select * from customers",nativeQuery=true)

"select * from customers cust_id = :id"
```

#### 3.2 Specifications动态查询 

1. Dao 实现 JpaSpecificationExecutor<实体类>接口

   ```java
   public interface CustomerRepository extends JpaRepository<Customer,Long>,JpaSpecificationExecutor<Customer>
   ```

2. service层方法中创建Specification<实体类> 匿名类，实现toPredicate方法

   ```java
   Specification<Customers> specification = new Specification<Customers>() {
               @Override
               public Predicate toPredicate(Root<Customers> root, CriteriaQuery<?> 
                                            criteriaQuery, CriteriaBuilder criteriaBuilder) {
                   // root ：要查询的实体类
                   // criteriaBuilder : 构造查询条件 Predicate
                   // criteriaQuery : 查询语句 select groupby等
                   
                   //criteriaQuery.select(root.get("custName")); // 实体类必须有该参数的构造方法
                   // 查询条件1
                   Predicate p1 = criteriaBuilder.gt(root.get("custId").as(Long.class), 1);
                   // 查询条件2
                   Predicate p2 = criteriaBuilder.equal(root.get("custLevel").as(String.class), "2");
                   return criteriaBuilder.and(p1,p2);//组合查询
               }
           };
   ```

   

3. 将specification作为参数传递给dao层的 findOne,findAll,findCount方法

   ```java
   // 查询所有
   List<Customers> custs = customerRepository.findAll(specification);
   // 分页
   PageRequest pageable = PageRequest.of(当前页码,每页大小);
   Page<Customers> page = customerRepository.findAll(specification,pageable);
   ```

   