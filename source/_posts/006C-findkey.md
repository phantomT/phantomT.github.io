---
title: 新手编程训练项目-006——C语言文件夹文本文件关键词检测
date: 2019-05-02 19:13:41
tags:
	- C/C++
---
日期：2017年10月28日

任务目标：编写一个程序可以检测指定文件夹（包括子文件夹）中的文本文件中是否包含某关键词，并输出存在关键词的行数

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise、编译字符集Unicode

<!-- more -->
写了一个头文件functions.h
```
#pragma once

#ifndef __FUNCTIONS_H
#define	__FUNCTIONS_H

#define MAX_LINE 1024

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <io.h>

#endif /* __FUNCTIONS_H */
```

程序前声明：
```
#pragma warning(disable:4996) //VS 编译声明？

#include<functions.h>

struct _finddata_t;
```

调用的用户编写函数如下：

读取不定长字符串：请见[新手编程训练项目]002——C语言文本合并02：不定长字符串输入函数

读取文件夹地址、对文件夹进行递归遍历：请见[新手编程训练项目]005——C语言文件夹文件信息输出002：递归输出指定文件夹中的文件信息
```
void dir_search(char*file_loc, int layer, char* key)				//dir_search
{
	struct _finddata_t fileinfo;							//文件存储信息结构体 
	long fHandle;											//保存文件句柄 
	int i = 0;												//文件数记录器		

	int Layer;												//子文件夹层
	char *curr;
	int loc_len, type_len;

	loc_len = strlen(file_loc);
	type_len = strlen("*.*");

	curr = (char*)malloc(loc_len + type_len + 1);
	if (NULL == curr)
	{
		exit(1);
	}

	strcpy(curr, file_loc);
	strcat(curr, "*.*");
	printf("file_add test output: %s\n", curr);

	if ((fHandle = _findfirst(curr, &fileinfo)) != -1L)
	{
		do
		{
			if (strcmp(fileinfo.name, "..") == 0 || strcmp(fileinfo.name, ".") == 0)
				continue;
			for (Layer = 0; Layer < layer; Layer++);
			printf("\t");

			if (_A_SUBDIR != fileinfo.attrib)
			{
				if (type_cmp(fileinfo.name))
				{
					file_search(file_end_change(curr, fileinfo.name), key);
				}
			}
			else												// 是目录  
			{
				printf("[Dir]:\t%s\n", fileinfo.name);
				char* curr_n;
				curr_n = (char*)malloc(loc_len + 1 + strlen(fileinfo.name));
				if (NULL == curr_n)
				{
					exit(1);
				}

				strcpy(curr_n, file_loc);
				strcat(curr_n, fileinfo.name);
				strcat(curr_n, "/");
				dir_search(curr_n, layer + 1, key);                  // 递归遍历子目录 
			}

		} while (_findnext(fHandle, &fileinfo) == 0);
	}

	_findclose(fHandle);									//关闭文件 

}
```
读取关键词
```
char* get_key(void)
{
	char * key;
	printf("##### 输入关键词 #####\n");								//关键词
	key = get_name();
	return key;
}
```
文件类型比较
```
int type_cmp(char* file_name)
{
	char type[4];
	int i = 0,l=0;
	l = strlen(file_name);
	for (i = 0; i < 4; i++)
		type[i] = file_name[l - 4 + i];
	
	//for(i=0;i<4;i++)
		//printf("%c", type[i]);

	//printf("\n");

	if (strcmp(type, ".txt"))
		return 1;
	else
		return 0;
}
```
对文本文件相对地址的字符串进行修改的函数
```
char* file_end_change(char* file_name,char* fileinfo_name)
{
	int l1 = strlen(file_name);
	int lenth = l1+strlen(fileinfo_name)-3;
	//int lenth = strlen(fileinfo_name) + 2 - 3;

	char* name_ch;												// *.*->[],+fileinfo.name
	name_ch = (char*)malloc(lenth);
	if (NULL == name_ch)
		exit(1);
	//strcpy(name_ch, "./");
	//strcat(name_ch, fileinfo_name);

	strcpy(name_ch, file_name);
	name_ch[l1 - 3] = '\0';
	strcat(name_ch, fileinfo_name);

	printf("%s\n", name_ch);
	return name_ch;
}
```
文本文件逐行关键词检测（windows系统文本文件一行最多1024个字符）
```
void file_search(char* file_loc, char* key)
{
	/*打开文本文件，查找关键词，输出关键词的第一个字的位置*/
	FILE *fp;
	if ((fp = fopen(file_loc, "rb")) == NULL)
	{
		printf("Open failure...\n");                        // 如果打开时出错，就输出"打不开"的信息    
		system("pause");
	}

	//定义一行的字符串结构体
	typedef struct String
	{
		char string[1024];				//字符串
		int len;						//一行长度
		int line;						//行数
	}String;

	String pstring;
	pstring.line = 0;

	while (!feof(fp))												//循环读取每一行，直到文件尾  
	{
		fgets(pstring.string, MAX_LINE, fp);						//将fp所指向的文件一行内容读到strLine缓冲区  
																	//printf("%s", pstring.string);								//输出所读到的内容  
		pstring.line++;
		if (strstr(pstring.string, key))
		{
			printf("\t\t第%d行出现了关键词\"%s\"\n", pstring.line, key);
		}
	}

	/*################### 关闭文件 ###################*/

	fclose(fp);
}
```
主函数：
```
int main(void)
{
	printf("##### Program initiating #####\n");
	char* key, *fileloc;

	key = get_key();
	fileloc = get_address();

	dir_search(fileloc, 0, key);

	system("pause");

}
```
试验结果就不放了，有兴趣的读者可以去[Github](https://github.com/phantomT/Training-program-003-find_key)上下载全部文件自行测试，如果有问题可以给我留言


这是我10月份编程练习的题目，因为学业和其他的项目的原因，一个月只能写一个小练习

这次是尽量需要什么功能就写成一个函数，也为以后积累经验