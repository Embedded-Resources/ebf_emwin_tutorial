.. vim: syntax=rst

内存设备
============

内存设备是emWin的一种绘图机制，可以用在各种绘图操作中，可以通过它实现一些华丽炫酷的界面效果。
不过其最主要的作用是用来消除在绘制重叠项目时出现的屏幕闪烁。

内存设备简介
~~~~~~~~~~~~~~~~

内存设备消除屏幕闪烁的基本思路很简单，不使用内存设备时，每一步绘制操作都会直接写入屏幕，
这就导致屏幕刷新时内容出现闪烁，例如要显示一张位图并在位图上绘制透明文本，首先必须绘制位图，
然后绘制透明文本，那么效果就是文本会出现闪烁。如果使用内存设备，所有的绘制操作都在内存中进行，
绘制完成后才更新到屏幕，所以没有闪烁。所谓闪烁其实就是屏幕出现了明显可观察到的绘图过程，
而内存设备机制只将绘制完成的图形写入屏幕，所以观察不到明显的绘图过程，也就消除了闪烁。


.. image:: media/Memory_device/Memory002.png
   :align: center
   :name: 内存设备使用效果
   :alt: 内存设备使用效果

表格 内存设备使用效果_ 是绘制同一种图形时使用存储设备和不使用存储设备的不同效果，分为两种情况。
第一种情况不使用内存设备，屏幕必须先清除，然后再依次显示旋转后的矩形和字符串；第二种情况使用内存设备，
绘图的步骤和之前相同但在内存中执行，此时屏幕不刷新，调用GUI_MEMDEV_CopyToLCD函数更新屏幕，并且一更新就反映出所有的绘图操作。

内存设备可以与窗口管理器（下一章节讲解）完美配合。每个窗口都有一个标志位，告诉窗口管理器是否应该使用内存设备进行渲染。
此标志位可在创建窗口时指定，也可随时设置或重置。如果为特定窗口设置了内存设备标志位，那么窗口管理器将自动使用内存设备进行窗口绘制。
窗口管理器会在绘制窗口之前创建一个内存设备，并在绘制完成后删除这个内存设备。如果创建的内存设备有足够多的内存，
那么整个窗口都会放到内存设备中进行绘制，如果这个内存设备的可用内存无法绘制完整窗口，则窗口管理器会使用“分段”来绘制窗口。
用于绘图操作的内存只在绘图操作期间分配，如果在绘制或重绘窗口时没有足够的可用内存，则不使用内存设备进行绘制或重绘窗口。

内存要求
~~~~~~~~~~~~

内存设备使用的是位于GUIConf.c中GUI_ALLOC_AssignMemory函数分配的内存块，也就是emWin内存的一部分，
所占用的大小取决于内存设备的色彩深度以及是否需要支持透明度。

不支持透明度的内存设备
-----------------------

表格 不支持透明度的内存使用情况_ 显示了不支持透明度的内存设备在不同色彩深度下的内存需求。


.. image:: media/Memory_device/Memory004.png
   :align: center
   :name: 不支持透明度的内存使用情况
   :alt: 不支持透明度的内存使用情况

例如创建一个X方向为111像素，Y方向为33像素，16bpp色深的内存设备，不支持透明度。那这个内存设备的所需字节数计算方法如下：

所需字节数 = 111 \* 33 \* 2 = 7326 bytes

支持透明度的内存设备
----------------------

表格 支持透明度的内存使用情况_ 显示了支持透明度的内存设备在不同色彩深度下的内存需求。
支持透明度的内存设备与不支持透明度的内存设备相比，除了32bpp色深之外，其余色深每8个像素就多出1个字节，用于管理透明度信息。


.. image:: media/Memory_device/Memory005.png
   :align: center
   :name: 支持透明度的内存使用情况
   :alt: 支持透明度的内存使用情况


例如创建一个X方向200像素，Y方向50像素，8bpp色深的内存设备，支持透明度。那这个内存设备的所需字节数计算方法如下：

所需字节数 = ( 200 + ( 200 + 7 ) / 8) \* 50 = 11250 bytes

内存设备API
~~~~~~~~~~~~~~~~~~~

emWin提供的存储设备相关API函数比较多，其中还涉及到一些旋转、测量、动画和模糊混合等高级功能，
这里我们只介绍一些基础和常用的API函数，见表格 内存设备API函数_ 。


.. image:: media/Memory_device/Memory006.png
   :align: center
   :name: 内存设备API函数
   :alt: 内存设备API函数

GUI_MEMDEV_CopyToLCD()
------------------------

将内存设备的内容从内存复制到LCD。



