.. vim: syntax=rst

emWin在Windows上的仿真
========================

为了方便用户学习emWin，Segger官方设计了一个个相互独立的emWin应用小例子，一步步引导用户由浅入深熟悉各种emWin函数接口。当用户熟悉了emWin之后，还可以参考它提供的综合例程，从而开发出炫丽的应用，也提高了开发速度。更重要的是这些程序都可以直接在windows系统上仿真运行，初学者
不需要在毫不熟悉emWin的情况下，为移植它到嵌入式环境而烦恼。而且在验证开发的时候直接在windows上仿真，可以大大减少下载程序到嵌入式平台的次数，从而提高开发效率。得知此等好消息，是不是觉得学习更有信心了？赶紧来尝试一下。

搭建emWin的Windows仿真环境
~~~~~~~~~~~~~~~~~~~

emWin的Windows仿真版本是免费的，大家可以直接在Segger官网下载，下载链接：\ https://www.segger.com/downloads/emwin/\ 。

下载得到压缩包的文件名为：SeggerEval_WIN32_MSVC_MinGW_GUI_V548.zip，它是emWin 的V5.48版本，本教程中我们就以这个版本为例子讲解。

解压该压缩包，其文件内容说明见图 4‑1。

|simula002|

图 4‑1 emWin仿真版本的文件

emWin仿真版本的工程在Windows上是使用Visual c++作为编译器的，而现在Visual c++编译器一般都集成到Visual Studio开发环境中了，而且由于VC6.0实在是太老太丑，笔者无法忍受，所以在本教程中野火使用VS 2019 社区版 (Microsoft visual
studio community 2019)作为开发环境进行，请读者自行安装VS 2019 社区版开发环境。

使用VS 2019 社区版仿真emWin
~~~~~~~~~~~~~~~~~~~~

接下来我们介绍一下用VS 2019 社区版仿真emWin的步骤。

1) 解压文件。解压emWin仿真版本压缩包，注意解压后的文件路径不要出现中文，否则在编译时会出错的；

2) 打开仿真工程。安装了VS 2019 社区版集成环境后，打开emWin仿真版本的目录，找到“SimulationTrial.sln”文件，双击打开，打开后会弹出一个对话框提示VC++版本的问题，直接选择“确定”即可，见图 4‑2

|simula003|

图 4‑2 打开emWin仿真工程

1)

2)

3) 配置仿真工程属性。成功打开工程后，右键单击SimulationTrial项目，在弹出的选项中选择属性，见图 4‑3；

|simula004|

图 4‑3 配置emWin仿真工程属性

4) 在弹出的工程属性页面中，展开链接器选项，进入输入设置；

5) 在其中的附加依赖项中添加：legacy_stdio_definitions.lib，注意添加分号；

6) 在忽略所有默认库中填：否；

7) 在忽略特定默认库中添加：LIBC.lib;LIBCMTD.lib，具体见图 4‑4；

|simula005|

图 4‑4 配置工程链接器输入属性

8) 接下来转到链接器选项中的高级设置，修改映像具有安全异常处理程序的值为：否，具体见图 4‑5。

|simula006|

图 4‑5 配置仿真工程链接器高级设置

1)

2)

3)

4)

5)

6)

7)

8)

9) 开始编译工程。工程属性修改完成后，可以直接按快捷键“F5”，或者点击编译器中的“本地Windows调试器”按钮，编译并运行工程。接下来就开始了编译过程，正常的话都会编译通过，然后弹出一个emWin仿真运行的界面，见图 4‑6。

|simula007|

图 4‑6 运行仿真

按照我们以上的步骤，默认编译的是emWin官方的演示例程，它向我们展示了使用emWin库制作出来的GUI效果，跟嵌入式平台相比，受液晶屏及CPU运行速度的影响，一般其效果会好一些，读者可亲自品味一下。部分仿真效果图见图 4‑7。

|simula008|

图 4‑7 emWin演示例程仿真效果图

emWin仿真文件概览
~~~~~~~~~~~

现在我们来看看emWin仿真工程里的文件，它主要包含了Application、Config、GUI、Simulation目录及一些外部依赖项文件，见图 4‑8。

|simula009|

图 4‑8 emWin仿真工程文件介绍

从V5.38版本开始，emWin仿真工程的文件内容和结构出现了较大变化，下面我们就来介绍一下该工程下的各类文件。

1. **Application文件夹**

