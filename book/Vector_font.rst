.. vim: syntax=rst

矢量字体显示
=================

矢量字体(Vector font)中每一个字形是通过数学曲线来描述的，它包含了字形边界上的关键点，连线的导数信息等，字体的渲染引擎通过读取这些数学矢量，然后进行一定的数学运算来进行渲染。这类字体的优点是字体实际尺寸可以任意缩放而不变形、变色。目前较为流行的矢量字体主要包括 Type1 、
TrueType、OpenType等几类。

Type 1(也称为PostScript Type 1或Adobe Type 1)是Adobe Systems为专业数字排版开发的轮廓字体规范编码的字体文件，使用三次贝塞尔曲线来描述字形，1985年刚推出时被用在激光打印机上。

TrueType是苹果公司在20世纪80年代末开发的一种轮廓字体标准，是Type
1字体的竞争对手。这种字体的轮廓采用直线段和二次贝塞尔曲线描述字形。与三次贝塞尔曲线相比，这些曲线在数学上更简单，处理速度也更快，但是对于复杂形状而言二次曲线比三次曲线需要更多的点来描述。目前已经成为macOS和Microsoft Windows操作系统中最常见的字体格式。

OpenType由微软和Adobe共同开发，在TrueType的基础上发展而来，保留了TrueType的基本结构，并添加了许多复杂的数据结构来规定排版行为。由于这种字体具有广泛的可用性和排版灵活性，包括处理世界上所有书写系统的各种行为的规定，所以在主要的计算机平台上通常使用OpenType字体。

矢量字体原理简介
~~~~~~~~

之前几个章节所讲的字体都属于点阵字体，这种字体并不是以矢量描述的，放大以后会出现锯齿。而矢量字体由于采用数学语言进行描述，在放大若干倍后依旧很清晰。图 39‑1展示的是把点阵字体和矢量字体放大到相同大小时的效果。

|Vector002|

图 39‑1 点阵字体与矢量字体

我们以“火”字为例，给大家简单粗略地介绍一下矢量字体的显示原理。

|Vector003|

图 39‑2 “火”字

一个字可以使用多条线段来表示，对于这些线段，我们只需要保存它的端点值，如图 39‑2的白色空心圆所示，当我们想要显示某个字时，就取出这些关键点，通过贝塞尔曲线将这些点的坐标连接起来，最后进行填充。放大或缩小字体的时候，只需要按比例缩放改变这些端点值的相对位置就可以了。

在矢量字体文件中，每个字符都是以一系列的点存放的，其中有一些点是控制点，利用这些控制点来实现绘制抛物线的功能，如图 39‑3所示，p1是控制点，c1、c2是曲线的端点，因此曲线可以用下面的公式进行描述，这描述方式就是贝塞尔曲线。

:math:`p\left( t \right) = \ \left( 1 - t \right)^{2}p_{0}\  + \ 2t\left( 1 - t \right)p_{1}\  + \ t^{2}p_{2}\ ` （0 < t < 1 ）

|Vector004|

图 39‑3 点绘制线

回过头，看图 39‑4，方框处是不是和上面说的原理是一样的，空心的点是线段的端点，实心的点是控制点，利用贝塞尔曲线，描绘出字体的轮廓，最后在进行黑色填充，就完成了。

|Vector005|

图 39‑4 “火”

移植TTF渲染引擎
~~~~~~~~~

emWin支持3种主流矢量字体格式中的TrueType格式。和SIF格式一样，使用TTF格式字库的时候需要将字库全部加载到RAM中，然后使用TTF字体渲染引擎显示字体。emWin可以使用一款叫FreeType的TTF字体渲染引擎，此引擎占用的RAM空间在很大程度上取决于所用的字体，但最少会占用80~
300KB，其中200KB用于字体的光栅化位图缓存。

虽然emWin支持FreeType，但库本身并不包含这款字体渲染引擎，需要用户自行移植。接下来我们讲解一下如何将TTF字体渲染引擎移植到emWin工程中。

1) 下载并解压TTF引擎。FreeType字体渲染引擎可以从emWin官网下载到，链接如下：\ https://www.segger.com/downloads/emwin/emWin_FreeType\ 。下载完成后解压emWin_FreeType压缩包，可以看到如图
39‑5所示的结构，压缩包内包含了对应emWin各个版本的TTF引擎，我们选取适合我们例程的版本即选择V544；

|Vector006|

图 39‑5 emwin_freetype内容

2) 添加TTF引擎到工程。选择好版本之后，将PNG库整个文件夹复制到工程文件夹下，其实随便那个文件夹都行，只要在工程文件夹内。如所示，我们选择将PNG库放在工程的STemWin文件夹下。然后在MDK工程内新建分组并把PNG库添加进新分组中，如图 39‑6；

