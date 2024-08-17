> [!warning]
> 🚨 扩展 SQL 只支持 MySQL。

## 第 1 课：了解 SQL

### SQL

**SQL** (Structured Query Language) ：结构化查询语言。专门用来与数据库进行沟通的语言，可以对数据库中的数据进行读写。

> [!note]
> 标准 SQL 也叫 ANSI SQL，由 ANSI 标准委员会管理，但不同的 DBMS 厂商或多或少都会对 SQL 进行扩展，这也是 SQL 兼容性问题的来源。

### Database

**“数据库”** (database) 是以某种组织方式存储的数据集合，是保存数据的容器。

>[!info]
>“数据库” 这个术语通常有两种含义：一种是指管理数据库的软件 —— 数据库管理系统（DBMS）；另一种指的是使用 DBMS 创建和管理的用来保存具有组织方式的数据的容器。
### Table

**“表”** (table) 是一种结构化的文件，用来存储特定种类的数据。例如顾客清单、产品目录等。

> [!warning]
> 表中的数据只能属于某一个种类，不能将多种类型数据存储到同一个表中，例如把顾客清单与订单详情都存储在一张表中，这对以后的查询和维护都会造成困难。

- 唯一性：同一数据库中的表名是唯一的。
- 表的模式 (schema) ：定义表的结构与特性。在结构上，比如表名、列名、列的数据类型以及约束条件；在特性上，比如表的索引、主键、外键等。
- “表” 由“行”和“列”组成。

### Column

**“列”** (column) 也叫**字段** (Field)。表由一个或多个列组成。

> [!warning]
> 表中的所有列都应属于某一特定种类的数据。

- 数据类型（datatype）：规定了列可以存储的数据类别，例如数值、文本、日期等。

### Row

**“行”** (row) 也叫**记录** (record)。表中的数据是按行存储的。

>[!info]
>行与列的交集区域被称之为单元格 (cell)，单元格的数据类型由其所在列定义。
### Primary key

**“主键”** (primary key)：由列定义，一列或几列，用于唯一标识表中的每一行数据。

> [!tip]
> 可以理解为表中的每一行数据定义一个唯一标识该记录的 ID。员工表，则可以使用员工 id；图书表，则可以使用国际标准书号 ISBN。
> 而这个唯一标识的 ID 就是保存在作为主键的列下，每一行都可以唯一标识当前行。

没有主键、更新与删除记录将会极为困难，因为你不能保证你的条件不会涉及到其它意外的行。

表中的任何列都可以设置为主键，只要满足以下条件：
- 主键值必须唯一。
- 主键值不能为 `NULL`。
- 主键值不允许被修改。
- 主键值不能被复用（被删除的行，以后新增的行也不能使用）。
### 语句

SQL 语句由子句（clause）组成。子句则有关键字加上所提供的数据构成。
```sql
SELECT prod_id
FROM Products
```

例如像上面的 SQL 语句，分别由 `SELECT prod_id` 子句与 `FROM Products` 子句构成，而每个子句也由 `SELECT` 与 `FROM` 关键字以及所需的数据构成。

- 关键字建议大写。
- 多条 SQL 语句必须要用分号`；`隔开。
- SQL 会忽略语句中的空白字符。
- 单行注释：`—` （两个连字符）或 `#`；多行注释：`/* */`

## 第 2 课：检索数据

使用 `SELECT` 语句从表中检索一个或多个数据列。

### 检索多个列

```sql
SELECT prod_id, prod_name, prod_price 
FROM Products;
```

* `SELECT` 关键字指定要检索的列。
* `FROM` 关键字指定来源的表。

### 检索所有列

使用 `*` 通配符来检索表的所有列
```sql
SELECT * 
FROM Products
```

> 列的顺序一般是表中出现的物理顺序

### 检索不同的值

使用 `DISTINCT` 关键字对检索列的结果进行去重。
```sql
SELECT DISTINCT prod_id 
FROM Products
```

> [!tip]
> `DISTINCT` 可以用与多个列。它本质是对检索的“行”进行去重。
### 限制检索行数

