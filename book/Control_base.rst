.. vim: syntax=rst

控件基础
----

emWin中的控件又被称为小部件，是具有对象类型属性的窗口。

控件是构成用户界面的元素。他们可以对某些事件做出自动反应。例如，如果按下按钮，它会以不同的状态出现。控件具有在其存在期间随时可以更改的属性。它们通常会在不再使用时被删除。与窗口类似，控件由句柄引用，句柄由相应的创建函数返回。控件需要窗口管理器。创建控件后，它就像任何其他窗口一样被处理。使用窗口管理器
确保在必要时正确显示和重绘。对于应用程序或用户界面并不是必须使用控件的，但是使用控件可以减少开发时间。由于控件本质上是窗口，所以也可以使用大多数窗口管理器API函数。

支持的控件类型
~~~~~~~

目前V5.44a版本的emWin一共支持24种不同类型的控件，具体见表格 15‑1。

表格 15‑1 支持的控件类型

.. list-table::
   :widths: 25 25 25 25
   :header-rows: 0


   * - 控件名称  | 控件
     - 图  | 控件截图  （经典）  | （带
     - | 描述

       肤） |
     - |

            |

   * - BUTTON
     - |Contro002|
     - |Contro003|
     - 按钮，可按下。               | 文本或位图可以显示在按钮上。 |

   * - CHECKBOX
     - |Contro004|
     - |Contro005|
     - 复选框可以选中或取消选中。   |

   * - DROPDOWN
     - |Contro006|
     - |Contro007|
     - 下                           | 拉列表框，按下后打开列表框。 |

   * - EDIT
     - |Contro008|
     -
     - 单行编辑字                   | 段，提示用户键入数字或文本。 |

   * - FRAMEWIN
     - |Contro009|
     - |Contro010|
     - 框架窗口，                   | 创建典型的GUI外观。          |

   * - GRAPH
     - |Contro011|
     -
     - 图形                         | 控件，用于显示曲线或测量值。 |

   * - HEADER
     - |Contro012|
     - |Contro013|
     - 标头控件，用于管理列。       |

   * - ICONVIEW
     - |Contro014|
     -
     - 图标视图控件，适用于常见     | 手持设备中的基于图标的平台。 |

   * - IMAGE
     - |Contro015|
     -
     - 图像                         | 控件，自动显示多种图像格式。 |

   * - KNOB
     - |Contro016|
     -
     - 旋钮                         | 控件，可用于调整不可数的值。 |

   * - LISTBOX
     - |Contro017|
     -
     - 列表框                       | ，其中突出显示用户选择的项。 |

   * - LISTVIEW
     - |Contro018|
     -
     - 列表视图控件用于创建表。     |

   * - LISTWHEEL
     - |Contro019|
     -
     - 列表轮控件，可以通过         | 指针输入设备移动和加速数据。 |

   * - MENU
     - |Contro020|
     - |Contro021|
     - 菜单                         | 控件用于创建水平和垂直菜单。 |

   * - MULTIEDIT
     - |Contro022|
     -
     - 此控件用于编辑多行文本。     |

   * - MULTIPAGE
     - |Contro023|
     - |Contro024|
     - 多页控件用                   | 于创建具有多个页面的对话框。 |

   * - PROGBAR
     - |Contro025|
     - |Contro026|
     - 用于可视化的进度条。         |

   * - RADIO
     - |Contro027|
     - |Contro028|
     - 单选按钮可以被               | 选择，一次只能选择一个按钮。 |

   * - SCROLLBAR
     - |Contro029|
     - |Contro030|
     - 滚                           | 动条控件可以是水平或垂直的。 |

   * - SLIDER
     - | image101|
     - |Contro032|
     - 滑块控件用于更改值。         |

   * - SPINBOX
     - | image103|
     - |Contro034|
     - 旋转框控件显示和调整特定值。 |

   * - SWIPELIST
     - | image105|
     -
     - 滑动列表控件                 | 用于创建可滑动的列表，可通过 | 在触摸屏上滑动手指（或任何其 | 他PID设备）来移动滑动列表。  |

   * - TEXT
     - | image106|
     -
     - 通常在                       | 对话框中使用的静态文本控件。 |

   * - TREEVIEW
     - | image107|
     -
     - 用                           | 于管理分层列表的列表树控件。 |


理解控件的重绘机制
~~~~~~~~~