.. code-block:: c
    :caption: 代码清单:内存设备-1 函数原型
    :name: 代码清单:内存设备-1
    :linenos:

    void GUI_MEMDEV_CopyToLCD(GUI_MEMDEV_Handle hMem );

1) hMem：
内存设备的句柄。

该函数忽略窗口管理器的剪切区域和alpha通道。因此，不建议在paint事件中使用此函数。为了显示关于剪切区域和alpha通道的内存设备，应该使用函数GUI_MEMDEV_WriteAt()。

GUI_MEMDEV_CopyToLCDAt()
------------------------

将内存设备的内容复制到LCD的指定位置。

.. code-block:: c
    :caption: 代码清单:内存设备-2 函数原型
    :name: 代码清单:内存设备-2
    :linenos:

    void GUI_MEMDEV_CopyToLCDAt(GUI_MEMDEV_Handle hMem, int x, int y);

1) hMem：
内存设备的句柄；

2) x：
指定的X位置；

3) y：
指定的Y位置。

GUI_MEMDEV_Create()
-------------------------

创建一个内存设备。

.. code-block:: c
    :caption: 代码清单:内存设备-3 函数原型
    :name: 代码清单:内存设备-3
    :linenos:

    GUI_MEMDEV_Handle GUI_MEMDEV_Create(int x0, int y0, int xSize, int ySize);

1) x0：
内存设备的X位置；

2) y0：
内存设备的Y位置；

3) xSize：
内存设备的X方向大小；

4) ySize：
内存设备的Y方向大小。

返回值：创建的内存设备句柄，如果创建失败，则返回值为0。

GUI_MEMDEV_CreateFixed()
---------------------------

创建一个具有固定色彩深度和调色板的内存设备。

.. code-block:: c
    :caption: 代码清单:内存设备-4 函数原型
    :name: 代码清单:内存设备-4
    :linenos:

    GUI_MEMDEV_Handle GUI_MEMDEV_CreateFixed(int x0, int y0,int xSize, int
                                            ySize, int Flags,const
                                            tLCDDEV_APIList * pMemDevAPI,
                                            const LCD_API_COLOR_CONV *
                                            pColorConvAPI);


1) x0：
内存设备的X位置；

2) y0：
内存设备的Y位置；

3) xSize：
内存设备的X方向大小；

4) ySize：
内存设备的Y方向大小；

5) Flags：
创建标志，允许的参数如下：

..

   GUI_MEMDEV_HASTRANS：默认值，内存设备使用透明标志创建，该标志确保正确绘制背景；

   GUI_MEMDEV_NOTRANS：创建不透明的内存设备，必须正确绘制背景。可用于非矩形区域，比透明标志内存设备快30 - 50%。

6) pMemDevAPI：
定义内存设备的色彩深度，允许的参数值如下：

..

   GUI_MEMDEV_APILIST_1：创建具有1bpp色彩深度的内存设备；

   GUI_MEMDEV_APILIST_8：创建具有8bpp色彩深度的内存设备；

   GUI_MEMDEV_APILIST_16：创建具有16bpp色彩深度的内存设备；

   GUI_MEMDEV_APILIST_32：创建具有32bpp色彩深度的内存设备。

7) pColorConvAPI：
所需的固定调色板，允许的参数值如下：

..

   GUICC_1：固定调色板模式1(黑/白)；

   GUICC_2：固定调色板模式2(4种灰度)；

   GUICC_4：固定调色板模式4(16种灰度)；

   GUICC_565：固定调色板模式565；

   GUICC_M565：固定调色板模式M565；

   GUICC_8666：固定调色板模式8666；

   GUICC_888：固定调色板模式888；

   GUICC_8888：固定调色板模式8888。

返回值：创建的内存设备句柄，如果创建失败，则返回值为0。

GUI_MEMDEV_Delete()
---------------------

删除内存设备。

.. code-block:: c
    :caption: 代码清单:内存设备-5 函数原型
    :name: 代码清单:内存设备-5
    :linenos:

    void GUI_MEMDEV_Delete(GUI_MEMDEV_Handle MemDev);

1) hMem：
需要删除的内存设备句柄。

GUI_MEMDEV_Select()
--------------------

激活内存设备(如果句柄为0，则激活LCD)。

.. code-block:: c
    :caption: 代码清单:内存设备-6 函数原型
    :name: 代码清单:内存设备-6
    :linenos:

    GUI_MEMDEV_Handle GUI_MEMDEV_Select(GUI_MEMDEV_Handle hMemDev);

1) hMem：
内存设备的句柄。

返回值：上一个内存设备的句柄，如果上一次选择的是LCD，则返回0。

