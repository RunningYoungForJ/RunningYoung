# Chapter2-查询基础

###Section1-SELECT语句基础

####列的查询

1. select语句包括了select子句和from子句。**(其中，from子句不是必须的，但没有from子句的查询比较少见)**

   ```sql
   SELECT product_id,product_name,purchase_price FROM product;
   ```

2. 查询结果中的列的顺序与select子句中列顺序一致

3. 查询出所有列
   1. 使用*查询所有列时，就不能指定结果的列顺序，最后的顺序依据所查询表定义的列顺序。

      ```sql
      SELECT * FROM product;
      ```

4. 为列设定别名

   1. 使用as为列设定别名

      ```sql
      SELECT product_id AS id,product_name AS NAME,purchase_price AS price  FROM product;
      ```

   2. 别名是中文时，需要使用双引号。因为使用双引号可以设定包含空格的别名，如果中文别名中没有空格，那么单引号也可以。

      ```sql
      SELECT product_id AS '编号',product_name AS '名称',purchase_price AS '价格'  FROM product;
      ```

5. 常数的查询

   select子句中不仅可以书写列名，还可以书写常数。

   ```sqk
   SELECT '商品' AS STRING, 38 AS number, '2009-02-24' AS DATE, product_id, product_name FROM Product;
   ```

6. 从结果中删除重复行

7. 删除某一列中的重复行，在select子句中使用distinct来实现

   ```sql
   SELECT DISTINCT product_type FROM product;
   ```

   1. 在使用distinct时，null也会作为一类数据，多个指定列为null的数据会合并为一个。

   2. distinct也可以作用在多个列上，此时，多个列将组合，多个列均相同的数据会组合为一条

      ```sql
      SELECT DISTINCT product_typeregist_date FROM product;
      ```

   3. 注意：distinct只能用在第一个列名之前，不可以放在列中间。

      ```sql
      错误：SELECT  product_type DISTINCT regist_date FROM product;
      ```

8. 根据where语句选择记录

   1. where子句用来指定查询数据的条件

      ```sql
      SELECT product_name,product_type FROM product WHERE product_type='衣服';
      ```

   2. 执行顺序：首先通过where子句查询出符合条件的结果，然后，再根据select子句构造出指定列的结果视图

   3. where子句必须紧跟在from子句之后。

9. 注释的书写方法

   1. —：单行注释
   2. /* */：多行注释

---

### Section2-算术运算符和比较运算符

> 运算符就是对其两边的列或值进行运算，（计算大小或比较）的符号。
>
> 使用算数运算符可以进行四则运算。
>
> 括号可以提升运算的优先顺序。
>
> 包含NULL的运算，其结果也是NULL。
>
> 比较运算符可以用来半段列或值是否相等。
>
> 与NULL比较，需要用**IS NULL**或**IS NOT NULL**运算符。



#### 算术运算符

1. 将商品价格*2后以sale_price_2字段名表示出来。

   ```sql
   SELECET sale_price,sale_price*2 AS sale_price_2 From product;
   ```

2. 每种运算都是以行为单位来执行。

3. **所有包含NULL的计算，结果肯定为NULL。像5/0会报除数为0的错误，但NULL/0不会报错，其计算结果是NULL，这个需要注意一下。**

   ```sql
   5+NULL=NULL;
   10-NULL=NULL;
   1*NULL=NULL;
   4/NULL=NULL;
   NULL/9=NULL;
   NULL/0=NULL;
   ```



#### 比较运算符

> 包含：>、=、<、>= 、<= 、<> 、IS NULL、 IS NOT NULL



1. 比较运算符=，相等。

   ```sql
   SELECT * FROM product WHERE sale_price=500;
   ```

2. 比较运算符<>，不等于。

   ```sql
   SELECT * FROM product WHERE sale_price=500;
   ```

3. 其它的比较运算符还有：>、>=、<、<=。

4. 可以结合使用算术运算符和比较运算符，对计算结果进行比较。

   ```sql
   SELECT * FROM product WHERE sale_price-purchase_price>=500;
   ```