Application文件夹是默认演示例程的应用文件包。在上一个小节中，我们编译的文件都是在这个Application目录下的，它包含了一个官方演示例程的应用文件，展开Application目录后，可以到这些文件命名都很规范，文件内容一目了然。读者如果看到很炫酷的示例，可以到这里找找它的源代码来学习
(综合演示例程学习难度较大，适合有一定emWin基础的用户)。emWin仿真工程附带了8个官方演示例程，除了默认启动的例程以外，其余的演示例程路径位于：\ **\\Sample\Application**\ 中，Application文件列表见图 4‑9。

|simula010|

图 4‑9 Application目录下的文件

2. **Config文件夹**

Config文件夹是显示配置文件包。在Config文件夹下，有GUIConf.c、GUIConf.h、LCDConfig.c、LCDConfig.h及SIMConfig.c文件，其中前三个文件在实际的嵌入式平台是非常重要的，它们分别起到了分配存储空间、裁剪emWin功能模块和LCD底层驱动实现。而L
CDConfig.h文件没有内容，SIMConfig.c文件是VS 2019 社区版仿真环境需求的一些特定设置，在此我们不作探讨。关于GUIConf.c、GUIConf.h与LCDConfig.c文件的内容见代码清单 4‑1、代码清单 4‑2和代码清单 4‑3。

代码清单 4‑1 GUIConf.c文件内容

1 #include "GUI.h"

2

3 /\*

4 \*

5 \* Defines

6 \*

7 \\*

8 \*/

9 //

10 // Define the available number of bytes available for the GUI

11 //

12 #define GUI_NUMBYTES 0x280000

13

14 /\*

15 \*

16 \* Public code

17 \*

18 \\*

19 \*/

20 /\*

21 \*

22 \* GUI_X_Config

23 \*

24 \* Purpose:

25 \* Called during the initialization process in order to set up the

26 \* available memory for the GUI.

27 \*/

28 void GUI_X_Config(void)

29 {

30 //

31 // 32 bit aligned memory area

32 //

33 static U32 aMemory[GUI_NUMBYTES / 4];

34 //

35 // Assign memory to emWin

36 //

37 GUI_ALLOC_AssignMemory(aMemory, GUI_NUMBYTES);

38 }

39

40 /\* End of file \/

41

GUIConf.c文件主要包含了一个GUI_X_Config函数，在这个函数中向系统申请了一个大小为2.5MB(0x280000) 的静态空间aMemory，接着调用emWin的库函数GUI_ALLOC_AssignMemory把这个空间分配给emWin。所以emWin的库函数使用的存储空间都是aM
emory中分配的。在嵌入式平台上，我们需要根据其限制控制分配给emWin库的aMemory大小，修改宏GUI_NUMBYTES即可。根据我们的测试，STemWin的演示例程在野火STM32开发板平台上使用43KB即可正常运行。

代码清单 4‑2 GUIConf.h文件内容

1 #ifndef GUICONF_H

2 #define GUICONF_H

3

4 /\*

5 \*

6 \* Multi layer/display support

7 \*/

8 #define GUI_NUM_LAYERS 16 // Maximum number of available layers

9

10 /\*

11 \*

12 \* Multi tasking support

13 \*/

14 #define GUI_OS (1) // Compile with multitasking support

15

16 /\*

17 \*

18 \* Configuration of available packages

19 \*/

20 #define GUI_SUPPORT_TOUCH (1) // Support a touch screen (req.
win-manager)

21 #define GUI_SUPPORT_MOUSE (1) // Support a mouse

22 #define GUI_WINSUPPORT (1) // Window manager package available

23 #define GUI_SUPPORT_MEMDEV (1) // Memory devices available

24

25 /\*

26 \*

27 \* Configuration of window manager

28 \*/

29 #define WM_SUPPORT_NOTIFY_VIS_CHANGED (1) // Enable sending of WM_NOTIFY_VIS_CHANGED messages

30

31 /\*

32 \*

33 \* Default font

34 \*/

35 #define GUI_DEFAULT_FONT &GUI_Font6x8

36

37 #endif /\* Avoid multiple inclusion \*/

38

39 /\* End of file \/

40

GUIConf.h文件聚集了各种与emWin功能裁剪相关的宏，如GUI_NUM_LAYERS用于配置emWin的最大可用层数(多显示器及复合视图支持)，GUI_OS用于告诉emWin是否运行在带多任务系统的平台上等等，通过设置这些宏，可以方便地根据需要裁剪emWin的大小。

代码清单 4‑3 LCDConf.c 文件内容

1 #include "GUI.h"

2

3 /\*

4 \*

5 \* Layer configuration (to be modified)

6 \*

7 \\*

