.. vim: syntax=rst

GIF图片显示
-------

GIF（Graphics Interchange Format）即图形交换格式，是由CompuServe公司在1980年代开发的一种图像文件格式。它被设计用来在数据网络之间传输图像。GIF标准支持交错、透明度、应用程序定义的数据，动画和原始文本的渲染。GIF文件使用LZW （Lempel-Zif-
Welch）文件压缩方法来压缩图像数据。GIF格式有两种版本，一种版本只能存储一张图像，而另一种版本加入了对多帧图像的支持，这便是我们熟知的动图。

emWin在做GIF解压的时候需要大约16KB左右的RAM空间。在完成图像绘制后，用于解压的RAM空间会被释放。

GIF显示相关API
~~~~~~~~~~

表格 33‑1 GIF显示相关API

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 函数名                    | 描
     - |

   * - GUI_GIF_Draw()
     - 绘制已加载到内存中的GIF文件的第一张图像 |

   * - GUI_GIF_DrawEx()
     - 绘制                                    | 不需要加载到内存中的GIF文件的第一张图像 |

   * - GUI_GIF_DrawSub()
     - 绘制已加载到内存中的GIF文件的给定子图像 |

   * - GUI_GIF_DrawSubEx()
     - 绘制                                    | 不需要加载到内存中的GIF文件的给定子图像 |

   * - GUI_GIF_DrawSubScaled()
     - 使用缩放比例                            | 绘制已加载到内存中的GIF文件的给定子图像 |

   * - GUI_GIF_DrawSubScaledEx()
     - 使用缩放比例绘制GIF文件的给             | 定子图像，无需缩放即可将其加载到内存中  |

   * - GUI_GIF_GetComment()
     - 返回已加载到内存中的GIF文件的给定注释   |

   * - GUI_GIF_GetCommentEx()
     - 返                                      | 回不需要加载到内存中的GIF文件的给定注释 |

   * - GUI_GIF_GetImageInfo()
     - 返回有关已                              | 加载到内存中的GIF文件的给定子图像的信息 |

   * - GUI_GIF_GetImageInfoEx()
     - 返回有关GIF文件的给                     | 定子图像的信息，该信息无需加载到内存中  |

   * - GUI_GIF_GetInfo()
     - 返回有关已加载到内存中的GIF文件的信息   |

   * - GUI_GIF_GetInfoEx()
     - 返                                      | 回有关不需要加载到内存中的GIF文件的信息 |

   * - GUI_GIF_GetXSize()
     - 返回加载到内存中的位图的X大小           |

   * - GUI_GIF_GetXSizeEx()
     - 返回不需要加载到内存中的位图的X大小     |

   * - GUI_GIF_GetYSize()
     - 返回加载到内存中的位图的Y大小           |

   * - GUI_GIF_GetYSizeEx()
     - 返回不需要加载到内存中的位图的Y大小     |


GUI_GIF_DrawSub()
'''''''''''''''''

在当前窗口中的指定位置绘制gif文件的给定子图像，该文件已被加载到内存中。

代码清单 33‑1 函数原型

1 int GUI_GIF_DrawSub(const void \*pGIF, U32 NumBytes, int x0, int y0,

2 int Index);

1) pGIF：指向gif文件所在的存储区的开始的指针；

2) NumBytes：gif文件的字节数；

3) x0：GIF左上角在屏幕上的x位置；

4) y0：GIF左上角在屏幕上的x位置；

5) Index：要显示的子图像的索引，从0开始。

返回值：绘制成功返回0，绘制失败返回非0。

GUI_GIF_DrawSubEx()
'''''''''''''''''''

在当前窗口的指定位置绘制gif文件的给定子图像，该文件不必加载到内存中。

代码清单 33‑2 函数原型

1 int GUI_GIF_DrawSubEx(GUI_GET_DATA_FUNC \*pfGetData, void \*p, int x0,

2 int y0, int Index);

1) pfGetData：指向用于获取数据的函数的指针；

2) p：传递给pfGetData指向的函数的空指针；

3) x0：GIF左上角在屏幕上的x位置；

4) y0：GIF左上角在屏幕上的x位置；

5) Index：要显示的子图像的索引，从0开始。

..

   返回值：绘制成功返回0，绘制失败返回非0。

GUI_GIF_GetImageInfo()
''''''''''''''''''''''

