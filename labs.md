---
layout: page
title: Labs
description: Listing of course modules and topics.
---

# Labs

欢迎各位同学选修编译原理课程并参与相应的编码实习！

本实习分为两个部分。第一部分，你将熟悉yacc和lex工具的基本使用，并在此基础上利用词法分析和语法分析的相关知识实现一个简易的计算器。第二部分，你将进一步完成对C语言的子集的词法分析和语法分析，并构建出抽象语法树（Abstract Syntax Tree）。

## Ⅰ）环境说明与工具介绍
### 编程语言与运行环境
考虑到大家对各种语言的熟悉程度，本次实习统一使用Python语言。  
使用Windows系统或Mac系统来完成本lab都可以。

### Lex & Yacc
Lex和Yacc是最流行的对目标语言进行词法分析和语法分析的工具。其详细使用说明可参考[lex&yacc_manual.pdf](./assets/lex&yacc_manual.pdf) 文件。**注意**：由于lex和yacc是由c语言实现的，而在本次实习中我们统一使用Python语言，因此在实际操作中我们用python包PLY（见下）来实现同样的功能，对于lex和yacc的具体使用只需简单了解即可。
### PLY
Python包PLY提供了对Lex和Yacc的纯Python实现。本次实习推荐使用ply-3.11。其安装过程如下：
1. 下载[ply-3.11.zip](./assets/ply-3.11.zip)
2. 解压此zip文件
3. 打开终端（即windows电脑的cmd/powershell，或mac电脑的terminal），进入文件夹ply-3.11中
4. 执行如下指令：python setup.py install
5. 完成上述指令后，你可以通过 import ply.lex 等方式简单地判断是否安装成功。

PLY包的详细使用说明参见[以下链接](https://www.dabeaz.com/ply/ply.html)。
## Ⅱ） 实习1：简易计算器
### 任务描述
借助PLY包实现一个简易的计算器，支持加减乘除、括号、幂运算。所有测试数据见[test_calc.zip](#)文件。
### 样例输入与输出

|  输入   | 输出  |
|  :----:  | :----:  |
| 1+5-2+7  | 11 |
| 3\*5+5\*9+60/2  | 90 |
| 6\*(5+7)+18/(4/2)\*3  | 99 |
| (5+7)^(6/3)  | 144 |

### 评分标准
1. （40%）实现非负整数的加减运算（不含括号）
2. （60%）实现非负整数的加减乘除运算（不含括号）
3. （80%）实现任意整数的加减乘除运算（不含括号）
4. （90%）实现任意整数的加减乘除运算（含括号）
5. （100%）实现任意整数的加减乘除及幂运算（含括号，幂运算用^算符表示）

### Hints
首先用PLY中的lex模块实现分词。其次写出符合四则运算法则的文法（注意运算符优先级的处理，比如乘除高于加减，幂运算高于乘除，括号最高）。然后利用PLY中的YACC模块实现语法分析，并在归约的同时计算结果或先建出AST树再计算结果等。
## Ⅲ） 实习2：简化的C语言编译器
### 任务描述
使用PLY构造一个简化的C语言编译器。只需要进行词法分析和语法分析，如果分析失败，则报出错误；如果分析成功，则给出源码中的符号列表和语句列表。  
不需要支持数组、结构体、联合、文件、集合、switch语句、do while语句、位运算等操作。可能出现的数据类型只有int型。忽略源码中的空格、tab、换行符等空白符（whitespace）。
### 文法描述
下面给出简化C语言的完整语法，其中以大写字母开头的为非终结符，其它均为终结符。

~~~ebnf
Function        ::= Type identifier "(" ArgList ")" CompoundStmt
                    | Type identifier "(" ")" CompoundStmt;
ArgList         ::= Arg
                    | ArgList "," Arg;
Arg             ::= Type identifier;
Declaration     ::= Type IdentList;
Type            ::= "int" | "float";
IdentList       ::= identifier "," IdentList
                    | identifier;
Stmt            ::= ForStmt
                    | WhileStmt
                    | Expr ";"
                    | IfStmt
                    | CompoundStmt
                    | Declaration
                    | ";" /* 空语句 */ ;
ForStmt         ::= "for" "(" OptExpr ";" OptExpr ";" OptExpr ")" Stmt;
OptExpr         ::= Expr
                    | ε /* 可能为空 */ ;
WhileStmt       ::= "while" "(" Expr ")" Stmt;
IfStmt          ::= "if" "(" Expr ")" Stmt ElsePart;
ElsePart        ::= "else" Stmt
                    | ε /* 可能为空 */ ;
CompoundStmt    ::= "{" StmtList "}";
StmtList        ::= StmtList Stmt
                    | ;
Expr            ::= identifier "=" Expr
                    | Rvalue;
Rvalue          ::= Rvalue Compare Mag
                    | Mag;
Compare         ::= "==" | "<" | ">" | "<=" | ">=" | "!=";
Mag             ::= Mag "+" Term 
                    | Mag "-" Term
                    | Term;
Term            ::= Term "*" Factor
                    | Term "/" Factor
                    | Factor;
Factor          ::= "(" Expr ")"
                    | "-" Factor
                    | "+" Factor
                    | identifier
                    | number;
~~~

**相关说明**
1. 数字可能是8进制（Oct）、10进制（Dec）、16进制（Hex）。其中8进制数以0开头，10进制数以1~9开头，16进制数以0x或0X开头。如：019(Oct) = 17(Dec) = 0x11(Hex)；-025(Oct) = -21(Dec) = -0x15(Hex)。

### 测试数据
所有测试数据见[test_subsetc.zip](./assets/test_subsetc.zip)。
### 评分标准
1. （40%）考察在测试数据上的正确率（这一部分只考察是否会在语法正确的测试点上报错或将语法错误的测试点判定为正确）
2. （30%）考察建出的抽象语法树（AST）的合理性。（实际评测时以[test_subsetc.zip](./assets/test_subsetc.zip)中`ast`开头的测试点为输入，人工分析其合理性）。
3. （10%）在AST的基础上进行一定的类型检查，主要是整型数据和浮点型数据的计算问题。
4. （20%）提交一份实习报告。简述实验过程、遇到的困难及解决方案、实验感想、对课程的建议等。这一部分没有严格的要求，推荐页数是3~4页，不及或超出都可，不会影响评分。

### Hints
