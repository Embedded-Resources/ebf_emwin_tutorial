.. vim: syntax=rst

PNG图片显示
==============

PNG（Portable Network Graphics）格式，是一种光栅图形文件格式，支持无损数据压缩和透明度，支持基于调色板的图像、灰度图像以及非调色板的全色RGB图像。1999年，Unisys公司进一步中止了对自由软件和非商用软件开发者的GIF专利免费许可，从而使PNG格式获得了更多的关注。
PNG是目前保证最不失真的格式，采用LZ77算法的派生算法进行压缩，能把图像文件压缩到极限以利于网络传输，但又能保留所有与图像品质有关的信息。

移植PNG库
~~~~~~

emWin对PNG支持可以通过使用基于Glenn Randers-Pehrson，Guy Eric Schalnat和Andreas Dilger的“libpng”库来实现。但emWin内部并不包含PNG解码库，需要用户自行移植。

接下来我们讲解一下将PNG库移植到emWin工程中的步骤。

1) 下载并解压emWin_png压缩包。PNG解码库可以从emWin官网下载到，链接如下：\ https://www.segger.com/downloads/emwin/emWin_PNG\ 。下载完成后解压得到的emWin_png压缩包，可以看到如图
34‑1所示的结构，压缩包内包含了对应emWin各个版本的PNG解码库，我们选取适合我们例程的版本即选择V544。

|PNG002|

图 34‑1 emWin_png压缩包内容

2) 添加PNG库到工程文件夹。选择好版本之后，将PNG库整个文件夹复制到工程文件夹下，其实随便那个文件夹都行，只要在工程文件夹内。如图 34‑2所示，我们选择将PNG库放在工程的STemWin文件夹下，并重命名为png；

|PNG003|

图 34‑2 添加PNG库到工程

3) 添加PNG库文件到工程。在MDK工程内新建分组并把PNG库添加进新分组中，如图 34‑3所示；

|PNG004|

图 34‑3 添加PNG库文件到工程

4) 添加PNG库头文件路径。最后一步，在工程设置中把PNG库的头文件路径添加进去，见图 34‑4。

|PNG005|

图 34‑4 添加PNG库头文件路径

至此，emWin的PNG移植就完成了，可能最后工程编译完成后有较多警告，属于正常情况。

此PNG库在做解码操作的时候会固定占用21KB的RAM空间，这部分与图像大小无关，而总的RAM占用可通过下面的公式得到：

总RAM占用 = （图像xSize + 1）\* 图像ySize + 21KB

PNG显示相关API
~~~~~~~~~~

表格 34‑1 PNG显示相关API

==================== ===================================
函数名               描述
==================== ===================================
GUI_PNG_Draw()       绘制已加载到内存中的PNG文件
GUI_PNG_DrawEx()     绘制不需要加载到内存中的PNG文件
GUI_PNG_GetXSize()   返回加载到内存中的位图的X大小
GUI_PNG_GetXSizeEx() 返回不需要加载到内存中的位图的X大小
GUI_PNG_GetYSize()   返回加载到内存中的位图的Y大小
GUI_PNG_GetYSizeEx() 返回不需要加载到内存中的位图的Y大小
==================== ===================================

GUI_PNG_Draw()
''''''''''''''

在当前窗口的指定位置绘制一个已加载到内存中的png文件。

代码清单 34‑1 函数原型

1 int GUI_PNG_Draw(const void \*pFileData, int FileSize, int x0, int y0);

1) pFileData：指向png文件所在的存储区域的开头的指针；

2) DataSize：png文件的字节数；

3) x0：png左上角在屏幕上的x位置；

4) y0：png左上角在屏幕上的y位置。

返回值：绘制成功返回0，绘制失败返回非0。不过当前的实现是任何情况都返回0。

