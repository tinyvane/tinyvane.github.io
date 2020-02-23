---
title: C#模拟键盘操作--SendKey(),SendKeys()
date: 2015-12-10 13:58:22
categories: 编程
tags: [c#, 键盘模拟]
urlname: simulation_of_key_press_in_c_sharp_using_sendkey
---

C#实现模拟键盘输入可以使用以下2个语法实现的，分别是：
SendKeys.Send(string keys);  //模拟汉字(文本)输入
SendKeys.SendWait(string keys); //模拟按键输入

先了解一下2个语法的用法吧！

（1）每个按键由一个或多个字符表示。为了指定单一键盘字符，必须按字符本身的键。例如，为了表示字母 A，可以用 "A" 作为 string。为了表示多个字符，就必须在字符后面直接加上另一个字符。例如，要表示 A、B 及 C，可用 "ABC" 作为 string。
（2）对 SendKeys 来说，加号 (+)、插入符 (^)、百分比符号 (%)、上划线 (~) 及圆括号 ( ) 都具有特殊意义。为了指定上述任何一个字符，要将它放在大括号 ({}) 当中。例如，要指定正号，可用 {+} 表示。方括号 ([ ]) 对 SendKeys 来说并不具有特殊意义，但必须将它们放在大括号中。在其它应用程序中，方括号有特殊意义，在出现动态数据交换 (DDE) 的时候，它可能具有重要意义。为了指定大括号字符，请使用 {}。
（3）为了在按下按键时指定那些不显示的字符，例如 ENTER 或 TAB 以及那些表示动作而非字符的按键，请使用下列代码：
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>按键</td>
<td>代码</td>
</tr>
<tr>
<td>BACKSPACE</td>
<td>{BACKSPACE}, {BS}, 或 {BKSP}</td>
</tr>
<tr>
<td>BREAK</td>
<td>{BREAK}</td>
</tr>
<tr>
<td>CAPS LOCK</td>
<td>{CAPSLOCK}</td>
</tr>
<tr>
<td>DEL or DELETE</td>
<td>{DELETE} 或 {DEL}</td>
</tr>
<tr>
<td>DOWN ARROW</td>
<td>{DOWN}</td>
</tr>
<tr>
<td>END</td>
<td>{END}</td>
</tr>
<tr>
<td>ENTER</td>
<td>{ENTER}或 ~</td>
</tr>
<tr>
<td>ESC</td>
<td>{ESC}</td>
</tr>
<tr>
<td>HELP</td>
<td>{HELP}</td>
</tr>
<tr>
<td>HOME</td>
<td>{HOME}</td>
</tr>
<tr>
<td>INS or INSERT</td>
<td>{INSERT} 或 {INS}</td>
</tr>
<tr>
<td>LEFT ARROW</td>
<td>{LEFT}</td>
</tr>
<tr>
<td>NUM LOCK</td>
<td>{NUMLOCK}</td>
</tr>
<tr>
<td>PAGE DOWN</td>
<td>{PGDN}</td>
</tr>
<tr>
<td>PAGE UP</td>
<td>{PGUP}</td>
</tr>
<tr>
<td>PRINT SCREEN</td>
<td>{PRTSC}</td>
</tr>
<tr>
<td>RIGHT ARROW</td>
<td>{RIGHT}</td>
</tr>
<tr>
<td>SCROLL LOCK</td>
<td>{SCROLLLOCK}</td>
</tr>
<tr>
<td>TAB</td>
<td>{TAB}</td>
</tr>
<tr>
<td>UP ARROW</td>
<td>{UP}</td>
</tr>
<tr>
<td>F1</td>
<td>{F1}</td>
</tr>
<tr>
<td>F2</td>
<td>{F2}</td>
</tr>
<tr>
<td>F3</td>
<td>{F3}</td>
</tr>
<tr>
<td>F4</td>
<td>{F4}</td>
</tr>
<tr>
<td>F5</td>
<td>{F5}</td>
</tr>
<tr>
<td>F6</td>
<td>{F6}</td>
</tr>
<tr>
<td>F7</td>
<td>{F7}</td>
</tr>
<tr>
<td>F8</td>
<td>{F8}</td>
</tr>
<tr>
<td>F9</td>
<td>{F9}</td>
</tr>
<tr>
<td>F10</td>
<td>{F10}</td>
</tr>
<tr>
<td>F11</td>
<td>{F1}</td>
</tr>
<tr>
<td>F12</td>
<td>{F12}</td>
</tr>
<tr>
<td>F13</td>
<td>{F13}</td>
</tr>
<tr>
<td>F14</td>
<td>{F14}</td>
</tr>
<tr>
<td>F15</td>
<td>{F15}</td>
</tr>
<tr>
<td>F16</td>
<td>{F16}</td>
</tr>
</tbody>
</table>
（4）SHIFT、CTRL 及 ALT 等按键结合的组合键，可在这些按键码的前面放置一个或多个代码，这些代码列举如下：
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>按键</td>
<td>代码</td>
</tr>
<tr>
<td>Shift</td>
<td>+</td>
</tr>
<tr>
<td>Ctrl</td>
<td>^</td>
</tr>
<tr>
<td>Alt</td>
<td>%</td>
</tr>
</tbody>
</table>
（5）输入汉字用SendKeys.Send("汉字");

样例代码：
<pre class="lang:default decode:true ">using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
namespace ApplicationForm
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        private void button1_Click(object sender, EventArgs e)
        {
            //光标移至richTextBox1
            richTextBox1.Focus();

            //模拟按下"ABCDEFG"
            SendKeys.SendWait("(ABCDEFG)");
            SendKeys.SendWait("{left 5}");
            SendKeys.SendWait("{h 10}");
            /*
            更多举例:
            SendKeys.SendWait("^C");  //Ctrl+C 组合键
            SendKeys.SendWait("+C");  //Shift+C 组合键
            SendKeys.SendWait("%C");  //Alt+C 组合键
            SendKeys.SendWait("+(AX)");  //Shift+A+X 组合键
            SendKeys.SendWait("+AX");  //Shift+A 组合键,之后按X键
            SendKeys.SendWait("{left 5}");  //按←键 5次
            SendKeys.SendWait("{h 10}");   //按h键 10次
            SendKeys.Send("汉字");  //模拟输入"汉字"2个字
            */
        }
    }
}</pre>
原文出处：http://www.cnblogs.com/liujie1111/p/4268174.html