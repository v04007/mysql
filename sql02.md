#### 1,最大值: max (列名)  
#### 2,最小值: min (列名)  
#### 3.平均值: avg(列名)  
#### 4,总和: sum(列名)  
#### 5,总记录数: count(列名)  
#### 最终都会得到一个结果,对于一个结果的使用可以当成是一个数字来用  

#### SELECT max(salePrice) FROM product;  
  
    
### 查询所有商品平均零售价  
  
SELECT avg(salePrice) FROM product;  
  
查询商品ID总数  
SELECT COUNT(ID) FROM product;  
SELECT COUNT(*) FROM product;  
  
  
查询分类为2的商品总数  
  
SELECT COUNT(*) FROM product WHERE dir_id=2  
  
查询商品的最小零售价,最高零售价,以及所有商品零售价总和  
SELECT MIN(salePrice),MAX(salePrice),SUM(salePrice) FROM product;  
  
###  ------分组函数------  
语法格式：SELECT 聚集函数或者分组的列  
FROM table_name  
WHERE 条件  
GROUP BY列名  
HAVING分组之后的条件  
  
   
查询每个商品分类编号和每个商品分类各自的平均零售价,  
SELECT dir_id,AVG(salePrice) FROM product GROUP BY dir_id;  
查询每个商品分类编号和每个商品分类各自的商品总数。  
SELECT dir_id, COUNT(*) FROM product GROUP BY dir_id;  
查询每个商品分类编号和每个商品分类中零售价大于100的商品总数.  
SELECT dir_id,COUNT(*) FROM product WHERE salePrice>100 GROUP BY dir_id;  
查询零售价总和大于1500的商品分类编号以及总零售价和:  
#### 1、where后面不能跟聚合函数  
#### 2、使用聚合函数的结果做筛选时候必须使用HAVING  
SELECT dir_id,SUM(salePrice) FROM product GROUP BY dir_id HAVING SUM(salePrice)>1500;   

###  -------------  使用等值条件链表查询  -------------  
SELECT *FROM product,productdir WHERE product.dir_id=product.id  
  
  
#### 内连接查询:严格按照等值条件配置数据。  
#### 内连接分为:隐式内连接、显示内连接,其查询效果相同。  
#### -- 隐式内连接：  
SELECT <selectList>  
FROM A,B WHERE A.列=B.列 
  
###  ------------- 显示内连接：(推荐写法)------------  
SELECT <selectList>  
FROM A [INNER] JOIN B ON A.列=B.列  
  
查询货品id,货品名称,货品所属分类名称  
  
### -- 隐式内连接写法  
SELECT product.id,product.productName,productdir.dirName FROM product,productdir WHERE product.dir_id=productdir.id  
### -- 隐式内连接写法  
SELECT product.id,product.productName,productdir.dirName FROM  
product INNER JOIN productdir ON product.dir_id=productdir.id  

### -- 显示内连接：(推荐写法)  
SELECT p.id,p.productName,p.salePrice,d.dirName,s.store_num  
FROM product p INNER JOIN productdir d ON p.dir_id=d.id  
INNER JOIN product_stock s ON s.product_id=p.id  
  
查询零售价大于200的无线鼠标(使用表的别名)  
  
### -- 隐式内连接写法  
SELECT p.id,p.productName,p.salePrice,d.dirname FROM product p,productdir d WHERE p.dir_id=d.id AND p.salePrice>200 AND d.dirname ='无线鼠标'  
### -- 显式内链接写法  
SELECT p.id,p.productName,p.salePrice,d.dirname FROM product p INNER JOIN productdir d ON p.dir_id=d.id WHERE p.salePrice>200 AND d.dirname='无线鼠标'  
  
查询每个货品对应的分类以及对应的库存  
  
### -- 隐式内连接写法  
SELECT p.id,p.productName,p.salePrice,d.dirName,s.store_num  
FROM product p,productdir d ,product_stock s  
WHERE p.dir_id=d.id AND s.product_id=p.id  
  
如果库存货品都销售完成,按照利润从高到低查询货品名称,零售价,货品分类(三张表)  
  
