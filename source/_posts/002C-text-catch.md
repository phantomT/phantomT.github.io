---
title: 新手编程训练项目-002——C语言文本合并02：不定长字符串输入函数
date: 2019-05-02 18:39:27
tags:
	- C/C++
---

日期：2017年8月26日

来源：001项目中文本文件地址输入，减少内存浪费

思路：循环交换两列动态生成的字符串，并在每次交换后加长并释放上次申请的内存
<!-- more -->
注意：

1.若使用函数strlen()作为申请内存大小时，字符串结尾必须有'\0'，这是strlen()函数算法决定的。申请的内存大小也可使用计数变量。

2.字符串函数strcpy()的使用，两参数的位置。

3.申请内存时要判断是否申请成功。

```
char* get_name(void)											//不定长输入地址
{
	char *name, *temp;
	char ch;
	int j = 1;

	name = (char*)malloc(sizeof(char)*(j + 1));
	if (NULL == name)
	{
		exit(1);
	}

	while ((name[j - 1] = getchar()) != '\n')
	{
		j++;
		temp = (char*)malloc(j + 1);
		if (NULL == temp)
		{
			exit(1);
		}
		name[j - 1] = '\0';
		strcpy(temp, name);
		free(name);

		name = (char*)malloc(sizeof(char)*(j + 1));
		if (NULL == name)
		{
			exit(1);
		}
		strcpy(name, temp);
		free(temp);
	}

	name[j - 1] = '\0';

	return(name);
}				
```