使用 `LIMIT` 关键字可以限制要检索的行数，使用 `OFFSET` 关键字可以指定检索行的起始位置。
```sql
SELECT prod_id
FROM Products
LIMIT 5 OFFSET 4
```

表的行号是从 `0` 开始。

> [!warning]
> 注意：`LIMIT` 与 `OFFSET` 关键字由 MySQL 扩展，非 ANSI SQL

## 第 3 课：排序检索数据

如果不对检索的数据进行排序，则默认按照据库中的顺序返回。但是一旦数据随后经过更新或删除，那么这个顺序也会被影响，从而可能导致检索的数据处于无序动态变化。

> 关系数据库设计理论认为，如果不明确规定排序顺序，就不应假定检索出的数据的顺序有任何意义。

### 排序数据

使用 `ORDER BY` 关键字对检索的列进行排序。
```sql
SELECT prod_id,prod_name
FROM Products
ORDER BY prod_id;
```

>[!tip] 
>注意：`ORDER BY` 子句应该总是出现在整个 `SQL` 语句的最后面，这是由 `ORDER BY` 子句的执行优先级决定的。

要排序的列并非必须要出现在检索的列中：
```sql
SELECT prod_name
FROM Products
ORDER BY prod_id;
```

### 排序多个列

`ORDER BY` 关键字在对多个列进行排序时，会优先以最近的列进行排序，如果排序的列存在多行相同的数据，才会用下一个列进行排序，依次类推。
```sql
SELECT prod_name
FROM Products 
ORDER BY prod_price, prod_id, prod_name
```

### 按列位置排序

使用表中列的相对位置来替代列名进行排序。
```sql
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY 2, 3
```

>[!tip]
> 该技术的应用场景只要看中表中列的位置，而不是列名。

### 排序方向

`ORDER BY` 关键字默认升序排序（`ASC`），通过指定关键字 `DESC`，可以实现降序排序。
```sql
SELECT prod_name
FROM Products
ORDER BY prod_price DESC, prod_id;
```

>[!tip]
>`DESC` 关键字只会对前面的列产生作用，如果需要对多个列都进行降序排序，则必须对每一个列都指定 `DESC` 关键字。

## 第 (4~6) 课：过滤检索数据

### WHERE 子句

使用 `WHERE` 子句对检索的数据进行过滤，得出符合条件的行（数据子集）。为 `WHERE` 关键字提供的数据也被称之为“过滤条件（filter condition）”或搜索条件。
```sql
SELECT prod_name, prod_id 
FROM Products
WHERE prod_price > 3
```

>[!info]
> 虽然在应用层也能通过逻辑代码对数据进行过滤，但是不建议这样去做。一是影响应用的性能、浪费网络带宽；二是应用控制层与模型层尽可能的解耦。
### 基本操作符

操作符与要过滤的列构成了 `WHERE` 子句的“过滤条件”。

| 操作符 | 说明   |
| --- | ---- |
| =   | 等于   |
| !=  | 不等于  |
| <   | 小于   |
| <=  | 小于等于 |
| >   | 大于   |
| >=  | 大于等于 |

### BETTWEN - 范围操作符

使用 `BETWEEN` 关键字过滤某个范围的值，需要通过 `ADN` 操作符指定范围的起始值和结束值。
```sql
SELECT prod_name 
FROM Products 
WHERE BETWEEN 5 ADN 10;
```

> [!tip]
> `BETWEEN` 是一个闭区间因此包含起始与结束值。

### IS NULL - 为空操作符

`NULL` 表示“无值”或“空值”。
它与数值的 0 ；字符串的空字符串以及空白字符并不等同。
```sql
SELECT prod_name
FROM Products
WHERE prod_price IS NULL;
```

### NOT 操作符

否定其后的所有条件（`NOT` 操作符不能单独使用，它总是出现在其它操作符的前面）。
```sql
SELECT prod_id, prod_name
FROM Products
WHERE NOT vend_id IN (200,201,202);
```

> `NOT` 操作符的优先级最高。

