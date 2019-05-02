---
title: 新手编程训练项目-007——C语言文本文件的含通配符的模糊单词搜索
date: 2019-05-02 19:14:52
tags:
	- C/C++
---
日期：2017年11月30日

任务目标：编写一个程序可以对文本文件进行含通配符的模糊单词搜索，其中‘*’表示多个位置字符，‘？’表示单个未知字符，并可以使用字母以及上述两种符号组合搜索。

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise、编译字符集Unicode
<!-- more -->

注：由于笔者水平并不高，以下代码可能包含bug。


首先是头文件：functions.h
```
#pragma once

#ifndef __FUNCTIONS_H
#define	__FUNCTIONS_H

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>

#define MAX_LINE 1023

//单词结构体
struct word_type
{
	char* word_store;				//指向动态字符串存储的指针
	int len;						//单词长度
};

//不定长输入
char* get_str(void);
//输入函数
void input_info(char** text_str, char** addr_str);
//读取一个单词
void read_word(FILE* fp, char* src_str);

bool match(char* str1, char* pattern);

#endif /* __FUNCTIONS_H */
```
其中布尔函数使用了stdbool.h头文件


以下是主函数文件中的内容：main.c
```
#pragma warning(disable:4996) //VS 编译声明？

#include<functions.h>

int main()
{
	//初始模糊字段
	char* src_str = NULL;
	//文本地址
	char* tgt_str = NULL;
	printf("##### program initiating #####\n");

	input_info(&src_str, &tgt_str);
	printf("模糊字段:%s\n文本地址:%s\n", src_str, tgt_str);
	
	FILE *fp;
	if ((fp = fopen(tgt_str, "r")) == NULL)
	{
		printf("Open failure....\n");
		exit(1);
	}

	read_word(fp, src_str);

	fclose(fp);

	printf("##### running complete #####\n");
	system("pause");
	return 0;
}

void input_info(char** text_str, char** addr_str)
{
	printf("输入模糊字段\n");
	*text_str = get_str();

	printf("输入文本地址\n"); 
	*addr_str = get_str();
}

char* get_str(void)											
{
	char *str=NULL;
	int j = 1;

	str = (char*)malloc(sizeof(char)*(j + 1));
	if (NULL == str)
	{
		exit(1);
	}

	while ((str[j-1] = getchar()) != '\n')
	{
		j++;
		str = (char*)realloc(str, j);
		if (NULL == str)
		{
			exit(1);
		}
	}
	str[j - 1] = '\0';

	return(str);
}
```

其中get_str()函数是使用realloc()函数改进的变长字符串输入函数，是前几个训练项目的修改。

下面是读取文件函数
```
void read_word(FILE* fp,char* src_str)
{
	char *token;
	const char seps[9] = " ,.:\t\n\"\'";
	char temp[MAX_LINE+1];
	int i = 0;
	bool answer;

	while (fgets(temp, MAX_LINE, fp)!=NULL)
	{	
		//fgets(temp, MAX_LINE, fp);
		token = strtok(temp, seps);
		while (token != NULL)
		{
			//printf("%s\n", token);
			answer = match(token, src_str);
			if (answer == true)
				printf("true:%s\n", token);
			token = strtok(NULL, seps);
		}
	}
}
```

使用了strtok()函数，据VS说这函数不安全？？

反正我是用了，暂时还不管那么多，这个就是使用分隔符字符串将目标字符串按分隔符分开并将分割完的字符串地址返回，是需要循环的，具体使用参见MSDN上的例子


最后也是最重要的部分：模糊匹配函数，这个函数我是参考了网上的代码，并做出了一定修改，使得可以对长度进行匹配控制，就自己的几次测试来看没有出现问题，如果出现问题请一定告知我
```
bool match(char* str1, char* pattern)
{
	if (str1 == NULL)		//待匹配字符串指向空，返回false
		return false;
	if (pattern == NULL)		//模式字符串指向空，返回false
		return false;
	int len1 = strlen(str1);		//待匹配字符串的长度
	int len2 = strlen(pattern);			//模式字符串的长度
	int mark = 0;		//用于分段标记,'*'分隔的字符串
	int p1 = 0, p2 = 0;		//待匹配/模式的偏移量
	int flag_s = 0, flag_q = 0;			//如果出现* 和？则标识置位

	while ((p1<len1) && (p2<len2))			//在长度范围内运行
	{
		if (pattern[p2] == '?')			//如果模式中有‘？’通配符，则：同时偏移1，并进入下一循环
		{
			flag_q = 1;
			p1++;
			p2++;
			continue;
		}
		if (pattern[p2] == '*')//如果当前是*号，则mark前面一部分已经获得匹配，//从当前点开始继续下一个块的匹配
		{
			flag_s = 1;
			p2++;
			mark = p2;
			continue;
		}
		if (str1[p1] != pattern[p2])
		{
			if (p1 == 0 && p2 == 0)
			{
				//如果是首字符，特殊处理，不相同即匹配失败
				return false;
			}
			if (flag_s == 1)
			{
				p1 -= p2 - mark - 1;
				p2 = mark;
				continue;
			}
			else
			{
				return false;
			}
			
		}
		//此处处理相等的情况
		p1++;
		p2++;
	}
	if (p2 == len2)
	{
		if (p1 == len1)
		{
			//两个字符串都结束了，说明模式匹配成功
			return true;
		}
		if (pattern[p2 - 1] == '*')
		{
			//str1还没有结束，但pattern的最后一个字符是*，所以匹配成功
			return true;
		}
		else
		{
			return false;
		}
	}
	while (p2<len2)
	{
		//pattern多出的字符只要有一个不是*,匹配失败
		if (pattern[p2] != '*')
			return false;
		p2++;
	}
	if ((len2 != len1) && (flag_s == 0))			//如果没有出现*但是长度不一致则返回false
	{
		return false;
	}
	return true;
}
```

我加的注释还是比较多的= =

可能不是很规范= =

最后如果有需要可以去[Github](https://github.com/phantomT/Training-program-004-blur_search)下载全部文件，如果有bug请告诉我，毕竟是练习，以提高水平为主。