.. vim: syntax=rst

显示位图
=========

emWin支持三种位图文件形式：.c文件位图、.dta格式流位图和.bmp格式位图。
本章主要讲解如何显示C文件位图和流位图以及位图转换器的使用，bmp格式位图的显示将会在专门的章节进行讲解。

.c文件形式的位图必须跟随工程文件一起放置在MCU的内部FLASH中，这就导致c文件位图的大小、质量和数量等受到内部FLASH容量的限制。
相反流位图可放置在任何存储器中，并可按照与 C 文件位图相同的方式使用。

位图转换器
~~~~~~~~~~~~~

位图转换器SEGGER公司为emWin开发的一种主要用于将PC格式的图片转换为C文件格式或C流位图格式的工具，支持BMP、GIF、PNG和JPEG格式的转换。
某些常用的图片格式数据量可能相当大，手动生成C文件可能会很费时间，而位图转换器则会自动从PC格式生成C文件。位图转换器还具有颜色格式转换功能，
这项功能可以通过减少单个像素所占的位数，有效减少C文件对内存的消耗。软件界面如图 软件界面_ 所示。

.. image:: media/display_bitmap/displa002.png
   :align: center
   :name: 软件界面
   :alt: 软件界面


位图转换器可以从SEGGER官网的emWin页面下载，也可以在STM32的CUBE库中找到。转换器在CUBE库中的路径如下：

**STM32Cube_FW_F4_V1.24.1\\Middlewares\\ST\\STemWin\\Software\\BmpCvtST.exe**

生成图片数组
~~~~~~~~~~~~~~~~~~

那么，如何使用位图转换器生成C数组呢？步骤如下：

1) 配置颜色保存模式。由于我们用到了emWin的ARGB颜色模式，所以在首次使用位图转换器时，需要将转换器配置为“在ARGB模式下保存颜色”。
点击Options弹出选项对话框，勾选“Save colors in ARGB mode”，如图 配置颜色保存模式_ 所示。

.. image:: media/display_bitmap/displa003.png
   :align: center
   :name: 配置颜色保存模式
   :alt: 配置颜色保存模式


2) 添加图像文件。点击File->Open添加文件，
也可直接将图片拖到转换器空白处添加，见图 添加文件_ 。

.. image:: media/display_bitmap/displa004.png
   :align: center
   :name: 添加文件
   :alt: 添加文件


3) 保存C文件。点击File->Save As，在弹出来的保存对话框中，选择需要保存的路径，这里我们保存到image文件夹。
修改文件名为ngc7293.c，然后选择保存类型为.C文件。见图 保存C文件_ 。

.. image:: media/display_bitmap/displa005.png
   :align: center
   :name: 保存C文件
   :alt: 保存C文件


4) 选择C文件的颜色格式。在保存对话框中点击确定后，会弹出一个选择颜色格式的窗口，
这里我们选择True Color with alpha，见图 选择C文件颜色格式_ 。

.. image:: media/display_bitmap/displa006.png
   :align: center
   :name: 选择C文件颜色格式
   :alt: 选择C文件颜色格式


通过上述步骤，即可成功创建BMP图像文件的C数组。如果需要生成.dta格式的流位图，
在保存文件时选择保存类型为.dta文件即可。

GUI_BITMAP结构体
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

emWin使用GUI_BITMAP结构体来存放位图的相关信息，见 代码清单:显示位图-1_ 。

.. code-block:: c
    :caption: 代码清单:显示位图-1 GUI_BITMAP结构体（文件GUI_Type.h）
    :name: 代码清单:显示位图-1
    :linenos:

    typedef struct {
        U16P XSize;                         //位图宽度
        U16P YSize;                         //位图高度
        U16P BytesPerLine;                  //每行字节数
        U16P BitsPerPixel;                  //每像素位数
        const U8 * pData;                   //指向图片数据的指针
        const GUI_LOGPALETTE * pPal;        //指向图片调色板的指针
        const GUI_BITMAP_METHODS * pMethods;//绘图颜色模式
    } GUI_BITMAP;


1) XSize：
位图的宽度；

2) YSize：
位图的高度；

3) BytesPerLine：
位图图像每一行的字节数，该参数与位图的宽度和颜色格式有关，假设位图使用的颜色格式是ARGB8888，乘上图片的宽度，就是图像每一行的字节数；

4) BitsPerPixel：
位图图像单个像素所占的位数，该参数和上一个参数都与位图的宽度和颜色格式有关，假设位图使用的颜色格式是ARGB8888，那么图像单个像素数据就占32位；

5) pData：
指向位图像素数据；

