# Excel按固定行数拆分为多个工作表的方法

今天从网上下了份《再要你命3000》的Excel版，准备开刷GRE单词。之前一直在用Anki背热心网友提供的3000词库，但是感觉按Anki默认的计划背下去要耗费太长的时间，不适合短期备考，决定还是先用Excel版快刷一遍，之后再用Anki进行复习。

然而下载下来的Excel工作簿里单词没有按List分类，全部单词都集中在一个工作页（Sheet1）内，背起来有点麻烦。于是从网上找来了一段宏代码，能实现**按固定行数将一个工作表拆分为多个工作表**的功能。

代码如下（固定行数设置为100）：

```vb
Sub split()
    Rows("1:100").Select
    Selection.Cut
    Sheets.Add After:=Sheets(Sheets.Count)
    ActiveSheet.Paste
    Sheet1.Select
    Sheet1.Rows("1:100").Delete
End Sub
```

**使用方法**

1. 打开Excel开发工具。

   **在Excel工具栏上右击 - 选择「自定义功能区」-  勾选「开发工具」**

   ![自定义功能区](https://raw.githubusercontent.com/Nobiel/Blog/master/images/自定义功能区.png)

   这样一来「开发工具」便会出现在工具栏上。

2. 新建一个宏。

   **在「开发工具」中点击「宏」- 在「宏名」中输入「split」- 点击「创建」- 在代码编辑窗口内输入代码 - 保存**

   ![开发工具](https://raw.githubusercontent.com/Nobiel/Blog/master/images/开发工具.png)

   ![创建宏](https://raw.githubusercontent.com/Nobiel/Blog/master/images/创建宏.png)

   ![编辑代码](https://raw.githubusercontent.com/Nobiel/Blog/master/images/编辑代码.png)

3. 设置快捷键。

   **在「开发工具」中点击「宏」- 选择刚刚创建的宏「split」- 点击「选项」- 设置快捷键 - 确定**

4. 删除空白工作页「Sheet2」和「Sheet3」。

5. 使用快捷键开始拆分。Enjoy！

   

