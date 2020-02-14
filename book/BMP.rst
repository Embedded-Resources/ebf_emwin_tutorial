.. vim: syntax=rst

BMP图片显示
-------

emWin支持BMP图片的显示，提供的API函数主要支持两种图片显示方式：一种是从外部存储器中读取数据并显示，一边读一边显示，占用RAM空间小，但显示速度很慢；另一种是从外部存储器把图片数据全部读取出来再显示，显示速度较快，但占用大量RAM空间。

emWin支持BMP文件格式版本3，它包含几种不同的图像格式，如表格 31‑1所示：

表格 31‑1 emWin支持的BMP格式

======== ==== ==== ========
色彩深度 索引 压缩 是否支持
======== ==== ==== ========
1        是   否   是
4        是   否   是
4        是   是   是
8        是   否   是
8        是   是   是
16       否   否   是
24       否   否   是
32       否   否   是
======== ==== ==== ========

BMP图片格式
~~~~~~~

BMP文件格式，又称为位图（Bitmap）或是DIB(Device-Independent Device，设备无关位图)，是Windows系统中广泛使用的图像文件格式。BMP文件保存了一幅图像中所有的像素。

BMP格式可以保存单色位图、16色或256色索引模式像素图、24位真彩色图象，每种模式中单一像素点的大小分别为1/8字节，1/2字节，1字节和32字节。目前最常见的是256位色BMP和24位色BMP。

BMP文件格式还定义了像素保存的几种方法，包括不压缩、RLE压缩等。常见的BMP文件大多是不压缩的。

BMP文件的数据可以分为四个部分：

1) BMP文件头：存放了有关文件的格式、大小等信息。我们仅讨论24位色不压缩的BMP，所以文件头中的信息基本不需要注意；

2) 位图信息头：包括图像大小、位平面数、压缩方式、颜色索引等信息。在位图信息头之中，只有“大小”这一项对我们比较有用。图像的宽度和高度都是一个32位整数，在文件中的地址分别为0x0012和0x0016。

3) 调色板：索引与其对应的颜色的映射表。16色或256色BMP有颜色表，但在24位色BMP文件则没有，我们这里不考虑。

4) 位图数据：实际的像素数据。

因此总的来说BMP图片的优点是简单。下面我们使用WinHex软件(跟UltraEdit软件功能类似)来分析一下BMP图像的文件内容。

BMP文件头
^^^^^^

图 31‑1的阴影部分处是某张BMP图片文件的头部信息，具体说明参考表格 31‑2。

|BMP002|

图 31‑1 BMP文件头部信息

表格 31‑2 BMP文件头说明

.. list-table::
   :widths: 20 20 20 20 20
   :header-rows: 0


   * - 变量名      | 地
     - | 作用
     - | 阴影部分处
     - |
     - |

   * -
     -
     -
     - 值          |
     - 参数说明     |

   * - bfType
     - 00~01h
     - 文件类型     | 42
     - 4D       | 如果
     - 位图   | 文件类型，必 | 须分别为0x42 | 和0x4D       | ，0          | x424D=’BM’。 |

   * - bfSize
     - 02~05h
     - 文件大小     | 36
     - 30 0F 00 | 00
     - F3036h =  | ‬995382B 约等于 972k  |

   * - bfReserved1
     - 06~07h
     - 保留字       | 0
     - 00       | 不
     - 虑       |

   * - bfReserved2
     - 08~09h
     - 保留字       | 0
     - 00       | 同
     - |

   * - bfOffBits
     - 0a~0dh
     - 实           | 际位图数据的 | 偏移字节数， | 即BMP文件头  | ，位图信息头 | 与调色板之和 |
     - 36 00 00 00 | | 54，刚 | 刚好等于 | 文件 | （BMP | 和位图信
     - 00000036h =  | | 们 | 部信息 | 件头  | 头 | ），因为这张 | 图片是24位位 | 图，所以调色 | 板的大小为0  |


3到14字节的意义可以用一个结构体来描述，见代码清单 31‑1。

代码清单 31‑1 BMP文件信息数据结构体

1 typedef struct tagBITMAPFILEHEADER