### AND 操作符

检索满足所有条件的行。
> [!info]
> `AND` 操作符可以为 `WHERE` 子句**联结**多个过滤条件，实现更强的过滤控制。

```sql
SELECT prod_id, prod_name 
FROM Products
WHERE prod_price > 100 AND vend_id = 200;
```

> `AND` 操作符的优先级高于 `OR` 操作符但低于 `NOT` 操作符

```sql
SELECT prod_id, prod_name 
FROM Products
WHERE vend_id = 200 OR vend_id = 201 AND prod_price >= 10;
```

由于 `AND` 运算符优先级高的缘故，上面的条件,优先执行如下条件：
```SQL
vend_id = 201 AND prod_price >= 10
```

最后再执行
```sql
vend_id = 200
```

如果是想查看供应商id 200 与 201 下所有价格大于 10 的产品，应主动使用“圆括符”改变运算符的默认优先级：
```sql
WHERE (vend_id = 200 OR vend_id = 201) AND prod_price > 10
```

### OR 操作符

检索满足任一条件的行。
```sql
SELECT prod_id, prod_name 
FROM Products
WHERE vend_id = 200 OR vend_id = 201;
```

根据上面的条件过滤，会返回所有供应商id 为 200 或 201 的产品。

> `OR` 运算符优先级最低。`NOT` > `AND` > `OR`

### IN 操作符

检索满足条件范围内的任一条件的行。“条件范围”是在圆括号中用逗号隔开的多个合法值。
```sql
SELECT prod_id, prod_name 
FROM Products
WHERE vend_id IN (200,201,202);
```

> [!tip]
> `IN` 操作符与 `OR` 操作符功能类似，但是语法比 `OR` 更简洁，直观。

`IN` 操作符的其它优点如下：
1. 对比 `OR` 操作符，语法更清楚、直观、执行效率更高。
2. 检索行的顺序更容易管理。
3. 可以包含其它 `SELECT` 语句，可以动态的建立 `WHERE` 子句（子查询）。

### LIKE - 通配符操作符

>[!info]
>前面的操作符都是使用“字面量”来对已知的值进行过滤，如果想搜索未知的值，便需要使用通配符进行“模式匹配”。
>
>**通配符（Wildcard）**：匹配特定数据的特殊字符。
>**搜索模式（Search Pattern）**： 使用通配符、字面量或两者组合构成的搜索条件。

- **`%` 百分号通配符**
    
    匹配 0 个或多个字符。不能匹配 `NULL` 空值。
    ```sql
    SELECT cust_name, cust_email
    FROM Customers
    WHERE cust_email LIKE "b%@gmail.com";
    ```
    
- **`_` 下划线通配符**
    
    匹配 1 个字符，可以多次使用，以匹配多个字符。
    ```sql
    SELECT prod_id, prod_name
    FROM Products
    WHERE prod_name LIKE "__ car";
    ```
    

一些通配符的使用技巧：
1. 不要过度使用通配符，通配符执行效率低。
2. 不要将通配符放置在搜索模式的开始处，会影响匹配速度。

> [!warning]
> 通配符搜索只能用于文本(字符串)，非文本数据类型字段不能使用通配符搜索。

## 第 7 课：创建计算字段

> [!tip]
> 大多数的计算字段可由客户端或前端程序自行处理，利用用户代理设备的计算资源，减轻服务端的压力。

### 计算字段

接收一个或多个列的结果，组合处理为一份内容返回。（常见的场景便是组合多个列的值进行格式化输出，例如姓名与 ID）

### 字符串拼接

使用 `CONCAT` 函数在 `SELECT` 语句中拼接多个列的值。
```sql
SELECT CONCAT(cust_name, '(', cust_id, ')') 
FROM Customers
ORDER BY cust_id DESC; 
```

拼接字符串时还可以借助一些文本处理函数来对字符串进行处理，比如去除空格的函数：
- `TRIM`： 去除左右空格。
- `LTRIM`：去除左空格。
- `RTRIM`：去除右空格。
```sql
SELECT CONCAT(TRIME(cust_name), '(', TRIME(cust_id), ')')
FROM Customers;
```