5. **对字符串类型的值进行比较运算符计算，是按照字典顺序进行比较**。从前向后，依次每位进行比较，直到某位满足比较条件，则输出该字符串。

   ```sql
   SELECT * FROM Chars WHERE chr>'2';
   ```

   在Chars表中，只有chr列，该列的类型是字符串类型。里面存储的值为'1','2','3','10','11','222'。在执行上述比较时，根据字典规则，满足>'2'的字符串只有'3'和'222'。

6. 在上述四种比较运算符种，比较结果均不会包含NULL，因为NULL不是一个确定值，不能确定是否满足比较条件。

   ```sql
   SELECT * FROM product WHERE sale_price<>2800;
   ```

   在product表中，查询sale_price不等于2800的，表中原有数据包含两个sale_price为NULL的，但在查询结果中，是不包含这两个NULL行的。因为NULL不是一个确定值，无法与2800进行判断。**（实际上，NULL无法与任何确定的类型进行比较判断）**

7. 常规的比较运算符均是针对确定类型的值比较，为了能够比较NULL，需要使用特使的运算符：IS NULL/IS NOT NULL。

   ```sql
   SELECT * FROM product WHERE sale_price IS NULL;
   ```



###Section3-逻辑运算符

> 使用逻辑运算符，可以把多个查询条件进行组合。
>
> NOT运算符可以生成“不是～”这样的查询条件。
>
> AND运算符表示当两边查询条件都成立时，该行结果才为true。
>
> OR运算符表示两边条件有一个成立即可。
>
> 值在数据库中有三种结果：TRUE、FALSE、**UNKNOWN（不确定）**。
>
> SQL中的逻辑运算包含**真、假、不确定三种逻辑判断**。



#### NOT运算符

1. 表示否定的运算符，即逻辑判断中的非。

2. **NOT不能单独使用，必须结合查询条件。用来对某一查询条件进行否定操作。**

   ```sql
   SELECT * FROM product WHERE NOT sale_price >=1000;
   ```

   对查询条件sale_price>=1000做NOT操作，表示查询product表中，价格不大于等于1000的商品。等价于下面一条查询语句。

   ```sql
   SELECT * FROM product WHERE sale_price <1000;
   ```

3. 需要知道的是：NOT可以用来否定某一查询条件，但也不能滥用NOT。



#### AND和OR运算符

1. AND：在其两侧的查询条件都成立时，整个查询条件才会成立。
2. OR：在其两侧的查询条件有一个成立时，整个查询条件就成立。



#### 使用括号强化查询处理

1. 查询商品种类为办公用品，并且，登记日期是2009年9月11日或者2009年9月20日的商品。

   ```sql
   SELECT * FROM product WHERE product_type='办公用品' AND (regist_date='2009.9.11' OR regist_date='2009.9.20');
   ```

2. 在逻辑运算符中，优先级从高到低分别是：NOT、AND、OR。在上面语句中，如果不使用括号，会先执行AND然后再执行OR，在查询条件组合上会出错。



#### 逻辑运算符和真值

​	逻辑运算符作用在查询条件上时，每一个查询条件的结果都是用真值来反映。TRUE为查询条件成立，FALSE为查询条件不成立。



#### 含有NULL的真值

1. 在比较运算符中，针对NULL采用IS NULL或者IS NOT NULL来特殊处理。

2. 在逻辑运算符中，对NULL值的逻辑判断，也采用特殊处理。其结果既不是TRUE，也不是FALSE，而是UNKNOWN（不确定）。

3. UNKNOWN组合的真值表

   | P      | Q      | AND    | OR     |
   | ------ | ------ | ------ | ------ |
   | 真     | 不确定 | 不确定 | 真     |
   | 假     | 不确定 | 假     | 不确定 |
   | 不确定 | 真     | 不确定 | 真     |
   | 不确定 | 假     | 假     | 不确定 |
   | 不确定 | 不确定 | 不确定 | 不确定 |



