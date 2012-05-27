---
layout: blog
title: Linux 中的时间
---


关于在Linux环境下编程时对时间的讨论主要涉及到3个方面
注：学过数学的童鞋应该都知道，C里面的的function并不是严格的数学意义上的函数，但是我们都是被这么教过来的，大量中文
文献上也都是用中文“函数”表示编程语言里面的“function”，其实本身名称并没有什么多大意义，而且大家也公认了，只是我不喜欢
将他们混为一谈而已，所以我的文章中都用“函式”来表示编程语言中的“function”，大家不用在意，只要能理解就好，个人偏好而已。


Linux里面的时间和UNIX中的起源是一样的，都是使用从1970年1月1日00：00：00的UTC时间开始到现在的计数秒数。
按照SUSV3标准，存储在类型为time_t类型变量中。

## 表示时间的数据结构
Linux和UNIX的发展过程中，出现了一系列的表示时间的数据结构，同时对于不同的和时间有关的系统调用也会使用不同的表示时间
的数据结构，这里便先来讨论下表示时间的数据结构，在后面谈到时间获取的时候在把各个的数据结构和其相对应的系统调用对应起来。

* 1)秒级时间：time_t

		#include <time.h>
		typedef long time_t;

该结构是最简单和常见的，同时也是来自ANSI C标准库中的，可见其历史悠久性。该类型变量表示的内容以秒为单位，一般表示一个相对多少
秒的相对时间。在Linux中，time_t被定义成long 型,如上面头文件所示。


* 2)微秒级时间：struct timeval

		#include <sys/time.h>
		struct timeval{
			time_t tv_sec; 	/* 表示秒 */	
			susecond_t tv_usec; 	/* 表示微秒 */
		};
	
该数据结构的引入，提高了时间表示的精度，从time_t的秒级精确到了微秒级。

* 3)纳秒级时间：struct timespec

		#include <time.h>
		struct timespec{
			time_t tv_sec;  /* 表示秒 */
			long tv_nsec;	/* 表示纳秒 */
		};

该数据结构的精确度更高，达到了纳秒级。同时也是C标准库中使用的，可移植性较好。因此一般的函式都采用此结构，只有少部分较老的
函式任使用time_val。

* 4)分段时间：strcut tm

		#include <time.h>
		struct tm{
			int tm_sec;                   /* Seconds.     [0-60] (1 leap second) */
			int tm_min;                   /* Minutes.     [0-59] */
			int tm_hour;                  /* Hours.       [0-23] */
			int tm_mday;                  /* Day.         [1-31] */
			int tm_mon;                   /* Month.       [0-11] */
			int tm_year;                  /* Year - 1900.  */
			int tm_wday;                  /* Day of week. [0-6] */
			int tm_yday;                  /* Days in year.[0-365] */
			int tm_isdst;                 /* DST.         [-1/0/1]*/

		#ifdef  __USE_BSD
			long int tm_gmtoff;           /* Seconds east of UTC.  */
			__const char *tm_zone;        /* Timezone abbreviation.  */
		#else
			long int __tm_gmtoff;         /* Seconds east of UTC.  */
			__const char *__tm_zone;      /* Timezone abbreviation.  */
		#endif
		};

上面的代码摘自kernel3.2.x的/usr/include/time.h文件。各个成员的含义注释中都表示的很清楚，不多解释。
其中秒的最大值为60时是因为要表示用第61秒表示润秒。而年表示的是自1900年后的第几年。DST表示的是Daylight Saveing time,具体什么
我也不是很清楚，望大家不吝告之与我。

* 5)进程时间:struct tms

		#include <sys/times.h>
		struct tms 
  		{
    			clock_t tms_utime;          /* User CPU time.  */
    			clock_t tms_stime;          /* System CPU time.  */

	    		clock_t tms_cutime;         /* User CPU time of dead children.  */
    			clock_t tms_cstime;         /* System CPU time of dead children.  */
		};  

上面的代码取自kernel 3.2.x的/usr/include/sys/times.h注意不是time.h关于time头文件有好多个，如/usr/include/time.h /usr/include/time.h 
、/usr/include/sys/timex.h、/usr/include/times.h。该结构定义了和CPU有关的时间，从注释中可以看到有用户CPU时间、系统CPU时间、子进程用户时间以及子进程系统时间。

其中，clock_t最终在Kerenl 3.2.x(32bit)中为long int,其定义颇为曲折，首先在/usr/include/time.h

	typedef __clock_t clock_t

然后在/usr/include/bits/types.h中

	__STD_TYPE__CLOCK_T_TYPE __CLOCK_T_TYPE __clock_t

接着在/usr/include/bits/typesize.h中
	
	#define __CLOCK_T_TYPE __SLONGWORD_TYPE

然后又回到/usr/include/bits/types中

	#define __SLONGWORD_TYPE long int 

终于找到。呵呵。

##获得时间
* 1) 获取当前时间
	* time
		
			#include <time.h>
			time_t time(time_t *t);

		该函式将得到自UNIX元年（1970.1.1.00：00：00）到调用函式时的秒数。参数t是值-结果参数，如果其不为NULL，则将时间填入给位置，否则不做操作。该函式返回前面所述的时间
单位为秒。
	* gettimeofday

			#include <sys/time.h>
			int gettimeofday(strcut timeval *tv,struct timezone *tz)

		该函式的作用与上面一个相同,只是其精度更高，由前面的时间数据结构我们知道其可以得到微秒级。参数tv为值-结果参数，成功时，将时间保存在该位置；参数tz是历史遗留物,
		在Linux里面始终将其赋值为NULL即可。函式成功时返回0，并填写tv结构，否则返回-1，表示失败。这时要查看errno。
	
	这个两个函式的errno可以参考LSP（Linux System Programming By Robert）。
	