### 算术计算

SQL 还支持在 `SELECT` 语句中对列进行基本的算术运算：`+、-、*、/` 。
```sql
SELECT prod_name, prod_price * 0.9
FROM Products
ORDER BY prod_price DESC;
```

### 别名

“计算字段” 是在 `SELECT` 语句运行时创建的，并不会保存到实际的数据库表中，因此计算字段没有列名，无法被客户端程序引用，为了解决这个问题，可以通过“别名”为计算字段赋予一个字段名 （Field name）。

“别名（Alias）” 是一个字段或值的替换名，通过 `AS` 关键字定义：
```sql
SELECT prod_name, (prod_price * 0.9) AS prod_sales_price
FROM Products;
```

> “别名”也被称之为导出列。

### 执行自定义表达式

利用 `SELECT` 语句可以执行计算字段、函数的特性，当省略了 `FROM` 子句后，便可以执行自定义的表达式。这对测试、计算以及检验非常有用。
```sql
SELECT 2 * 3;
SELECT LTRIM(' abc');
SELECT CURDATE();
```

## 第 8 课：使用函数处理数据

使用函数来对数据进行处理与格式。
函数可以运行在多个 SQL 语句中，比如 `SELECT` 语句、`WHERE` 子句等。

>[!warning]
> SQL 函数因 DBMS 的原因不具有可移植性（Portable）。

### 文本处理函数

- `LENGTH` ：返回字符串长度。
- `LEFT`：返回字符串左边的字符。
- `RIGHT`：返回字符串右边的字符。
- `SUBSTRING`：截取字符串指定的字符。
- `LOWER`：将字符串转换为小写。
- `UPPER`：将字符串转换为大写。
- `TRIM`：去除空格。
- `LTRIM`：去除左边空格。
- `RTRIM`：去除右边空格。
- `SOUNDEX`：返回谐音类似的字符（只能用于英文）。

### 日期时间函数

- `CURDATE`：返回当前日期。
- `YEAR` / `MONTH` / `DAY` / `HOUR` / `MINUTE` / `SECOND` ：返回对应的日期时间。
- `WEEK` ：返回当前多少周。
- `NOW`：返回当前日期和时间。
- `CURTIME`：返回当前时间。
- `DATE_FORMAT`：格式化日期。

```sql
SELECT order_id, order_num 
FROM OrderItems
WHERE YEAR(CURDATE()) = "2024";
```

### 数值处理函数

- `ABS`：绝对值函数。
- `SQRT`：平方根函数。
- `EXP`：指数函数。
- `COS`：余弦函数。
- `SIN`：正弦函数。
- `TAN`：正切函数。
- `PI`：π 函数，保留 6 位小数。

### 系统信息函数

- `DATABASE`：返回数据库名称
- `CURRENT_USER`：返回当前用户名

## 第9课：汇总数据

>[!tip] 
>检索数据无非两种：返回“明细数据”或“汇总数据”。

使用“**聚合函数（aggregate function）**”可以返回表的汇总数据。无需返回表的全部数据，从而提高性能，减少检索时间以及节省网络带宽。
使用“聚合函数”可以很方便地对表的全部数据进行检索，返回汇总数据（摘要数据），而不用返回具体的明细数据。
### AVG

平均值函数 - average。用当前列的“和”除以行数。
传参：列名。
```sql
SELECT AVG(prod_price) AS prod_avg_price
FROM Products
WHERE prod_price >= 20;
```

> AVG 函数会忽略值为 `null` 的行（不会列入除数之中）。

### COUNT

合计行函数。统计表中记录的行数或指定列的行数。
传参：`*`、列名。
```sql
UPDATE Products 
SET prod_price = NULL
WHERE prod_id = 5;
```

参数如果是 `*` 号，则会统计表中的所有行，包含值为 `null` 的行。
```sql
SELECT COUNT(*)
FROM Products;
--- 5
```

