## <font color="#0FF">SELECT</font>
|	属性	|	描述	|
| ---- | ---- |
|	`id`	|	在命名空间中唯一的标识符，可以被用来引用这条语句。	|
|	`parameterType`	|	将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 `MyBatis` 可以通过类型处理器（`TypeHandler`）推断出具体传入语句的参数，默认值为未设置（`unset`）。	|
|	`parameterMap`	|	用于引用外部 `parameterMap` 的属性，目前已被废弃。请使用行内参数映射和 `parameterType` 属性。	|
|	`resultType`	|	期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 `resultType` 和 `resultMap` 之间只能同时使用一个。	|
|	`resultMap`	|	对外部 `resultMap` 的命名引用。结果映射是 `MyBatis` 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 `resultType` 和 `resultMap` 之间只能同时使用一个。	|
|	`flushCache`	|	将其设置为 `true` 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：`false`。	|
|	`useCache`	|	将其设置为 `true` 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 `select` 元素为 `true`。	|
|	`timeout`	|	这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（`unset`）（依赖数据库驱动）。|
|	`fetchSize`	|	这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值。 默认值为未设置（`unset`）（依赖驱动）。	|
|	`statementType`	|	可选 `STATEMENT`，`PREPARED` 或 `CALLABLE`。这会让 `MyBatis` 分别使用 `Statement`，`PreparedStatement` 或 `CallableStatement`，默认值：`PREPARED`。	|
|	`resultSetType`	|	`FORWARD`_`ONLY`，`SCROLL`_`SENSITIVE`, `SCROLL`_`INSENSITIVE` 或 `DEFAULT`（等价于 `unset`） 中的一个，默认值为 `unset` （依赖数据库驱动）。	|
|	`databaseId`	|	如果配置了数据库厂商标识（`databaseIdProvider`），`MyBatis` 会加载所有不带 `databaseId` 或匹配当前 `databaseId` 的语句；如果带和不带的语句都有，则不带的会被忽略。	|
|	`resultOrdered`	|	这个设置仅针对嵌套结果 `select` 语句：如果为 `true`，将会假设包含了嵌套结果集或是分组，当返回一个主结果行时，就不会产生对前面结果集的引用。 这就使得在获取嵌套结果集的时候不至于内存不够用。默认值：`false`。	|
|	`resultSets`	|	这个设置仅适用于多结果集的情况。它将列出语句执行后返回的结果集并赋予每个结果集一个名称，多个名称之间以逗号分隔。|


```xml
<select id="selectUsers" resultType="User">
  select id, username, password
  from users
  where id = #{id}
</select>
```

## <font color="#0FF">INSERT</font>
批量插入
```xml
<insert 
    id="insertAuthor" 
    useGeneratedKeys="true"
    keyProperty="id">
  insert into 
  Author(username, password, email, bio) 
  values
  <foreach item="item" collection="list" separator=",">
    (#{item.username}, #{item.password}, #{item.email}, #{item.bio})
  </foreach>
</insert>
```
## <font color="#0FF">UPDATE</font>

## <font color="#0FF">DELETE</font>


## <font color="#0FF">SQL 静态复用</font>
```sql
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password ></sql>
```
```xml
<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

```xml
<sql id="sometable">
  ${prefix}Table
</sql>

<sql id="someinclude">
  from
    <include refid="${include_target}"/>
</sql>

<select id="select" resultType="map">
  select
    field1, field2, field3
  <include refid="someinclude">
    <property name="prefix" value="Some"/>
    <property name="include_target" value="sometable"/>
  </include>
</select>
```
resultMapy映射
```xml
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
```
高级结果映射
```xml
<!-- 非常复杂的语句 -->
<select id="selectBlogDetails" resultMap="detailedBlogResultMap">
  select
       B.id as blog_id,
       B.title as blog_title,
       B.author_id as blog_author_id,
       A.id as author_id,
       A.username as author_username,
       A.password as author_password,
       A.email as author_email,
       A.bio as author_bio,
       A.favourite_section as author_favourite_section,
       P.id as post_id,
       P.blog_id as post_blog_id,
       P.author_id as post_author_id,
       P.created_on as post_created_on,
       P.section as post_section,
       P.subject as post_subject,
       P.draft as draft,
       P.body as post_body,
       C.id as comment_id,
       C.post_id as comment_post_id,
       C.name as comment_name,
       C.comment as comment_text,
       T.id as tag_id,
       T.name as tag_name
  from Blog B
       left outer join Author A on B.author_id = A.id
       left outer join Post P on B.id = P.blog_id
       left outer join Comment C on P.id = C.post_id
       left outer join Post_Tag PT on PT.post_id = P.id
       left outer join Tag T on PT.tag_id = T.id
  where B.id = #{id}
</select>
```
```xml
<!-- 非常复杂的结果映射 -->
<resultMap id="detailedBlogResultMap" type="Blog">
  <constructor>
    <idArg column="blog_id" javaType="int"/>
  </constructor>

  <result property="title" column="blog_title"/>

  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
    <result property="favouriteSection" column="author_favourite_section"/>
  </association>

  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>

    <result property="subject" column="post_subject"/>

    <association property="author" javaType="Author"/>

    <collection property="comments" ofType="Comment">
      <id property="id" column="comment_id"/>
    </collection>

    <collection property="tags" ofType="Tag" >
      <id property="id" column="tag_id"/>
    </collection>
    
    <discriminator javaType="int" column="draft">
      <case value="1" resultType="DraftPost"/>
    </discriminator>
  </collection>
</resultMap>
```