控件根据自身属性绘制自身。由于控件实际上都是一个个窗口，所以控件的重绘也是通过调用WM_Exec()、GUI_Exec()或GUI_Delay()完成的。当控件的属性被更改时，控件的窗口或控件的一部分会被标记为无效，但此时不立刻执行重绘，重绘操作由窗口管理器在稍后完成，或者控件可以通过调用WM_Pa
int()或WM_Exec()强制执行重绘，直到所有无效窗口都被重绘。通常在多任务环境中使用WM_Exec()更新控件和其他所有带有回调函数的窗口。和窗口不同的是，控件创建时有一个默认的回调函数，调用GUI_Exec()会使得窗口管理器向这些控件回调函数发送 WM_PAINT
消息来执行重绘操作，所有以被标记为无效的控件的回调函数都会收到WM_PAINT消息。

如何使用控件
~~~~~~

这里我们以进度条控件为例简单介绍一下如何使用控件，可以不用关注这些函数具体怎么用，有个大概认识就好，见代码清单 15‑1。常用控件的具体使用方法会在之后的章节详细讲解。

代码清单 15‑1 创建进度条控件

1 PROGBAR_Handle hProgBar;

2 GUI_DispStringAt("Progress bar", 100, 20);

3 hProgBar = PROGBAR_Create(100, 40, 100, 20, WM_CF_SHOW);

4 PROGBAR_SetValue(hProgBar , 45);

代码清单 15‑1首先定义了一个进度条句柄hProgBar，然后调用PROGBAR_Create函数创建进度条。创建控件后，可以通过调用其成员函数更改属性。例如通过PROGBAR_SetValue函数修改了进度条的值为45。不过上述代码只是创建了控件，并不会立刻显示出来，需要调用函数
WM_Exec()、GUI_Exec()或GUI_Delay()后才会显示。创建完成的进度条如图 15‑1所示。

|Contro038|

图 15‑1 创建好的进度条控件

控件如何通信
~~~~~~

控件通常作为子窗口创建，其父窗口可以是任何类型的窗口，甚至是另一个控件。为了确保同步，父窗口内的子窗口无论发生什么事件都应通知父窗口。当发生某个事件时，子窗口通过向父窗口发送WM_NOTIFY_PARENT消息与父窗口进行通信。通知代码作为消息的一部分，发送哪种通知代码取决于事件的类型。大多数控件都
有一个或多个通知代码，用于定义不同类型的事件。常用控件支持的通知代码将在后续章节进行讲解。

控件皮肤
~~~~

控件的外观可以使用其成员函数来修改。某些控件支持皮肤功能，如果控件使能了皮肤功能，则控件外观由皮肤决定，此时控件原有的某些成员函数是无效的。emWin从V5.28版本开始默认使能所有控件的皮肤，像上面提到的进度条控件，在默认情况下类似PROGBAR_SetBarColor这种改颜色的函数是用不了的，
想要改颜色必须关掉皮肤，这个在后面会具体讲解。如果遇到控件的某个成员函数没有效果的情况，有可能就是由于使能了皮肤造成的。

控件动态内存
~~~~~~

在嵌入式应用中，由于内存碎片效应，通常不太需要使用动态内存。可以使用许多不同的策略来避免这种情况，emWin使用了一种方法：所有对象以及运行时存储的所有数据都存储在由句柄引用的内存区域中。这使得可以在运行时重新定位分配的内存区域，从而避免使用指针时出现的长期分配问题。因此，所有控件都由句柄引用。

控件通用API
~~~~~~~

表格 15‑2列出了与控件相关的可用API函数。这些函数对所有控件都是通用的。

表格 15‑2 控件通用API

========================= ================================
函数名                    描述
========================= ================================
<WIDGET>_Callback()       默认的回调函数
<WIDGET>_CreateIndirect() 用于在对话框中自动创建控件
<WIDGET>_CreateUser()     使用额外字节作为用户数据创建控件
<WIDGET>_GetUserData()    检索上个函数创建的额外用户数据
<WIDGET>_SetUserData()    设置控件的额外数据
WIDGET_EnableStreamAuto() 是否支持所有流式位图格式
WIDGET_GetDefaultEffect() 返回控件的默认效果
WIDGET_SetDefaultEffect() 设置控件的默认效果
WIDGET_SetEffect()        设置给定控件的效果
WIDGET_SetFocusable()     设置接收输入焦点的功能
========================= ================================

<WIDGET>_CreateIndirect()

间接创建一个要在对话框中使用的控件。

代码清单 15‑2 函数原型