|Vector007|

图 39‑6 添加引擎到工程文件夹

3) 添加PNG库头文件路径。在工程设置中把TTF引擎的头文件路径添加进去，见图 39‑7。

|Vector008|

图 39‑7 添加头文件路径

4) 修改启动文件。由于TTF引擎需要用到malloc、free和relloc等C库函数，而这些函数又使用的是系统堆内存空间，所以需要增加堆内存空间大小。见图 39‑8和图 39‑9。

|Vector009|

图 39‑8 修改堆空间大小

|Vector010|

图 39‑9 在main函数前初始化SDRAM

至此，emWin的TTF字体渲染引擎就移植完成了，可能最后工程编译完成后有较多警告，属于正常情况。

TTF字体显示相关API
~~~~~~~~~~~~

表格 39‑1 TTF字体显示相关API

======================= ==================================
函数名                  描述
======================= ==================================
GUI_TTF_CreateFont()    从TTF字体文件创建GUI字体
GUI_TTF_CreateFontAA()  使用抗锯齿从TTF字体文件创建GUI字体
GUI_TTF_DestroyCache()  销毁TTF引擎的缓存
GUI_TTF_Done()          释放TTF引擎的所有动态分配的内存
GUI_TTF_GetFamilyName() 返回字体的家族名称
GUI_TTF_GetStyleName()  返回字体的样式名称
GUI_TTF_SetCacheSize()  可用于设置TTF缓存的默认大小
======================= ==================================

GUI_TTF_CreateFont()
''''''''''''''''''''

使用TTF字体文件创建和选择emWin字体。

代码清单 39‑1 函数原型

1 int GUI_TTF_CreateFont(GUI_FONT \*pFont, GUI_TTF_CS \*pCS);

1) pFont：指向RAM中由该函数填充的GUI_FONT结构的指针；

2) pCS：指向包含创建参数的GUI_TTF_CS结构的指针，GUI_TTF_CS结构原型见代码清单 39‑2。

返回值：字体创建成功时返回0，创建失败返回1。

代码清单 39‑2 GUI_TTF_CS原型

1 typedef struct {

2 GUI_TTF_DATA \* pTTF;

3 U32 aImageTypeBuffer[4];

4 int PixelHeight;

5 int FaceIndex;

6 } GUI_TTF_CS;

1) pTTF：指向GUI_TTF_DATA结构的指针，该结构包含要使用的字体文件的位置和大小，GUI_TTF_DATA结构原型见代码清单 39‑3；

2) aImageTypeBuffer：用于图像类型结构的缓冲区；

3) PixelHeight：新字体的像素高度。它表示字体的边界高度，不是两行文本之间的距离。换句话说，GUI_GetFontSizeY()返回的值与这个值不相同；

4) FaceIndex：一些字体文件可以包含多个字体面。如果有多个面，此索引指定用于创建字体的基于0的面索引，默认为0。

代码清单 39‑3 GUI_TTF_DATA原型

1 typedef struct {

2 const void \* pData;

3 U32 NumBytes;

4 } GUI_TTF_DATA;

1) pData：指向可寻址内存区域中的TTF字体文件的指针；

2) NumBytes：文件大小，以字节为单位。

