## mysql-connector-python

### [Connector/Python](Connector/Python https://dev.mysql.com/doc/connector-python/en/)

### 安装 
    pip install mysql-connector-python

### 使用示例

1. connect mysql 链接mysql

	```
	import mysql.connector
	
	cnx=mysql.connector.connect(
	    user='scott’,
	    password='password’,
	    host='127.0.0.1’,
	    database='employees’)
	```

2. create table 建表

	```
	cursor=cnx.cursor()
	
	cursor.execute（
	    "CREATE TABLE `employees` (“
	    " `emp_no` int(11) NOT NULL AUTO_INCREMENT,”
	    " `birth_date` date NOT NULL,”
	    " `first_name` varchar(14) NOT NULL,”
	    " `last_name` varchar(16) NOT NULL,”
	    " `gender` enum('M','F') NOT NULL,”
	    " `hire_date` date NOT NULL,”
	    " PRIMARY KEY (`emp_no`)”
	    ") ENGINE=InnoDB")
	```

3.  insert data 插入数据

	```
	add_employee = (
	    "INSERT INTO employees “
	    "(first_name, last_name, hire_date, gender, birth_date) “
	    "VALUES (%s, %s, %s, %s, %s)”)
	
	data_employee=('Geert','Vanderkelen',tomorrow,'M',date(1977,6,14))
	
	cursor.execute(add_employee,data_employee)
	
	```

4. query data 查询数据

	```
	query=(
	    "SELECT first_name, last_name, hire_date FROM employees “
	    "WHERE hire_date BETWEEN %s AND %s")
	
	hire_start=datetime.date(1999,1,1)
	hire_end=datetime.date(1999,12,31)
	cursor.execute(query, (hire_start,hire_end))
	
	```

5. close cursor and connect 提交改变以及关闭连接
	
	```
	cnx.commit()
	cursor.close()
	cnx.close()
	```