6) pPal：
调色板，该参数在24位色及以上的位图中是没有的，只有低于24位色的位图才有调色板；

7) pMethods：
emWin的绘图调色板，在位图转换时生成。

绘图API
~~~~~~~~~~~~~

表格 BMP图片绘制API_ 列出了BMP图片显示的API函数。由于流位图的API函数较多，
这里只列出一部分，全部API函数的说明请参考《STemWin5.44参考手册》。

.. image:: media/display_bitmap/displa009.png
   :align: center
   :name: BMP图片绘制API
   :alt: BMP图片绘制API

GUI_DrawBitmap()
--------------------

在当前窗口中的指定位置绘制位图图像。

.. code-block:: c
    :caption: 代码清单:显示位图-2 函数原型
    :name: 代码清单:显示位图-2
    :linenos:

    void GUI_DrawBitmap(const GUI_BITMAP * pBM, int x, int y);

1) pBM：
指向要显示的位图的指针；

2) x：
位图在显示屏中左上角的 X轴坐标；

3) y：
位图在显示屏中左上角的Y轴坐标。

GUI_DrawBitmapEx()
-------------------------

通过此例程可以缩放和/或镜像显示屏上的位图。

.. code-block:: c
    :caption: 代码清单:显示位图-3 函数原型
    :name: 代码清单:显示位图-3
    :linenos:

    void GUI_DrawBitmapEx(const GUI_BITMAP * pBitmap,
                        int x0, int y0,
                        int xCenter, int yCenter,
                        int xMag, int yMag);


1) pBM：
指向要显示的位图的指针；

2) x0：
显示屏中定位点的X坐标；

3) y0：
显示屏中定位点的Y坐标；

4) xCenter：
位图中定位点的X坐标；

5) yCentert：
位图中定位点的Y坐标；

6) xMag：
X方向的缩放比例因子，单位为1/1000，当值为负值时将在X轴镜像位图；

7) yMag：
Y方向的缩放比例因子，单位为1/1000，当值为负值时将在Y轴镜像位图。

GUI_CreateBitmapFromStream()
---------------------------------------

该函数通过传递任何类型的位图流来创建位图结构体。

.. code-block:: c
    :caption: 代码清单:显示位图-4 函数原型
    :name: 代码清单:显示位图-4
    :linenos:

    int GUI_CreateBitmapFromStream(GUI_BITMAP * pBMP,
                                GUI_LOGPALETTE * pPAL,
                                const void * p);


1) pBMP：
指向要由函数初始化的GUI_BITMAP结构体的指针；

2) pPAL：
指向要由函数初始化的GUI_LOGPALETTE结构体的指针；

3) p：
指向数据流的指针。

GUI_DrawStreamedBitmapAuto()
-----------------------------------

从任何支持格式的位图数据流中绘制位图。

.. code-block:: c
    :caption: 代码清单:显示位图-5 函数原型
    :name: 代码清单:显示位图-5
    :linenos:

    void GUI_DrawStreamedBitmapAuto(const void * p, int x, int y);

1) p：
指向数据流的指针；

2) x：
显示屏中位图左上角的X坐标；

3) y：
显示屏中位图左上角的Y坐标。

显示C文件位图实验
~~~~~~~~~~~~~~~~~~~~~~~~~

下面介绍emWin从内部FLASH中读取位图数据并显示的实验。

设计要求
-----------

调用GUI_DrawBitmap()函数，使ngc7293.c的图像铺满整个屏幕。

代码分析
-----------

(1) 生成C数组

.. code-block:: c
    :caption: 代码清单:显示位图-6 bmngc7293图片数据结构体（ngc7293.c）
    :name: 代码清单:显示位图-6
    :linenos:

    GUI_CONST_STORAGE GUI_BITMAP bmngc7293 = {
        307, // xSize
        230, // ySize
        1228, // BytesPerLine
        32, // BitsPerPixel
        (unsigned char *)_acngc7293,  // Pointer to picture data
        NULL,  // Pointer to palette
        GUI_DRAW_BMP8888
    };


根据前面讲解的步骤，用位图转换器生成了包含图片数据的C数组，
同时生成了 代码清单:显示位图-6_ bmngc7293图片数据结构体（ngc7293.c）中的图片数据结构体。
从结构体中可以看出这是32位的位图，因此，使用的emWin绘图调色板为GUI_DRAW_BMP8888，宽和高为307和203，
每行的字节数为宽度*4。32位色的位图不带调色板。

(2) 显示函数