内存设备基础实验
~~~~~~~~~~~~~~~~~~~~~~~~

下面介绍分别在不使用内存设备和使用内存设备的情况下快速刷新文本并显示的实验。

代码分析
---------------

.. code-block:: c
    :caption: 代码清单:内存设备-7 内存设备演示函数（MainTask.c文件）
    :name: 代码清单:内存设备-7
    :linenos:

    /**
    * @brief 绘图函数
    * @note 无
    * @param
    * @retval 无
    */
    static void _Draw(int x0, int y0, int x1, int y1, int i)
    {
        char buf[] = {0};

        /* 绘制矩形背景 */
        GUI_SetColor(GUI_BLUE);
        GUI_FillRect(x0, y0, x1, y1);

        /* 绘制文本 */
        GUI_SetFont(GUI_FONT_D64);
        GUI_SetTextMode(GUI_TEXTMODE_XOR);
        sprintf(buf, "%d", i);
        GUI_DispStringHCenterAt(buf, x0 + (x1 - x0)/2, (y0 + (y1 - y0)/2) - 32);
    }

    /**
    * @brief 内存设备演示函数
    * @note 无
    * @param 无
    * @retval 无
    */
    static void _DemoMemDev(void)
    {
        GUI_MEMDEV_Handle hMem = 0;
        int i = 0;

        /* 设置背景色 */
        GUI_SetBkColor(GUI_BLACK);
        GUI_Clear();

        /* 显示提示文字 */
        GUI_SetColor(GUI_WHITE);
        GUI_SetFont(GUI_FONT_32_ASCII);
        GUI_DispStringHCenterAt("MEMDEV_MemDev - Sample", 400, 5);
        GUI_SetFont(GUI_FONT_24_ASCII);
        GUI_DispStringHCenterAt("Shows the advantage of using a\nmemorydevice", 400, 45);
        GUI_SetFont(GUI_FONT_16_ASCII);
        GUI_DispStringHCenterAt("Draws the number\nwithout a\nmemory device", 100, 90);
        GUI_DispStringHCenterAt("Draws the number\nusing a\nmemory device", 680, 90);

        /* 创建内存设备 */
        hMem = GUI_MEMDEV_Create(600, 150, 150, 100);

        while (1) {
            /* 直接绘制 */
            _Draw(25, 150, 175, 250, i);

            /* 激活内存设备 */
            GUI_MEMDEV_Select(hMem);
            /* 向内存设备中绘制图形 */
            _Draw(600, 150, 750, 250, i);
            /* 选择LCD */
            GUI_MEMDEV_Select(0);
            /* 将内存设备中的内容复制到LCD */
            GUI_MEMDEV_CopyToLCDAt(hMem, 600, 150);

            GUI_Delay(40);
            i++;
            if (i > 999) {
                i = 0;
            }
        }
    }


在 代码清单:内存设备-7_ 的_DemoMemDev中，通过GUI_MEMDEV_Create 函数创建一个左上角坐标（600,150）、
X方向大小150像素、Y方向大小100像素的内存设备，创建成功后返回这个内存设备的句柄hMem。
此时绘图操作并不会写入内存设备，因为没有激活（选中）内存设备。

调用_Draw函数不使用内存设备直接绘制一个矩形背景和一组异或文本模式的文字，_Draw函数的入口参数分别是矩形左上角的坐标（x0,y0）、
矩形右下角的坐标（x1,y1）和需要显示的文字i。然后激活刚刚创建的内存设备，再次调用_Draw函数把图形绘制到内存设备中。注意，
图形的绘制坐标必须在内存设备的范围内，否则将无法显示。绘制完成后选择LCD，并将内存设备中的内容复制到LCD。这样就完成了一次最简单的内存设备的使用。

因为内存设备占用了部分内存空间，所以当不再使用这个内存设备时一定要及时将其删除并释放占用的内存空间，否则可能会出现不可预估的错误。

内存设备机制除了可以消除由于频繁刷新屏幕造成的闪烁以外，还可以用来加快各种图片在LCD上的显示速度。例如在屏幕像素格式为RGB565的情况下，
使用内存设备显示一张800*480分辨率的图片只需要20ms左右，具体内容将在图片显示章节讲解。

实验现象
---------------

内存设备基础实验的结果如图 内存设备实验结果_ 所示，将例程下载到开发板运行后，
在屏幕上能看到左侧不使用内存设备直接绘制的数字有明显的闪烁现象，而右侧使用内存设备绘制的数字稳定无闪烁。

.. image:: media/Memory_device/Memory003.png
   :align: center
   :name: 内存设备实验结果
   :alt: 内存设备实验结果