如果参数是具体的列名，则会忽略值为 `null` 的行。
```sql
SELECT COUNT(prod_price)
FROM Products;
--- 4
```

### MAX

最大值函数。返回指定列的最大值。
传参：列名。
1. 如果列的类型是数值、日期则返回最大值。
2. 如果列的类型是文本，则返回该列在自动排序（升序）后的最后一行。

```sql
SELECT MAX(prod_price) as prod_max_price
FROM Products;
```

> `MAX()` 函数会忽略列值为 `null` 的行。

### MIN

最小值函数。返回指定列的最小值。
传参：列名。
1. 如果列的类型是数值、日期则返回该列的最小值。
2. 如果列的类型是文本、则返回该列在自动排序（降序）后的首行。

> `MIN()` 函数会忽略列值为 `nul` 的行。

### SUM

求和函数。对指定的列进行求和。
传参：列名。
```sql
SELECT SUM(prod_price) as summary
FROM Products; 
```

你还可以在 `SUM` 函数中执行计算字段。
```sql
SELECT SUM(item_price * quantity) as total_price
FROM OrderItems
WHERE order_num = 20005;
```

> `SUM()` 函数会忽略值为 `null` 的行。

### 聚合不同的值

在第二课检索数据我们学过用 `DISTINCT` 关键字来对检索结果进行去重。同理，该关键字在聚合函数中依然适用。先去重再聚合：
```sql
SELECT AVG(DISTINCT prod_price) as prod_avg_price
FROM Products
WHERE vend_id = 203;
```

在聚合函数中使用 `DISTINCT` 关键字需要以下事项：
- `DISTINCT` 关键字不能与 `COUNT(*)` 函数一起使用。
- `DISTINCT` 关键字与 `MAX` 或 `MIN` 函数结合使用无意义。

## 第 10 课：分组数据

“分组”是另一种更有效的数据汇总方式。数据分组实质上是按特定维度来聚和数据，找出当前维度下具有共性的数据（记录），而“维度”便是分组的“列”。

> 分组可以将数据分为多个逻辑组， 对每个组进行聚合计算。

### 创建分组

使用 `GROUP BY` 子句可以创建 “分组列”。
```sql
SELECT sale_id, cust_id
FROM Customers
GROUP BY sale_id, cust_id;
```

在客户表中，销售与客户是一对多的关系，因此按销售 id 进行分组，可以聚合计算出每个销售所关联的所有客户信息。

`SELECT` 语句结合 `WHERE` 子句可以从表中检索出符合条件的记录行，这些记录行称之为“明细数据”。
`GROUP BY` 子句则可以对记录行再进一步按照指定的列进行分组，基于特定的维度重新聚合计算。而这些聚合后的记录则被称之为“分组数据”。再结合聚合函数，还可以对每个分组数据进行统计和汇总。

实际的例子，有一个学生表 —— `Students`。有姓名、时间、班级等列，我们既可以按照入学时间分组，返回每个入学时间下的学生分组数据。
```sql
SELECT start_time, name
FROM Students
GROUP BY start_time, name
```

也可以按照班级进行分组，使用聚合函数，返回隶属于同一个班级下的学生数量。
```sql
SELECT class, COUNT(*) as student_num
FROM Students
GROUP BY class, name
```

### 过滤分组

使用 `HAVING` 子句对分组数据进行过滤。
```sql
SELECT sale_id, COUNT(cust_id) as cust_num
FROM Customers
WHERE YEAR(datetime) = "2024"
GROUP BY sale_id
HAVING COUNT(cust_id) >= 10
```

先通过 `WHERE` 子句对数据按年过滤，然后再按销售 id 进行分组，最后使用 `HAVING` 子句对分组数据按统计行数过滤，最终实现 —— 统计 24 年达成 10 个及以上的销售。