* 2) 获取CPU时间

		#include <sys/time.h>
		clock_t times(strcut tms *buf);

该函式将会得到此时进程及其子进程运行的用户CPU时间和系统CPU时间。并填充到结构buf中。该函式成功时会返回一个相对于某个时间的值，但是处于移植性考虑，各个平台对这个时间的参考点
并不一样，因此该返回值通常没有实际意义，唯一有用的地方在于计算两个点之间的时间差，单位为Linux所用的Hz
可以通过

	sysconf(_SC_CLK_TCK)

得到，然后用clock_t除以该值即可得到秒数。
见示例：
	#include <stdio.h>
	#include <sys/times.h>
	#include <unistd.h> /* for sysconf(); */
	#include <time.h>


	int main(int argc,char *argv[]) 
	{

       		clock_t b,e;
        	b = times(NULL);
        	sleep(2);
        	e = times(NULL);
        	printf("b is %ld,e is %ld \n",b,e);
        	printf("total time is %ld s \n",(b-e)/sysconf(_SC_CLK_TCK));
        	return 0;

	}

可以得到程序运行的时间。
buf的结构可以参考上面的时间数据结构，其成员为结果,比较直观。这里解释下什么叫用户CPU时间和系统CPU时间。用户CPU时间就是用户写的代码中不涉及系统调用那部份代码执行的时间，而系统CPU时间
则是用户代码中设计系统调用，系统执行系统调用所用的CPU时间。
出错时返回-1。所以一般用该返回值来判断函式调用是否出错，而不是上述展示的用法，如想计算进程时间，可以用

下面这个函式获得进程时间的函式

	#include <time.h>
	clock_t clock(void);

该函式就如同上面times中将buf设置为NULL的情形，但是在Linux中其值不报括子进程执行时间，而在某些UNIX中则包括。

##时间格式的转换

通过之前的时间数据结构的介绍，以及我们使用时间的经验可以知道，时间格式的转换主要涉及3个时间数据结构，分别是time_t,struct tm 以及字符串（包括特定格式的字符串）。

* 1) time_t -- struct tm

从time_t到struct tm 有两个转换函式，分别为gmtime和localtime ，从字面意思便可以知道，前者得到的是GMTime，也就是格林乔治时间，而后者则是转换为本地时间域并计算了DST（Daylight
 Saving Time）的时间。从我机子的试验（fedora 16 kernel 3.2.x 上海时间）二者只在小时上相差8个小时，其他年月日和分秒一样。

 	#include <time.h>
	struct tm *gmtime(const time_t *pt)
	struct tm *localtime(const time_t *pt)

这里要注意的时，time_t采用传地址的方式。另外，这个两个函式返回的tm空间是共享的，如果想在后续继续使用其结果需要做保存操作。函式操作失败时返回NULL。

而从struct tm到time_t则可以使用mktime

	#include <time.h>
	time_t mktime(struct tm *ptm);

这个使用需要填写ptm结构，注意其中的isdst成员,对其的设置会影响DST是否计算，具体可以参见TLPI（The Linux Programming Interface )第10章。mktime会将ptm结构里的时间转换成local的
time_t时间，并且他足够只能以致于如果ptm里面的成员数值填写超过范围，他会自动进行截断并适当的进行“进一”操作。非常的智能。

* 2) time_t -- 字符串
要将数值很大的time_t数据变成人为可读的字符串可以采用ctime函式。

		#include <time.h>
		char *ctime(const time_t *pt);

使用很简单，该函式会返回一个26个byte的字符串，包括了一个换行和一个null字符，所有%s可以直接打印，并且已经包含一个换行符。

* 3)struct tm -- 字符串
一个比较简单和常用的操作是asctime，他和ctime基本一样，只是是对struc tm进行操作。如果想得到特定格式的结果可以用strftime来得到特定格式的时间字符串，而strptime则进行相反的操作。

		#inlcude <time.h>
		char asctime(const struct tm * ptm);

这个和上面的ctime几乎一样，不做过多讨论。

	#include <time.h>
	#define _XOPEN_SOURCE

	size_t strftime(char *outstr, size_t maxsize, const char *format, const struct tm *timeptr);
	char *strptime(const char *str, const char *format, struct tm *timeptr);

这两个函式可以实现把时间按特定格式呈现和把特定格式的字符串转换成时间的功能，由于涉及转换的格式占位符，太多，可以自行参考TLPI。

##设置系统时间

如果想更新系统时间，可以使用下面方法：

	#define _SVID_SOURCE	
	#include <time.h>
	int stime(time_t *t)

函式成功时会将系统时间设置为t所指时间，如果失败则返回-1。
可以尝试一下代码看看自己电脑的时间编程什么样了。
	
	#include <stdio.h>
	#include <stdlib.h>
	#include <time.h>

	int main(int argc,char *argv[]) 
	{

       		time_t t;  
        	if (2 != argc){
               		printf("settime time please \n");    
                	exit(-1);
        	}   
        	t = atoi(argv[1]);
        	if (stime(&t)){
               		 printf("stime error\n");
                	exit(-1);
        	}   
       		return 0;
	}

	sudo ./settime 0

由前面可知，上面的单位在秒级，如果想精确点，可以容易的猜想到要用struct timeval 或者 struct timespec这时可以使用接口settimeofday

	#include <sys/time.h>
	int settimeof(const struct timeval *tv,const struct timezone *tz);

由gettimeofday我们知道tz作为历史遗留的问题。Linux里面只要NULL就好。
可以把系统设置成tv所指向的值。

##休眠
##定时器（待补充）


	