.. code-block:: c
    :caption: 代码清单:显示位图-7 ShowBitmap函数（MainTask.c）
    :name: 代码清单:显示位图-7
    :linenos:

    extern GUI_CONST_STORAGE GUI_BITMAP bmngc7293;

    /**
    * @brief 从内部存储器中读取并绘制BMP图片数据
    * @note 无
    * @param 无
    * @retval 无
    */
    static void ShowBitmap(void)
    {
        /* 显示C文件位图 */
        for (int y = 0; y < 480; y += bmngc7293.YSize) {
            for (int x = 0; x < 800; x += bmngc7293.XSize) {
                /* 绘制图片 */
                GUI_DrawBitmap(&bmngc7293, x, y);
            }
        }
    }


在ShowBitmap函数中，变量x和y用来控制每次显示图片的坐标偏移量。利用GUI_DrawBitmap函数即可绘制图片。

最后，将ShowBitmap函数加入到MainTask函数中即可。

实验现象
-------------

实验结果如图 实验现象_ 所示，和设计要求完全一致。

.. image:: media/display_bitmap/displa007.png
   :align: center
   :name: 实验现象
   :alt: 实验现象


显示流位图实验
~~~~~~~~~~~~~~~~~~~

上一个实验中用到的图片C文件大小有275KB，对于STM32F429的内部FALSH来说已经非常大了，况且实际的工程应用中并不会只有一张图片。
对于这样的问题，我们可以将图片转换为.dta格式的流位图，然后存放在外部存储器中。实际上JPEG和PNG格式的图片也可以转成流位图显示，
这样芯片端不需要做解码，因为已经是原始图片数据了。


设计要求
-------------

SD卡内有一个用位图转换器生成的流位图文件，使用相关API函数，将它显示在屏幕上。


代码分析
---------------

.. code-block:: c
    :caption: 代码清单:显示位图-8 ShowStreamedBitmap函数（MainTask.c）
    :name: 代码清单:显示位图-8
    :linenos:

    /**
    * @brief 从外部存储器中读取并绘制BMP图片数据
    * @note 无
    * @param sFilename：要读取的文件名
    *        x：要显示的x轴坐标
    *        y：要显示的y轴坐标
    * @retval 无
    */
    static void ShowStreamedBitmap(const char *sFilename, int x, int y)
    {
        WM_HMEM hMem;
        char *_acbuffer = NULL;

        /* 进入临界段 */
        taskENTER_CRITICAL();
        /* 打开图片 */
        result = f_open(&file, sFilename, FA_READ);
        if ((result != FR_OK)) {
            printf("文件打开失败！\r\n");
            _acbuffer[0]='\0';
        }

        /* 申请一块动态内存空间 */
        hMem = GUI_ALLOC_AllocZero(file.fsize);
        /* 转换动态内存的句柄为指针 */
        _acbuffer = GUI_ALLOC_h2p(hMem);

        /* 读取图片数据到动态内存中 */
        result = f_read(&file, _acbuffer, file.fsize, &f_num);
        if (result != FR_OK) {
            printf("文件读取失败！\r\n");
        }
        /* 读取完毕关闭文件 */
        f_close(&file);
        /* 退出临界段 */
        taskEXIT_CRITICAL();
        /* 绘制流位图 */
        GUI_DrawStreamedBitmapAuto(_acbuffer, x, y);

        /* 释放内存 */
        GUI_ALLOC_Free(hMem);
    }


由于流位图文件存储在SD卡中，所以我们需要先将流位图数据加载到内存。首先打开文件，
使用emWin的动态内存管理函数GUI_ALLOC_AllocZero()和GUI_ALLOC_h2p()申请一块内存空间，
然后将流位图数据从SD卡读到申请到的内存中。接下来就可以显示流位图了，如果不确定流位图的像素格式，
直接用GUI_DrawStreamedBitmapAuto()函数即可，该函数支持任意像素格式的流位图显示。

最后，在MainTask函数中调用ShowStreamedBitmap即可完成实验，见 代码清单:显示位图-9_ 。

.. code-block:: c
    :caption: 代码清单:显示位图-9 MainTask函数（Maintask.c）
    :name: 代码清单:显示位图-9
    :linenos:

    /**
    * @brief GUI主任务
    * @note 无
    * @param 无
    * @retval 无
    */
    void MainTask(void)
    {
        ShowStreamedBitmap("0:/image/illustration.dta",//流位图名称
                        (LCD_GetXSize() - 480)/2, //X轴坐标
                        (LCD_GetYSize() - 270)/2);//Y轴坐标

        while (1) {
            GUI_Delay(2000);
        }
    }


实验现象
------------

实验结果如图 实验结果_ 所示，符合实验设计要求。

.. image:: media/display_bitmap/displa008.png
   :align: center
   :name: 实验结果
   :alt: 实验结果


