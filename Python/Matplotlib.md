# Matplotlib - Python的数据可视化库
- 超过100种数据可视化效果
- 由各种可视化类构成,内部结构复杂,受Matlab的启发
- matplotlib.pyplot 是绘制各类可视化图形的命令子库,相当于一个快捷方式

```python
>>> import matplotlib.pyplot as plt
>>> plt.plot([3,1,2,4,5])
[<matplotlib.lines.Line2D object at 0x7f06d1eacda0>]
>>> plt.ylabel("grade")
<matplotlib.text.Text object at 0x7f06d1edbc88>
>>> plt.savefig("test", dpi=600) #默认png格式
>>> plt.show()
# 此时出现新窗口
# 关闭新窗口后继续继续回到命令行
# 关闭窗口以后, plt就成了空的了

#二维坐标轴,前一个为x坐标,后一个为y坐标
>>> plt.plot([0,2,4,6,8],[3,1,4,5,2])
[<matplotlib.lines.Line2D object at 0x7f06d1d9b940>]
>>> plt.ylabel("grade")
<matplotlib.text.Text object at 0x7f06d2526f28>
#设置x轴起始值为-1,终点为10; y轴起始0,终止6
>>> plt.axis([-1,10,0,6])
[-1, 10, 0, 6]
>>> plt.show()
```
### plt.subplot( nrows, ncols, plot_number)
- nrows 横轴个数
- ncols 纵轴个数
- plot\_number 当前画的图在哪个区域
- 可以通过 plot\_number 切换绘图区域

```python
>>> import matplotlib.pyplot as plt
>>> import numpy as np
>> a=np.arange(0.0,5.0,0.02)
>>> a
>>> plt.subplot(2,1,1)
<matplotlib.axes._subplots.AxesSubplot object at 0x7f06cd313908>
>>> plt.plot(a,f(a))
[<matplotlib.lines.Line2D object at 0x7f06d24f8e80>]
>>> plt.subplot(2,1,2)
>>> plt.plot(a, np.cos(2*np.pi*a), 'r--')
[<matplotlib.lines.Line2D object at 0x7f06cfd0aba8>]
>>> plt.show()
```

### plt.plot(x, y, format_string, **kwargs)
- x - x轴数据,列表或数组,可选
- y - y轴数据,列表或数组
- format\_string - 控制曲线的格式字符串,可选
- \*\*kwargs - 第二组或更多的(x,y,format\_string),用于同时绘制多条曲线,此时曲线的 x 不能省略

#### format_string
- 由颜色字符、风格字符和标记字符组成
- 不指定时有系统默认,会对不同线进行区分

| 颜色字符 | 说明 | 颜色字符 | 说明 |
|  :-----: | :--: | :------: | :--: |
|'b'|蓝色|'m'|洋红色magenta|
|'g'|绿色|'y'|黄色|
|'r'|红色|'k'|黑色
|'c'|青绿色cyan|'w'|白色
|'#008000'|RGB某颜色|'0.8'|灰度值

|风格字符|说明
|:---:|:---:|
|'-'|实线
|'--'|破折线
|'-.'|点划线
|':'|虚线
|"''|无线条

|标记字符|说明|标记|说明|标记|说明
|:-:|:--:|:---:|:---:|:-:|:--:
|'.'|点标记|'1'|下花三角标记|'h'|竖六边形标记
|','|像素标记(极小点)|'2'|上花三角标记|'H'|横六边形标记
|'o'|实心圈标记|'3'|左花三角标记|'+'|十字标记
|'v'|倒三角标记|'4'|右花三角标记|'x'|x标记
|'^'|上三角标记|'s'|实心方形标记|'D'|菱形标记
|'>'|右三角标记|'p'|实心五角标记|'d'|瘦菱形标记
|'<'|左三角标记|'\*'|星形标记|'&#124;'|垂直线标记

```python
>>> import matplotlib.pyplot as plt
>>> import numpy as np
>>> a = np.arange(10)
#绿色圆圈实线,红色x不画线,任意色*号不画线,蓝色折点线
>>> plt.plot(a, a*1.5,'go-', a, a*2.5,'rx', a, a*3.5,'*', a, a*4.5,'b-.')
[<matplotlib.lines.Line2D object at 0x7f1535000940>, <matplotlib.lines.Line2D object at 0x7f1535000b00>, <matplotlib.lines.Line2D object at 0x7f1534f8c4e0>, <matplotlib.lines.Line2D object at 0x7f1534f8ccc0>]
>>> plt.show()
#显示4条曲线

```
