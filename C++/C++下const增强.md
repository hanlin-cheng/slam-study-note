# C++下const增强

### 全局const间接修改

```c++
const int m_a = 100;	//分配到常量区
void fun()
{
	int * p = (int *)&m_a;
	*p = 200;
}
```

结论：编译成功，但是运行失败，修改失败

###  局部const间接修改

```c++
void fun()
{
	const int m_b = 100;	//分配到栈上
	int * p = (int *)&m_b;
	*p = 200; 
    cout << m_b << endl;
}
```

结论：运行成功，修改失败

### 原因分析：

![c++const修饰的局部变量分配到符号表上](media/c++const%E4%BF%AE%E9%A5%B0%E7%9A%84%E5%B1%80%E9%83%A8%E5%8F%98%E9%87%8F%E5%88%86%E9%85%8D%E5%88%B0%E7%AC%A6%E5%8F%B7%E8%A1%A8%E4%B8%8A.png)

`因此c++ const可以称为常量`

### 链接属性

const变量默认是内部链接属性，加extern可以提高作用域

### 内存分配

```c++
void fun()
{
	const int m_b = 100;	//分配到栈上
	int * p = (int *)&m_b;
	*p = 200; 
}
```

同上分配的临时内存

```c++
void fun()
{
	int a = 10;
	const int m_b = a;	//分配到栈上
	int * p = (int *)&m_b;
	*p = 200; 
}
```

修改成功，因为分配了内存

```c++
struct Person
{
	string m_Name;
	int m_Age;
};
void test03()
{
	const Person p;
	//p.m_Age = 10;      //直接修改失败

	Person * pp = (Person *)&p;
	(*pp).m_Name = "Tom";
	pp->m_Age = 10;

	cout << "姓名 " << p.m_Name << " 年龄" << p.m_Age << endl;
}
```

间接修改成功，分配了内存