1 <WIDGET>_Handle <WIDGET>_CreateIndirect(const GUI_WIDGET_CREATE_INFO \*

2 pCreateInfo, WM_HWIN

3 hWinParent, int x0, int y0,

4 WM_CALLBACK \* cb);

1) pCreateInfo：指向GUI_WIDGET_CREATE_INFO结构的指针；

2) hParent：父窗口的句柄；

3) x0：控件在父坐标中的最左边像素；

4) y0：控件在父坐标中的最顶部像素；

5) cb：回调函数指针。

可以使用适当的前缀间接创建任何控件。例如使用BUTTON_CreateIndirect间接创建按钮控件，使用CHECKBOX_CreateIndirect间接创建一个复选框控件，依此类推。只有在对话框中才需要使用此函数间接创建控件。否则使用<WIDGET>_CreateEx函数直接创建即可。

在对话框中间接创建窗口和控件需要一个资源表，而GUI_WIDGET_CREATE_INFO结构对应的就是对话框资源表，其原型如代码清单 15‑3所示。

代码清单 15‑3 GUI_WIDGET_CREATE_INFO结构原型

1 struct GUI_WIDGET_CREATE_INFO_struct {

2 GUI_WIDGET_CREATE_FUNC \* pfCreateIndirect;

3 const char \* pName;

4 I16 Id;

5 I16 x0;

6 I16 y0;

7 I16 xSize;

8 I16 ySize;

9 U16 Flags;

10 I32 Para;

11 U32 NumExtraBytes;

12 };

13

1)  pfCreateIndirect：指向控件创建函数的指针；

2)  pName：控件名称；

3)  Id：控件ID；

4)  x0：控件的最左侧坐标；

5)  y0：控件的最顶部坐标；

6)  xSize：控件的横向尺寸；

7)  ySize：控件的纵向尺寸；

8)  Flags：控件的创建标志，默认为0；

9)  para：控件的参数，默认为0；

10) NumExtraBytes：控件的额外字节。

对于上述资源表结构，不是所有的控件都会用到所有的资源表参数，不同控件用到的资源表参数会有所变化。例如BUTTON控件就没有用到Flags和para参数。资源表在emWin中比较重要，下一章将要介绍的对话框就必须得用资源表，所以请一定要熟悉和理解资源表中的各个成员参数。

.. |image0| image:: media\Contro002.png
   :width: 0.86614in
   :height: 0.17323in
.. |image1| image:: media\Contro003.png
   :width: 0.8622in
   :height: 0.17323in
.. |image2| image:: media\Contro004.png
   :width: 0.12205in
   :height: 0.12205in
.. |image3| image:: media\Contro005.png
   :width: 0.54331in
   :height: 0.12205in
.. |image4| image:: media\Contro006.png
   :width: 0.79921in
   :height: 0.66142in
.. |image5| image:: media\Contro007.png
   :width: 0.81102in
   :height: 0.75197in
.. |image6| image:: media\Contro008.png
   :width: 0.80315in
   :height: 0.16142in
.. |image7| image:: media\Contro009.png
   :width: 0.64961in
   :height: 0.3189in
.. |image8| image:: media\Contro010.png
   :width: 0.77165in
   :height: 0.32283in
.. |image9| image:: media\Contro011.png
   :width: 0.89764in
   :height: 0.58268in
.. |image10| image:: media\Contro012.png
   :width: 0.72441in
   :height: 0.11417in
.. |image11| image:: media\Contro013.png
   :width: 1.06693in
   :height: 0.12205in
.. |image12| image:: media\Contro014.png
   :width: 0.54331in
   :height: 0.79921in
.. |image13| image:: media\Contro015.png
   :width: 0.83465in
   :height: 0.62598in
.. |image14| image:: media\Contro016.png
   :width: 0.81102in
   :height: 0.81102in
.. |image15| image:: media\Contro017.png
   :width: 0.79921in
   :height: 0.43307in
.. |image16| image:: media\Contro018.png
   :width: 1in
   :height: 1in
.. |image17| image:: media\Contro019.png
   :width: 0.94488in
   :height: 0.62992in
.. |image18| image:: media\Contro020.png
   :width: 1.29528in
   :height: 0.62598in
.. |image19| image:: media\Contro021.png
   :width: 0.98031in
   :height: 1.2874in
.. |image20| image:: media\Contro022.png
   :width: 1.20472in
   :height: 0.80315in
