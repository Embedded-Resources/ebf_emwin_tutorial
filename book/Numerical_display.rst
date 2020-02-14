.. vim: syntax=rst

数值显示
----

上一章我们讲解了如何在LCD屏幕上显示字符串，这一章我们来讲解emWin数值显示。emWin支持十进制、浮点数、十六进制和二进制的数值显示。虽然我们可以用C库中的sprintf 函数将数值转换成字符串，但是emWin的数值显示API可以节省部分内存空间和运行时间。

数值显示API
~~~~~~~

emWin所有的数值显示API函数如表格 9‑1所示。

表格 9‑1数值显示API函数列表

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 函数名                 | 描
     - |

   * - 显示十进制数           |
     - |

   * - GUI_DispDec()
     - 在当前位置显示指定位数的十进制数                   |

   * - GUI_DispDecAt()
     - 在指定位置显示指定位数的十进制数                   |

   * - GUI_DispDecMin()
     - 在当前位置显示最小位数的十进制数                   |

   * - GUI_DispDecShift()
     - 在当前位置显示指定位数的十进制长数，带小数点       |

   * - GUI_DispDecSpace()
     - 在当前位置显示指定位数的十进制数，用空格代替前导零 |

   * - GUI_DispSDec()
     - 在当前位置显示指定位数的十进制数，带符号           |

   * - GUI_DispSDecShift()
     - 在当前位置显示指定位数的十进制长数，带小数点和符号 |

   * - 显示浮点数             |
     - |

   * - GUI_DispFloat()
     - 在当前位置显示指定位数的浮点数                     |

   * - GUI_DispFloatFix()
     - 在当前位置显示指定小数点右侧位数的浮点数           |

   * - GUI_DispFloatMin()
     - 在当前位置显示最小位数的浮点数                     |

   * - GUI_DispSFloatFix()
     - 在当前位置显示指定小数点右侧位数的浮点数，带符号   |

   * - GUI_DispSFloatMin()
     - 在当前位置显示最小位数的浮点数，带符号             |

   * - 显示二进制数           |
     - |

   * - GUI_DispBin()
     - 在当前位置显示二进制数                             |

   * - GUI_DispBinAt()
     - 在指定位置显示二进制数                             |

   * - 显示十六进制数         |
     - |

   * - GUI_DispHex()
     - 在当前位置显示十六进制数                           |

   * - GUI_DispHexAt()
     - 在指定位置显示十六进制数                           |

   * - emWin版本              |
     - |

   * - GUI_GetVersionString()
     - 返回emWin的当前版本                                |


数值显示实验
~~~~~~

代码设计
^^^^

代码清单 9‑1展示了emWin的十进制显示API函数的用法。

代码清单 9‑1 十进制显示函数（MainTask.c文件）

