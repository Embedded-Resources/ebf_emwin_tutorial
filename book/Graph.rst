.. vim: syntax=rst

曲线图控件
============

曲线图（Graph）控件主要是用于数据可视化，通常用于显示某项数据随时间或其他有序序列而变化的趋势。
曲线图控件可以同时显示多条曲线，拥有水平和垂直刻度尺，可以用来标记曲线刻度，背景上可以显示水平和垂直间距不相同的网格。
如果数据数组不适合图形的可见区域，控件可以自动显示滚动条，滚动条允许在大型数据数组中滚动。

emWin中曲线图控件的结构如图 曲线图控件结构_ 所示，结构中各部分含义的说明见表格 曲线图控件结构含义_ 。

.. image:: media/Graph/Graph002.png
   :align: center
   :name: 曲线图控件结构
   :alt: 曲线图控件结构


.. image:: media/Graph/Graph01.png
   :align: center
   :name: 曲线图控件结构含义
   :alt: 曲线图控件结构含义


数据对象、图形的功能要求和应用场景等往往存在些许差异，
曲线图控件针对这些差异提供了两种曲线显示模式，一种是X-Y模式，一种是Y-T模式。

1. GRAPH_DATA_XY

此模式用于显示由一组点阵坐标(x,y)组成的曲线，对象数据被绘制为折线。
此模式的典型使用场景是绘制函数图形，显示效果如图 X-Y模式显示效果_ 所示。

.. image:: media/Graph/Graph003.png
   :align: center
   :name: X-Y模式显示效果
   :alt: X-Y模式显示效果


2. GRAPH_DATA_YT

此显示模式用于显示图形上每个横向位置都带有一个Y值的曲线。Y-T模式的典型应用是绘制测量值在不断更新的曲线，
例如温湿度等。此模式的显示效果如图 Y-T模式显示效果_ 所示。

.. image:: media/Graph/Graph004.png
   :align: center
   :name: Y-T模式显示效果
   :alt: Y-T模式显示效果


曲线图控件不支持任何的通知代码、聚焦操作和按键消息，
也就是说无法在对话框回调函数中通过通知代码操作曲线图控件，控件也无法响应外部键盘的动作。

创建曲线图控件
~~~~~~~~~~~~~~~~~~~

曲线图控件API
^^^^^^^^^^^^^^^^^^^^^^^^

曲线图控件的 API 函数比较多，这里我们只列出一些较为常用的，见表格 曲线图控件API_ 。完整的API函数列表请查阅emWin官方手册。


.. image:: media/Graph/Graph02.png
   :align: center
   :name: 曲线图控件API
   :alt: 曲线图控件API

曲线图控件创建函数
^^^^^^^^^^^^^^^^^^^^^^^^^

GRAPH_CreateEx()
'''''''''''''''''''''''

在指定位置创建一个具有指定大小的新曲线图控件。

.. code-block:: c
    :caption: 代码清单:曲线图-1 函数原型
    :name: 代码清单:曲线图-1
    :linenos:

    GRAPH_Handle GRAPH_CreateEx(int x0, int y0, int xSize, int ySize,
                                WM_HWIN hParent, int WinFlags, int ExFlags,
                                int Id);

1) x0：
曲线图控件在父坐标中的最左侧像素；

2) y0：
曲线图控件在父坐标中的最顶侧像素；

3) xSize：
曲线图的水平尺寸，以像素为单位；

4) ySize：
曲线图的垂直尺寸，以像素为单位；

5) hParent：
父窗口句柄。如果为0，则将桌面窗口作为其父窗口；

6) WinFlags：
窗口创建标志。由于控件本质上是窗口，所以曲线图控件在创建时可以使用几乎所有的窗口创建标志。
这些标志中按钮最常用的是WM_CF_SHOW。全部创建标志请参考emWin手册的窗口管理器章节；

7) ExFlags：
特殊创建标志。用于设置是否固定横轴网格，默认不固定，使用参数值GRAPH_CF_GRID_FIXED_X可固定横轴网格；

8) Id：
控件ID号。

返回值：创建成功后返回已创建的曲线图控件句柄，创建失败则返回0。

GRAPH_CreateIndirect()
'''''''''''''''''''''''''''''