2 {

3 //attention: sizeof(DWORD)=4 sizeof(WORD)=2

4 DWORD bfSize; //文件大小

5 WORD bfReserved1; //保留字，不考虑

6 WORD bfReserved2; //保留字，同上

7 DWORD bfOffBits; //实际位图数据的偏移字节数，即前三个部分长度之和

8 } BITMAPFILEHEADER,tagBITMAPFILEHEADER;

位图信息头
^^^^^

剩下的部分就是位图信息头，也就是图 31‑2的处，具体说明参考表格 31‑3。

|BMP003|

图 31‑2 位图信息头

表格 31‑3 位图信息头

.. list-table::
   :widths: 20 20 20 20 20
   :header-rows: 0


   * - 变量名       | 地
     - | 作用
     - | 阴影部分处
     - |
     - |

   * -
     -
     -
     - 值          |
     - 参数说明     |

   * - biSize
     - 0e~11h
     - 指           | 定结构体BITM | APINFOHEADER 的长度，为40 |
     - 28 00 00 00 | | 40

               | 信息头
     - 00000028h =  | 就        | 是说这个位图 | 大小 | 为40个字节。 |

   * - biWidth
     - 12~15h
     - 位图宽，     | 00 以像素为单位 |
     - 03 00 00 | 00 | 768像
     - 0300h =   | |

   * - biHeight
     - 16~19h
     - 位图高，     | B0 以像素为单位 |
     - 01 00 00 | 00 | 432像
     - 001B0h =  | |

   * - biPlanes
     - 1A~1Bh
     - 平面数       | 0 ，该值总为1  |
     - 01       | 平 |
     - 数：1    | |

   * - biBitCount
     - 1C~1Dh
     - 图像色       | 1 彩深度，可以 | 是1，2，4，  | 8，16，24或  | 32
     - 00       | 0 | 24位色 | |
     - 18h=       | 深度 | | |

   * - biCompression
     - 1E~21h
     - 压缩方       | 0 式，可以是0  | ，1，2，其中 | 0表示不压缩  |
     - 00 00    | 不 | | |
     - 缩       | | | |

   * - biSizeImage
     - 22~25h
     - 实际位图数据 | 00 0 占用的字节数 |
     - 00 00 | 图 | 像不压缩
     - |

         |

   * - biXPelsPerMeter
     - 26~29h
     - X方向分辨率  | C4
     - E 00 00 | 000
     - 0EC4h    | =3780像素/米 |

   * - biYPelsPerMeter
     - 2A~2Dh
     - Y方向分辨率  | C4
     - E 00 00 | 000
     - 0EC4h    | =3780像素/米 |

   * - biClrUsed
     - 2E~31h
     - 使用的颜色数 | 00 0 ，如果为0，  | 则表示默认值 | (2^颜色位数) |
     - 00 00 | 默认值 | | |
     - |
           |
          |
            |

   * - biClrImportant
     - 32~35h
     - 重要颜色数   | 00 ，如果为0，  | 则表示所有颜 | 色都是重要的 |
     - 0 00 00 | 所有颜 | 色都是 | |
     - |

         |
         |


位图信息头结构体，见代码清单 31‑2。

代码清单 31‑2 位图信息头内容

1 typedef struct tagBITMAPINFOHEADER

2 {

3 //attention: sizeof(DWORD)=4 sizeof(WORD)=2

4 DWORD biSize; //指定此结构体的长度，为40

5 LONG biWidth; //位图宽，说明本图的宽度，以像素为单位

6 LONG biHeight; //位图高，指明本图的高度，像素为单位

7 WORD biPlanes; //平面数，为1

8 WORD biBitCount; //采用颜色位数，可以是1，2，4，8，16，24新的可以是32

9 DWORD biCompression; //压缩方式，可以是0，1，2，其中0表示不压缩

10 DWORD biSizeImage; //实际位图数据占用的字节数

11 LONG biXPelsPerMeter; //X方向分辨率

12 LONG biYPelsPerMeter; //Y方向分辨率

13 DWORD biClrUsed; //使用的颜色数，如果为0，则表示默认值(2^颜色位数)

14 DWORD biClrImportant; //重要颜色数，如果为0，则表示所有颜色都是重要的

15 } BITMAPINFOHEADER,tagBITMAPINFOHEADER;

由于使用的是24位的位图，所以没有调色板，位图的大小为768*432。

