# 数字华容道游戏

## 介绍

使用威纶通触摸屏开发的华容道小游戏，颇有一些乐趣。

触摸屏型号：TK6071IP

使用 EasyBuilder Pro 创建。

## 效果演示

![](http://wangpeng-imgsubmit.oss-cn-hangzhou.aliyuncs.com/img/202205262203196.png)

## 代码分析

主要有3块代码

1、初始化

> HMI启动时执行

```vba
macro_command main()

short cs[9]={8,5,4,2,1,7,6,3,0}
short i,tmp
bool tp

SetData(cs[0], "Local HMI", LW, 1, 9)
tmp = 9
SetData(tmp, "Local HMI", LW, 11, 1)
tmp=0
SetData(tmp, "Local HMI", LW, 20, 1)

for i=0 to 8
    if cs[i]==0 then
        tp=1
        SetData(tp, "Local HMI", LB, i, 1)
    else
        tp=0
        SetData(tp, "Local HMI", LB, i, 1)
    end if
next

end macro_command
```

2、执行

> 点击按钮后执行
> 
> PS：这宏不支持二维数组，所以用一维数组强行实现。

```vba
sub short z2i(short num)
    short tmp,i
    tmp = num-1
    i = tmp/3
    return i
end sub

sub short z2j(short num)
    short tmp,j
    tmp = num-1
    j = tmp%3
    return j
end sub

macro_command main()

short qb[9]

short yz[9]={1,2,3,4,5,6,7,8,0}

short wz,bk,tmp,bz,az

short i,j,bi,bj,tmpi,tmpj

bool zd,tp

DELAY(50)
//获取当前按下的位置（1-9）
GetData(wz, "Local HMI", LW, 10, 1)
//获取白块位置（1-9）
GetData(bk, "Local HMI", LW, 11, 1)
//判断是否是可交换块

TRACE("wz:%d",wz)
TRACE("bk:%d",bk)

//一维坐标转二维
i = z2i(wz)
j = z2j(wz)

bi = z2i(bk)
bj = z2j(bk)

TRACE("i:%d",i)
TRACE("j:%d",j)

zd=0

tmpi=i-1
tmpj=j
if tmpi==bi and tmpj==bj then
    zd=1
end if

tmpi=i+1
tmpj=j
if tmpi==bi and tmpj==bj then
    zd=1
end if

tmpi=i
tmpj=j-1
if tmpi==bi and tmpj==bj then
    zd=1
end if

tmpi=i
tmpj=j+1
if tmpi==bi and tmpj==bj then
    zd=1
end if

if zd==1 then
    //白块的按下块值交换
    //获取值
    GetData(bz, "Local HMI", LW, bk, 1)
    GetData(az, "Local HMI", LW, wz, 1)
    //交换
    SetData(az, "Local HMI", LW, bk, 1)
    SetData(bz, "Local HMI", LW, wz, 1)

    //更新白块位置
    SetData(wz, "Local HMI", LW, 11, 1)

    //更新白块隐藏
    GetData(qb[0], "Local HMI", LW, 1, 9)
    for i=0 to 8
        if qb[i]==0 then
            tp=1
            SetData(tp, "Local HMI", LB, i, 1)
        else
            tp=0
            SetData(tp, "Local HMI", LB, i, 1)
        end if
    next
    for i=0 to 8
        if qb[i]<>yz[i] then
            break
        end if
    next
    if i==9 then
        tp=1
        SetData(tp, "Local HMI", LB, 10, 1)
    else
        tp=0
        SetData(tp, "Local HMI", LB, 10, 1)
    end if
end if


end macro_command
```

3、计时

> 周期执行，时间间隔1秒

```vba
macro_command main()

short sj
bool zt
GetData(zt, "Local HMI", LB, 10, 1)

if zt==0 then
    GetData(sj, "Local HMI", LW, 20, 1)
    sj=sj+1
    SetData(sj, "Local HMI", LW, 20, 1)
end if



end macro_command
```