.. |image21| image:: media\Contro023.png
   :width: 0.70079in
   :height: 0.92126in
.. |image22| image:: media\Contro024.png
   :width: 0.90157in
   :height: 0.67717in
.. |image23| image:: media\Contro025.png
   :width: 0.62205in
   :height: 0.12598in
.. |image24| image:: media\Contro026.png
   :width: 0.62205in
   :height: 0.12598in
.. |image25| image:: media\Contro027.png
   :height: 0.30315in
.. |image26| image:: media\Contro028.png
   :width: 0.37795in
   :height: 0.34252in
.. |image27| image:: media\Contro029.png
   :width: 0.51575in
   :height: 0.11024in
.. |image28| image:: media\Contro030.png
   :width: 0.72047in
   :height: 0.12598in
.. |image29| image:: media\Contro031.png
   :width: 0.49213in
   :height: 0.1063in
.. |image30| image:: media\Contro032.png
   :width: 0.55118in
   :height: 0.16142in
.. |image31| image:: media\Contro033.png
   :width: 0.93307in
   :height: 0.18898in
.. |image32| image:: media\Contro034.png
   :width: 0.93307in
   :height: 0.16142in
.. |image33| image:: media\Contro035.png
   :width: 0.81496in
   :height: 1.20079in
.. |image34| image:: media\Contro036.png
   :width: 0.36614in
   :height: 0.12992in
.. |image35| image:: media\Contro037.png
   :width: 0.96063in
   :height: 0.83858in
.. |image36| image:: media\Contro002.png
   :width: 0.86614in
   :height: 0.17323in
.. |image37| image:: media\Contro003.png
   :width: 0.8622in
   :height: 0.17323in
.. |image38| image:: media\Contro004.png
   :width: 0.12205in
   :height: 0.12205in
.. |image39| image:: media\Contro005.png
   :width: 0.54331in
   :height: 0.12205in
.. |image40| image:: media\Contro006.png
   :width: 0.79921in
   :height: 0.66142in
.. |image41| image:: media\Contro007.png
   :width: 0.81102in
   :height: 0.75197in
.. |image42| image:: media\Contro008.png
   :width: 0.80315in
   :height: 0.16142in
.. |image43| image:: media\Contro009.png
   :width: 0.64961in
   :height: 0.3189in
.. |image44| image:: media\Contro010.png
   :width: 0.77165in
   :height: 0.32283in
.. |image45| image:: media\Contro011.png
   :width: 0.89764in
   :height: 0.58268in
.. |image46| image:: media\Contro012.png
   :width: 0.72441in
   :height: 0.11417in
.. |image47| image:: media\Contro013.png
   :width: 1.06693in
   :height: 0.12205in
.. |image48| image:: media\Contro014.png
   :width: 0.54331in
   :height: 0.79921in
.. |image49| image:: media\Contro015.png
   :width: 0.83465in
   :height: 0.62598in
.. |image50| image:: media\Contro016.png
   :width: 0.81102in
   :height: 0.81102in
.. |image51| image:: media\Contro017.png
   :width: 0.79921in
   :height: 0.43307in
.. |image52| image:: media\Contro018.png
   :width: 1in
   :height: 1in
.. |image53| image:: media\Contro019.png
   :width: 0.94488in
   :height: 0.62992in
.. |image54| image:: media\Contro020.png
   :width: 1.29528in
   :height: 0.62598in
.. |image55| image:: media\Contro021.png
   :width: 0.98031in
   :height: 1.2874in
.. |image56| image:: media\Contro022.png
   :width: 1.20472in
   :height: 0.80315in
.. |image57| image:: media\Contro023.png
   :width: 0.70079in
   :height: 0.92126in
.. |image58| image:: media\Contro024.png
   :width: 0.90157in
   :height: 0.67717in
.. |image59| image:: media\Contro025.png
   :width: 0.62205in
   :height: 0.12598in
.. |image60| image:: media\Contro026.png
   :width: 0.62205in
   :height: 0.12598in
.. |image61| image:: media\Contro027.png
   :height: 0.30315in
.. |image62| image:: media\Contro028.png
   :width: 0.37795in
   :height: 0.34252in
.. |image63| image:: media\Contro029.png
   :width: 0.51575in
   :height: 0.11024in
.. |image64| image:: media\Contro030.png
   :width: 0.72047in
   :height: 0.12598in
.. |image65| image:: media\Contro031.png
   :width: 0.49213in
   :height: 0.1063in