返回已加载到内存中的GIF文件的给定子图像的信息。

代码清单 33‑3 函数原型

1 int GUI_GIF_GetImageInfo(const void \*pGIF, U32 NumBytes,

2 GUI_GIF_IMAGE_INFO \*pInfo, int Index);

1) pGIF：指向gif文件所在的存储区的开始的指针；

2) NumBytes：gif文件的字节数；

3) pInfo：指向GUI_GIF_IMAGE_INFO结构的指针，该结构由函数填充；

4) Index：要显示的子图像的索引，从0开始；

..

   返回值：获取成功返回0，获取失败返回非0。

此函数主要用来获取GIF动图的一些基本信息，其中子图象的显示时长在播放动图的时候很有用，如果显示时长为0，则使用默认的10ms长度。

代码清单 33‑4 GUI_GIF_IMAGE_INFO结构原型

1 typedef struct {

2 int xPos;

3 int yPos;

4 int xSize;

5 int ySize;

6 int Delay;

7 } GUI_GIF_IMAGE_INFO;

1) xPos：最后绘制图像的X位置；

2) yPos：最后绘制图像的Y位置；

3) xSize：最后绘制图像的X大小；

4) ySize：最后绘制图像的Y大小；

5) Delay：每张子图象的显示时长，单位10ms。

