#  jpg

#### exiftool（查看图片exif信息）

查看属性的照片。

```python
exiftool 1.jpg   # 显示图片所有信息
exiftool 1.jpg | grep flag   # 查看图片有关‘flag’字符的信息
exiftool *   # 查看此文件夹所有图片信息
exiftool -b -ThumbnailImage attachment.jpg >flag.jpg   # 提取缩略图
```

#### stegdetect（检查jpg图片隐写方法_win）

检查jpg图片隐写方法,Stegdetect可以检测到通过JSteg、JPHide、OutGuess、Invisible Secrets、F5、appendX和Camouflage等这些隐写工具隐藏的信息

将图片复制到 **stegdetect.exe** 所在文件夹，打开 cmd 输入

```python
stegdetect.exe -tjopi -s 10.0 [stego_file]

-s 修改检测算法的敏感度，该值的默认值为1。检测结果的匹配度与检测算法的敏感度成正比，算法敏感度的值越大，检测出的可疑文件包含敏感信息的可能性越大。
-t 设置要检测哪些隐写工具（默认检测jopi），可设置的选项如下：
j 检测图像中的信息是否是用jsteg嵌入的。
o 检测图像中的信息是否是用outguess嵌入的。
p 检测图像中的信息是否是用jphide嵌入的。
i 检测图像中的信息是否是用invisible secrets嵌入的。
```

如果显示为 `[stego_file]：jphide(***)`，则可以用 **stegbreak** 破解密码stegdetect.exe -tjopi -s 10.0 [stego_file]

```python
stegbreak -r rules.ini -f password.txt -t p [stego_file]
#password.txt为自带字典
Loaded 1 files...
[stego_file]：jphide[v5](2333)
#2333为该文件jphide的密码
```

#### f5-steganography （F5隐写，需要passwd）

**F5是一个可以将文件隐藏到图片中的java工具**

##### 使用（解密）

进入 **F5-steganography 文件夹** 打开终端

```python
> java Extract 1.jpg -p 123456
# -p后接f5的key
```

打开 **output.txt** 文件即可

##### 使用（加密）

```python
> java Embed 原图.jpg 生成图.jpg -e 隐藏的文件.txt -p 密码
```

#### outguess （可需要passwd）



##### 使用（加密）

右键打开终端

```python
> outguess -k 12345 -d hidden.txt 1.jpg 2.jpg
......
# -k后接密码
# -d后接要隐藏的内容
# 加密后1.jpg会覆盖2.jpg
```

##### 使用（解密）

右键打开终端（接上述）

```python
> outguess -k 12345 -r 2.jpg out.txt
......
```

**out.txt** 中内容即为想要隐藏的 **hidden.txt** 中内容

也可不需要密码，即

```python
> outguess -r 2.jpg out.txt
```

#### steghide（jpg隐藏信息）

**steghide可以在一个图片或音频文件中隐藏信息**

##### 使用（加密）

将 flag.txt 文件隐藏到 out.jpg 中：

```python
steghide embed -cf out.jpg -ef flag.txt [-p 123456] # -p后接密码，可无
```

##### 使用（解密）

查看图片中嵌入的文件信息：

```python
steghide info out.jpg
```

提取**含有密码**的隐藏内容：

```python
steghide extract -sf out.jpg -p 123456
```

提取**不含有密码**的隐藏内容：

```python
steghide extract -sf out.jpg
```

直接按回车即可

##### steghide爆破密码

有些题目用steghide加密文件但是不给密码，此时就需要爆破，steghide本身并不支持爆破，需要一些其他的方法：https://github.com/Va5c0/Steghide-Brute-Force-Tool

```python
python steg_brute.py -b -d [字典] -f [jpg_file]
```

需要安装的库：**progressbar**

```python
pip install progressbar2
```

##### jphide

基于lsb的隐写，将文件加密隐藏到图片中。

- 可使用工具 `JPHS`

## png & bmp

