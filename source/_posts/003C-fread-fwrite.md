---
title: 新手编程训练项目-003——C语言文本合并03：fread与fwrite函数对文本文件的读写
date: 2019-05-02 19:09:13
tags:
    - C/C++
---
日期：2017年9月4日

任务目标：编写使用fread()和fwrite()函数对文本文件进行读写，并将两文本文件合并的函数

编程环境：Windows 10 Enterprise、Visual Studio 2017 Enterprise、编译字符集Unicode

代码中fread()和fwrite()函数的参数中size_t size 使用的是一个字节，也可使用sizeof(char)。
<!-- more -->
注意：

1. 在读取待合并文本长度后，该文本的文本内指针已被移至文本最后，需要使用rewind()函数复位，否则读取内容是就是一堆乱码。

2. 在使用临时内存buff存储文本内容后，要及时释放内存。

```
void connect(void)
{
	/*################### 打开文件1和2，并建立合并文件"Output_file.txt" ###################*/

	FILE *fpa, *fpb, *fpc;
	char *file_name_1, *file_name_2;						//文件名暂存数组  

	printf("请输入文件1的相对地址：");
	//scanf("%s", file_name_1);
	file_name_1 = get_name();
	printf("Openning %s\n", file_name_1);                   //状态标识  

	if ((fpa = fopen(file_name_1, "r")) == NULL)            // 打开输出文件并使fpa指向此文件  
	{
		printf("Open failure...\n");                        // 如果打开时出错，就输出"打不开"的信息    
		exit(0);                                            // 终止程序  
	}

	printf("\n请输入文件2的相对地址：");
	//scanf("%s", file_name_2);
	file_name_2 = get_name();
	printf("Openning %s\n", file_name_2);                   //状态标识  

	if ((fpb = fopen(file_name_2, "r")) == NULL)            // 打开输出文件并使fpb指向此文件  
	{
		printf("Open failure...\n");                        // 如果打开时出错，就输出"打不开"的信息    
		exit(0);                                            // 终止程序  
	}


	if ((fpc = fopen("Output_file.txt", "w")) == NULL)      // 打开输出文件并使fpc指向此文件  
	{
		printf("Open failure...\n");                        // 如果打开时出错，就输出"打不开"的信息    
		exit(0);                                            // 终止程序  
	}
	printf("Building Output_file.txt\n");                   //状态标识  

	/*################### 将文件1和2的内容复制到文件Output_file中 ###################*/
	int last_a, last_b;										//定义文件大小
	char *buff;

	fseek(fpa, 0, SEEK_END);								//查找第一个文件的大小
	last_a = ftell(fpa);
	buff = (char*)malloc(sizeof(char)*last_a);
	if (NULL == buff)
	{
		exit(1);
	}
	rewind(fpa);										//将第一个文件指针移到开头（不可忘记）

	fread(buff, 1,last_a, fpa);								//将第一个文件写入
	fwrite(buff, 1,last_a, fpc);

	free(buff);

	fseek(fpc, 0, SEEK_END);								//将合并文件的指针移到最后
	
	fseek(fpb, 0, SEEK_END);								//查找第二个文件的大小
	last_b = ftell(fpb);
	buff = (char*)malloc(sizeof(char)*last_b);
	if (NULL == buff)
	{
		exit(1);
	}

	rewind(fpb);										//将第二个文件指针移到开头
	fread(buff, 1, last_b, fpb);							        //将第二个文件写入
	fwrite(buff, 1, last_b, fpc);
	
	free(buff);

	/*################### 关闭文件Output_file和文件1、2 ###################*/

	fclose(fpc);
	fclose(fpb);
	fclose(fpa);
}
```