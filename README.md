# Klang
stock K-line language 

Klang 是Kline语言,即股票语言,支持方便快捷的股票编程,可以轻松搞定股票公式

是股票编程和量化交易的必备工具

Klang 最大的亮点是支持中文编程,可以看最后的完整例子

# 在线试用
* 内置中文编程公式
你可以在线体验 Klang 编程, 无需安装,网站内置了多个选股公式 [Online](http://klang.org.cn/online.html)


# Klang 是股票K线形态语言,基于 python3
* 语言解释器采用 python的 ply库，他采用了flex，yacc语法的实现
* 股票数据来源 baostock,zhanluejia
* 支持 同花顺、通达信、文华财经等的公式, 感谢funcat 移植到了 Python 版本
* 支持结构形态 感谢talib
* Klang 语言是基于Python语法拓展了部分自己的语法，并且编写了快捷的股票公式 
* 支持网页直接使用,交互方便
* 支持中文编程,一看就懂

# install
* 安装过程中遇到talib错误，需要搜索talib源代码解决
```
git clone https://github.com/asmcos/Klang
cd Klang
pip3 install -r requirements.txt 
python3 setup.py install
```


# 应用示例 python  版本
* 第一次会下载数据,预计需要几分钟
```
from Klang import *

C/C[1]

```
以上代码就快捷的计算了今日收盘价/昨日收盘价
默认计算第一个股票 sh.600000
今日收盘价，更新时间为下午5点左右

### 设置当前股票 'sh.601012','sz.000100'

```
Kl.code('sh.601012')
```
你也可以设置你想要的股票code

### 设置时间，周期默认是 2021-01-01开始
* 默认数据是日K

```
Kl.date(start='2021-04-02',end='2021-05-01')
```

### 显示收盘价

```
print(C.data) #显示已经下载周期的所有收盘价
print(O.data) #显示所有周日的开盘价
```

### 收盘价涨幅

```
print((C-C[1])/C[1]) #当前周期的收盘 比上一个周一的收盘价涨跌比率
```

### REF 和通达信兼容，获取之前周期的价格

```
REF(C,5) # 5周期前的收盘价，5日前收盘价
```

### 同时实现了MA,SMA,HHV,LLV,ABS等


# Klang 语言(K浪)
* testparse.py 代码片段

```
from Klang.lang import kparser,setPY,Kexec

# Klang和python的桥梁，支持函数,变量共享
# 具体使用方法 参见 testparse.py

def getpyglobals(name):
    return globals().get(name)

def setpyglobals(name,val):    
    globals()[name]=val


setPY(getpyglobals,setpyglobals) 

```

###  执行 K语言语句
* 赋值
```
pi = 3.14 #python
Kexec("ABCD=pi+1;") #调用K语言执行，执行后K变量共享给了python

```
调用 Kexec执行Klang语法，Kexec参数里穿的字符串语法遵守Klang语法规则


### 执行一段代码
* 详细例子参见 testparse.py
* 这里我们定制了一段kloop...endp，表示循环计算所有的A股
```
testblock=\
"""
kloop
ret = CROSS(C,MA(C,60))
ma5 := MA(C,5)
ma10 = MA(C,10)
ma20 = MA(C,20)
ma60 = MA(C,60)
ret1 = (C - C[1])/C[1]
ret2 = (C - C[1])/C[1] > 0.05
info = getstockinfo(0)
if ma60[-1] < ma20[-1] and ma20[-1] < ma10[-1] and ma10[-1] < ma5[-1] and ret2 :
    print(info,ret1*100)
endp
print("计算完成")
"""

Kexec(testblock)

```



### 支持功能列表
* 逐步兼容通达信公式，C,MA,CROSS
* 兼容 := 赋值语法
* 支持兼容 python函数,getstockinfo等 getstockinfo是在python里面自定义的函数
* kloop,endp 是自定义语法，会遍历整个A股的股票，


### examples
* 使用了网页+websocket server模式
* server里调用 Kexec执行 所有的输入
* 默认的输入会扫描所有的A股4000多只股票
* 计算结果回传到网页界面显示

```
python3 wsserver.py
```

浏览 online.html 即可运算公式了
# 支持中文编程

```

        涨停 = 9.9
        今日收盘 = C
        昨日收盘 = C[1]
        日期范围 = date
        显示 = DISPLAY
        ###############################
        # 参数涨停,表示涨幅的幅度
        # 以上为参数的值可以修改
        # 日期范围('2021-01-01','2021-06-25')
        ###############################

        实际涨幅 = (今日收盘-昨日收盘) / 昨日收盘
        实际涨幅 = 实际涨幅 * 100
        if 实际涨幅 > 涨停:
            显示(实际涨幅)
```

# 更新
* 2021.7.3 增加了板块和概念信息，板块来自通达信 