GUI_TTF_CreateFontAA()
''''''''''''''''''''''

使用TTF字体文件创建和选择带抗锯齿的emWin字体。

代码清单 39‑4 函数原型

1 int GUI_TTF_CreateFontAA(GUI_FONT \*pFont, GUI_TTF_CS \*pCS);

1) pFont：指向RAM中由该函数填充的GUI_FONT结构的指针；

2) pCS：指向包含创建参数的GUI_TTF_CS结构的指针，GUI_TTF_CS结构原型见代码清单 39‑2。

返回值：字体创建成功时返回0，创建失败返回1。

TTF格式字体显示实验
~~~~~~~~~~~

本次实验我们选择安卓系统自带的中文矢量字库Droid Sans来显示如何使用TTF格式字体，字库文件放在SD卡的Font文件夹中。

代码分析
^^^^

(1) 创建字体

首先，在创建TTF字体前需要加载TTF文件和配置一些字体参数，见代码清单 39‑5。

代码清单 39‑5 FONT_TTF_GetData函数（GUIFont_Create.c文件）

1 /第1部分/

2 /\* 存储器初始化标志 \*/

3 static uint8_t Storage_Init_Flag = 0;

4

5 /\* 字库属性结构体 \*/

6 GUI_TTF_CS cs0, cs1, cs2, cs3, cs4;

7

8 /\* 字库数据结构体 \*/

9 GUI_TTF_DATA ttf_data;

10

11 /\* 定义emwin字体 \*/

12 GUI_FONT FONT_TTF_24;

13 GUI_FONT FONT_TTF_48;

14 GUI_FONT FONT_TTF_72;

15 GUI_FONT FONT_TTF_96;

16 GUI_FONT FONT_TTF_120;

17

18 /\* 字库数据缓冲区 \*/

19 char \*TTFfont_buffer;

20 GUI_HMEM hFontMem;

21

22 /\* 字库存储路径 \*/

23 static const char FONT_STORAGE_ROOT_DIR[] = "0:";

24 static const char FONT_TTF_ADDR[] = "0:/Font/DroidSansFallbackFull.

25 ttf";

26

27 /第2部分/

28 /*\*

29 \* @brief 获取字体数据

30 \* @note 无

31 \* @param res_name：要读取的文件名

32 \* @retval 无

33 \*/

34 static void FONT_TTF_GetData(const char \*res_name)

35 {

36 if (Storage_Init_Flag == 0) {

37 /\* 挂载sd卡文件系统 \*/

38 res = f_mount(&fs,FONT_STORAGE_ROOT_DIR,1);

39 Storage_Init_Flag = 1;

40 }

41

42 /\* 打开文件 \*/

43 taskENTER_CRITICAL();

44 res = f_open(&fnew , res_name, FA_OPEN_EXISTING \| FA_READ);

45 taskEXIT_CRITICAL();

46 if (res != FR_OK) {

47 printf("无法找到字库文件\r\n");

48 while (1);

49 }

50

51 /\* 申请一块动态内存空间 \*/

52 hFontMem = GUI_ALLOC_AllocZero(fnew.fsize);

53 /\* 转换动态内存的句柄为指针 \*/

54 TTFfont_buffer = GUI_ALLOC_h2p(hFontMem);

55

56 /\* 读取内容 \*/

57 taskENTER_CRITICAL();

58 res = f_read( &fnew, TTFfont_buffer, fnew.fsize, &br );

59 taskEXIT_CRITICAL();

60 if (res != FR_OK) {

61 printf("无法读取字库文件\r\n");

62 while (1);

63 }

64 f_close(&fnew);

65

66 /\* 链接TTF数据 \*/

67 ttf_data.pData = TTFfont_buffer;

68 ttf_data.NumBytes = fnew.fsize;

69

70 /\* 配置字体参数 \*/

71 cs0.pTTF = &ttf_data;

72 cs0.PixelHeight = 24;

73 cs0.FaceIndex = 0;

74

75 cs1.pTTF = &ttf_data;

76 cs1.PixelHeight = 48;

77 cs1.FaceIndex = 0;

78

79 cs2.pTTF = &ttf_data;

80 cs2.PixelHeight = 72;

81 cs2.FaceIndex = 0;

82

83 cs3.pTTF = &ttf_data;

84 cs3.PixelHeight = 96;

85 cs3.FaceIndex = 0;

86

87 cs4.pTTF = &ttf_data;

88 cs4.PixelHeight = 120;

89 cs4.FaceIndex = 0;

90 }

如代码清单 39‑5第1部分所示，定义了5个GUI_TTF_CS类型的结构体cs0~cs4，用来设计字体参数，1个GUI_TTF_DATA类型结构体ttf_data用来链接TTF文件数据，5个GUI_FONT类型结构体FONT_TTF_24~FONT_TTF_120提供给emWin使用。

第二部分是FONT_TTF_GetData函数，函数内部首先挂载SD卡文件系统，然后f_read函数读取TTF文件信息，获取尺寸参数，然后利用emWin内部的函数分配一块动态内存并转为指针，接着再使用f_read函数把TTF文件读取到由TTFfont_buffer指向的SDRAM空间中。读取完成后使
用ttf_data结构体链接TTF文件数据，cs0~cs4共5个属性结构体分别设置5种字体大小。

代码清单 39‑6 Create_TTF_Font函数（GUIFont_Create.c文件）

1 /*\*

2 \* @brief 创建TTF字体

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void Create_TTF_Font(void)

8 {

9 /\* 获取字体数据 \*/

10 FONT_TTF_GetData(FONT_TTF_ADDR);

11

12 /\* 创建TTF字体 \*/

13 GUI_TTF_CreateFontAA(&FONT_TTF_24,

14 &cs0);

15 GUI_TTF_CreateFontAA(&FONT_TTF_48,

16 &cs1);

17 GUI_TTF_CreateFontAA(&FONT_TTF_72,

18 &cs2);

19 GUI_TTF_CreateFontAA(&FONT_TTF_96,

20 &cs3);

21 GUI_TTF_CreateFontAA(&FONT_TTF_120,

22 &cs4);

23 }

如代码清单 39‑6所示，获取到TTF字体数据后，使用GUI_TTF_CreateFontAA函数依次创建5种不同大小的带抗锯齿的字体，由于TTF字体可能会放得比较大，在较低分辨率的显示屏上看起来稍微有些不平滑，加了抗锯齿之后显示效果会好很多。

(2) 创建并转换待显示字符

是的没错，TTF字体显示实验同样受到keil5文本编辑器BUG的影响，同样需要多一个单独转字符编码的步骤，具体如何转换字符编码请查看37.4.1 小节，在此不再重复说明。

(3) MainTask函数

代码清单 39‑7 MainTask函数（MainTask.c文件）

1 /*\*

2 \* @brief GUI主任务

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 void MainTask(void)

8 {

9 /\* 启用UTF-8编码 \*/

10 GUI_UC_SetEncodeUTF8();

11 /\* 创建字体 \*/

12 Create_TTF_Font();

13

14 /\* 设置背景颜色 \*/

15 GUI_SetBkColor(GUI_WHITE);

16 GUI_Clear();

17

18 /\* 设置字体模式和颜色 \*/

19 GUI_SetTextMode(GUI_TM_TRANS);

20 GUI_SetColor(GUI_BLACK);

21

22 /\* 显示中文 \*/

23 GUI_SetFont(&FONT_TTF_24);

24 GUI_DispString(text);

25 GUI_DispNextLine();

26 GUI_SetFont(&FONT_TTF_48);

27 GUI_DispString(text);

28 GUI_DispNextLine();

29 GUI_SetFont(&FONT_TTF_72);

30 GUI_DispString(text);

31 GUI_DispNextLine();

32 GUI_SetFont(&FONT_TTF_96);

33 GUI_DispString(text);

34 GUI_DispNextLine();

35 GUI_SetFont(&FONT_TTF_120);

36 GUI_DispString(text);

37

38 while (1) {

39 GUI_Delay(1000);

40 }

41 }

最后字体创建完成之后如果少了一步操作，那么整个TTF字体还是不能用。如代码清单 39‑7的GUI主任务函数MainTask所示，在创建我们需要的中文字体之前必须首先使用GUI_UC_SetEncodeUTF8函数开启emwin的UTF-8编码，这样才能保证中文字符的正常显示。

实验现象
^^^^

TTF格式字体显示实验的实验现象如图 39‑10所示，可以看到字体在被放大后依然很平滑没有锯齿。

|Vector011|

图 39‑10 TTF格式字体显示实验现象

其实emWin使用TTF矢量字体的限制还是蛮多的，并不是所有矢量字体文件都可以用。首先是只支持.ttf格式的矢量字库，.otf和其他一些格式均不支持；其次是字库编码，emWin只支持Unicode编码的矢量字库，而像方正这种国内做得比较好的中文字库基本上都是GB2312编码，要不就是GBK编码，无法
在emWin上使用；最后也是最容易被忽视的就是版权问题，举个例子，Windows系统中所有的方正字体包括微软雅黑这种“万能字体”，都只能是在Windows系统内使用，如果在电子产品中使用这些字体是需要向方正申请授权的，是的你没看错，微软雅黑的版权方是方正。不仅是方正系列字体，Windows系统自带的
其他字体也都只是对Windows进行了授权，其中有一些仍在版权保护期内，使用这些字体做产品或其他任何商业用途时都应考虑是否会有潜在的法律风险。

当然现在也有很多免费免授权甚至开源的字体，例如站酷系列、阿里巴巴普惠体和思源系列等等，但是这些字体又多多少少存在一些无法使用的情况：站酷全系GBK编码；阿里巴巴普惠体不知道是字库本身不全还是我用法不对，反正有很多常用字显示不出来；思源系列体积太大且没有.ttf格式。选择适合emWin的TTF字库需要
考虑以上种种因素，此时emWin的 TTF字体渲染引擎说到：我太难了。

.. |Vector002| image:: media\Vector002.png
   :width: 5.01181in
   :height: 2.47638in
.. |Vector003| image:: media\Vector003.jpg
   :width: 3.48019in
   :height: 3.46499in
.. |Vector004| image:: media\Vector004.jpg
   :width: 2.675in
   :height: 2.75833in
.. |Vector005| image:: media\Vector005.jpeg
   :width: 2.71184in
   :height: 2.7in
.. |Vector006| image:: media\Vector006.png
   :width: 5.76806in
   :height: 2.62433in
.. |Vector007| image:: media\Vector007.png
   :width: 5.76806in
   :height: 4.22924in
.. |Vector008| image:: media\Vector008.png
   :width: 4.70472in
   :height: 3.20472in
.. |Vector009| image:: media\Vector009.png
   :width: 5.76806in
   :height: 0.94933in
.. |Vector010| image:: media\Vector010.png
   :width: 4.82283in
   :height: 1.83858in
.. |Vector011| image:: media\Vector011.png
   :width: 5.76806in
   :height: 3.46083in
