通过mfc设计的简易计算机，在vs2017中运行通过

&ensp;&ensp;&ensp;&ensp;对于刚刚上手MFC或C++的朋友们，通过程序编写制作一个简易的计算器是一个不错的选择。以下我将介绍简易计算器的设计流程，它能实现通过键盘输入或按键输入完成四则运算，以下通过VS2017的MFC应用程序模块完成。

## 1. 创建项目
&ensp;&ensp;&ensp;&ensp;通过文件->新建->项目建立MFC应用程序，在应用程序类型选择基于对话框，点击右下角的“完成”即可创建项目。
![在这里插入图片描述](https://github.com/trybesthbk/calculator/blob/master/%E5%9B%BE%E7%89%87/20200519102708594.png)
## 2. 界面设计
&ensp;&ensp;&ensp;&ensp;首先打开mfc界面窗口，在资源视图的Dialog文件夹中可以看到，如果找不到资源视图可以到视图->其他窗口->资源视图中打开。现在我们就可以开始设计界面。
![在这里插入图片描述](https://github.com/trybesthbk/calculator/blob/master/%E5%9B%BE%E7%89%87/20200121224017624.png)
&ensp;&ensp;&ensp;&ensp;界面设计如上图所示。MFC模块内置的丰富的工具,在工具箱的代码编辑器中拖动就可以添加，如果找不到可以电机菜单栏的视图->工具箱打开。这里我们只需要Edit Control和Button两种工具就可以完成。按键与对话框的名称可以通过右键->属性->外观->Caption进行编辑。
&ensp;&ensp;&ensp;&ensp;Edit Control是数据交互的控件，可以进行数据的输入与输出，Button是一个事件发生器，可以设置单击或双击等情况下需要处理的函数。
&ensp;&ensp;&ensp;&ensp;接下来需要给各个Button添加事件，一般情况下双击按键就能完成创建，或者右键属性栏上方的闪电符号（控件事件）进行添加。
&ensp;&ensp;&ensp;&ensp;如果添加错了控件事件需要删除，需要同时将“项目名Dlg.cpp”中对应的事件函数，BEGIN_MESSAGE_MAP中相应的事件以及“项目名Dlg.h”中对应的事件函数三个字段均删除。

## 3. 等式创建
&ensp;&ensp;&ensp;&ensp;首先将实现键盘输入完成四则运算，这里只需要用到上述的等号按键（Button）。
&ensp;&ensp;&ensp;&ensp;双击等号按键或右键属性栏上方的闪电符号（控件事件）创建事件函数，在函数中放入如下代码：
```javascript
	// TODO: 在此添加控件通知处理程序代码
	
	GetDlgItemText(IDC_EDIT1, str1);        //将第一编辑框的内容读取到str1(IDC与设计界面的ID匹配)
	GetDlgItemText(IDC_EDIT2, str2);
	GetDlgItemText(IDC_EDIT3, str3);
	number1 = _ttof(str1);                  //将Cstring格式转化为浮点型数字（unicode编码格式） 
	number2 = _ttof(str3);
	if (str2 != _T(""))                     //判断第二读取框的内容是否为空
	{
		if (str2 == _T("+"))               //如果非空做出相应的数据处理
		{
			result = number1 + number2;
		}
		else if (str2 == _T("-"))
		{
			result = number1 - number2;
		}
		else if (str2 == _T("*"))
		{
			result = number1 * number2;
		}
		else if (str2 == _T("/"))
		{
			result = number1 / number2;
		}
		else
		{
			MessageBox(_T("运算符输入有误！"));  //如果不是+-*/则显示输出错误
		}
	}

	str.Format(_T("%g"), result);     //实现数字等到CString的转换
	SetDlgItemText(IDC_EDIT4, str);   //将结果显示到第四编辑框中
```
&ensp;&ensp;&ensp;&ensp;相对应的在头文件的class 项目名Dlg中添加以下字段。
![在这里插入图片描述](https://github.com/trybesthbk/calculator/blob/master/%E5%9B%BE%E7%89%87/20200121224007998.png)  
&ensp;&ensp;&ensp;&ensp;这里程序采用unicode编码格式，这是一种将编码中用到的所有字符（如数组，英文，汉字）统一为一样的字长的编码形式，其中LPCTSTR就是将字符转化为unicode格式。在这里是为了便于将字符串准确转化为数字。
&ensp;&ensp;&ensp;&ensp;编码格式需要在项目->属性->常规->字符集中进行设置。
&ensp;&ensp;&ensp;&ensp;单击F5运行程序，在前三个编辑框依次输入数字（如“1.2”），运算符（+ - * /），数字（如“2.3”），单击等号在最后一个编辑框就可以得到答案。

## 4. 按键输入
&ensp;&ensp;&ensp;&ensp;首先完成数字按键输入工作
&ensp;&ensp;&ensp;&ensp;因为数字及小数点应该在第一个编辑框以及第三个编辑框都能生效，这里我们通过运算符（第二编辑框）是否输入进行判断，未输入则按键输入对第一对话框生效，已输入对第三对话框生效。
&ensp;&ensp;&ensp;&ensp;因为多个数字都要进行这样的输入，为简化代码，我们设置一个输入函数用于统一的数字输入，在“项目名Dlg.cpp”代码中添加代码如下：
```javascript
/*定义数字输入函数*/
void CMFCApplication1Dlg::input(char *a)    //这里的CMFCApplication1Dlg需改为“你的项目名Dlg”
{
	GetDlgItemText(IDC_EDIT2, str2);         //判断第二编辑框是否为空
	if (str2 == _T(""))                      //是的话对第一编辑框进行操作
	{
		str1 += LPCTSTR(a);                  //对字符串进行扩展
		number1 = _ttof(str1);               //将字符串转为数字
		SetDlgItemText(IDC_EDIT1, str1);     //更新第一编辑框
	}
	else                                     //否则对第三编辑框进行操作
	{
		str3 += LPCTSTR(a);
		number2 = _ttof(str3);
		SetDlgItemText(IDC_EDIT3, str3);
	}
}
```
&ensp;&ensp;&ensp;&ensp;同样在头文件中也要添加这样的字段:
![在这里插入图片描述](https://github.com/trybesthbk/calculator/blob/master/%E5%9B%BE%E7%89%87/20200121223951320.png)
&ensp;&ensp;&ensp;&ensp;双击数字及小数点按键或右键属性栏上方的闪电符号（控件事件）创建事件函数，在函数中放入如下代码（以数字0为例，其他类似）：
```javascript
	// TODO: 在此添加控件通知处理程序代码
	input("0");                     //输入数字0
```
&ensp;&ensp;&ensp;&ensp;接下来完成运算符按键输入工作
&ensp;&ensp;&ensp;&ensp;双击运算符按键或右键属性栏上方的闪电符号（控件事件）创建事件函数，在函数中放入如下代码（以运算符+为例，其他类似）：
```javascript
	// TODO: 在此添加控件通知处理程序代码
	str2 = _T("+");                  //将第二编辑框的内容设置为+并显示
	SetDlgItemText(IDC_EDIT2, str2);
```
&ensp;&ensp;&ensp;&ensp;接下来完成清空按键输入工作
&ensp;&ensp;&ensp;&ensp;双击清零（C）按键或右键属性栏上方的闪电符号（控件事件）创建事件函数，在函数中放入如下代码：
```javascript
// TODO: 在此添加控件通知处理程序代码
	str1 = _T("");                  //将四个编辑框的内容都设置为空并显示
	str2 = _T("");
	str3 = _T("");
	str = _T("");
	SetDlgItemText(IDC_EDIT1, str1);
	SetDlgItemText(IDC_EDIT2, str2);
	SetDlgItemText(IDC_EDIT3, str3);
	SetDlgItemText(IDC_EDIT4, str);
```
&ensp;&ensp;&ensp;以上便完成了简易计算器的设计工作。

参考资料: [http://www.c0ks.com/thread-1582-1-1.html](https://mp.csdn.net).
