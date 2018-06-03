# Chapter3-聚合与排序

> 聚合：使用SQL函数进行多行汇总操作的方法。
>
> 排序：对查询结果进行生序、降序操作。



### Section1-对表进行聚合操作

> 使用聚合函数对表中查询结果进行计算合值、平均值等操作。
>
> 通常，聚合函数的计算，都会将包含NULL的行排除再进行计算。**但有一个是特殊的，count(*)	会统计包含NULL行在内的所有行数。**



#### 聚合函数

>五个常用的聚合函数：
>
>- COUNT
>- SUM
>- AVG
>- MAX/MIN
>- DISTINCT
>
>所谓聚合，就是将多行汇总为一行。表现在聚合函数上，就是将**查询出的多行结果作为参数输入聚合函数，由聚合函数进行计算，输出结果为一行。**



1. COUNT：计算查询结果的行数。**（COUNT函数的结果根据传入参数的不同而不同）**

   ```sql
   计算全部数据的行数：
   SELECT COUNT（*） FROM product；
   ```

   **注意：在COUNT（）函数中，不仅可以使用\*将全部列作为参数输入，也可以指定特定列。但这两种方式统计的行数是不同的。在COUNT（）函数中，根据传入参数的不同，选择不同的统计方式。如果传入\*，则会统计包含NULL行的所有行数，而如果传入特定列名，则在统计行数时会排除NULL行。**

   ```sql
   SELECT COUNT（purchase_price） FROM product；
   如果purchase_price列中存在NULL值，那么在COUNT的时候，会排除，统计结果也与COUNT（*）不一样。
   ```

2. SUM：计算合计值

   > **除了COUNT（）函数以外，其它聚合函数都不能将\*作为入参。**

   SUM（）只能传入特定列名去计算结果集中该列的总和。但是它不能传入\*，并且在计算合计值的时候，如果该列存在NULL值，则忽略。

   ```sql
   SELECT SUM（purchase_price） FROM product；
   ```

   **注意：SUM（）并不是将NULL作为0去处理，而是直接忽略不计，这一点与后续提到的AVG（）函数有一点区别。**

3. AVG：计算平均值

   用法与SUM（）一样

   ```sql
   SELECT AVG（purchase_price） FROM product；
   ```

   AVG（）针对NULL可以有两种处理方法：

   - 与SUM（）类似，忽略NULL值，在平均的总数上，也减去对应NULL的个数。
   - 可以将NULL作为0计算，在平均的总数上，会是所有参与数据的总数。

4. MAX/MIN：计算最大/最小值

   ```sql
   SELECT MAX（purchase_price）,MIN（purchase_price） FROM product；
   ```

   **有一点，MAX/MIN与其它函数不同。MAX/MIN能针对所有数据类型，而SUM/AVG函数只适用于数据类型为数值的列。**

5. DISTINCT：删除重复值

   ```sql
   SELECT COUNT（DISTINCT product_type） FROM product；
   ```

   统计商品的种类，先使用DISTINCT来删除重复的product_type，然后根据DISTINCT之后的结果，统计有多少个不同的product_type。如果DISTINCT于COUNT交换位置，将删除重复的操作放在最后，不能得到正确的结果。

---



