# **nodeMcu API说明** #
[English Version](https://github.com/nodemcu/nodemcu-firmware/wiki/nodemcu_api_en)
###版本 0.9.2 build 2014-12-11
<a id="change_log"></a>
###变更日志: 
2014-12-11<br />
修正uart.setup(), 当引脚被设置为其他用途时，可以恢复uart功能。<br />
增加wifi.sta.status() 接口，获取sta模式的当前状态。

2014-12-09<br />
增加tmr.alarm 的个数到7个<br />

2014-12-08<br />
增加uart.setup(), uart.write() 接口。<br />

2014-12-07<br />
增加ow（1-wire）接口，来自arduino，接口相似。<br />
增加一个18b20的示例.<br />
修改net.socket.send() 的payload最大值，由256改为1460.<br />
修改gpio.mode() 接口，增加内部上拉参数选项。

2014-12-04<br />
修正串口输入lua时，存在的内存泄漏问题。

2014-12-02<br />
修正作为server时，内存恢复很慢的问题。恢复速度从几分钟到几秒钟。<br />
修改file.open的返回值，打开失败返回nil，成功返回true。<br />
修改开机版本号显示顺序，在执行init.lua之前显示版本号。<br />
修改wifi.ap.config()，成功后不自动重启。

2014-11-30<br />
修改pwm的最大频率为1000。<br />
修改pwm的占空比最大为1023。<br />
增加uart模块，提供uart.on("data")接口从串口接收数据。

2014-11-29<br />
修正tmr.delay小于1s无效的问题。<br />
修正PWM模块频率小于77Hz无法工作问题。

2014-11-25<br />
修正dns接口内存泄露问题。

2014-11-24<br />
修正配置wifi长密码问题，密码最大64字节，ssid最大32字节。<br />
修正dns问题，wiki里增加了dns的简单例子。

2014-11-23<br />
修正重启问题，短暂的解决方案：在tcp server状态下，如果系统内存不足，将不接受来自客户端的连接。<br />
修改file.list() ，不再直接在串口输出，只返回一个table。

2014-11-20<br />
修正tmr.delay，支持2s以上的延时，但是长延时可能会引起beacon timer out，导致与AP之间重新连接。<br />
增加tmr.wdclr()，用来重置看门狗计数器，用在长循环中，以防芯片因看门狗重启。<br />
修正net模块UDP无法连接问题。<br />
createServer(net.TCP, timeout)函数增加连接超时参数设置。

2014-11-19<br />
增加adc模块，adc.read(0)读取adc的值。<br />
wifi模块增加wifi.sta.getap() 函数，用于获取ap列表。

2014-11-18<br />
修正tcp服务器不能使用:close()函数关闭tcp连接的问题。<br />
tcp服务器: 服务器将关闭30s内未使用的闲置的连接。（修正前为180s）<br />
增加了函数node.input()用来向lua解释器输入lua代码段, 支持多行输入。<br />
增加了函数node.ouput(function)用来将串口输出重定向于回调函数。<br />
file.readline()函数返回值包含了EOL'\n', 当读到EOF时，返回nil。

2014-11-12<br />
全功能版本固件<br />

2014-11-11<br />
文件模块中增加了file.seek()函数。<br />
最多支持6个PWM输出。<br />

2014-11-10<br />
log模块更名为file模块<br />
文件操作支持多次读写。<br />
当前仅支持打开一个文件进行操作。<br />

2014-11-5<br />
node模块中删除了log函数。<br />
增加了log模块。<br />
修改wifi模块的函数。<br />
修改了node.key长按与短按的默认回调函数。<br />
只有当按钮被松开后，key才会被触发。<br />


###flash 错误
注意：有些模块在烧写之后启动，串口输出 ERROR in flash_read: r=。。。<br />
这是因为模块原来的flash内部没有擦除。<br />
可使用blank512k.bin，<br />
内容为全0xFF，从0x00000开始烧入。<br />
烧入之后可以正常运行。

# 概述
- 快速、自动连接无线路由器
- 基于Lua 5.1.4，使用者需了解最简单的Lua语法
- 采用事件驱动的编程模型
- 内置file, timer, pwm, i2c, net, gpio, wifi, uart, adc模块
- 串口波特率:9600-8N1
- 对模块的引脚进行编号；gpio，i2c，pwm等模块需要使用引脚编号进行索引
- 目前的编号对应表格:

<table>
  <tr>
    <th scope="col">IO index</th><th scope="col">ESP8266 pin</th><th scope="col">IO index</th><th scope="col">ESP8266 pin</th>
  </tr>
  <tr>
    <td>0</td><td>GPIO12</td><td>8</td><td>GPIO0</td>
  </tr>
  <tr>
    <td>1</td><td>GPIO13</td><td>9</td><td>GPIO2</td>
   </tr>
   <tr>
    <td>2</td><td>GPIO14</td><td>10</td><td>GPIO4</td>
  </tr>
  <tr>
    <td>3</td><td>GPIO15</td><td>11</td><td>GPIO5</td>
   </tr>
   <tr>
    <td>4</td><td>GPIO3</td><td></td><td></td>
  </tr>
  <tr>
    <td>5</td><td>GPIO1</td><td></td><td></td>
   </tr>
   <tr>
    <td>6</td><td>GPIO9</td><td></td><td></td>
  </tr>
  <tr>
    <td>7</td><td>GPIO10</td<td></td><td></td>
   </tr>
</table>
	
#固件烧写
###地址
nodemcu_512k.bin: 0x00000<br />

#node模块
<a id="nm_restart"></a>
## node.restart()
####描述
重新启动

####语法

node.restart()

####参数
nil

####返回值
nil

####示例
   
```lua
    node.restart();
```

####参见
**-**   []()

<a id="nm_dsleep"></a>
## node.dsleep()
####描述

进入睡眠模式，计时时间之后唤醒<br />

####语法

node.dsleep(us)<br />
**-注意:** 如需使用此功能，需要将esp8266的PIN32(RST)和PIN8(XPD_DCDC)短接。

####参数
us: 睡眠时间，单位：us

####返回值
nil

####示例

```lua
    node.dsleep(us);
```

####参见
**-**   []()

<a id="nm_chipid"></a>
## node.chipid()
####描述
返回芯片ID

####语法
node.chipid()

####参数
nil

####返回值
number:芯片ID

####示例

```lua
    id = node.chipid();
```

####参见
**-**   []()

<a id="nm_heap"></a>
## node.heap()
####描述
返回当前系统剩余内存大小，单位：字节

####语法
node.heap()

####参数
nil

####返回值
number: 系统剩余内存字节数

####示例

```lua
    heap_size = node.heap();
```

####参见
**-**   []()

<a id="nm_key"></a>
## node.key()
####描述
定义按键的功能函数, 按键与GPIO16相连。

####语法
node.key(type, function())

####参数
type: type取字符串"long"或者"short". long:按下按键持续3s以上, short: 短按按键(时间短于3s)<br />
function(): 用户自定义的按键回调函数。 如果为nil, 则取消用户定义的回调函数。<br />
默认函数：long：改变LED闪烁频率，short：重新启动。

####返回值
nil

####示例    
```lua
    node.key("long", function(){print('hello world')})
```

####参见
**-**   []()

<a id="nm_led"></a>
## node.led()
####描述
设置LED的亮/暗时间, LED连接到GPIO16, 与node.key()复用。

####语法
node.led(low, high)

####参数
Low: LED关闭时间，如设置为0，则LED处于常亮状态。单位：毫秒，时间分辨率：80~100ms<br />
High: LED打开时间，单位：毫秒，时间分辨率：80~100ms

####返回值
nil

####示例

```lua
    -- LED常亮.
    node.led(0);
```

####参见
**-**   []()

<a id="nm_input"></a>
## node.input()
####描述
接收字符串并将字符串传入lua解释器。<br />
功能同pcall(loadstring(str))，增加了支持多行输入的功能。

####语法
node.input(str)

####参数
str: Lua代码段

####返回值
nil

####示例

```lua
    -- 注意：该函数不支持在命令行中使用。
    sk:on("receive", function(conn, payload) node.input(payload) end)
```

####参见
**-**   []()

<a id="nm_output"></a>
## node.output()
####描述
将lua解释器输出重定向于回调函数。

####语法
node.output(function(str), serial_debug)

####参数
function(str): 接收lua解释器输出的str作为输入，可以将该输出通过socket发送。<br />
serial_debug: 1：将输出送至串口； 0：输出不送至串口

####返回值
nil

####示例

```lua
    function tonet(str)
      sk:send(str)
      -- print(str) 错误!!! 千万不要在此函数中再使用print函数
      -- 因为这样会导致函数的嵌套调用！！
    end
    node.ouput(tonet, 1)  -- serial also get the lua output.
```

####参见
**-**   []()

#file 模块
<a id="fl_remove"></a>
## file.remove()
####描述
删除文件。

####语法
file.remove(filename)

####参数
filename: 需要删除的文件。

####返回值
nil

####示例

```lua
    -- 删除foo.lua文件
    file.remove("foo.lua")
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.close()](#fl_close)

<a id="fl_open"></a>
## file.open()
####描述
打开文件。

####语法
file.open(filename, mode)

####参数
filename: 需要打开的文件，不支持文件夹。<br />
mode:<br />
   "r": read mode (the default)<br />
   "w": write mode<br />
   "a": append mode<br />
   "r+": update mode, 文件内的数据保留<br />
   "w+": update mode, 文件内的数据清除<br />
   "a+": append update mode, 文件内的数据保留，要写入的数据仅能增加在文件最后。

####返回值
nil: 文件打开失败，不存在
true: 文件打开成功

####示例

```lua
    -- 打开'init.lua'，并打印文件的第一行。
    file.open("init.lua", "r")
    print(file.readline())
    file.close()
```

####参见
**-**   [file.close()](#fl_close)<br />
**-**   [file.readline()](#fl_readline)

<a id="fl_close"></a>
## file.close()
####描述
关闭文件。

####语法
file.close()

####参数
nil

####返回值
nil

####示例

```lua
    -- 打开'init.lua'，并打印文件的第一行，然后关闭文件。
    file.open("init.lua", "r")
    print(file.readline())
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.readline()](#fl_readline)

<a id="fl_readline"></a>
## file.readline()
####描述
读取文件的一行。

####语法
file.readline()

####参数
nil

####返回值
逐行返回文件内容。返回值末尾包含EOL('\n')<br />
如果读到EOF返回nil。

####示例

```lua
    -- 打开'init.lua'，读取并打印文件的第一行，然后关闭文件。
    file.open("init.lua", "r")
    print(file.readline())
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.close()](#fl_close)

<a id="fl_writeline"></a>
## file.writeline()
####描述
向文件写入一行，行末尾增加'\n'。

####语法
file.writeline(string)

####参数
string: 需要写入的字符串

####返回值
true: 写入成功<br />
nil: 写入失败

####示例

```lua
    -- 以'a+'的模式打开'init.lua'
    file.open("init.lua", "a+")
    -- 将'foo bar'写到文件的末尾
    file.writeline('foo bar')
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.write()](#fl_write)

<a id="fl_write"></a>
## file.write()
####描述
向文件写入字符串。

####语法
file.write(string)

####参数
string: 需要写入的字符串

####返回值
true: 写入成功<br />
nil: 写入失败

####示例

```lua
    -- 以'a+'的模式打开'init.lua'
    file.open("init.lua", "a+")
    -- 将'foo bar'写到文件的末尾
    file.writeline('foo bar')
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.writeline()](#fl_writeline)

<a id="fl_flush"></a>
## file.flush()
####描述
清空缓存写入文件。

####语法
file.flush()

####参数
nil

####返回值
nil

####示例

```lua
    -- 以'a+'的模式打开'init.lua'
    file.open("init.lua", "a+")
    -- 将'foo bar'写到文件的末尾
    file.write('foo bar')
    file.flush()
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.writeline()](#fl_writeline)

<a id="fl_seek"></a>
## file.seek()
####描述
设置或者读取文件的读写位置，位置等于whence加上offset的值。

####语法
file.seek(whence, offset)

####参数
whence:<br />
"set": base is position 0 (beginning of the file);<br />
"cur": base is current position;(default value)<br />
"end": base is end of file;<br />
offset: default 0

####返回值
成功: 返回当前的文件读写位置<br />
失败: 返回nil

####示例

```lua
    -- 以'a+'的模式打开'init.lua'
    file.open("init.lua", "a+")
    -- 将'foo bar'写到文件的末尾
    file.write('foo bar')
    file.flush()
    --将文件读写位置设置在文件开始
    file.seek("set")
    --读取并打印文件的第一行
    print(file.readline())
    file.close()
```

####参见
**-**   [file.open()](#fl_open)<br />
**-**   [file.writeline()](#fl_writeline)

<a id="fl_list"></a>
## file.list()
####描述
显示所有文件。

####语法
file.list()

####参数
nil

####返回值
返回包含{文件名：文件大小}的lua table

####示例

```lua
    l = file.list();
    for k,v in pairs(l) do
      print("name:"..k..", size:"..v)
    end
```

####参见
**-**   [file.remove()](#fl_remove)

#wifi模块
##常量
wifi.STATION, wifi.SOFTAP, wifi.STATIONAP

<a id="wf_setmode"></a>
## wifi.setmode(mode)
####描述
设置wifi的工作模式。

####语法
wifi.setmode(mode)

####参数
mode: 取值为：wifi.STATION, wifi.SOFTAP or wifi.STATIONAP

####返回值
返回设置之后的mode值

####示例

```lua
    wifi.setmode(wifi.STATION)
```

####参见
**-**   [wifi.getmode()](#wf_getmode)


<a id="wf_getmode"></a>
## wifi.getmode(mode)
####描述
获取wifi的工作模式。

####语法
wifi.getmode()

####参数
nil

####返回值
返回wifi的工作模式

####示例

```lua
    print(wifi.getmode())
```

####参见
**-**   [wifi.setmode()](#wf_setmode)


<a id="wf_startsmart"></a>
## wifi.startsmart()
####描述
开始自动配置，如果配置成功自动设置ssid和密码。

####语法
wifi.startsmart(channel, function succeed_callback())

####参数

channel: 1~13, 启动寻找的初始频段，如果为nil默认值为6频段。每个频段搜寻20s。<br />
succeed_callback: 配置成功的回调函数，配置成功并连接至AP后调用此函数。

####返回值
nil

####示例

```lua
    wifi.startsmart(6, function() end)
```

####参见
**-**   [wifi.stopsmart()](#wf_stopsmart)


<a id="wf_stopsmart"></a>
## wifi.stopsmart()
####描述
停止配置。

####语法
wifi.stopsmart()

####参数
nil

####返回值
nil

####示例

```lua
    wifi.stopsmart()
```

####参见
**-**   [wifi.startsmart()](#wf_startsmart)


#wifi.sta 子模块

<a id="ws_config"></a>
## wifi.sta.config()
####描述
设置station模式下的ssid和password。

####语法
wifi.sta.config(ssid, password)

####参数

ssid: 字符串，长度小于32字节。<br />
password: 字符串，长度小于64字节。

####返回值
nil

####示例

```lua
    wifi.sta.config("myssid","mypassword")
```

####参见
**-**   [wifi.sta.connect()](#ws_connect)<br />
**-**   [wifi.sta.disconnect()](#ws_disconnect)


<a id="ws_connect"></a>
## wifi.sta.connect()
####描述
station模式下连接AP。

####语法
wifi.sta.connect()

####参数
nil


####返回值
nil

####示例

```lua
    wifi.sta.connect()
```

####参见
**-**   [wifi.sta.disconnect()](#ws_disconnect)<br />
**-**   [wifi.sta.config()](#ws_config)


<a id="ws_disconnect"></a>
## wifi.sta.disconnect()
####描述
station模式下与AP断开连接。

####语法
wifi.sta.disconnect()

####参数
nil


####返回值
nil

####示例

```lua
    wifi.sta.disconnect()
```

####参见
**-**   [wifi.sta.config()](#ws_config)<br />
**-**   [wifi.sta.connect()](#ws_connect)


<a id="ws_autoconnect"></a>
## wifi.sta.autoconnect()
####描述
station模式下自动连接。

####语法
wifi.sta.autoconnect(auto)

####参数
auto: 0：取消自动连接，1：使能自动连接。


####返回值
nil

####示例

```lua
    wifi.sta.autoconnect()
```

####参见
**-**   [wifi.sta.config()](#ws_config)<br />
**-**   [wifi.sta.connect()](#ws_connect)<br />
**-**   [wifi.sta.disconnect()](#ws_disconnect)


<a id="ws_getip"></a>
## wifi.sta.getip()
####描述
station模式下获取ip

####语法
wifi.sta.getip()

####参数
nil


####返回值
ip地址字符串，如:"192.168.0.111"

####示例

```lua
    -- print current ip
    print(wifi.sta.getip())
```

####参见
**-**   [wifi.sta.getmac()](#ws_getmac)


<a id="ws_getmac"></a>
## wifi.sta.getmac()
####描述
station模式下获取mac地址。

####语法
wifi.sta.getmac()

####参数
nil


####返回值
mac地址字符串，如:"18-33-44-FE-55-BB"

####示例

```lua
    -- 打印当前的mac地址
    print(wifi.sta.getmac())
```

####参见
**-**   [wifi.sta.getip()](#ws_getip)

<a id="ws_getap"></a>
## wifi.sta.getap()
####描述
扫描并列出ap，结果以一个lua table为参数传递给回调函数。

####语法
wifi.sta.getap(function(table))

####参数
function(table): 当扫描结束时，调用此回调函数<br />
    扫描结果是一个lua table，key为ap的ssid，value为其他信息，格式：authmode,rssi,bssid,channel


####返回值
nil

####示例

```lua
    -- print ap list
    function listap(t)
      for k,v in pairs(t) do
        print(k.." : "..v)
      end
    end
    wifi.sta.getap(listap)
```

####参见
**-**   [wifi.sta.getip()](#ws_getip)

<a id="ws_status"></a>
## wifi.sta.status()
####描述
station模式下获取当前连接状态。

####语法
wifi.sta.status()

####参数
nil

####返回值
number： 0~5
0: STATION_IDLE,
1: STATION_CONNECTING,
2: STATION_WRONG_PASSWORD,
3: STATION_NO_AP_FOUND,
4: STATION_CONNECT_FAIL,
5: STATION_GOT_IP.

####参见
**-**   []()

#wifi.ap 子模块

<a id="wa_config"></a>
## wifi.ap.config()
####描述
设置ap模式下的ssid和password

####语法
wifi.ap.config(cfg)

####参数
cfg: 设置AP的lua table

####示例:

```lua
     cfg={}
     cfg.ssid="myssid"
     cfg.pwd="mypwd"
     wifi.ap.setconfig(cfg)
```

####返回值
nil

####示例

```lua
    wifi.ap.config(ssid, 'password')
```

####参见
**-**    []()

<a id="wa_getip"></a>
## wifi.ap.getip()
####描述
ap模式下获取ip

####语法
wifi.ap.getip()

####参数
nil

####返回值
ip地址字符串，如:"192.168.0.111"

####示例

```lua
    wifi.ap.getip()
```

####参见
**-**   [wifi.ap.getmac()](#wa_getmac)


<a id="wa_getmac"></a>
## wifi.ap.getmac()
####描述
ap模式下获取mac地址。

####语法
wifi.ap.getmac()

####参数
nil

####返回值
mac地址字符串，如:"1A-33-44-FE-55-BB"

####示例

```lua
    wifi.ap.getmac()
```

####参见
**-**   [wifi.ap.getip()](#wa_getip)


#timer 模块
<a id="tm_delay"></a>
## tmr.delay()
####描述
延迟us微秒。

####语法
tmr.dealy(us)

####参数
us: 延迟时间，单位：微秒

####返回值
nil

####示例

```lua
    -- delay 100us
    tmr.delay(100)
```

####参见
**-**   [tmr.now()](#tm_now)


<a id="tm_now"></a>
## tmr.now()
####描述
返回系统计数器的当前值，uint32，单位：us。

####语法
tmr.now()

####参数
nil

####返回值
uint32: value of counter

####示例

```lua
    -- 打印计数器的当前值。
    print(tmr.now())
```

####参见
**-**   [tmr.delay()](#tm_delay)


<a id="tm_alarm"></a>
## tmr.alarm()
####描述
闹钟函数。<br />

####语法
tmr.alarm(id, interval, repeat, function do())

####参数
id: 定时器的id，0~6.
Interval: 定时时间，单位：毫秒。<br />
repeat: 0：一次性闹钟；1：重复闹钟。<br />
function do(): 定时器到时回调函数。

####返回值
nil

####示例

```lua
    -- 每1000ms输出一个hello world
    tmr.alarm(0, 1000, 1, function() print("hello world") end )
```

####参见
**-**   [tmr.now()](#tm_now)


<a id="tm_stop"></a>
## tmr.stop()
####描述
停止闹钟功能。<br />

####语法
tmr.stop(id)

####参数
id: 定时器的id，0~6.

####返回值
nil

####示例

```lua
    -- 每隔1000ms打印hello world
    tmr.alarm(1, 1000, 1, function() print("hello world") end )

    -- 其它代码

    -- 停止闹钟
    tmr.stop(1)
```

####参见
**-**   [tmr.now()](#tm_now)

<a id="tm_wdclr"></a>
## tmr.wdclr()
####描述
清除看门狗计数器。<br />

####语法
tmr.wdclr()

####参数
nil.

####返回值
nil

####示例

```lua
    for i=1,10000 do 
      print(i)
      tmr.wdclr()   -- 一个长时间的循环或者事务，需内部调用tmr.wdclr() 清除看门狗计数器，防止重启。
    end 
```

####参见
**-**   [tmr.delay()](#tm_delay)

#GPIO 模块
##常量
gpio.OUTPUT, gpio.INPUT, gpio.INT, gpio.HIGH, gpio.LOW


<a id="io_mode"></a>
## gpio.mode()
####描述
将pin初始化为GPIO并设置输入输出模式, 内部上拉方式。

####语法
gpio.mode(pin, mode, pullup)

####参数
pin: 0~11, IO编号<br />
mode: 取值为：gpio.OUTPUT or gpio.INPUT, or gpio.INT(中断模式)
pullup: 取值为：gpio.PULLUP or gpio.FLOAT, 默认为gpio.FLOAT

####返回值
nil

####示例

```lua
    -- 将GPIO0设置为输出模式
    gpio.mode(0, gpio.OUTPUT)

```

####参见
**-**   [gpio.read()](#io_read)


<a id="io_read"></a>
## gpio.read()
####描述
读取管脚电平高低。

####语法
gpio.read(pin)

####参数
pin: 0~11, IO编号

####返回值
number:0：低电平, 1：高电平。

####示例

```lua
    -- 读取GPIO0的电平
    gpio.read(0)
```

####参见
**-**   [gpio.mode()](#io_mode)


<a id="io_write"></a>
## gpio.write()
####描述
设置管脚电平

####语法
gpio.write(pin)

####参数
pin: 0~11, IO编号<br />
level: gpio.HIGH or gpio.LOW

####返回值
nil

####示例

```lua
    -- 设置GPIO 1为输出模式，并将输出电平设置为高
    pin=1
    gpio.mode(pin, gpio.OUTPUT)
    gpio.write(pin, gpio.HIGH)
```

####参见
**-**   [gpio.mode()](#io_mode)<br />
**-**   [gpio.read()](#io_read)


<a id="io_trig"></a>
## gpio.trig()
####描述
设置管脚中断模式的回调函数。

####语法
gpio.trig(pin, type, function(level))

####参数
pin: 0~11, IO编号<br />
type: 取值为"up", "down", "both", "low", "high", 分别代表上升沿、下降沿、双边沿、低电平、高电平触发方式。<br />
function(level): 中断触发的回调函数，GPIO的电平作为输入参数。如果此处没有定义函数，则使用之前定义的回调函数。

####返回值
nil

####示例

```lua
    -- 使用GPIO0检测输入脉冲宽度
    pulse0 = 0
    du = 0
    gpio.mode(0,gpio.INT)
    function pin0cb(level)
     du = tmr.now() – pulse0
     print(du)
     pulse0 = tmr.now()
     if level == 1 then gpio.trig(0, "down ") else gpio.trig(0, "up ") end
    end
    gpio.trig(0, "down ",pin0cb)

```

####参见
**-**   [gpio.mode()](#io_mode)<br />
**-**   [gpio.write()](#io_write)


#PWM模块
<a id="pw_setup"></a>
## pwm.setup()
####描述
设置管脚为pwm模式，最多支持6个pwm。

####语法
pwm.setup(pin, clock, duty)

####参数
pin: 0~11, IO编号<br />
clock: 1~1000, pwm频率<br />
duty: 0~1023, pwm占空比，最大1023（10bit）。

####返回值
nil

####示例

```lua
    -- 将管脚0设置为pwm输出模式，频率100Hz，占空比50-50
    pwm.setup(0, 100, 512)
```

####参见
**-**   [pwm.start()](#pw_start)


<a id="pw_close"></a>
## pwm.close()
####描述
退出pwm模式。

####语法
pwm.close(pin)

####参数
pin: 0~11, IO编号

####返回值
nil

####示例

```lua
    pwm.close(0)
```

####参见
**-**   [pwm.start()](#pw_start)


<a id="pw_start"></a>
## pwm.start()
####描述
pwm启动，可以在对应的GPIO检测到波形。

####语法
pwm.start(pin)

####参数
pin: 0~11, IO编号

####返回值
nil

####示例

```lua
    pwm.start(0)
```

####参见
**-**   [pwm.stop()](#pw_stop)


<a id="pw_stop"></a>
## pwm.stop()
####描述
暂停pwm输出波形。

####语法
pwm.stop(pin)

####参数
pin: 0~11, IO编号

####返回值
nil

####示例

```lua
    pwm.stop(0)
```

####参见
**-**   [pwm.start()](#pw_start)


<a id="pw_setclock"></a>
## pwm.setclock()
####描述
设置pwm的频率<br />
**-Note:** 设置pwm频率将会同步改变其他pwm输出的频率，当前版本的所有pwm仅支持同一频率输出。

####语法
pwm.setclock(pin, clock)

####参数
pin: 0~11, IO编号<br />
clock: 1~1000, pwm周期

####返回值
nil

####示例

```lua
    pwm.setclock(0, 100)
```

####参见
**-**   [pwm.getclock()](#pw_getclock)


<a id="pw_getclock"></a>
## pwm.getclock()
####描述
获取pin的pwm工作频率

####语法
pwm.getclock(pin)

####参数
pin: 0~11, IO编号

####返回值
number:pin的pwm工作频率

####示例

```lua
    print(pwm.getclock(0))
```

####参见
**-**   [pwm.setclock()](#pw_setclock)


<a id="pw_setduty"></a>
## pwm.setduty()
####描述
设置pin的占空比。

####语法
pwm.setduty(pin, duty)

####参数
pin: 0~11, IO编号<br />
duty: 0~1023, pwm的占空比, 最大为1023.

####返回值
nil

####示例

```lua
    pwm.setduty(0, 512)
```

####参见
**-**   [pwm.getduty()](#pw_getduty)


<a id="pw_getduty"></a>
## pwm.getduty()
####描述
获取pin的pwm占空比。

####语法
pwm.getduty(pin)

####参数
pin: 0~11, IO编号

####返回值
number: 该pin的pwm占空比，最大为1023.

####示例

```lua
    -- D0 连接绿色led
    -- D1 连接蓝色led
    -- D2 连接红色led
    pwm.setup(0,500,512)
    pwm.setup(1,500,512)
    pwm.setup(2,500,512)
    pwm.start(0)
    pwm.start(1)
    pwm.start(2)
    function led(r,g,b)
      pwm.setduty(0,g)
      pwm.setduty(1,b)
      pwm.setduty(2,r)
    end
    led(512,0,0) --  led显示红色
    led(0,0,512) -- led显示蓝色

```

####参见
**-**   [pwm.setduty()](#pw_setduty)


#net 模块
##常量
net.TCP, net.UDP

<a id="nt_createServer"></a>
## net.createServer()
####描述
创建一个server。

####语法
net.createServer(type, timeout)

####参数
type: 取值为：net.TCP 或者 net.UDP<br />
timeout: 1~28800, 当为tcp服务器时，客户端的超时时间设置。

####返回值
net.server子模块

####示例

```lua
    net.createServer(net.TCP, 30)
```

####参见
**-**   [net.createConnection()](#nt_createConnection)


<a id="nt_createConnection"></a>
## net.createConnection()
####描述
创建一个client。

####语法
net.createConnection(type, secure)

####参数
type: 取值为：net.TCP 或者 net.UDP<br />
secure: 设置为1或者0, 1代表安全连接，0代表普通连接。

####返回值
net.server子模块

####示例

```lua
    net.createConnection(net.UDP, 0)
```

####参见
**-**   [net.createServer()](#nt_createServer)


#net.server 子模块
<a id="ns_listen"></a>
## listen()
####描述
侦听指定ip地址的端口。

####语法
net.server.listen(port,[ip],function(net.socket))

####参数
port: 端口号<br />
ip:ip地址字符串，可以省略<br />
function(net.socket): 连接创建成功的回调函数，可以作为参数传给调用函数。

####返回值
nil

####示例

```lua
    -- 创建一个server
    sv=net.createServer(net.TCP, 30)  -- 30s 超时
    -- server侦听端口80，如果收到数据将数据打印至控制台，并向远端发送‘hello world’
    sv:listen(80,function(c)
      c:on("receive", function(sck, pl) print(pl) end)
      c:send("hello world")
      end)
```

####参见
**-**   [net.createServer()](#nt_createServer)


<a id="ns_close"></a>
## close()
####描述
关闭server

####语法
net.server.close()

####参数
nil

####返回值
nil

####示例

```lua
    -- 创建server
    sv=net.createServer(net.TCP, 5)
    -- 关闭server
    sv:close()
```

####参见
**-**   [net.createServer()](#nt_createServer)


#net.socket 子模块
<a id="nk_connect"></a>
## connect()
####描述
连接至远端。

####语法
connect(port, ip)

####参数
port: 端口号<br />
ip: ip地址字符串

####返回值
nil

####参见
**-**   [net.socket:on()](#nk_on)


<a id="nk_send"></a>
## send()
####描述
通过连接向远端发送数据。

####语法
send(string, function(sent))

####参数
string: 待发送的字符串<br />
function(sent): 发送字符串后的回调函数。

####返回值
nil

####参见
**-**   [net.socket:on()](#nk_on)


<a id="nk_on"></a>
## on()
####描述
向事件注册回调函数。

####语法
on(event, function cb())

####参数
event: 字符串，取值为: "connection", "reconnection", "disconnection", "receive", "sent"<br />
function cb(net.socket, [string]): 回调函数。第一个参数是socket.<br />
如果事件是"receive",  第二个参数则为接收到的字符串。

####返回值
nil

####示例

```lua
    sk=net.createConnection(net.TCP, 0)
    sk:on("receive", function(sck, c) print(c) end )
    sk:connect(80,"192.168.0.66")
    sk:send("GET / HTTP/1.1\r\nHost: 192.168.0.66\r\nConnection: keep-alive\r\nAccept: */*\r\n\r\n")
```

####参见
**-**   [net.createServer()](#nt_createServer)


<a id="nk_close"></a>
## close()
####描述
关闭socket。

####语法
close()

####参数
nil

####返回值
nil

####参见
**-**   [net.createServer()](#nt_createServer)


<a id="nk_dns"></a>
## dns()
####描述
获取当前域的ip

####语法
dns(domain, function(net.socket, ip))

####参数
domain: 当前域的名称<br />
function (net.socket, ip): 回调函数。第一个参数是socket，第二个参数是当前域的ip字符串。

####返回值
nil

####示例

```lua
    sk=net.createConnection(net.TCP, 0)
    sk:dns("www.nodemcu.com",function(conn,ip) print(ip) end)
    sk = nil
```

####参见
**-**   [net.createServer()](#nt_createServer)


#i2c模块
##常量
i2c.SLOW,  i2c.TRANSMITTER, i2c. RECEIVER.  FAST（400k）模式目前不支持。

<a id="ic_setup"></a>
## i2c.setup()
####描述
初始化i2c。

####语法
i2c.setup(id, pinSDA, pinSCL, speed)

####参数
id = 0<br />
pinSDA: 0~11, IO编号<br />
pinSCL: 0~11, IO编号<br />
speed:  i2c.SLOW

####返回值
返回设置的速度

####参见
**-**   [i2c.read()](#ic_read)


<a id="ic_start"></a>
## i2c.start()
####描述
启动i2c传输。

####语法
i2c.start(id)

####参数
id = 0

####返回值
nil

####参见
**-**   [i2c.read()](#ic_read)


<a id="ic_stop"></a>
## i2c.stop()
####描述
停止i2c传输。

####语法
i2c.stop(id)

####参数
id = 0

####返回值
nil

####参见
**-**   [i2c.read()](#ic_read)


<a id="ic_address"></a>
## i2c.address()
####描述
设置i2c地址以及读写模式。

####语法
i2c.address(id, device_addr, direction)

####参数
id=0<br />
device_addr: 设备地址。<br />
direction: i2c.TRANSMITTER：写模式；i2c. RECEIVER：读模式。

####返回值
true: 收到ack
false：没有收到ack

####参见
**-**   [i2c.read()](#ic_read)

<a id="ic_write"></a>
## i2c.write()
####描述
向i2c写数据。数据可以是多个数字, 字符串或者lua table。

####语法
i2c.write(id, data1, data2,...)

####参数
id=0<br />
data: 数据可以是多个数字, 字符串或者lua table。

####返回值
number：成功写入的字节个数

####示例

```lua
    i2c.write(0, "hello", "world")
```

####参见
**-**   [i2c.read()](#ic_read)


<a id="ic_read"></a>
## i2c.read()
####描述
读取len个字节的数据。

####语法
i2c.read(id, len)

####参数
id=0<br />
len: 数据长度。

####返回值
string:接收到的数据。

####示例

```lua
    id=0
    sda=1
    scl=0

    -- 初始化i2c, 将pin1设置为sda, 将pin0设置为scl
    i2c.setup(id,sda,scl,i2c.SLOW)

    -- 用户定义函数:读取地址dev_addr的寄存器reg_addr中的内容。
    function read_reg(dev_addr, reg_addr)
      i2c.start(id)
      i2c.address(id, dev_addr ,i2c.TRANSMITTER)
      i2c.write(id,reg_addr)
      i2c.stop(id)
      i2c.start(id)
      i2c.address(id, dev_addr,i2c.RECEIVER)
      c=i2c.read(id,1)
      i2c.stop(id)
      return c
    end

    -- 读取0x77的寄存器0xAA中的内容。
    reg = read_reg(0x77, 0xAA)
    print(string.byte(reg))

```

####参见
**-**   [i2c.write()](#ic_write)

#adc 模块
##常量
无

<a id="adc_read"></a>
## adc.read()
####描述
读取adc的值，esp8266只有一个10bit adc，id为0，引脚为TOUT，最大值1024

####语法
adc.read(id)

####参数
id = 0<br />

####返回值
adc 值 10bit，最大1024.

####参见
**-**   []()

#uart 模块
##常量
无

<a id="uart_setup"></a>
## uart.setup()
####描述
设置uart的波特率，字节长度，校验，停止位，是否echo。

####语法
uart.setup( id, baud, databits, parity, stopbits, echo )

####参数
id = 0, 只支持一个串口<br />
baud = 9600, 19200, 38400, 57600, 74880, 115200, 230400, 460800, 921600。115200以上未测试。<br />
databits = 5, 6, 7, 8。表示字节长度。<br />
parity = 0(none)。<br />
stopbits = 1(1 stopbit), 2(2 stopbit).<br />
echo = 0(关闭回显)。

####返回值
返回波特率

####参见
**-**   []()

<a id="uart_on"></a>
## uart.on()
####描述
设置uart的事件回调函数，目前支持"data"事件，表示uart收到了数据，以行为单位。

####语法
uart.on(method, function, [run_input])

####参数
method = "data", 表示uart接收到了数据<br />
function 为回调函数，"data" 的回调函数签名为function(data) end<br />
run_input: 0或1，0表示从uart输入的data不经过lua解释器执行，1表示输入的行会被送到lua解释器执行。

####返回值
nil

####示例
```lua
    uart.on("data",
      function(data)
        print("receive from uart:", data)
        if data=="quit" then 
          uart.on("data") 
        end
    end, 0)
```

####参见
**-**   []()

<a id="uart_write"></a>
## uart.write()
####描述
向串口写入数据。

####语法
uart.write( id, string1, string2... )

####参数
id = 0, 只支持一个串口<br />
string1：需要写入的字符串。

####返回值
nil

####参见
**-**   []()

#onewire 模块
##常量
无

<a id="ow_setup"></a>
## ow.setup()
####描述
将pin设置为one wire模式。<br />

####语法
ow.setup(pin)

####参数
pin: 1~10, IO 编号。<br />

####返回值
nil

####参见
**-**   []()


<a id="ow_reset"></a>
## ow.reset()
####描述
执行一次1-wire复位操作。 <br />

####语法
ow.reset(pin)

####参数
pin: 1~10, IO 编号<br />

####返回值
number: 如果有器件响应返回1，如果没有器件响应或者总线被拉低超过250us返回0。

####参见
**-**   []()


<a id="ow_skip"></a>
## ow.skip()
####描述
发送一个1-wire“忽略rom”的命令，可以用来寻址总线上的所有器件。 <br />

####语法
ow.skip(pin)

####参数
pin: 1~10, IO 编号<br />

####返回值
nil

####参见
**-**   []()


<a id="ow_select"></a>
## ow.select()
####描述
发送一个1-Wire“选择rom”的命令，执行此函数前请务必先执行ow.reset()函数。 <br />

####语法
ow.select(pin,rom)

####参数
pin: 1~10, IO 编号<br />
rom: 包含slave器件rom内容的8个字节长度的string。

####返回值
nil

####示例
```lua
-- 18b20 Example
pin = 9
ow.setup(pin)
count = 0
repeat
  count = count + 1
  addr = ow.reset_search(pin)
  addr = ow.search(pin)
  tmr.wdclr()
until((addr ~= nil) or (count > 100))
if (addr == nil) then
  print("No more addresses.")
else
  print(addr:byte(1,8))
  crc = ow.crc8(string.sub(addr,1,7))
  if (crc == addr:byte(8)) then
    if ((addr:byte(1) == 0x10) or (addr:byte(1) == 0x28)) then
      print("Device is a DS18S20 family device.")
        repeat
          ow.reset(pin)
          ow.select(pin, addr)
          ow.write(pin, 0x44, 1)
          tmr.delay(1000000)
          present = ow.reset(pin)
          ow.select(pin, addr)
          ow.write(pin,0xBE,1)
          print("P="..present)  
          data = nil
          data = string.char(ow.read(pin))
          for i = 1, 8 do
            data = data .. string.char(ow.read(pin))
          end
          print(data:byte(1,9))
          crc = ow.crc8(string.sub(data,1,8))
          print("CRC="..crc)
          if (crc == data:byte(9)) then
             t = (data:byte(1) + data:byte(2) * 256) * 625
             t1 = t / 10000
             t2 = t % 10000
             print("Temperature="..t1.."."..t2.."Centigrade")
          end                   
          tmr.wdclr()
        until false
    else
      print("Device family is not recognized.")
    end
  else
    print("CRC is not valid!")
  end
end
```

####参见
**-**   []()



<a id="ow_write"></a>
## ow.write()
####描述
向选定的slave写一个字节。 <br />

####语法
ow.write(pin, v, power)

####参数
pin:  1~10, IO 编号 <br />
v:  向slave器件发送的字节 <br />
power:  1，用于向寄生供电器件供电；0，不需要寄生供电。注意：请务必调用ow.depower()或者发起新的读写操作来取消寄生供电。

####返回值
nil

####参见
**-**   []()


<a id="ow_write_bytes"></a>
## ow.write_bytes()
####描述
向选定的slave写多个字节。<br />

####语法
ow.write_bytes(pin, buf, power)

####参数
pin:  1~10, IO 编号 <br />
buf:  向slave发送的多个字节的字符串 <br />
power:  1，用于向寄生供电器件供电；0，不需要寄生供电。注意：请务必调用ow.depower()或者发起新的读写操作来取消寄生供电。

####返回值
nil

####参见
**-**   []()


<a id="ow_read"></a>
## ow.read()
####描述
从选定的slave读取一个字节。  <br />

####语法
ow.read(pin)

####参数
pin:  1~10, IO 编号 <br />

####返回值
从slave读取的一个字节。

####参见
**-**   []()



<a id="ow_read_bytes"></a>
## ow.read_bytes()
####描述
从选定的slave读取多个字节。 <br />

####语法
ow.read_bytes(pin, size)

####参数
pin:  1~10, IO 编号 <br />
size:  需要从slave读取的字节的个数<br />

####返回值
从slave返回的多个字节的字符串。

####参见
**-**   []()


<a id="ow_depower"></a>
## ow.depower()
####描述
取消向总线供电。仅需在ow.write()或者ow.write_bytes()中的'power=1' 且 不再进行读写slave的情况下使用。<br />

####语法
ow.depower(pin)

####参数
pin:  1~10, IO 编号 <br />

####返回值
nil

####参见
**-**   []()


<a id="ow_reset_search"></a>
## ow.reset_search()
####描述
清除查找状态用于重新开始进行查找操作。<br />

####语法
ow.reset_search(pin)

####参数
pin:  1~10, IO 编号 <br />

####返回值
nil

####参见
**-**   []()


<a id="ow_target_search"></a>
## ow.target_search()
####描述
设置查找选项'family_code'，用于在下次调用ow.search()时查找该'family_code'的器件。<br />

####语法
ow.target_search(pin, family_code)

####参数
pin:  1~10, IO 编号 <br />
family_code:  family_code字节

####返回值
nil

####参见
**-**   []()


<a id="ow_search"></a>
## ow.search()
####描述
寻找下一个slave器件。 <br />

####语法
ow.search(pin)

####参数
pin:  1~10, IO 编号 <br />

####返回值
查找成功则返回slave器件的8个字节的rom code字符串； <br />
查找失败则返回nil

####参见
**-**   []()


<a id="ow_crc8"></a>
## ow.crc8()
####描述
计算Dallas Semiconductor的8位CRC, 用于与ROM或者暂存器中的内容进行比较。 <br />

####语法
ow.crc8(buf)

####参数
buf:  需要进行crc8计算的字符串 <br />

####返回值
crc结果字节

####参见
**-**   []()


<a id="ow_check_crc16"></a>
## ow.check_crc16()
####描述
计算1-Wire的CRC16并与接收的CRC结果进行比较。 <br />

####语法
ow.check_crc16(buf, inverted_crc0, inverted_crc1, crc)

####参数
buf:  需要进行crc8计算的字符串 <br />
inverted_crc0:  接收到的CRC结果的低字节 <br />
inverted_crc1:  接收到的CRC结果的高字节 <br />
crc:  crc初始值 (可选)

####返回值
布尔值: true, crc结果相符; false，crc结果不相符。

####参见
**-**   []()


<a id="ow_crc16"></a>
## ow.crc16()
####描述
计算Dallas Semiconductor的16位的CRC值。用于1-wire总线中多器件通信的数据完整性校验。请注意：这里的CRC计算结果并不一定是1-wire总线中获得的CRC，原因如下：<br />
    1) 1-wire总线传输的CRC是低位先传输的。<br />
    2) 另外由于因处理器而异的字节顺序，ow.crc16()返回结果的MSB和LSB顺序可能不同于1-wire总线中获取的MSB和LSB顺序。 <br />

####语法
ow.crc16(buf, crc)

####参数
buf:  需要进行crc8计算的字符串 <br />
crc:  crc初始值 (可选)

####返回值
返回16位的Dallas Semiconductor CRC计算结果

####参见
**-**   []()