8 \*/

9 //

10 // Physical display size

11 //

12 #define XSIZE_PHYS 800

13 #define YSIZE_PHYS 480

14

15 //

16 // Color conversion

17 //

18 #if GUI_USE_ARGB

19 #define COLOR_CONVERSION GUICC_M8888I

20 #else

21 #define COLOR_CONVERSION GUICC_8888

22 #endif

23

24 //

25 // Display driver

26 //

27 #define DISPLAY_DRIVER GUIDRV_WIN32

28

29 /\*

30 \*

31 \* Configuration checking

32 \*

33 \\*

34 \*/

35 #ifndef VXSIZE_PHYS

36 #define VXSIZE_PHYS XSIZE_PHYS

37 #endif

38 #ifndef VYSIZE_PHYS

39 #define VYSIZE_PHYS YSIZE_PHYS

40 #endif

41 #ifndef VRAM_ADDR

42 #define VRAM_ADDR 0

43 #endif

44

45 #ifndef XSIZE_PHYS

46 #error Physical X size of display is not defined!

47 #endif

48 #ifndef YSIZE_PHYS

49 #error Physical Y size of display is not defined!

50 #endif

51 #ifndef COLOR_CONVERSION

52 #error Color conversion not defined!

53 #endif

54 #ifndef DISPLAY_DRIVER

55 #error No display driver defined!

56 #endif

57

58 /\*

59 \*

60 \* Public code

61 \*

62 \\*

63 \*/

64 /\*

65 \*

66 \* LCD_X_Config

67 \*

68 \* Purpose:

69 \* Called during the initialization process in order to set up the

70 \* display driver configuration.

71 \*

72 \*/

73 void LCD_X_Config(void)

74 {

75 //

76 // Set display driver and color conversion for 1st layer

77 //

78 GUI_DEVICE_CreateAndLink(DISPLAY_DRIVER, COLOR_CONVERSION, 0, 0);

79 //

80 // Display driver configuration

81 //

82 LCD_SetSizeEx (0, XSIZE_PHYS, YSIZE_PHYS);

83 LCD_SetVSizeEx (0, VXSIZE_PHYS, VYSIZE_PHYS);

84 LCD_SetVRAMAddrEx(0, (void \*)VRAM_ADDR);

85 //

86 // Set user palette data (only required if no fixed palette is

87 used)

88 //

89 #if defined(PALETTE)

90 LCD_SetLUTEx(0, PALETTE);

91 #endif

92 }

93

94 /\*

95 \*

96 \* LCD_X_DisplayDriver

97 \*

98 \* Purpose:

99 \* This function is called by the display driver for several purposes.

100 \* To support the according task the routine needs to be adapted to

101 \* the display controller.
Please note that the commands marked with

102 \* 'optional' are not cogently required and should only be adapted if

103 \* the display controller supports these features.

104 \*

105 \* Parameter:

106 \* LayerIndex - Index of layer to be configured

107 \* Cmd - Please refer to the details in the switch statement

108 below

109 \* pData - Pointer to a LCD_X_DATA structure

110 \*

111 \* Return Value:

112 \* < -1 - Error

113 \* -1 - Command not handled

114 \* 0 - Ok

115 \*/

116 int LCD_X_DisplayDriver(unsigned LayerIndex, unsigned Cmd, void \*

117 pData)

118 {

119 int r;

120

121 switch (Cmd) {

122 //

123 // Required

124 //

125 case LCD_X_INITCONTROLLER: {

126 //

127 // Called during the initialization process in order to set up

128 the

129 // display controller and put it into operation.
If the

130 display

131 // controller is not initialized by any external routine this

132 needs

133 // to be adapted by the customer...

134 //

135 // ...

136 return 0;

137 }

138 case LCD_X_SETVRAMADDR: {

139 //

140 // Required for setting the address of the video RAM for

141 drivers

142 // with memory mapped video RAM which is passed in the 'pVRAM'

143 element of p

144 //

145 LCD_X_SETVRAMADDR_INFO \* p;

146 p = (LCD_X_SETVRAMADDR_INFO \*)pData;

147 //...

148 return 0;

149 }

150 case LCD_X_SETORG: {

151 //

152 // Required for setting the display origin which is passed in

153 the 'xPos' and 'yPos' element of p

154 //

155 LCD_X_SETORG_INFO \* p;

156 p = (LCD_X_SETORG_INFO \*)pData;

157 //...

158 return 0;

159 }

160 case LCD_X_SETLUTENTRY: {

161 //

162 // Required for setting a lookup table entry which is passed

163 in the 'Pos' and 'Color' element of p

164 //

165 LCD_X_SETLUTENTRY_INFO \* p;

166 p = (LCD_X_SETLUTENTRY_INFO \*)pData;

167 //...

168 return 0;

169 }

170 case LCD_X_ON: {

171 //

172 // Required if the display controller should support switching

173 on and off

174 //

175 return 0;

176 }

177 case LCD_X_OFF: {

178 //

179 // Required if the display controller should support switching

180 on and off

181 //

182 // ...

183 return 0;

184 }

185 default:

186 r = -1;

187 }

188 return r;

189 }