**`HAVING` 与 `WHERE` 对比：**
1. 在大多数 DBMS 中 `HAVING` 可以兼容 `WHERE`，但 `HAVING` 主要用于过滤分组数据，并且支持以聚合函数为过滤条件。`WHERE` 子句只能以表达式、通配符、普通函数等为过滤条件。
2. 在语句执行的优先级上，`WHERE` 优先于 `GROUP BY`，因此通常说 `WHERE` 是在分组之前过滤，`HAVING` 是在分组之后过滤，这样便可以只让符合条件的记录参与分组。
3. 虽然 `HAVING` 兼容 `WHERE`，当对行过滤时理因只使用 `WHERE`，对分组过滤理因总使用 `HAVING`。
### 分组限制条件

**`GROUP BY` 与 `SELECT` 的相互影响：**

1. `GROUP BY` 子句支持分组嵌套（多个分组列），但数据只会在最后一个分组列上进行汇总。
2. `GROUP BY` 子句不能使用聚合函数、别名。
3. 除聚合函数、别名外，在 `SELECT` 语句中使用到的任何字段（包含表达式、函数）都必须也要在 `GROUP BY` 子句中给出。换句话说，在建立分组时，指定的所有列都一起计算，返回的分组数据是汇总后的数据，所以不能从个别的列取回数据。
4. `GROUP BY` 子句必须出现在 `WHERE` 子句之后，`ORDER BY`子句之前。
5. `HAVING` 子句兼容 `WHERE` 子句，可以使用聚合函数作为条件，但只建议用于分组数据过滤。

## 第 11 课：使用子查询

SQL 中的查询通常指的就是 `SELECT` 语句。
前面学习的查询都是 SQL 的“简单查询”，从一张表中检索数据。
### 子查询
子查询 (subquery) 也叫嵌套查询。其执行顺序是由内到外，本质是将内部查询的结果作为外部查询的条件。 ^0ba0e2
```sql
SELECT cust_id, cust_name, cust_email
FROM  Customers
WHERE cust_id IN (
					SELECT cust_id
					FROM Orders
					WHERE order_num IN (
											SELECT order_num
											FROM OrderItems
											WHERE item_price >= 10
										 )
					);
```
子查询只能返回单个列，原因在于 `IN` 操作符的条件范围只支持一个列的结果。

>[!tip]
>子查询是对 `WHERE` 子句和 `IN` 操作符可以嵌套 `SELECT` 语句的巧妙运用。

### 将子查询作为计算字段

`SELECT` 语句支持嵌入其它的 `SELECT` 语句，然后使用 `AS` 语句将查询结果作为导出列。
```sql
SELECT cust_id, cust_email, (SELECT COUNT(*) FROM Orders WHERE Customers.cust_id = cust_id) as total_orders
FROM Customers;
```

对于不同表的字段匹配，建议使用“完全限定列名”，即完整的指定“表名.列名”的格式： ^e718b1
```sql
WHERE Customers.cust_id = Orders.cust_id
```
如果省略命令，则字段默认从最近的 `SELECT` 语句中取得。

两种子查询对比，`WHERE IN` 中的子查询其复杂度会随着嵌套结构的增加而增加；嵌套在 `SELECT` 中的子查询，会随数着表数据量的增加而性能下降。

## 第 12 课：联结表

### 关系表

关系表的设计就是要把信息分解成多个表，一类数据一个表。这样的好处是：
1. 避免存储重复数据，存在关联的数据则需要通过多个表相同的值进行关联。
2. 更方便的更新数据，因为没有重复数据，所以只需对一张表更新一次即可，不会影响到相关的其它表。
3. 由于数据不重复，数据显然是一致的，使得处理数据和生成报表更简单。

>[!tip]
>只需记住，关系数据库的设计基础就是避免重复数据。

	一个具体的例子，存储产品信息时，会将产品对应的供应商与产品本身信息分解成两个表进行
	存储：一张产品表（Products）；一张产品供应商表（Vendors）。然后使用供应商 ID 进行
	关联。

> [!info]
> 这些特性都使得关系表可以更有效的存储数据、更方便的处理数据。因此关系型数据库对比其它的非关系数据库，更具有“可伸缩性”（Scale Well）。

### 为什么使用联结？

虽然将数据分解到多张表中，可以更有效的存储数据，更方便的处理数据，增加了数据库的可伸缩性，但这些是有代价的，那就是让数据的检索变的困难。

