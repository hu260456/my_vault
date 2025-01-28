#### 要点

- yolov5s是在640的图片下训练的
- yolov5s6是在1280的图片下训练的
- NMS: Non Maximum Suppression
- project: 结果的保存位置
- name: 保存结果命名的前缀
- exist-ok: 覆盖原有结果而不是命名后缀+1

#### yolov5项目结构

data: 主要存放数据集的配置文件和超参数等
models: 存放模型的配置文件和函数
utils: 存放工具类的函数
weights: 存放训练好的权重参数

#### 损失函数

- classification loss: 分类损失
- localization loss: 定位损失
- confidence loss: 置信度损失

#### train.py

![image 1.png](attachments/image%201.png)

- conf: 模型的配置
- nosave: 只保留最后一次训练的pt文件
- notest: 只测试最后一轮
- cache-images: 为了更快的训练速度缓存图片
- adam: 使用 torch.optim.Adam() 优化器，默认使用梯度下降

#### 导出为onnx

`pip install onnx>=1.7.0 -i https://pypi.tuna.tsinghua.edu.cn/simple # for ONNX export`
`pip install coremltools==4.0 -i https://pypi.tuna.tsinghua.edu.cn/simple # for CoreML export`
`python export.py --weights best.pt --img 640 --batch 1 --include onnx engine`

#### 网络可视化工具

lutzroeder.github.io/netron