190

191 /\* End of file \/

192

LCDConfig.c文件包含了LCD_X_Config和LCD_X_DisplayDriver两个函数。LCD_X_Config函数调用emWin库函数GUI_DEVICE_CreateAndLink配置了显示驱动及颜色转换格式，该函数的输入参数是根据Windows系统的环境配置的。接着调用LCD
_SetSizeEx配置了显示尺寸，输入参数是本文件中的两个宏：#define XSIZE_PHYS 800 及 #define YSIZE_PHYS 480，也就是说把屏幕配置成了800*480分辨率大小，我们可以通过修改这两个宏的数值改变仿真时的屏幕尺寸。

LCD_X_DisplayDriver是提供给emWin库的驱动回调函数，该函数根据输入的参数Cmd区分要进行的操作，如LCD_X_INITCONTROLLER表示初始化液晶控制器，LCD_X_ON 表示启动液晶屏，LCD_X_OFF表示关闭液晶屏，这些命令是由用户根据需要选择性实现的。

在使用emWin的应用函数之前，我们需要调用其库函数GUI_Init初始化emWin运行的环境，而GUI_Init函数会执行我们前面提到的GUI_X_Config、LCD_X_Config、LCD_X_DisplayDriver三个函数，从而完成初始化工作。当我们移植emWin时，主要的移植工作就是
根据自己的平台实现这些函数，而emWin的上层应用函数无需修改，也无法修改，因为那些函数都已经编译进库里了，不开放源码。

3. **GUI文件夹**

GUI文件夹是emWin库核心文件包。它包含一个Include文件夹和一个Library文件夹，在Include文件夹里的都是emWin库的头文件，由于emWin以库的形式提供给用户，所以必须带有这些头文件以方便我们使用。Library目录下的GUI.lib就是emWin的库文件了，它是编译好的二进
制编码，无法查看源程序。在不同的CPU平台下，需要使用不同版本的库，如这个工程里的GUI.lib库仅可以用在windows的VC++编译平台下，针对Cortex-M0、M3和M4芯片及IAR、MDK编译环境都有相对应的库文件，使用时要根据自己的平台选择适当的库文件。

1.

2.

3.

4. **Sample文件夹**

Sample文件夹是emWin的基础应用示例程序包。我们使用仿真的一个重要目的就是学习这个文件夹下的工程代码。此文件夹从仿真工程的V5.38版本开始不再包含在工程中，文件结构也有所变化。目前的Sample文件夹包含3个子文件夹：Application，Tutorial和WinMain。这里的Appl
ication文件夹包含着另外7个官方演示例程，Tutorial文件夹内提供的都是一个个很小的工程，简化了学习的难度，而且这个文件夹下提供的应用示例更加丰富，基本上涵盖了emWin库提供的所有功能，当我们在emWin参考手册中看到某些功能不会使用时，可以来这里找找，一般都能找到示例程序，WinMai
n文件夹则是一些模拟器相关的文件，我们不作讨论。Sample文件夹组成内容见图 4‑10。

|simula011|

图 4‑10 Sample文件介绍

Hello emWin
~~~~~~~~~~~

在本节中我们将讲解如何使用emWin仿真工程中提供给我们的各种小例程，学习emWin的使用方法。

上一小节提到emWin仿真工程有Application演示程序及Sample范例程序，默认的时候，不用对工程作任何修改，工程编译的是Application演示程序，这在4.3
节中已经介绍。看到演示程序炫丽的界面，当我们尝试阅读演示程序的代码时，却发现它太庞大以致不知道从何入手，这时我们可以从简单点的Sample范例程序学习。这需要配置一下工程的编译选项，本小节我们以Sample目录下的HelloWorld示例进行讲解，对于学习其它Sample程序，其过程是一样的。

1. **配置工程文件**