图像像素数据
^^^^^^

本节使用色彩深度为24位真彩色的BMP图片进行讲解，则54字节之后就是像素部分，如图 31‑3的阴影部分③处。

|BMP004|

图 31‑3 BMP像素数据

以上内容就是对BMP图像文件的介绍。如果想要了解其中更多的细节，可以上网搜索相关的内容。

BMP显示相关API
~~~~~~~~~~

表格 31‑4 BMP显示相关API

======================== =========================================
函数名                   描述
======================== =========================================
GUI_BMP_Draw()           绘制已加载到内存中的BMP文件
GUI_BMP_DrawEx()         绘制不加载到内存中的BMP文件
GUI_BMP_DrawScaled()     绘制已加载到内存中的带缩放的BMP文件
GUI_BMP_DrawScaledEx()   绘制不加载到内存中的带缩放的BMP文件
GUI_BMP_EnableAlpha()    为32bpp V3 BMP文件启用alpha通道
GUI_BMP_GetXSize()       返回加载到内存中的BMP文件的X大小
GUI_BMP_GetXSizeEx()     返回不加载到内存中的BMP文件的X大小
GUI_BMP_GetYSize()       返回加载到内存中的BMP文件的Y大小
GUI_BMP_GetYSizeEx()     返回不加载到内存中的BMP文件的Y大小
GUI_BMP_Serialize()      创建一张BMP文件
GUI_BMP_SerializeEx()    从给定的矩形创建一个BMP文件
GUI_BMP_SerializeExBpp() 使用指定的颜色深度从给定的矩形创建BMP文件
======================== =========================================

GUI_BMP_Draw()
''''''''''''''

在当前窗口中的指定位置绘制一个已加载到内存中的bmp文件。

代码清单 31‑3 函数原型

1 int GUI_BMP_Draw(const void \*pFileData, int x0, int y0);

1) pFileData：指向bmp文件所在的内存区域起始地址的指针；

2) x0：位图左上角在屏幕上的x位置；

3) y0：位图左上角在屏幕上的y位置。

返回值：绘制成功返回0，绘制失败返回非0。

