---
title: 新手编程训练项目-001——C语言文本合并01
date: 2019-05-02 18:35:50
tags:
    - C/C++
---

日期：2017年8月25日

第一个项目：

任务目标：将两个文本文件中的内容合并到一个新建的文本中（使用fgetc和fputc函数）  

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise  
<!-- more -->
```
#pragma warning(disable:4996) //VS 编译声明？

#include <stdio.h>
#include <stdlib.h>
#include<string.h> 

void main(void)
{
	printf("Program initiating...\n");
		
	/*################### 打开文件1和2，并建立合并文件"Output_file.txt" ###################*/

	FILE *fpa, *fpb, *fpc;
	char file_name_1[200], file_name_2[200];				//文件名暂存数组

	printf("请输入文件1的相对地址：");
	scanf("%s", file_name_1);
	printf("Openning %s\n", file_name_1);					//状态标识

	if ((fpa = fopen(file_name_1, "r")) == NULL)			// 打开输出文件并使fpa指向此文件
	{
		printf("Open failure...\n");						// 如果打开时出错，就输出"打不开"的信息  
		exit(0);											// 终止程序
	}

	printf("\n请输入文件2的相对地址：");
	scanf("%s", file_name_2);
	printf("Openning %s\n", file_name_2);					//状态标识

	if ((fpb = fopen(file_name_2, "r")) == NULL)			// 打开输出文件并使fpb指向此文件
	{
		printf("Open failure...\n");						// 如果打开时出错，就输出"打不开"的信息  
		exit(0);											// 终止程序
	}
	

	if ((fpc = fopen("Output_file.txt", "w")) == NULL)      // 打开输出文件并使fpc指向此文件
	{
		printf("Open failure...\n");						// 如果打开时出错，就输出"打不开"的信息  
		exit(0);											// 终止程序
	}
	printf("Building Output_file.txt\n");					//状态标识

	/*################### 将文件1和2的内容复制到文件Output_file中 ###################*/

	char cha,chb;
	while ((cha = fgetc(fpa)) != EOF)
	{
		fputc(cha, fpc);
	}

	while ((chb = fgetc(fpb)) != EOF)
	{
		
		fputc(chb, fpc);
	}

	/*################### 关闭文件Output_file和文件1、2 ###################*/

	fclose(fpc);
	fclose(fpb);
	fclose(fpa);

	system("pause");
}
```