### **CRC校验修复**

工具：tweakpng

此时该图片的crc值未出错，用该工具打开该图片，不会报错，如果将crc值修改一下，这里我修改成22614986，再用tweakpng打开则会报错，并会提示该图片现有宽高应该得到的crc是多少

### **宽高修复**

**crc正确**的话直接用脚本爆破出来正确宽高

**crc错误**的话给出高，可以直接生成几百张，找出正确的一张。

### idat块

使用tweakpng打开文件，F7预览，可以通过修改第一个idat块

###  zsteg（lsb隐写）

zsteg可以检测PNG和BMP图片里的隐写数据。

目前，zsteg支持检测：

- PNG 和 BMP 格式的 LSB 隐写术
- zlib 压缩数据
- [打开Stego](http://openstego.sourceforge.net/)
- Camouflage 1.2.1
- LSB

```python
#查看全部信息
zsteg -a 1.bmp

#提取固定通道的内容
zsteg -e b8,a,lsb,xy 1.png -> out.png
```

### BlindWaterMark （盲水印）

##### bwm

###### 解密

安装成功后打开 **bwm.py** 所在文件夹，在文件夹中打开终端

```python
# 1.png 为无水印原图
# 2.png 为有盲水印的图
# flag.png 为解出来的图片
> python bwm.py decode 1.png 2.png flag.png
```

查看 **flag.png** 即可

###### 加密

安装成功后打开 **bwm.py** 所在文件夹，在文件夹中打开终端

```python
# 1.png 为无水印原图
# water.png 为水印图
# 2.png 为有盲水印图
> python bwm.py encode 1.png water.png 2.png
```

查看 **2.png** 即可

##### 频域盲水印

###### 脚本

```python
import cv2
import numpy as np
import random
import os
from argparse import ArgumentParser

ALPHA = 5

def build_parser():
    parser = ArgumentParser()
    parser.add_argument('--original', dest='ori', required=True)
    parser.add_argument('--image', dest='img', required=True)
    parser.add_argument('--result', dest='res', required=True)
    parser.add_argument('--alpha', dest='alpha', default=ALPHA)
    return parser

def main():
    parser = build_parser()
    options = parser.parse_args()
    ori = options.ori
    img = options.img
    res = options.res
    alpha = options.alpha
    if not os.path.isfile(ori):
        parser.error("original image %s does not exist." % ori)
    if not os.path.isfile(img):
        parser.error("image %s does not exist." % img)
    decode(ori, img, res, alpha)

def decode(ori_path, img_path, res_path, alpha):
    ori = cv2.imread(ori_path)
    img = cv2.imread(img_path)
    ori_f = np.fft.fft2(ori)
    img_f = np.fft.fft2(img)
    height, width = ori.shape[0], ori.shape[1]
    watermark = (ori_f - img_f) / alpha
    watermark = np.real(watermark)
    res = np.zeros(watermark.shape)
    random.seed(height + width)
    x = range(height / 2)
    y = range(width)
    random.shuffle(x)
    random.shuffle(y)
    for i in range(height / 2):
        for j in range(width):
            res[x[i]][y[j]] = watermark[i][j]
    cv2.imwrite(res_path, res, [int(cv2.IMWRITE_JPEG_QUALITY), 100])

if __name__ == '__main__':
    main()
```

###### 使用

```python
python pinyubwm.py --original 1.png --image 2.png --result out.png
```

查看 **out.png** 即可，如果无法得到正常图片，可将 **1.png** 和 **2.png** 调换位置再次尝试

#### lsb

隐写信息，打开Stegsolve文件，通过查看各个通道不同数值的1到7，看看哪一个出现了不一样的特殊信息，就可以利用data extract来查看对应的通道，预览或者另存为图片，可以的得到，或者直接利用脚本，可以找出当中的二进制文件。

#### pngcheck.exe

##### 使用

在 **pngcheck.exe** 所在文件夹打开cmd

```python
pngcheck.exe -v 123.png
```

可检查 **png** 的 **IDAT** 块是否有问题

相关题目可参考：https://blog.csdn.net/u010391191/article/details/80818785

有关解题脚本可参考 **FzWjScJ** 师傅的blog：http://www.fzwjscj.xyz/index.php/archives/17/

# gif

# gif

## 文件头部分(Header)

#### GIF署名和版本号

GIf署名来判断一个文件是不是gif文件，这一部分由三个字符组成，文件版本号也是由3个字符组成，一般为87a，或者为89a

![img](C:/img/7.png)

#### GIF数据流部分

##### 逻辑屏幕标识符(Logical Screen Descriptor)

这一部分由7个字节组成，定义了GIF图象的大小（高和宽之类）、颜色深度(Color Bits)、背景色(Blackground ColorIndex)以及有无全局颜色列表(Global Color Table)和颜色列表的索引数(ndex Count)

具体描述见下表:

![1683517535_6458705f3a308796a2ff4.png!small?1683517535812](C:\Users\Administrator\AppData\Roaming\Typora\draftsRecover\img\1683517535_6458705f3a308796a2ff4.jpeg)

目前只是需要这些，还有一些理论知识，还是得结合题目去研究

### 考点

- 修补文件头
- 空间轴隐藏信息
- 时间轴隐藏信息

#### 修复文件头

这种基本还好，结合上面的文件头部信息，只需要找到一张标准的gif文件,对照进行修补就可以了。

#### 空间轴隐藏信息

由于gif文件的特性，由一帧一帧文件组成，**所以每一帧的图片，或者多张图片的结合，都可以隐藏信息**

gif的每一帧的宽高都得进行修改，或者进行分离，找出带有flag的那一帧

##### 解决方式

- 使用stesolvefj打开图片，Stegsolve Analyse-Frame Brower进行分帧，进行逐帧分析
- 可以使用convert命令逐帧分开
- convert persistant.gif transparent white result.gif

### 时间轴隐藏信息

每一帧的时间间隔也可以隐藏信息

#### 解决 

通过工具的利用，来获得时间的间隔

identify命令清晰的打打印出每一顿的时间间隔``` identify -format"%Tn”100.gif```根据时间间隔的规律解决题目

### stegpy（支持多种文件加密）

此种加密支持对PNG、BMP、GIF、WebP和WAV格式加密，同时可以选择有无password

##### 安装（kali中）

```python
pip3 install stegpy
```

##### 使用（加密）

不含有密码

```python
stegpy 'hello_world' image.png
```

含有密码

```python
stegpy "hello_world" image.png -p
Enter password (will not be echoed): # 输入密码（不显示）
Verify password (will not be echoed): # 确认密码（不显示）
```

##### 使用（解密）

不含有密码

```python
stegpy _image.png
```

含有密码

```python
stegpy _image.png -p
Enter password (will not be echoed): # 输入密码（不显示）
```

# 音频隐写

## MP3隐写

#### audacity mp3stego  d

## wave

#### 音频波形

**波形中隐含摩斯密码**，其中有两条音轨，可以先进行分离，然后导出奇怪的音轨，然后进行摩斯密码的一个解密

##### 音频 频谱

会有一段比较刺耳的声音，会有比较明显的特征。打开它的频谱图就可以得到flag

##### 音频倒放

听起来比较奇怪，但是能听出来是说话，所以就可以进行倒放

##### 音频LSB隐写

使用https://github.com/achorein/silenteye，前面的很多都不可以，就可以在这里来试试。

### word

##### 隐藏文字

直接打开，有时候会隐藏文字，或者文字是白色的。

##### 隐藏文件

word本质是一个zip文件，将文件后缀改为zip，可以向里面添加文件，然后改为原后缀。

#### pdf

wbstego4.3open  有这么一个工具，可以将文件隐藏进去。

解密还是需要使用这个工具。