在关系数据库中，怎样使用一条 `SELECT` 语句从多张关系表中检索出完整的数据？

虽然 “[[#子查询]]” 也可以检索多张表，但它是通过嵌套执行 `SELECT` 语句实现的，从性能考量，并不是最优解。好在 SQL 提供了另一套更可靠的机制，那就是 —— “**联结**（join）”。

“联结” 可以在一条 `SELECT` 语句中检索多张表，返回一组数据。注意，联结不是物理实体，而是在执行查询时创建的。

### 创建联结

创建“联结”时只需要提供以下信息：
1. 联结的多张表：表名。
2. 联接条件：决定了联结在运行时能否从多张表中关联出正确的行。
```SQL
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;
```

“联接条件” 非常重要。在联结两个表时，实际要做的就是按照联接条件将第一个表的每一行与第二个表的每一行进行配对，如果配对成功，则返回关联正确的行。

如果联接条件没有正确生效，则返回配对失败的行，也就是不正确的数据。

当联接条件被省略时，联结将返回多张表的“**笛卡尔积**（cartesian product）”， —— 也就是第一张表中的每一行将与第二张表中的每一行强行配对，而不管它们在逻辑上是否成立。

> [!info]
> 有时返回“笛卡尔积”的联结也被称之为“叉联结 (Cross Join)”

### 内联结

**内联结**（inner join），也叫“等值联结”。它基于两个表之间的相等性测试。

前面学习的内联接语法也被称之为简单等值语法，对应的，ANSI SQL 提供了另一种标准语法 —— `INNER JOIN` 语法，它明确指出了联结的类型：
```SQL
SELECT vend_name, prod_name, prod_price
FROM Vendors
INNER JOIN Products ON Vendors.vend_id = Products.vend_id;
```

对比简单等值语法，主要有以下几点不同：
1. `FROM` 子句不再列出要联结的多张表，而是只列出第一张表。
2. 新增了 `INNER JOIN` 关键字，用于指定要内联结的第二张表。
3. 联接条件用 `ON` 子句给出，而不再是使用 `WHERE` 子句。

> [!tip] 
> 是选择简单语法还是标准语法，这通常仁者见仁，智者见智，至少 DBMS 同时支持两者。但通常 SQL 语言纯正论者会用鄙视的眼光看待简单语法。

### 联结多张表

“内联结”虽然是基于两个表的相等性测试，但它限制的只是同一时刻最多只能两两联结，并不是限制最大可联结的表数量。

>[!tip]
>最多可联结的表数量并没有一个明确值，因为这是由不同的 DBMS 自身决定的。

回顾第11课子查询，为了检索订单价格大于等于 10 的所有客户信息，我们需要在客户表、订单表、订单详情表中通过嵌套查询语句实现。现在通过“联结”可以很方便的从多张表中检索出所需的关联数据。
```SQL
SELECT Customers.cust_id, cust_name, cust_email
FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id AND Orders.order_num = OrderItems.order_num AND item_price >= 10;
```

>[!warning]
>请注意需要使用完全限定列名的地方。

分析上面 `WHERE` 子句的过滤条件，它由两部分组成，两个“联结条件” 和一个“过滤条件”。首先看第一个联结条件 `Customers.cust_id = Orders.cust_id`,它通过相等性测试，返回 `Customers` 表与 `Orders` 表中匹配到的行，然后再用返回的结果，继续通过第二个关联条件 `Orders.order_num = OrderItems.order_num` 联结 `OrderItems` 表，最后再用过滤条件对 `item_price >= 10` 对联结结果进行过滤，返回订单价格大于等于 10 的所有联结行。最重才是由 SELECT 语句从中检索出客户 id、姓名、邮箱列返回。

下面是用标准语法编写：
```SQL
SELECT *
FROM Customers
INNER JOIN Orders ON Customers.cust_id = Orders.cust_id
INNER JOIN OrderItems ON Orders.order_num = OrderItems.order_num
WHERE item_price >= 10;
```
