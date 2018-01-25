# bat脚本

## 语法

1. if语句判断多个条件
```
if not "%type%"=="ip" if not "%type%"=="dvb" (
echo Input type error!
pause
exit
)
```