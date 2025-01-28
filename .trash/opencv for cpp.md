#### LUT 查找表

```C++
////////// 即可实现二值化，也可实现多值化 //////////
void LUT(
    InputArray src, // 输入矩阵，其数据类型只能是CV_8U
    InputArray lut, // 256像素灰度图查找表，单通道或者与src通道数相同
    OutputArray dst // 输出矩阵，尺寸与src相同，数据类型与lut相同
)
```

#### 模板匹配

```C++
void matchTemplate(
    InputArray image, // 待模板匹配的原图像，数据类型为CV_8U或CV_32F
    InputArray templ, // 模板图像，需要与image具有相同的数据类型，尺寸不大于image
    OutputArray, // 模板匹配结果输出图像，数据类型为CV_32F
    int  method, // 模板匹配方法的flag
    InputArray mask = noArray() // 模板匹配的掩码
)
```

