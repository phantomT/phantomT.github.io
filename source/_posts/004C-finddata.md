---
title: 新手编程训练项目-004——C语言文件夹文件信息输出001：io库中_finddata_t结构体的使用
date: 2019-05-02 19:10:30
tags:
    - C/C++
---
日期：2017年9月10日

任务目标：编写一个程序可以读取指定文件夹内指定或未指定类型（*.*）的文件信息，如：文件名、文件大小、创建日期、修改日期、最后访问日期等。

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise、编译字符集Unicode

这是第一次学习使用io.h库，跟`stdio.h`不一样，这个`io.h`不是标准C的头文件，在windows下简单搜索了一下，最大的可能是VC的runtime的头文件。

（emmm具体情况还不太清楚。。。）
<!-- more -->
这个程序还是用到了前面写过的动态输入名称函数`get_name()`。


可以调用`_finddata_t `结构体得到文件的基本信息，

在`corecrt_io.h`头文件中，此结构体的定义为：

```
struct _finddata64i32_t
{
    unsigned    attrib;
    __time64_t  time_create;    // -1 for FAT file systems
    __time64_t  time_access;    // -1 for FAT file systems
    __time64_t  time_write;
    _fsize_t    size;
    char        name[260];
};
```

得到信息调用了三个函数：`_findfirst()`、`_findnext()`和`_fineclose()`

此结构涉及名为句柄的概念，以下解释来自百度百科（= =）

“在程序设计中，句柄是一种特殊的智能指针 。当一个应用程序要引用其他系统（如数据库、操作系统）所管理的内存块或对象时，就要使用句柄。
句柄与普通指针的区别在于，指针包含的是引用对象的内存地址，而句柄则是由系统所管理的引用标识，该标识可以被系统重新定位到一个内存地址上。这种间接访问对象的模式增强了系统对引用对象的控制。
关于`_finddata_t`的具体情况可以参考此的文章，这是引用别人的CSDN中讲解的[`_finddata_t`](http://blog.csdn.net/wzhwho/article/details/6372353)


将信息写入输出文件时使用了`fprintf()`函数，用法和`fread()`和`fwrite()`差不多。

1.在初次输出时出现了无法换行的问题，因此使用了`\r\r\n`的方式换行。

2.在初次输出时还出现了文件夹内第一个文件的信息为输出的问题，后来发现是在`_findfirst()`后未输出导致缺少一个文件信息。

3.此版本的代码应该还不太完善，如遇到问题还会继续修改。

```
#pragma warning(disable:4996) //VS 编译声明？

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <io.h>
#include <time.h>

struct _finddata_t;

char* get_name(void)											//不定长输入地址
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

void Output_info(void)
{	
	char * file_loc, *file_type, *file_add;										
	int loc_len, type_len;
	printf("$$$$$ 输入文件夹地址(./****/) $$$$$\n");								//文件地址
	file_loc = get_name();
	loc_len = strlen(file_loc);

	printf("$$$$$ 输入要查找的文件类型(*.xxxx) $$$$$\n");							//文件类型
	file_type = get_name();
	type_len = strlen(file_type);

	file_add = (char*)malloc(loc_len+type_len+1);
	if (NULL == file_add)
	{
		exit(1);
	}

	strcpy(file_add, file_loc);
	strcat(file_add, file_type);
	printf("file_add test output: %s\n", file_add);

	/*################### 建立输出文件"Output_file.txt" ###################*/

	FILE *fp;
	
	printf("##### Building Output_file.txt #####\n");					//状态标识  

	if ((fp = fopen("Output_file.txt", "wb")) == NULL)				// 打开输出文件并使fp指向此文件  
	{
		printf("##### Building failure ! #####\n");						// 如果打开时出错，就输出"打不开"的信息    
		exit(0);														// 终止程序  
	}

	//char headline[] = {"文件名\t\t\t\t文件大小\t\t创建日期\t\t修改日期\t\t访问日期\n"};
	//fprintf(fp, "%s\r\r\n", headline);
	//fprintf(fp, "\n");

	/*################### 将文件夹内文件信息加入到文件Output_file中 ###################*/
	struct _finddata_t fileinfo;							//文件存储信息结构体 
	long fHandle;											//保存文件句柄 
	int i = 0;												//文件数记录器		
	time_t Time_create, Time_write, Time_access;			//时间格式

	if ((fHandle = _findfirst(file_add, &fileinfo)) == -1L)
	{
		printf("当前目录下没有该类型的文件\n");
		exit(0);
	}
	else 
	{
		Time_create = time(&fileinfo.time_create);
		Time_write = time(&fileinfo.time_write);
		Time_access = time(&fileinfo.time_access);

		fprintf(fp, "找到文件:%-s\r\r\n 文件大小：%-6d KB, 创建日期：%s, 修改日期：%s, 访问日期：%s.\r\r\n", fileinfo.name, (fileinfo.size)/1000, ctime(&fileinfo.time_create), ctime(&fileinfo.time_write), ctime(&fileinfo.time_access));
		
		while (_findnext(fHandle, &fileinfo) == 0)
		{
			Time_create = time(&fileinfo.time_create);
			Time_write = time(&fileinfo.time_write);
			Time_access = time(&fileinfo.time_access);

			i++;
			fprintf(fp, "\r\r\n找到文件:%-s\r\r\n 文件大小：%-6d KB, 创建日期：%s, 修改日期：%s, 访问日期：%s.\r\r\n", fileinfo.name, (fileinfo.size)/1000, ctime(&fileinfo.time_create), ctime(&fileinfo.time_write), ctime(&fileinfo.time_access));
			
			//printf("找到文件:%30s,文件大小：%10d,创建日期：%10d,修改日期：%10d,访问日期：%10d\n", fileinfo.name, fileinfo.size,fileinfo.time_create,fileinfo.time_write,fileinfo.time_access);
		}
	}

	_findclose(fHandle);									//关闭文件 

	printf("文件数量：%d\n", i);

	/*################### 关闭文件Output_file ###################*/

	fclose(fp);
}

int main(void)
{
	printf("##### Program initiating #####\n");

	Output_info();

	system("pause");
}
```