GUI_PNG_DrawEx()
''''''''''''''''

在当前窗口的指定位置绘制一个png文件，该文件不必加载到内存中。

代码清单 34‑2 函数原型

1 int GUI_PNG_DrawEx(GUI_GET_DATA_FUNC \*pfGetData, void \*p, int x0, int

2 y0);

1) pfGetData：指向为获取数据而调用的函数的指针；

2) p：传递给pfGetData指向的函数的空指针；

3) x0：png左上角在屏幕上的x位置；

4) y0：png左上角在屏幕上的y位置。

返回值：绘制成功返回0，绘制失败返回非0。不过当前的实现是任何情况都返回0

需要注意的是，PNG解码库会在其内部为整个图像分配一个缓冲区，这是此函数无法避免的。

PNG图片显示实验
~~~~~~~~~

接下来我们通过一个实验来讲解如何简单的显示PNG图片，更多API函数的演示实验可参考官方例程2DGL_DrawPNG.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\2DGL_DrawPNG.c

代码分析
^^^^

(1) 绘制外部存储器（SD卡）中的PNG

代码清单 34‑3 \_ShowPNGEx函数（MainTask.c文件）

1 /*\*

2 \* @brief 直接从存储器中绘制PNG图片数据

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* @retval 无

6 \*/

7 static void ShowPNGEx(const char \*sFilename, int x0, int y0)

8 {

9 /\* 进入临界段 \*/

10 taskENTER_CRITICAL();

11 /\* 打开图片 \*/

12 result = f_open(&file, sFilename, FA_READ);

13 if ((result != FR_OK)) {

14 printf("文件打开失败！\r\n");

15 }

16 /\* 退出临界段 \*/

17 taskEXIT_CRITICAL();

18

19 GUI_PNG_DrawEx(_GetData, &file, x0, y0);

20

21 /\* 读取完毕关闭文件 \*/

22 f_close(&file);

23 }

如代码清单 34‑3所示，从外部存储器种直接绘制JPEG图片的操作与绘制BMP图片的操作几乎是相同的，都是必须通过文件系统函数f_open函数打开图片文件，图片打开成功后调用GUI_JPEG_DrawEx函数绘制，这个函数和直接绘制BMP一样，需要一个专门的数据读取函数才能绘制图片，见代码清单
34‑4。

代码清单 34‑4 \_GetData函数（MainTask.c文件）

1 /*\*

2 \* @brief 从存储器中读取数据

3 \* @note 无

4 \* @param

5 \* @retval NumBytesRead：读到的字节数

6 \*/

7 static int \_GetData(void \* p, const U8 \*\* ppData, unsigned NumBytesReq,

8 U32 Off)

9 {

10 U8 \*pData;

11 FIL \*Picfile;

12 UINT NumBytesRead;

13 static int FileAddr = 0;

14

15 pData = (U8 \*)*ppData;

16 Picfile = (FIL \*)p;

17

18 if (Off == 1) FileAddr = 0;

19 else FileAddr = Off;

20 result = f_lseek(Picfile, FileAddr);

21

22 /\* 进入临界段 \*/

23 taskENTER_CRITICAL();

24 result = f_read(Picfile, pData, NumBytesReq, &NumBytesRead);

25 /\* 退出临界段 \*/

26 taskEXIT_CRITICAL();

27

28 return NumBytesRead;

29 }

如代码清单 34‑4所示，_GetData函数用于读取外部存储器中的图片数据，此函数函数将作为GUI_PNG_DrawEx函数的其中一个参数使用，当emWin从外部存储器直接绘制图片时，这个读取函数必须要有。需要注意的是，此函数与BMP、JPEG和GIF的_GetData函数是不同的。

(2) 绘制已加载到内存中的PNG

代码清单 34‑5 \_ShowPNG函数（MainTask.c文件）

1 /*\*

2 \* @brief 加载PNG图片到内存中并绘制

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* @retval 无

6 \*/

7 static void ShowPNG(const char \*sFilename, int x0, int y0)

8 {

9 WM_HMEM hMem;

10

11 /\* 进入临界段 \*/

12 taskENTER_CRITICAL();

13 /\* 打开图片 \*/

14 result = f_open(&file, sFilename, FA_READ);

15 if ((result != FR_OK)) {

16 printf("文件打开失败！\r\n");

17 \_acbuffer[0]='\0';

18 }

19

20 /\* 申请一块动态内存空间 \*/

21 hMem = GUI_ALLOC_AllocZero(file.fsize);

22 /\* 转换动态内存的句柄为指针 \*/

23 \_acbuffer = GUI_ALLOC_h2p(hMem);

24

25 /\* 读取图片数据到动态内存中 \*/

26 result = f_read(&file, \_acbuffer, file.fsize, &f_num);

27 if (result != FR_OK) {

28 printf("文件读取失败！\r\n");

29 }

30 /\* 读取完毕关闭文件 \*/

31 f_close(&file);

32 /\* 退出临界段 \*/

33 taskEXIT_CRITICAL();

34

35 GUI_PNG_Draw(_acbuffer, file.fsize, x0, y0);

36

37 /\* 释放内存 \*/

38 GUI_ALLOC_Free(hMem);

39 }

如代码清单 34‑5所示，绘制已加载到内存的PNG的步骤与直接从外部存储器绘制的操作略有不同，并且没有了专门的数据读取函数。首先还是必须要用f_open打开PNG图片文件，然后不同的是用GUI_ALLOC_AllocZero函数申请一块动态内存，并且用GUI_ALLOC_h2p把这段动态内存的句柄转
为指针_acbuffer，方便之后使用，接着用f_read函数把图片数据读到刚刚申请到的动态内存中，读取完成后关闭文件，使用GUI_PNG_Draw函数将动态内存中的PNG数据绘制到LCD上，如果之后的程序不再使用这张PNG，就必须使用GUI_ALLOC_Free函数释放动态内存。

(3) MainTask函数

本实验我们用两种PNG绘制方法绘制4张PNG图片，见代码清单 34‑6。

代码清单 34‑6 MainTask函数（MainTask.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 int i = 0;

10 /\* 设置背景颜色 \*/

11 GUI_SetBkColor(GUI_LIGHTCYAN);

12 GUI_Clear();

13

14 GUI_SetTextMode(GUI_TM_TRANS \| GUI_TM_XOR);

15 GUI_SetFont(GUI_FONT_24B_ASCII);

16

17 while (1) {

18 i++;

19

20 switch (i) {

21 case 1:

22 GUI_DispStringHCenterAt("ShowPNGEx", LCD_GetXSize()/2, 10);

23 ShowPNGEx("0:/image/野火.png", 80, 153);

24 break;

25 case 2:

26 GUI_DispStringHCenterAt("ShowPNG", LCD_GetXSize()/2, 10);

27 ShowPNG("0:/image/one_punch_man.png", 45, 80);

28 ShowPNG("0:/image/pokemon.png", 405, 60);

29 break;

30 case 3:

31 GUI_DispStringHCenterAt("ShowPNG", LCD_GetXSize()/2, 10);

32 ShowPNG("0:/image/滑稽.png", 280, 100);

33 break;

34 default:

35 i = 0;

36 break;

37 }

38 GUI_Delay(1000);

39 GUI_Clear();

40 }

41 }

如代码清单 34‑6所示，在while循环中调用ShowPNGEx、ShowPNG显示4张PNG图。由于每调用一次显示API就要做一次PNG解码，所以在开发板上的ShowPNGEx和ShowPNG实际消耗的时间是差不多的。如果需要频繁调用较大尺寸和质量的PNG图像，建议使用内存设备进行加速。

实验现象
^^^^

PNG图片显示实验的实验现象如图 34‑5、图 34‑6和图 34‑7所示，把程序下到开发板上后，可以观察到实验所显示的PNG图片会依次循环显示。

|PNG006|

图 34‑5 PNG图片显示实验现象1

|PNG007|

图 34‑6 PNG图片显示实验现象2

|PNG008|

图 34‑7 PNG图片显示实验现象3

.. |PNG002| image:: media\PNG002.png
   :width: 5.61321in
   :height: 2.66757in
.. |PNG003| image:: media\PNG003.png
   :width: 3.08491in
   :height: 1.67204in
.. |PNG004| image:: media\PNG004.png
   :width: 2.18349in
   :height: 3.85849in
.. |PNG005| image:: media\PNG005.png
   :width: 4.18195in
   :height: 2.84906in
.. |PNG006| image:: media\PNG006.png
   :width: 5.76806in
   :height: 3.46228in
.. |PNG007| image:: media\PNG007.png
   :width: 5.76806in
   :height: 3.46228in
.. |PNG008| image:: media\PNG008.png
   :width: 5.76806in
   :height: 3.46228in