.. |image66| image:: media\Contro032.png
   :width: 0.55118in
   :height: 0.16142in
.. |image67| image:: media\Contro033.png
   :width: 0.93307in
   :height: 0.18898in
.. |image68| image:: media\Contro034.png
   :width: 0.93307in
   :height: 0.16142in
.. |image69| image:: media\Contro035.png
   :width: 0.81496in
   :height: 1.20079in
.. |image70| image:: media\Contro036.png
   :width: 0.36614in
   :height: 0.12992in
.. |image71| image:: media\Contro037.png
   :width: 0.96063in
   :height: 0.83858in
.. |Contro002| image:: media\Contro002.png
   :width: 0.86614in
   :height: 0.17323in
.. |Contro003| image:: media\Contro003.png
   :width: 0.8622in
   :height: 0.17323in
.. |Contro004| image:: media\Contro004.png
   :width: 0.12205in
   :height: 0.12205in
.. |Contro005| image:: media\Contro005.png
   :width: 0.54331in
   :height: 0.12205in
.. |Contro006| image:: media\Contro006.png
   :width: 0.79921in
   :height: 0.66142in
.. |Contro007| image:: media\Contro007.png
   :width: 0.81102in
   :height: 0.75197in
.. |Contro008| image:: media\Contro008.png
   :width: 0.80315in
   :height: 0.16142in
.. |Contro009| image:: media\Contro009.png
   :width: 0.64961in
   :height: 0.3189in
.. |Contro010| image:: media\Contro010.png
   :width: 0.77165in
   :height: 0.32283in
.. |Contro011| image:: media\Contro011.png
   :width: 0.89764in
   :height: 0.58268in
.. |Contro012| image:: media\Contro012.png
   :width: 0.72441in
   :height: 0.11417in
.. |Contro013| image:: media\Contro013.png
   :width: 1.06693in
   :height: 0.12205in
.. |Contro014| image:: media\Contro014.png
   :width: 0.54331in
   :height: 0.79921in
.. |Contro015| image:: media\Contro015.png
   :width: 0.83465in
   :height: 0.62598in
.. |Contro016| image:: media\Contro016.png
   :width: 0.81102in
   :height: 0.81102in
.. |Contro017| image:: media\Contro017.png
   :width: 0.79921in
   :height: 0.43307in
.. |Contro018| image:: media\Contro018.png
   :width: 1in
   :height: 1in
.. |Contro019| image:: media\Contro019.png
   :width: 0.94488in
   :height: 0.62992in
.. |Contro020| image:: media\Contro020.png
   :width: 1.29528in
   :height: 0.62598in
.. |Contro021| image:: media\Contro021.png
   :width: 0.98031in
   :height: 1.2874in
.. |Contro022| image:: media\Contro022.png
   :width: 1.20472in
   :height: 0.80315in
.. |Contro023| image:: media\Contro023.png
   :width: 0.70079in
   :height: 0.92126in
.. |Contro024| image:: media\Contro024.png
   :width: 0.90157in
   :height: 0.67717in
.. |Contro025| image:: media\Contro025.png
   :width: 0.62205in
   :height: 0.12598in
.. |Contro026| image:: media\Contro026.png
   :width: 0.62205in
   :height: 0.12598in
.. |Contro027| image:: media\Contro027.png
   :height: 0.30315in
.. |Contro028| image:: media\Contro028.png
   :width: 0.37795in
   :height: 0.34252in
.. |Contro029| image:: media\Contro029.png
   :width: 0.51575in
   :height: 0.11024in
.. |Contro030| image:: media\Contro030.png
   :width: 0.72047in
   :height: 0.12598in
.. |image101| image:: media\Contro031.png
   :width: 0.49213in
   :height: 0.1063in
.. |Contro032| image:: media\Contro032.png
   :width: 0.55118in
   :height: 0.16142in
.. |image103| image:: media\Contro033.png
   :width: 0.93307in
   :height: 0.18898in
.. |Contro034| image:: media\Contro034.png
   :width: 0.93307in
   :height: 0.16142in
.. |image105| image:: media\Contro035.png
   :width: 0.81496in
   :height: 1.20079in
.. |image106| image:: media\Contro036.png
   :width: 0.36614in
   :height: 0.12992in
.. |image107| image:: media\Contro037.png
   :width: 0.96063in
   :height: 0.83858in
.. |Contro038| image:: media\Contro038.png
   :width: 1.15611in
   :height: 0.61451in