从对话框资源表中创建曲线图控件。

.. code-block:: c
    :caption: 代码清单:曲线图-2 函数原型
    :name: 代码清单:曲线图-2
    :linenos:

    GRAPH_Handle GRAPH_CreateIndirect(const GUI_WIDGET_CREATE_INFO
                                    *pCreateInfo, WM_HWIN hWinParent,
                                    int x0, int y0, WM_CALLBACK *cb);


1) pCreateInfo：
指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：
父窗口的句柄；

3) x0：
曲线图控件在父坐标中的最左边像素；

4) y0：
曲线图控件在父坐标中的最顶部像素；

5) cb：
回调函数指针。

此函数的第一个参数指向的是 **代码清单:控件基础-3** 的资源表结构，但不使用其中的para参数，
其中的Flags参数的可选值与GRAPH_CreateEx()函数的ExFlags参数相同，用于设置是否固定横轴网格。

曲线图控件基础实验
~~~~~~~~~~~~~~~~~~~~~~~~~

接下来我们通过一个实验来讲解如何以对话框方式间接创建一个只包含基本功能的简易曲线图控件，
完整的曲线图控件演示实验可参考官方例程WIDGET_GraphYT.c，例程路径如下：

**SeggerEval_WIN32_MSVC_MinGW_GUI_V548\\Sample\\Tutorial\\WIDGET_GraphYT.c**

本实验将使用ADC采集挑战者V2底板上电位器的电压值，并把采集到的原始AD值通过曲线图控件绘制到显示屏上。

代码分析
^^^^^^^^^^^^


创建对话框
''''''''''''''

.. code-block:: c
    :caption: 代码清单:曲线图-3 创建对话框（GraphDLG.c文件）
    :name: 代码清单:曲线图-3
    :linenos:

    /* 控件ID */
    #define ID_FRAMEWIN_0 (GUI_ID_USER + 0x00)
    #define ID_GRAPH_0 (GUI_ID_USER + 0x01)

    /* 资源表 */
    static const GUI_WIDGET_CREATE_INFO _aDialogCreate[] = {
        { FRAMEWIN_CreateIndirect, "Framewin", ID_FRAMEWIN_0, 0, 0, 800,
        480, 0, 0x0, 0 },
        { GRAPH_CreateIndirect, "Graph", ID_GRAPH_0, 60, 10, 645, 425, 0,
        0x0, 0 },
    };

    /**
    * @brief 以对话框方式间接创建控件
    * @note 无
    * @param 无
    * @retval hWin：资源表中第一个控件的句柄
    */
    WM_HWIN CreateFramewin(void)
    {
        WM_HWIN hWin;

        hWin = GUI_CreateDialogBox(_aDialogCreate, GUI_COUNTOF(
            _aDialogCreate), _cbDialog, WM_HBKWIN, 0, 0);
        return hWin;
    }


在 代码清单:曲线图-3_ 中我们定义了2个ID：一个框架窗口ID和1个曲线图控件。
当然也可以使用emWin预定义好的曲线图控件ID，但最多只有GUI_ID_GRAPH0到GUI_ID_GRAPH3，共4个ID可供使用。