GUI_BMP_DrawEx()
''''''''''''''''

在当前窗口的指定位置绘制不需要加载到内存中的bmp文件。

代码清单 31‑4 函数原型

1 int GUI_BMP_DrawEx(GUI_GET_DATA_FUNC \*pfGetData, void \*p, int x0, int

2 y0);

1) pfGetData：指向用于获取数据的函数的指针；

2) p：传递给pfGetData指向的函数的空指针；

3) x0：位图左上角在屏幕上的x位置；

4) y0：位图左上角在屏幕上的y位置。

返回值：绘制成功返回0，绘制失败返回非0。

BMP图片显示实验
~~~~~~~~~

接下来我们通过一个实验来讲解如何简单的显示一张BMP图片，更多API函数的演示实验可参考官方例程2DGL_DrawBMP.c，例程路径如下：

SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial\2DGL_DrawBMP.c

代码分析
^^^^

(1) 绘制外部存储器（SD卡）中的BMP

代码清单 31‑5 ShowBMPEx函数（MainTask.c文件）

1 /*\*

2 \* @brief 直接从外部存储器中绘制BMP图片

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* x0：图片左上角在屏幕上的横坐标

6 \* y0：图片左上角在屏幕上的纵坐标

7 \* @retval 无

8 \*/

9 static void ShowBMPEx(const char \*sFilename, int x0, int y0)

10 {

11 /\* 进入临界段 \*/

12 taskENTER_CRITICAL();

13 /\* 打开图片 \*/

14 result = f_open(&file, sFilename, FA_READ);

15 if ((result != FR_OK)) {

16 printf("文件打开失败！\r\n");

17 }

18 /\* 退出临界段 \*/

19 taskEXIT_CRITICAL();

20

21 /\* 绘制图片 \*/

22 GUI_BMP_DrawEx(_GetData, &file, x0, y0);

23 }

由代码清单 31‑5所示，由于是从外部存储器绘制图片，在本实验中是从SD卡加载，所以必须通过文件系统函数f_open函数打开图片文件。图片打开成功后调用GUI_BMP_DrawEx函数绘制，但是这个函数还需要一个专门的数据读取函数才能绘制图片，见代码清单 31‑6。

代码清单 31‑6 \_GetData函数（MainTask.c文件）

1 /*\*

2 \* @brief 从外部存储器中读取数据

3 \* @note 无

4 \* @param

5 \* @retval NumBytesRead：读到的字节数

6 \*/

7 static char \_acBuffer[1024 \* 4];

8 int \_GetData(void \*p, const U8 \**ppData, unsigned NumBytesReq, U32 Off)

9 {

10 static int FileAddress = 0;

11 UINT NumBytesRead;

12 FIL \*Picfile;

13

14 Picfile = (FIL \*)p;

15

16 /\* 检查缓冲区大小 \*/

17 if (NumBytesReq > sizeof(_acBuffer)) {

18 NumBytesReq = sizeof(_acBuffer);

19 }

20

21 /*偏移量 \*/

22 if (Off == 1)

23 FileAddress = 0;

24 else

25 FileAddress = Off;

26 result = f_lseek(Picfile, FileAddress);

27

28 /\* 进入临界段 \*/

29 taskENTER_CRITICAL();

30 /\* 读取图片数据 \*/

31 result = f_read(Picfile, \_acBuffer, NumBytesReq, &NumBytesRead);

32 /\* 退出临界段 \*/

33 taskEXIT_CRITICAL();

34

35 \*ppData = (const U8 \*)_acBuffer;

36

37 /\* 返回以读到的字节数 \*/

38 return NumBytesRead;

39 }

如代码清单 31‑6所示，_GetData函数用于读取外部存储器中的图片数据，每调用一次就读取图片一整行的像素数据，请确保数据缓冲区_acBuffer[]的大小足够装下一整行像素数据。

\_GetData函数将作为GUI_BMP_DrawEx函数的其中一个参数使用，当emWin从外部存储器直接绘制图片时，这个读取函数必须要有。

(2) 绘制已加载到内存中的BMP

代码清单 31‑7 绘制已加载到内存中的图片（MainTask.c文件）

1 /*\*

2 \* @brief 加载BMP图片到内存中并绘制

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* x0：图片左上角在屏幕上的横坐标

6 \* y0：图片左上角在屏幕上的纵坐标

7 \* @retval 无

8 \*/

9 static void ShowBMP(const char \*sFilename, int x0, int y0)

10 {

11 WM_HMEM hMem;

12

13 /\* 进入临界段 \*/

14 taskENTER_CRITICAL();

15 /\* 打开图片 \*/

16 result = f_open(&file, sFilename, FA_READ);

17 if ((result != FR_OK)) {

18 printf("文件打开失败！\r\n");

19 \_acbuffer[0]='\0';

20 }

21

22 /\* 申请一块动态内存空间 \*/

23 hMem = GUI_ALLOC_AllocZero(file.fsize);

24 /\* 转换动态内存的句柄为指针 \*/

25 \_acbuffer = GUI_ALLOC_h2p(hMem);

26

27 /\* 读取图片数据到动态内存中 \*/

28 result = f_read(&file, \_acbuffer, file.fsize, &f_num);

29 if (result != FR_OK) {

30 printf("文件读取失败！\r\n");

31 }

32 /\* 读取完毕关闭文件 \*/

33 f_close(&file);

34 /\* 退出临界段 \*/

35 taskEXIT_CRITICAL();

36

37 GUI_BMP_Draw(_acbuffer, x0, y0);

38

39 /\* 释放内存 \*/

40 GUI_ALLOC_Free(hMem);

41 }

如代码清单 31‑7所示，绘制已加载到内存的BMP的操作与直接从外部存储器绘制的操作略有不同，并且没有了专门的数据读取函数。首先还是必须要用f_open打开BMP图片文件，然后不同的是用GUI_ALLOC_AllocZero函数申请一块动态内存，并且用GUI_ALLOC_h2p把这段动态内存的句柄转
为指针_acbuffer，方便之后使用，接着用f_read函数把图片数据读到刚刚申请到的动态内存中，读取完成后关闭文件，使用GUI_BMP_Draw函数将动态内存中的BMP数据绘制到LCD上，如果之后的程序不再使用这张BMP，就必须使用GUI_ALLOC_Free函数释放动态内存。

(3) 使用内存设备绘制BMP

GUI_BMP_Draw函数是直接从动态内存中读取数据，所以速度相比从外部存储器绘制要快不少。但这还不是最快的，利用emWin的内存设备还可以将刷图速度继续提升到几十个ms的量级，见代码清单 31‑8。

代码清单 31‑8 LoadBMP_UsingMEMDEV 函数(MainTask.c文件)

1 /*\*

2 \* @brief 加载BMP图片数据到内存设备

3 \* @note 无

4 \* @param sFilename：需要加载的图片名

5 \* ScaledMode：是否启用缩放，0不启用，1启用

6 \* Num：缩放系数的分子

7 \* Denom：缩放系数的分母

8 \* @retval 无

9 \*/

10 WM_HMEM hMem;

11 GUI_MEMDEV_Handle hBMP;

12 static void LoadBMP_UsingMEMDEV(const char \*sFilename, int x0, int y0)

13 {

14 /\* 进入临界段 \*/

15 taskENTER_CRITICAL();

16 /\* 打开图片 \*/

17 result = f_open(&file, sFilename, FA_OPEN_EXISTING \| FA_READ);

18 if ((result != FR_OK)) {

19 printf("文件打开失败！\r\n");

20 \_acbuffer[0] = '\0';

21 }

22

23 /\* 申请一块动态内存空间 \*/

24 hMem = GUI_ALLOC_AllocZero(file.fsize);

25 /\* 转换动态内存的句柄为指针 \*/

26 \_acbuffer = GUI_ALLOC_h2p(hMem);

27

28 /\* 读取图片数据到动态内存中 \*/

29 result = f_read(&file, \_acbuffer, file.fsize, &f_num);

30 if (result != FR_OK) {

31 printf("文件读取失败！\r\n");

32 }

33 /\* 读取完毕关闭文件 \*/

34 f_close(&file);

35 /\* 退出临界段 \*/

36 taskEXIT_CRITICAL();

37 /\* 创建内存设备 \*/

38 hBMP = GUI_MEMDEV_CreateEx(x0, y0, /\* 起始坐标 \*/

39 GUI_BMP_GetXSize(_acbuffer), /\* x方向尺寸 \*/

40 GUI_BMP_GetYSize(_acbuffer), /\* y方向尺寸 \*/

41 GUI_MEMDEV_HASTRANS); /\* 带透明度的内存设备 \*/

42 /\* 选择内存设备 \*/

43 GUI_MEMDEV_Select(hBMP);

44 /\* 绘制BMP到内存设备中 \*/

45 GUI_BMP_Draw(_acbuffer, x0, y0);

46 /\* 选择内存设备，0表示选中LCD \*/

47 GUI_MEMDEV_Select(0);

48 }

如代码清单 31‑8所示，把BMP图片数据加载到内存设备中的步骤其实和代码清单
31‑7差不多的，都是先用f_open函数打开BMP文件，然后用GUI_ALLOC_AllocZero函数申请一块动态内存，并且用GUI_ALLOC_h2p把这段动态内存的句柄转为指针_acbuffer，方便之后使用，接着用f_read函数把图片数据读到刚刚申请到的动态内存中，读取完成后关闭文件。

不同的操作是，代码清单
31‑8使用GUI_MEMDEV_CreateEx函数创建一个带透明度的内存设备，然后GUI_MEMDEV_Select函数激活内存设备，再调用GUI_BMP_Draw函数把图片数据绘制到内存设备，绘制完成后重复调用一次GUI_MEMDEV_Select函数选择LCD，方便之后的操作。

目前代码清单 31‑8完成的工作只是把BMP数据搬到内存设备中，真正的显示需要调用专门的函数完成，见代码清单 31‑9。

(4) MainTask函数

代码清单 31‑9 MainTask函数（MainTask.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 int i = 0;

10

11 GUI_SetFont(GUI_FONT_20B_ASCII);

12 GUI_SetTextMode(GUI_TM_XOR);

13

14 /\* 加载BMP图片数据到内存设备 \*/

15 LoadBMP_UsingMEMDEV("0:/image/ngc7635.bmp",

16 (LCD_GetXSize()-768)/2,

17 (LCD_GetYSize()-432)/2);

18

19 while (1) {

20 i++;

21 switch (i) {

22 case 1:

23 t0 = GUI_GetTime();

24 /\* 直接从外部存储器绘制BMP图片 \*/

25 ShowBMPEx("0:/image/ngc7635.bmp",

26 (LCD_GetXSize()-768)/2,

27 (LCD_GetYSize()-432)/2);

28 t1 = GUI_GetTime();

29 GUI_DispStringAt("GUI_BMP_DrawEx()", 0, 0);

30 printf("\r\n直接从外部存储器绘制BMP：%dms\r\n",t1 - t0);

31 break;

32 case 2:

33 t0 = GUI_GetTime();

34 /\* 加载BMP图片到内存中并绘制 \*/

35 ShowBMP("0:/image/ngc7635.bmp",

36 (LCD_GetXSize()-768)/2,

37 (LCD_GetYSize()-432)/2);

38 t1 = GUI_GetTime();

39 GUI_DispStringAt("GUI_BMP_Draw()", 0, 0);

40 printf("加载BMP到内存中并绘制：%dms\r\n",t1 - t0);

41 break;

42 case 3:

43 /\* 显示内存设备中的BMP图片 \*/

44 t0 = GUI_GetTime();

45 /\* 从内存设备写入LCD \*/

46 GUI_MEMDEV_CopyToLCDAt(hBMP,

47 (LCD_GetXSize()-768)/2,

48 (LCD_GetYSize()-432)/2);

49 t1 = GUI_GetTime();

50 GUI_DispStringAt("USE MEMDEV", 0, 0);

51 printf("使用内存设备显示BMP：%dms\r\n",t1 - t0);

52 break;

53 default:

54 i = 0;

55 break;

56 }

57 GUI_Delay(2000);

58 GUI_Clear();

59 }

60 }

本实验我们用三种BMP绘制方法绘制同一张图片，看看每种方法各自需要多少时间。如代码清单
31‑9所示，首先在一开始先加载BMP数据到内存设备中以备之后使用，然后在while循环中调用ShowBMPEx、ShowBMPEx和GUI_MEMDEV_CopyToLCDAt，分别记录各自花费的时间并通过串口1打印出来。

GUI_MEMDEV_CopyToLCDAt函数是内存设备专用函数，作用是将内存设备中的内容搬到LCD中，使用前请一定确保没有激活任何内存设备，否则函数无法正常使用。此函数绘制BMP图片速度很快，其他任何地方需要显示BMP图片都可以调用此函数，只要内存设备没被删掉。

讲到这里可能有人已经看出来了，本实验中的内存设备实际是作为加速高质量BMP图片显示速度的一种手段而存在的，本质上emwin还是只有两种BMP图片显示方式。代码清单
31‑8中使用内存设备替代了GUI_BMP_Draw函数的显示过程，其实GUI_BMP_DrawEx函数的显示过程同样可以用内存设备来加速。

使用内存设备的方式可以说是以较多的SDRAM空间占用换取图片超快的显示速度，此方式在野火H743 Pro和H750 Pro等使用32位宽SDRAM的开发板上，显示速度还可以进一步提高。

实验现象
^^^^

BMP图片显示实验的实验现象如图 31‑4和图 31‑5所示，可以在LCD上看到GUI_BMP_DrawEx函数的BMP有很明显的从下到上的显示过程，GUI_BMP_Draw函数稍微好些，但也还是有比较显眼的显示过程，而使用内存设备的BMP几乎看不到显示过程，闪一下就出来了。

在串口助手上也可以看到三种方式所消耗的时间，内存设备方式刷图消耗的时间相比于前两种有非常显著的差距。

|BMP005|

图 31‑4 BMP图片显示实验现象

|BMP006|

图 31‑5 串口助手打印的信息

.. |BMP002| image:: media\BMP002.png
   :width: 5.76806in
   :height: 1.10554in
.. |BMP003| image:: media\BMP003.png
   :width: 5.76806in
   :height: 1.10554in
.. |BMP004| image:: media\BMP004.png
   :width: 5.76806in
   :height: 4.46624in
.. |BMP005| image:: media\BMP005.png
   :width: 5.76806in
   :height: 3.46228in
.. |BMP006| image:: media\BMP006.png
   :width: 5.76806in
   :height: 4.58641in