1 /*\*

2 \* @brief 十进制数显示

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void Disp_DecNum(void)

8 {

9 GUI_SetFont(GUI_FONT_24B_ASCII);

10 GUI_SetColor(GUI_WHITE);

11

12 GUI_DispStringAt("GUI_DispDec():", 0, 0);

13 GUI_DispNextLine();

14 GUI_DispDec(12345, 6);

15 GUI_GotoX(12 \* 9);

16 GUI_DispDec(-12345, 6);

17

18 GUI_DispStringAt("GUI_DispDecAt():", 0, 24 \* 2);

19 GUI_DispDecAt(12345, 0, 24 \* 3, 6);

20 GUI_DispDecAt(-12345, 12 \* 9, 24 \* 3, 6);

21

22 GUI_DispStringAt("GUI_DispDecMin():", 0, 24 \* 4);

23 GUI_DispNextLine();

24 GUI_DispDecMin(12345);

25 GUI_GotoX(12 \* 9);

26 GUI_DispDecMin(-12345);

27

28 GUI_DispStringAt("GUI_DispDecShift():", 0, 24 \* 6);

29 GUI_DispNextLine();

30 GUI_DispDecShift(12345, 7, 2);

31 GUI_GotoX(12 \* 9);

32 GUI_DispDecShift(-12345, 7, 2);

33

34 GUI_DispStringAt("GUI_DispDecSpace():", 0, 24 \* 8);

35 GUI_DispNextLine();

36 GUI_DispDecSpace(12345, 6);

37

38 GUI_DispStringAt("GUI_DispSDec():", 0, 24 \* 10);

39 GUI_DispNextLine();

40 GUI_DispSDec(12345,6);

41

42 GUI_DispStringAt("GUI_DispSDecShift():", 0, 24 \* 12);

43 GUI_DispNextLine();

44 GUI_DispSDecShift(12345, 7, 2);

45 }

46

所有十进制显示函数的数值类型都是有符号int型，即可输入的数值范围为\ :math:`- 2^{31}`\ 至\ :math:`2^{31} - 1`\ 。使用这些函数需要注意以下问题：

(1) 待显示数值的最高位不能是0，否则无法显示。例如需要显示的数是012345，不管指定的显示位数设置为多少，函数都无法显示。

(2) 指定的显示位数必须大于等于待显示数值的位数，否则无法显示。例如待显示数为12345，当指定显示位数小于5时，函数无法显示，大于5时，会在待显示数值的最高位补零，其中GUI_DispDecSpace()函数会在最高位补空格。

(3) 如果待显示数值中含有负号或小数点，包括使用GUI_DispDecShift()和GUI_DispSDecShift()，在指定显示位数时需要把这些符号也计算在内，否则函数无法正常显示。

浮点数显示函数的用法如代码清单 9‑2所示。

代码清单 9‑2 浮点数显示函数（MainTask.c文件）

1 /*\*

2 \* @brief 浮点数显示

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void Disp_FloatingNum(void)

8 {

9 int x0 = 245;

10

11 GUI_SetFont(GUI_FONT_24B_ASCII);

12 GUI_SetColor(GUI_WHITE);

13

14 GUI_GotoX(x0);

15 GUI_DispStringAt("GUI_DispFloat():", x0, 0);

16 GUI_DispNextLine();

17 GUI_GotoX(x0);

18 GUI_DispFloat(123.4567, 9);

19 GUI_GotoX(x0 + 12 \* 12);

20 GUI_DispFloat(-123.4567, 9);

21

22 GUI_DispStringAt("GUI_DispFloatFix():", x0, 24 \* 2);

23 GUI_DispNextLine();

24 GUI_GotoX(x0);

25 GUI_DispFloatFix(123.4567, 9, 3);

26 GUI_GotoX(x0 + 12 \* 12);

27 GUI_DispFloatFix(-123.4567, 9, 3);

28

29 GUI_DispStringAt("GUI_DispFloatMin():", x0, 24 \* 4);

30 GUI_DispNextLine();

31 GUI_GotoX(x0);

32 GUI_DispFloatMin(123.4567, 3);

33 GUI_GotoX(x0 + 12 \* 12);

34 GUI_DispFloatMin(-123.4567, 3);

35

36 GUI_DispStringAt("GUI_DispSFloatFix():", x0, 24 \* 6);

37 GUI_DispNextLine();

38 GUI_GotoX(x0);

39 GUI_DispSFloatFix(123.4567, 9, 3);

40 GUI_GotoX(x0 + 12 \* 12);

41 GUI_DispSFloatFix(-123.4567, 9, 3);

42

43 GUI_DispStringAt("GUI_DispSFloatMin():", x0, 24 \* 8);

44 GUI_DispNextLine();

45 GUI_GotoX(x0);

46 GUI_DispSFloatMin(123.4567, 3);

47 GUI_GotoX(x0 + 12 \* 12);

48 GUI_DispSFloatMin(-123.4567, 3);

49 }

50

所有的浮点显示函数的数值参数类型都是float型。和十进制函数一样，浮点显示函数也需要注意显示位数和符号位的问题，而待显示数值最高位为零的问题对其没有影响。不过需要注意的是，当指定的显示位数大于待显示数值的位数时，GUI_DispFloat函数是在最右侧补零。

二进制和十六进制显示函数的使用如代码清单 9‑3所示。

代码清单 9‑3 二进制和十六进制显示函数（MainTask.c文件）

1 /*\*

2 \* @brief 二进制数和十六进制数显示

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void Disp_BinHexNum(void)

8 {

9 int x0 = 540;

10

11 GUI_SetFont(GUI_FONT_24B_ASCII);

12 GUI_SetColor(GUI_WHITE);

13

14 GUI_DispStringAt("GUI_DispBin():", x0, 0);

15 GUI_DispNextLine();

16 GUI_GotoX(x0);

17 GUI_DispBin(10, 8);

18

19 GUI_DispStringAt("GUI_DispBinAt():", x0, 24 \* 2);

20 GUI_DispBinAt(10, x0, 24 \* 3, 8);

21

22 GUI_DispStringAt("GUI_DispHex():", x0, 24 \* 4);

23 GUI_DispNextLine();

24 GUI_GotoX(x0);

25 GUI_DispHex(4779, 4);

26

27 GUI_DispStringAt("GUI_DispHexAt():", x0, 24 \* 6);

28 GUI_DispNextLine();

29 GUI_DispHexAt(4779, x0, 24 \* 7, 4);

30

31 GUI_DispStringAt("GUI_GetVersionString()", x0, 24 \* 8);

32 GUI_DispStringAt(GUI_GetVersionString(), x0, 24 \* 9);

33 }

34

二进制显示函数最大支持输入32 bit的十进制或十六进制数，十六进制显示函数最大支持输入16 bit十进制或十六进制数。实际上这两组函数也支持输入二进制数，只不过Keil 5的C编译器不支持直接表示二进制数，在模拟器中就能很好的显示。

以上就是emWin数值显示API函数的全部内容。数值显示API函数也属于emWin的基础功能之一，但是这些函数的限制较多不容易用好。不过我们还可以使用sprintf函数结合上一章的文本显示函数，这样对于数值本身的限制就不那么大了。

实验现象
^^^^

数值显示的实验现象如图 9‑1所示。可以看出各种进制间不同函数的显示效果。

|Numeri002|

图 9‑1 数值显示实验效果

.. |Numeri002| image:: media\Numeri002.png
   :width: 5.77165in
   :height: 3.46457in
