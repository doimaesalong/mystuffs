[中文版本API文档](https://github.com/nodemcu/nodemcu-firmware/wiki/nodemcu_api_cn)<br />
[API doc English](https://github.com/nodemcu/nodemcu-firmware/wiki/nodemcu_api_en)

###change log: 
2015-01-27<br />
Support floating point LUA.<br />
Use macro LUA_NUMBER_INTEGRAL in user_config.h control this feature.<br />
build pre_build bin.

2015-01-26<br />
Applied sdk095_patch1 to sdk 0.9.5.<br />
Added LUA examples and modules [by dvv](https://github.com/dvv). <br />
Added node.readvdd33() API [by alonewolfx2](https://github.com/alonewolfx2).<br />
build pre_build bin.

2015-01-24<br />
migrate to sdk 0.9.5 release.<br />
tmr.time() now return second(not precise yet). <br />
build pre_build bin.

2015-01-23<br />
merge mqtt branch to master.<br />
build pre_build bin.

2015-01-18<br />
merge mqtt module to [new branch mqtt](https://github.com/nodemcu/nodemcu-firmware/tree/mqtt) from [https://github.com/tuanpmt/esp_mqtt](https://github.com/tuanpmt/esp_mqtt).<br />
merge spi module from iabdalkader:spi. <br />
fix #110,set local port to random in client mode.<br />
modify gpio.read to NOT set pin to input mode automatic.<br />
add PATH env with C:\MinGW\bin;C:\MinGW\msys\1.0\bin;C:\Python27 in eclipse project. resolve #103.

2015-01-08<br />
 fix net.socket:send() issue when multi sends are called. <br />
**NOTE:** if data length is bigger than 1460, send next packet AFTER "sent" callback is called.<br />
 fix file.read() api, take 0xFF as a regular byte, not EOF.<br />
 pre_build/latest/nodemcu_512k_latest.bin is removed. use pre_build/latest/nodemcu_latest.bin instead.<br />

2015-01-07<br />
 retrive more ram back.<br />
 add api file.format() to rebuild file system.<br />
 rename "NodeMcu" to "NodeMCU" in firmware.<br />
 add some check for file system op.<br />


2014-12-30<br />
modify uart.on api, when run_input set to 0, uart.on now can read raw data from uart.<br />
serial input now accept non-ascii chars.<br />
fix dev-kit gpio map..<br />
add setip, setmac, sleeptype api to wifi module. <br />
add tmr.time() api to get rtc time and calibration.

2014-12-26<br />
fix a bug when readline from uart.<br />

2014-12-22<br />
update to sdk 0.9.4<br />
opensource<br />
folder "pre_build" contain pre-build bin firmware.<br />
folder "lua_examples" contain some pure lua examples.<br />
folder "lua_modules" contain some pure lua lib based on NodeMCU.<br />

2014-12-19<br />
**Important** Re-arrange GPIO MAP due to development kit.[New Gpio Map](#new_gpio_map)<br />
Add bitwise operation module.<br />
Modify net.socket:connect() api to accept domain name, auto DNS.

2014-12-12<br />
modify wifi.xx.getip() to return nil, if ip is 0.0.0.0.

2014-12-11<br />
fix uart.setup(), now setup can set pins in other mode back to uart mode. <br />
add wifi.sta.status() api, to get connection status in station mode. <br />
modify tmr.now() to return a uint31 rather than uint32. now-=0x80000000 if now>0x7fffffff.

2014-12-09<br />
increased the number of alarm in tmr module, now it has 7 alarm. 

2014-12-08<br />
add uart.setup(), uart.write() api.

2014-12-07<br />
add ow(1-wire module), from arduino, and use same api.<br />
add an 18b20 1-wire example.<br />
change net.socket.send() payload max len from 256 to 1460.<br />
modify gpio.mode() api, add internal pullup/float para.

2014-12-04<br />
fix memory leak issue when input and run from console.

2014-12-02<br />
fix the heap recover too slow issue, heap will recover in seconds, not minutes.<br />
modify the return of file.open, return nil if file not exist, true if opend ok.<br />
move startup version display before doing the init.lua.<br />
modify wifi.ap.config(), remove the restart at the end of config.

2014-11-30<br />
modify the max freq of pwm module to 1000.<br />
modify the max duty cycle of pwm module to 1023.<br />
add uart module, uart.on("data") api to receive data from uart.

2014-11-29<br />
fix tmr.delay not work when delayed time < 1s.<br />
fix pwm module not work when freq<77.

2014-11-25<br />
Fix memory leak for the dns api.

2014-11-24<br />
Fix the wrong length of wifi password comparison when configuring sta. pwd,64byte. ssid,32byte.<br />
Fix dns problem, add a dns example to wiki.

2014-11-23<br />
A temporary and dirty fix to heap drop issue: <br />
to avoid reboot, a tcp-server will NOT accept a connection from client, if there is not enough memory. :(<br />
file.list() now returns a table only. will not print result in serial directly.

2014-11-20<br />
fix tmr.delay to support more than 2s delay, may cause bacon time out, lost connection to AP.<br />
add tmr.wdclr to clear watchdog counter in chip, use in long time loop.<br />
fix UDP part of net module.<br />
add a timeout para to createServer(net.TCP, timeout).

2014-11-19<br />
add adc module, use adc.read(0) to read adc value, no tests made.<br />
add wifi.sta.getap() api to wifi.sta module, to get ap list.

2014-11-18<br />
bug fixed: net.socket:connect() has no effect.

2014-11-18<br />
bug fixed: as a tcp server, the connection can't closed with :close().<br />
tcp server: inactive connection will closed by server in 30s (previously 180s).<br />
add a test api node.input() to put lua chunk into lua interpretor, multi-line supported.<br />
add a test api node.ouput(function) to direct serial output to a callback function.<br />
file.readline() now returns line include EOL('\n'), and returns nil when EOF. 

2014-11-12<br />
full version firmware<br />

2014-11-11<br />
add file.seek() api to file module<br />
now max 6 pwm channel is supported<br />

2014-11-10<br />
change log module to file module<br />
now file operation support multiple read/write<br />
for now file module only allowed one file opened<br />

2014-11-5<br />
delete log operation api from node module<br />
add log module<br />
modify wifi module api<br />
modify node.key long_press and short_press default function<br />
key is triged only when key is released<br />