### -- 隐式  
SELECT p.id,p.productName,p.salePrice,d.dirName,(p.salePrice-p.costPrice)*s.store_num lr  
FROM product p,productdir d,product_stock s  
WHERE p.dir_id=d.id AND s.product_id=p.id  
ORDER BY lr DESC  
  
### -- 显示  
SELECT p.id,p.productName,p.salePrice,d.dirName,(p.salePrice-p.costPrice)*s.store_num lr  
FROM product p INNER JOIN productdir d ON p.dir_id=d.id  
INNER JOIN product_stock s ON s.product_id=p.id  
ORDER BY lr DESC  
  
SELECT e.id ,e.ename ,d.dname FROM emp e INNER JOIN dept d ON e.d_id=d.id  


### 外连接查询：  
	左外连接:查询出JOIN左边表的全部数据查询出来,JOIN右边的表不匹配的数据使用NULL来填充数据.  
  右外连接:查询出JOIN右边表的全部数据查询出来,JOIN左边的表不匹配的数据使用NULL来填充数据.  
	  
	查询每种商品分类的名称和包含的商品库存总数：  
  
IFNULL(expr1,expr2)  当expr1为null的时候使用expr2 来代替  
expr1:指定的列  
expr2：一个值  

SELECT p.productName,d.dirName,IFNULL(s.store_num,0)  
FROM product p LEFT JOIN productdir d ON p.dir_id=d.id LEFT JOIN product_stock s ON s.product_id=p.id  

### ---------------------自关联查询----------------------  
需求: 查询每个商品分类的名称和父分类名称（所属分类的名称）:  
SELECT p.id,p.dirName ,d.dirName FROM productdir p LEFT JOIN productdir d ON p.parent_id=d.id   
  
  
### -----------------子查询-----------------  
需求: 查询零售价比罗技MX1100更高的所有商品信息。  
  
1.先查询罗技MX1100的零售价  
SELECT salePrice FROM product WHERE productName='罗技MX1100'  
2.查询零售价大于第一步商品  
SELECT *FROM product WHERE salePrice>550  
两句sql汇总  
SELECT *FROM product WHERE salePrice>(SELECT salePrice FROM product WHERE productName='罗技MX1100')  
  
需求: 查询分类编号和折扣与罗技M100相同的所有商品信息。  
1.查询罗技M100的 分类编号  
SELECT dir_id FROM product WHERE productName='罗技M100'  
2.查询罗技M100的折扣  
SELECT cutoff FROM product WHERE productName='罗技M100'  
汇总  
SELECT * FROM product WHERE   
dir_id=(SELECT dir_id FROM product WHERE productName='罗技M100')  
AND  
cutoff =(SELECT cutoff FROM product WHERE productName='罗技M100')  
  
精简:  
SELECT * FROM product WHERE   
(dir_id,cutoff)=(SELECT dir_id,cutoff FROM product WHERE productName='罗技M100')  
  
###  -------------------插入数据------------------------  
INSERT INTO 表名 (column1,column2,column3...)  
VALUES	(value1,value2,value3...);   
  
INSERT INTO user (id,name,age) VALUES (27,'操语言',1000)  
  
2.插入多条  
 INSERT INTO	table_name (column1,column2,column3...)  
   VALUES (value1,value2,value3...),  
          (value1,value2,value3...),  
          (value1,value2,value3...)..;  
					  
INSERT INTO user (name,age) VALUES ('白云',1000),('黑土',1000);  
  
3.查询结果再次插入  
	 INSERT INTO	table_name (column1,column2,column3...)  
     SELECT (column1,column2,column3...)  
     FROM table_name   
  
INSERT INTO user(name,age) SELECT name,age FROM user  
  
查看多少条数据  
SELECT COUNT(*) FROM user;  
  
  
### ---------------------修改数据--------------------------  
UPDATE	表名  
SET	列名 = 值 [, columnName = value,columnName = value] …  
[WHERE	condition];  
如果省略了where子句，则全表的数据都会被修改。注意:没有FROM  
  
UPDATE user SET name='操语言',age=3 WHERE id <30;  
UPDATE user SET name='goland',age=30 WHERE id =13;  
