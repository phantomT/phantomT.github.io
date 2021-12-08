---
title: 新手编程训练项目-005——C语言文件夹文件信息输出002：递归输出指定文件夹中的文件信息
date: 2019-05-02 19:11:21
tags:
    - C/C++
---
日期：2017年10月6日

任务目标：编写一个程序可以递归读取指定文件夹内指定或未指定类型（*.*）的文件信息，如：文件名、文件大小、创建日期、修改日期、最后访问日期等。

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise、编译字符集Unicode

尝试添加了微软符号集（不知道有没有影响）
<!-- more -->
```
#pragma warning(disable:4996) //VS 编译声明？

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <io.h>
#include <time.h>

struct _finddata_t;

char* get_name(void)									//不定长输入地址
{
	char *name, *temp;
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

char* get_address(void)
{
	char * file_loc;
	printf("##### 输入文件夹地址(./****/) #####\n");				//文件地址
	file_loc = get_name();
	return file_loc;
}

void file_search(char*file_loc, int layer, char* file_type)				//file_search
{
	/*################### 将文件夹内文件信息输出 ###################*/
	struct _finddata_t fileinfo;							//文件存储信息结构体 
	long fHandle;									//保存文件句柄 
	int i = 0;									//文件数记录器		
	time_t Time_create, Time_write, Time_access;			                //时间格式

	int Layer;									//子文件夹层
	char *curr;
	int loc_len, type_len;

	loc_len = strlen(file_loc);
	type_len = strlen(file_type);

	curr = (char*)malloc(loc_len + type_len+1);
	if (NULL == curr)
	{
		exit(1);
	}

	strcpy(curr, file_loc);
	strcat(curr, file_type);
	//printf("file_add test output: %s\n", curr);

	if ((fHandle = _findfirst(curr , &fileinfo)) != -1L)
	{
		do
		{
			if (strcmp(fileinfo.name, "..") == 0)
				continue;
			if (strcmp(fileinfo.name, ".") == 0)
				continue;
			for (Layer = 0; Layer < layer; Layer++)
				printf("\t");

			if ((_A_SUBDIR == fileinfo.attrib))                                 // 是目录  
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
				file_search(curr_n, layer + 1, file_type);                  // 递归遍历子目录  
			}
			else
			{
				Time_create = time(&fileinfo.time_create);
				Time_write = time(&fileinfo.time_write);
				Time_access = time(&fileinfo.time_access);

				//i++;
				printf("[File]:\t找到文件:%-s\t文件大小：%-d KB\n", fileinfo.name, (fileinfo.size) / 1000);
				for (Layer = 0; Layer < (layer + 1); Layer++)
					printf("\t");
				printf("创建日期：%-s\n", ctime(&fileinfo.time_create));
				for (Layer = 0; Layer < (layer + 1); Layer++)
					printf("\t");
				printf("修改日期：%-s\n", ctime(&fileinfo.time_write));
				for (Layer = 0; Layer < (layer + 1); Layer++)
					printf("\t");
				printf("访问日期：%-s\n", ctime(&fileinfo.time_access));
				//printf("[File]:\t%s\n",fileinfo.name);
			}

		} while (_findnext(fHandle, &fileinfo) == 0);
	}

	_findclose(fHandle);									//关闭文件 

	//printf("文件数量：%d\n", i);
}

int main(void)
{	
	printf("##### Program initiating #####\n");

	char* filetype,*fileloc;
	printf("##### 输入要查找的文件类型(*.xxxx) #####\n");					//文件类型
	filetype = get_name();

	fileloc = get_address();

	file_search(fileloc,0,filetype);

	system("pause");
}
```

实验文件夹的输出结果：
```
##### Program initiating #####
##### 输入要查找的文件类型(*.xxxx) #####
*.*
##### 输入文件夹地址(./****/) #####
./Thinkpad/
[Dir]:  cn
        [File]: 找到文件:fx570MS_991MS_CN.pdf   文件大小：616 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

        [File]: 找到文件:fx_95MS_Etype_CN.pdf   文件大小：519 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

        [File]: 找到文件:qw5398.pdf     文件大小：645 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

[File]: 找到文件:test1.txt      文件大小：0 KB
        创建日期：Fri Oct  6 22:08:47 2017

        修改日期：Fri Oct  6 22:08:47 2017

        访问日期：Fri Oct  6 22:08:47 2017

[File]: 找到文件:test2.txt      文件大小：0 KB
        创建日期：Fri Oct  6 22:08:47 2017

        修改日期：Fri Oct  6 22:08:47 2017

        访问日期：Fri Oct  6 22:08:47 2017

[Dir]:  think
        [File]: 找到文件:t440p_ug_en.pdf        文件大小：4765 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

        [File]: 找到文件:t460p_hmm_en_sp40k04964.pdf    文件大小：60867 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

        [File]: 找到文件:ThinkPad E570、E570c 和E575 用户指南V1.0.pdf   文件大小：4129 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

        [File]: 找到文件:ThinkPad T460p 用户指南V1.0.pdf        文件大小：5358 KB
                创建日期：Fri Oct  6 22:08:47 2017

                修改日期：Fri Oct  6 22:08:47 2017

                访问日期：Fri Oct  6 22:08:47 2017

请按任意键继续. . .
```

果然自己对编译/链接的原理还是一无所知。。。

对系统的操作还是要学习一个。。。

距离精通还有很远的路啊。。。

看起来**CSAPP**可以有所帮助