# Pillow
- pip install pillow

```python
>>> from PIL import Image
>>> import numpy as np
>>> a=np.array(Image.open("myLogo.png"))
>>> print (a.shape, a.dtype)
(640, 640, 4) uint8
>>> b=[255,255,255,510]-a
>>> d=Image.fromarray(b.astype("uint8"))
>>> d.save("test.png")
# "L" 表示变为灰度值
>>> s=np.array( Image.open("source2.png").convert("L") )
>>> b=255-s
>>> im = Image.fromarray(b.astype("uint8"))
>>> im.save("test2.jpg")

>>> e = s*(100/255)+150 #区间变换
>>> im3 = Image.fromarray(e.astype("uint8"))
>>> im3.save("test4.jpg")

>>> f=255*(s/255)**2
>>> im4 = Image.fromarray(f.astype("uint8"))
>>> im4.save("test5.jpg")
```
#### 手绘效果
- 黑白线条
- 边界线条较重
- 相同或相近的色彩趋于白色
- 略有光源效果

```python
>>> from PIL import Image
>>> import numpy as np
>>> a=np.asarray(Image.open("source2.png").convert("L").astype("float"))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Image' object has no attribute 'astype'
>>> a=np.asarray(Image.open("source2.png").convert("L")).astype("float")
>>> depth = 10. #(0-100)
>>> grad = np.gradient(a) #取图像灰度梯度值
>>> grad_x, grad_y = grade #分别取横纵图像梯度值
>>> grad_x *= depth/100
>>> grad_y *= depth/100
>>> A = np.sqrt(grad_x**2 + grad_y**2 + 1.)
>>> uni_x = grad_x/A
>>> uni_y = grad_y/A
>>> uni_z = 1./A
>>> 
>>> vec_el = np.pi/2.2 #光源的俯视角度, 弧度值
>>> vec_az = np.pi/4 #光源的方位角度, 弧度值
>>> dx = np.cos( vec_el)*np.cos(vec_az) #光源对 x 轴的影响
>>> dy = np.cos( vec_el)*np.sin(vec_az) #光源对 y 轴的影响
>>> dz = np.sin(vec_el) #光源对 z 轴的影响
>>> 
>>> b = 255 * (dx*uni_x + dy*uni_y + dz*uni_z) #光源归一化
>>> b = b.clip(0,255)
>>> 
>>> im = Image.fromarray(b.astype("uint8")) #重构图像
>>> im.save("test110.jpg")
```

#### rcParams
- pyplot并不默认支持中文显示,需要rcParams修改字体实现

##### 全局字体
- 'font.family' 用于显示字体的名字
- 'font.style' 字体风格,正常'normal'或斜体'italic'
- 'font.size' 字体大小,整数字号或者'large''x-small'

```python
>>> import matplotlib.pyplot as plt
>>> import matplotlib
>>> 
>>> matplotlib.rcParams['font.family']='SimHei' #黑体
>>> plt.ylabel('纵轴(值)')
>>> plt.savefig('test', dpi=600)
>>> plt.show()
```

##### 局部字体
- 在有中文输出的地方,增加一个属性: fontproperties
- 而不改变全局字体

```python
>>> import numpy as np
>>> import matplotlib.pyplot as plt
>>> a = np.arange(0, 5.0, 0.2)
>>> plt.xlabel('横轴:时间', fontproperties='SimHei', fontsize=20)
>>> plt.ylabel('纵轴:振幅', fontproperties='SimHei', fontsize=20)
>>> plt.plot(a, np.cos(2 * np.pi * a), 'r--')
>>> plt.show()
```

### 文本显示
- plt.xlabel()
- plt.ylabel()
- plt.title()
- plt.text() 在任意位置增加文本
- plt.annotate() 在图形中增加带箭头的注解


### 图表函数
**选取恰当图形展示数据含义**
##### pyplot有大量绘图函数,用时再仔细查找
- plt.plot(x,y,fmt,...) 绘制坐标图
- plt.boxplot(data, notch, position) 绘制箱形图
- plt.bar(left, height, width, bottom) 绘制条形图
- plt.barh(width, bottom, left, height) 绘制横向条形图
- plt.polar(theta, r) 绘制极坐标图
- plt.pie(data, explode) 绘制饼图
- plt.psd(x, NFFT=256, pad\_to, Fs) 绘制功率谱密度图
- plt.specgram(x, NFFT=256, pad\_to, F) 绘制谱图
- plt.cohere(x, y, NFFT=256, Fs) 绘制X-Y的相关性函数
- plt.scatter(x, y) 绘制散点图, 其中, x和y长度相同
- plt.step(x, y, where) 绘制布阶图
- plt.hist(x, bins, normed) 绘制直方图
- plt.contour(X, Y, Z, N) 绘制等值图
- plt.vlines() 绘制垂直图
- plt.stem(x, y, linefmt, marketfmt) 绘制柴火图
- plt.plot\_data() 绘制数据日期

