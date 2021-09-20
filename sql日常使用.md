```
case判断building是不是不为空，不为空就为1，为空就是0，
在对为0或不为0的进行分组

SELECT Role,CASE when Building is NOT NULL 
THEN '1' ELSE '0' END
AS Have,COUNT(Name)
FROM Employees
GROUP BY Role,Have;
```