GUI_GIF_GetInfo()
'''''''''''''''''

返回一个信息结构，其中包含关于已加载到内存中的给定GIF文件中的子图像的大小和数量的信息。

代码清单 33‑5 函数原型

1 int GUI_GIF_GetInfo(const void \*pGIF, U32 NumBytes, GUI_GIF_INFO

2 \*pInfo);

1) pGIF：指向gif文件所在的存储区的开始的指针；

2) NumBytes：gif文件的字节数；

3) pInfo：指向GUI_GIF_INFO结构的指针，该结构由这个函数填充。

返回值：获取成功返回0，获取失败返回非0。

代码清单 33‑6 GUI_GIF_INFO结构原型

1 typedef struct {

2 int xSize;

3 int ySize;

4 int NumImages;

5 } GUI_GIF_INFO;

1) xSize：图像的X大小；

2) ySize：图像的Y大小；

3) NumImages：文件中子图像的数目。

GIF图片显示实验
~~~~~~~~~

接下来我们通过一个实验来讲解如何简单的显示GIF图片，更多API函数的演示实验可参考官方例程2DGL_DrawGIF.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\2DGL_DrawGIF.c

代码分析
^^^^

(1) 绘制外部存储器（SD卡）中的GIF

代码清单 33‑7 \_ShowGIFEx函数（MainTask.c文件）

1 /*\*

2 \* @brief 直接从存储器中绘制BMP图片数据

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* @retval 无

6 \*/

7 static void ShowGIFEx(const char \*sFilename)

8 {

9 GUI_GIF_INFO Gifinfo = {0};

10 GUI_GIF_IMAGE_INFO Imageinfo = {0};

11 int i = 0;

12

13 /\* 进入临界段 \*/

14 taskENTER_CRITICAL();

15 /\* 打开图片 \*/

16 result = f_open(&file, sFilename, FA_READ);

17 if ((result != FR_OK)) {

18 printf("文件打开失败！\r\n");

19 \_acBuffer[0]='\0';

20 }

21 /\* 退出临界段 \*/

22 taskEXIT_CRITICAL();

23

24 /\* 获取GIF文件信息 \*/

25 GUI_GIF_GetInfoEx(_GetData, &file, &Gifinfo);

26 /\* 循环显示所有的GIF帧 \*/

27 for (i = 0; i < Gifinfo.NumImages; i++) {

28 /\* 获取GIF子图象信息 \*/

29 GUI_GIF_GetImageInfoEx(_GetData, &file, &Imageinfo, i);

30 /\* 绘制GIF子图象 \*/

31 GUI_GIF_DrawSubEx(_GetData, &file,

32 (LCD_GetXSize() - Gifinfo.xSize) / 2,

33 (LCD_GetYSize() - Gifinfo.ySize) / 2, i);

34 /\* 帧延时 \*/

35 GUI_Delay(Imageinfo.Delay);

36 }

37 /\* 读取完毕关闭文件 \*/

38 f_close(&file);

39 }

如代码清单 33‑7所示，从外部存储器中直接绘制GIF图片前必须先通过文件系统函数f_open函数打开GIF文件，图片打开成功后调用GUI_GIF_GetInfoEx函数获取GIF图片的X大小、Y大小和总帧数等信息，根据获取到的总帧数使用for循环绘制GIF子图象，循环体中GUI_GIF_GetIm
ageInfoEx函数主要用来获取GIF每一帧的显示时间，GUI_GIF_DrawSubEx负责绘制GIIF图像，GUI_Delay负责长度执行每一帧显示时间的演示。

GUI_GIF_DrawSubEx函数和直接绘制BMP一样，需要一个专门的数据读取函数才能绘制图片，见代码清单 33‑8。

代码清单 33‑8 \_GetData函数（MainTask.c文件）

1 /*\*

2 \* @brief 从存储器中读取数据

3 \* @note 无

4 \* @param

5 \* @retval NumBytesRead：读到的字节数

6 \*/

7 int \_GetData(void \* p, const U8 \*\* ppData, unsigned NumBytesReq, U32

8 Off)

9 {

10 static int FileAddress = 0;

11 UINT NumBytesRead;

12 FIL \*Picfile;

13

14 Picfile = (FIL \*)p;

15

16 if (NumBytesReq > sizeof(_acBuffer)) {

17 NumBytesReq = sizeof(_acBuffer);

18 }

19

20 if (Off == 1) FileAddress = 0;

21 else FileAddress = Off;

22 result = f_lseek(Picfile, FileAddress);

23

24 /\* 进入临界段 \*/

25 taskENTER_CRITICAL();

26 result = f_read(Picfile, \_acBuffer, NumBytesReq, &NumBytesRead);

27 /\* 退出临界段 \*/

28 taskEXIT_CRITICAL();

29

30 \*ppData = (const U8 \*)_acBuffer;

31

32 return NumBytesRead;

33 }

如代码清单 33‑8所示，_GetData函数用于读取外部存储器中的图片数据，每调用一次就读取图片一整行的像素数据，请确保数据缓冲区_acBuffer[]的大小足够装下一整行像素数据。_GetData函数将作为GUI_JPEG_DrawEx函数的其中一个参数使用，当emWin从外部存储器直接绘制图片
时，这个读取函数必须要有。事实上显示GIF图片的_GetData函数与用来显示BMP和JPEG的是同一个函数。

(2) 绘制已加载到内存中的GIF

代码清单 33‑9 \_ShowGIF函数（MainTask.c文件）

1 /*\*

2 \* @brief 加载GIT图片到内存中并绘制

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* @retval 无

6 \*/

7 static void ShowGIF(const char \*sFilename)

8 {

9 WM_HMEM hMem;

10 GUI_GIF_INFO Gifinfo = {0};

11 GUI_GIF_IMAGE_INFO Imageinfo = {0};

12 int i = 0;

13 int j = 0;

14

15 /\* 进入临界段 \*/

16 taskENTER_CRITICAL();

17 /\* 打开图片 \*/

18 result = f_open(&file, sFilename, FA_READ);

19 if ((result != FR_OK)) {

20 printf("文件打开失败！\r\n");

21 \_acbuffer[0]='\0';

22 }

23

24 /\* 申请一块动态内存空间 \*/

25 hMem = GUI_ALLOC_AllocZero(file.fsize);

26 /\* 转换动态内存的句柄为指针 \*/

27 \_acbuffer = GUI_ALLOC_h2p(hMem);

28

29 /\* 读取图片数据到动态内存中 \*/

30 result = f_read(&file, \_acbuffer, file.fsize, &f_num);

31 if (result != FR_OK) {

32 printf("文件读取失败！\r\n");

33 }

34 /\* 读取完毕关闭文件 \*/

35 f_close(&file);

36 /\* 退出临界段 \*/

37 taskEXIT_CRITICAL();

38

39 /\* 获取GIF文件信息 \*/

40 GUI_GIF_GetInfo(_acbuffer, file.fsize, &Gifinfo);

41 /\* 显示2遍GIF \*/

42 for (j = 0; j < 2; j++) {

43 /\* 循环显示所有的GIF帧 \*/

44 for (i = 0; i<Gifinfo.NumImages; i++) {

45 /\* 获取GIF子图象信息 \*/

46 GUI_GIF_GetImageInfo(_acbuffer, file.fsize, &Imageinfo, i);

47 /\* 绘制GIF子图象 \*/

48 GUI_GIF_DrawSub(_acbuffer, file.fsize,

49 (LCD_GetXSize() - Gifinfo.xSize) / 2,

50 (LCD_GetYSize() - Gifinfo.ySize) / 2, i);

51 /\* 帧延时 \*/

52 GUI_Delay(Imageinfo.Delay);

53 }

54 }

55 /\* 释放内存 \*/

56 GUI_ALLOC_Free(hMem);

57 }

如代码清单 33‑9所示，加载GIT图片到内存中并绘制的操作与从外部存储器中直接绘制的操作几乎是相同的，都是必须通过文件系统函数f_open函数打开图片文件，图片打开成功后调用GUI_GIF_GetInfoEx函数获取GIF图片的X大小、Y大小和总帧数等信息，根据获取到的总帧数使用for循环绘制GI
F子图象，循环体中GUI_GIF_GetImageInfoEx函数主要用来获取GIF每一帧的显示时间，GUI_Delay负责长度执行每一帧显示时间的演示。不同的是绘制GIF子图象用的是GUI_GIF_DrawSub函数，不需要额外的数据读取函数。

(3) MainTask函数

本实验我们用两种GIF绘制方法绘制3张GIF图片，见代码清单 33‑10。

代码清单 33‑10 MainTask函数（MainTask.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 /\* 设置背景色 \*/

10 GUI_SetBkColor(GUI_WHITE);

11 GUI_Clear();

12 /\* 设置字体 \*/

13 GUI_SetFont(GUI_FONT_24B_ASCII);

14 GUI_SetColor(GUI_BLACK);

15 while (1) {

16 /\* 直接从存储器中绘制BMP图片数据 \*/

17 GUI_DispStringHCenterAt("ShowGIFEx", LCD_GetXSize()/2, 10);

18 ShowGIFEx("0:/image/dolphin.gif");

19 GUI_Delay(100);

20 GUI_Clear();

21

22 /\* 加载GIT图片到内存中并绘制 \*/

23 GUI_DispStringHCenterAt("ShowGIF", LCD_GetXSize()/2, 10);

24 ShowGIF("0:/image/dolphin.gif");

25 GUI_Delay(100);

26 GUI_Clear();

27

28 GUI_DispStringHCenterAt("ShowGIF", LCD_GetXSize()/2, 10);

29 ShowGIF("0:/image/rabbit.gif");

30 GUI_Delay(100);

31 GUI_Clear();

32

33 GUI_DispStringHCenterAt("ShowGIF", LCD_GetXSize()/2, 10);

34 ShowGIF("0:/image/Groundhog.gif");

35 GUI_Delay(100);

36 GUI_Clear();

37 }

38 }

如代码清单 33‑10所示，在while循环中调用ShowGIFEx、ShowGIF显示三张GIF图，其中“dolphin.gif”分别被ShowGIFEx和ShowGIF各显示了一次，用来比较两种方式的显示效果。

实验现象
^^^^

GIF图片显示实验的实验现象如图 33‑1所示，把实验代码下到开发板上可以看到，ShowGIFEx函数显示的GIF非常的不流畅，而ShowGIF函数显示的GIF就很流畅。

|GIF002|

图 33‑1 GIF图片显示实验现象

.. |GIF002| image:: media\GIF002.png
   :width: 5.76806in
   :height: 3.46228in