1) 把Application中的文件从生成中排除。因为emWin文件包中每个例程都是是独立的工程，不能同时存在，所以我们想编译其他的例程时首先要把Application的文件排除在工程编译之外。操作步骤如下：全选Application下的文件，右键->属性，在弹出的对话框中“从生成中排除”一栏中填
“是”，这样就可以把这些文件排除在工程编译之外了，见图 4‑11。

|simula012|

图 4‑11 把Application下的文件从生成中排除

2) 新建Sample文件夹。操作步骤如下：右键单击SimulationTrial项目，在弹出的选项中选择添加->新建筛选器，然后将新建的文件夹命名为Sample，见图 4‑12。

|simula013|

图 4‑12 新建Sample文件夹

3) 添加将要学习的Sample例程。在本小节中，我们选择一个最简单的程序，用emWin库函数显示“HelloWorld”，这个程序是由BASIC_HelloWorld.c文件实现的，文件路径：\
**SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial**\ 。操作步骤如下：右键点击新建好的Sample文件夹->添加->现有项，然后根据路径找到BASIC_HelloWorld.c文件，把该文件添加到工程中，见图 4‑13。

|simula014|

图 4‑13 添加BASIC_HelloWorld.c文件到工程

4) 完成了上面步骤之后，我们会看到Application下的文件图标都有了一个禁止的小标签，Sample目录下的BASIC_HelloWorld.c文件图标则没有，接下来我们就可以编译工程，点击“本地Windows调试器”按钮或“F5”运行，在仿真界面可以看到它以黑底白字的形式显示了“Hello
World!”字样，见图 4‑14。

|simula015|

图 4‑14 编译BASIC_HelloWorld工程

如果读者想要尝试仿真其他的例程，直接按照上面给出的方法添加和排除文件即可。

2. **学习BASIC_HelloWorld工程代码**

运行BASIC_HelloWorld程序之后，自然比较好奇它的代码是如何实现的，打开BASIC_HelloWorld.c文件，发现它的代码非常简单，见。

代码清单 4‑4 BASIC_HelloWorld.c文件

1 #include "GUI.h"

2

3 /\*

4 \*

5 \* Defines

6 \*

7 \\*

8 \*/

9 //

10 // Recommended memory to run the sample with adequate performance

11 //

12 #define RECOMMENDED_MEMORY (1024L \* 5)

13

14 /\*

15 \*

16 \* Public code

17 \*

18 \\*

19 \*/

20 /\*

21 \*

22 \* MainTask

23 \*/

24 void MainTask(void)

25 {

26 GUI_Init();

27 //

28 // Check if recommended memory for the sample is available

29 //

30 if (GUI_ALLOC_GetNumFreeBytes() < RECOMMENDED_MEMORY) {

31 GUI_ErrorOut("Not enough memory available.");

32 return;

33 }

34 GUI_DispString("Hello world!");

35 while (1);

36 }

37

38 /\* End of file \/

39

在这个文件中的MainTask函数是主函数，就相当于平时我们熟悉的main函数一样，程序开始后就从MainTask开始执行，在这个函数里先是调用了GUI_Init函数，初始化了emWin所需要的运行环境，接着检查为例程分配的内存是否够用，然后调用emWin库函数GUI_DispString在屏幕输出
“Hello World!”的字符串。

.. |simula002| image:: media\simula002.png
   :width: 5.76806in
   :height: 3.67246in
.. |simula003| image:: media\simula003.png
   :width: 5.86614in
   :height: 2.32283in
.. |simula004| image:: media\simula004.png
   :width: 1.92708in
   :height: 3.71764in
.. |simula005| image:: media\simula005.png
   :width: 4.85039in
   :height: 3.52362in
.. |simula006| image:: media\simula006.png
   :width: 4.85039in
   :height: 3.52362in
.. |simula007| image:: media\simula007.png
   :width: 5.76806in
   :height: 2.94011in
.. |simula008| image:: media\simula008.png
   :width: 5.6953in
   :height: 3.63207in
.. |simula009| image:: media\simula009.png
   :width: 5.67925in
   :height: 2.45706in
.. |simula010| image:: media\simula010.png
   :width: 2.70866in
   :height: 2.71654in
.. |simula011| image:: media\simula011.png
   :width: 3.16038in
   :height: 2.13586in
.. |simula012| image:: media\simula012.png
   :width: 5.76806in
   :height: 3.3173in
.. |simula013| image:: media\simula013.png
   :width: 5.65094in
   :height: 4.41611in
.. |simula014| image:: media\simula014.png
   :width: 5.71698in
   :height: 3.89205in
.. |simula015| image:: media\simula015.png
   :width: 5.76806in
   :height: 2.94011in