对话框回调函数
'''''''''''''''''''''

.. code-block:: c
    :caption: 代码清单:曲线图-4 对话框回调函数（GraphDLG.c文件）
    :name: 代码清单:曲线图-4
    :linenos:

    /**
    * @brief 对话框回调函数
    * @note 无
    * @param pMsg：消息指针
    * @retval 无
    */
    static void _cbDialog(WM_MESSAGE* pMsg)
    {
        WM_HWIN hItem;
        GRAPH_SCALE_Handle hScaleV;

        switch (pMsg->MsgId) {
        case WM_INIT_DIALOG:
            /* 初始化Framewin控件 */
            hItem = pMsg->hWin;
            FRAMEWIN_SetText(hItem, "STemWIN@EmbedFire STM32F429");
            FRAMEWIN_SetFont(hItem, GUI_FONT_20_1);
            /* 初始化Graph控件 */
            hItem = WM_GetDialogItem(pMsg->hWin, ID_GRAPH_0);
            GRAPH_SetColor(hItem, GUI_WHITE, GRAPH_CI_BK);
            GRAPH_SetColor(hItem, GUI_BLACK, GRAPH_CI_GRID);
            GRAPH_SetBorder(hItem, 30, 10, 10, 10);
            GRAPH_SetGridDistX(hItem, 50);
            GRAPH_SetGridDistY(hItem, 50);
            GRAPH_SetLineStyleH(hItem, GUI_LS_DOT);
            GRAPH_SetLineStyleV(hItem, GUI_LS_DOT);
            GRAPH_SetGridVis(hItem, 1);
            /* 创建垂直刻度对象 */
            hScaleV = GRAPH_SCALE_Create(15, GUI_TA_HCENTER | GUI_TA_LEFT,
                                        GRAPH_SCALE_CF_VERTICAL, 50);
            GRAPH_AttachScale(hItem, hScaleV);
            GRAPH_SCALE_SetFactor(hScaleV, 10);
            /* 创建数据对象 */
            Graphdata = GRAPH_DATA_YT_Create(GUI_RED, 600, 0, 0);
            GRAPH_AttachData(hItem, Graphdata);
            break;
        default:
            WM_DefaultProc(pMsg);
            break;
        }
    }


1. WM_INIT_DIALOG消息

在 代码清单:曲线图-4_ 中，我们首先设置框架窗口。将框架窗口的字体设置为GUI_FONT_20_1，
并在标题栏中显示 **STemWIN@EmbedFire STM32F429**。

接着是曲线图控件的初始化设置，使用GRAPH_SetColor函数将曲线显示区域背景颜色设置为白色，将网格线颜色设置为黑色，
使用GRAPH_SetBorder函数设置曲线图控件的左边框宽度30像素，其余三边宽度为10像素，
使用GRAPH_SetGridDistX函数和GRAPH_SetGridDistY函数分别设置垂直方向和水平方向的网格线间距各为50像素，
这样除了边框以外，图形显示区域就变成了一个由水平方向9条刻度线和垂直方向13条刻度线组成的600*400像素的网格区域。

网格线的初始化设置其实还是比较麻烦的，因为它不仅会影响到后面将要设置的刻度对象，而且会影响最终图形的显示效果。
需要寻找一个合适的网格线间距，使得整个网格区域看上去较为整齐，同时又不影响之后刻度对象的初始化设置以及图形显示效果。

GRAPH_SetLineStyleH函数和GRAPH_SetLineStyleV函数则是用来设置设置网格线样式的，在上述代码中，
我们将曲线图的垂直方向和水平方向的网格线风格均设置为点样式，最后调用GRAPH_SetGridVis函数开启网格线，曲线图控件的主体初始化就完成了。

曲线图控件主体初始化完成之后还需要接创建和添加刻度对象和数据对象。如 代码清单:曲线图-4_ 所示，
刻度对象的创建需要调用GRAPH_SCALE_Create函数，设置刻度文本到曲线图外框的距离为15像素，刻度文本设置为水平中心对齐和左对齐，
参数GRAPH_SCALE_CF_VERTICAL表示将刻度对象设置为垂直方向，创建函数最后一个参数是两个刻度的间距，需要和水平网格线的线间距相同。
刻度对象创建成功后会返回它的句柄，利用这个句柄再加上GRAPH_AttachScale函数将刻度对象附加到曲线图控件中。需要注意的是，
STM32F429的ADC的采样原始值范围是0~4095，但 代码清单:曲线图-4_ 中的曲线图控件垂直方向只有400像素无法直接显示ADC原始数据，
需要设置一个比例系数，利用GRAPH_SCALE_SetFactor函数将比例系数设为10，这样就能显示几乎全部数据了。

创建数据对象的创建相对比较简单，首先是调用GRAPH_DATA_YT_Create函数，设置待显示曲线的颜色为红色，最大可显示600个数据，
函数的后面两个参数分别是待添加到对象中的数据的指针和待添加数据的个数，在 代码清单:曲线图-4_ 中我们不在初始化阶段添加数据，
所有后两个参数都填0，数据对象创建成功后会返回它的句柄，得到数据对象的句柄之后就可以使用GRAPH_AttachData函数将数据对象添加到曲线图控件中。

2. 其他消息

所有我们不关心或者没有用到的系统消息都可以调用默认消息处理函数WM_DefaultProc进行处理。


MainTask函数
''''''''''''''

.. code-block:: c
    :caption: 代码清单:曲线图-5 MainTask函数（GraphDLG.c文件）
    :name: 代码清单:曲线图-5
    :linenos:

    /**
    * @brief GUI主任务
    * @note 无
    * @param 无
    * @retval 无
    */
    void MainTask(void)
    {
        /* 创建窗口 */
        CreateFramewin();

        while (1) {
            /* 向GRAPH数据对象添加数据 */
            GRAPH_DATA_YT_AddValue(Graphdata, ADC_ConvertedValue/10);
            GUI_Delay(30);
        }
    }


所有初始化和创建工作完成后，需要向数据对象添加数据才能让图形显示出来。如 代码清单:曲线图-5_ 所示，
我们在emWin的主任务循环中，调用GRAPH_DATA_YT_AddValue函数将ADC采集到的原始数据源源不断的添加到曲线图空间的数据对象中，每隔30ms添加一个数据。

实际上曲线的刷新速度很慢，粗略测量了一下，在30ms的数据更新时间间隔下，曲线第一个点从控件最右侧移到最左侧总共600像素，
大概花了35秒多一点，也就是说曲线图控件实际上需要将近60ms才能更新一个新点，这个速度确实有点慢了，
这就导致曲线图控件只适合显示一些例如温湿度这类变化较慢的数据，而类似加速度值这种变化较快的，或者响应时间很短的数据，显示效果可能就会比较差。

ADC在中断服务函数中获取电位器电压的原始数值，见 代码清单:曲线图-6_ 。

.. code-block:: c
    :caption: 代码清单:曲线图-6 ADC中断服务函数（stm32f4xx_it.c文件）
    :name: 代码清单:曲线图-6
    :linenos:

    extern __IO uint16_t ADC_ConvertedValue;
    /**
    * @brief  ADC 转换完成中断服务程序
    * @param  None
    * @retval None
    */
    void ADC_IRQHandler(void)
    {
        uint32_t ulReturn;
        /* 进入临界段 */
        ulReturn = taskENTER_CRITICAL_FROM_ISR();

        if (ADC_GetITStatus(RHEOSTAT_ADC,ADC_IT_EOC)==SET) {
            /* 读取ADC的转换值 */
            ADC_ConvertedValue = ADC_GetConversionValue(RHEOSTAT_ADC);
        }
        ADC_ClearITPendingBit(RHEOSTAT_ADC,ADC_IT_EOC);

        /* 退出临界段 */
        taskEXIT_CRITICAL_FROM_ISR(ulReturn);
    }


实验现象
^^^^^^^^^^^^

曲线图控件基础实验的实验现象如 曲线图控件实验_ 所示，可以看到控件中的红色曲线随着电位器的转动而出现变化

.. image:: media/Graph/Graph005.png
   :align: center
   :name: 曲线图控件实验
   :alt: 曲线图控件实验
