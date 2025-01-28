#### Dataset

> 提供一种方式获取 data 及其 label

```Python
########## 自定义数据集集 ##########
from torch.utils.data import Dataset
class MyData(Dataset):
    def __init__(self):
        
    def __getitem__(self, index):
########## 公共数据集 ##########
import torchvision
test_set = torchvision.datasets.CIFAR10(
    root='./dataset',  # 测试集的保存位置
    download=False,  # 如果不存在是否自动下载
    train=False,  # 是否是训练集
    # 对图形的变换
    transform=torchvision.transforms.Compose([
        torchvision.transforms.ToTensor()
    ]),
    target_transform=None  # 对 target 进行变换
)
```

#### Dataloader

> 对于与 pytorch API 不相符的数据集，使用 Dataloader 可以比较自由的加载数据

> 为后面的网络提供不同的数据形式

```Python
from torch.utils.data import DataLoader
test_loader = Dataloader(
    dataset=test_set,
    batch_size=4,  # 每次加载的数据量，默认是随机抓取
    shuffle=True,  # 打乱
    num_workers=0,  # 子进程数
    drop_last=False  # 如果最后一次抓取的数量不足batch_size，这些数据会被丢弃
)
```

#### tensorboard

`pip install tensorboard`
`tensorboard --logdir=<log文件夹名称> --host=localhost --port=6666`

#### transforms

```Python
from torchvision import transforms
# 获取 tensor 化变换
transoforms.Totensor()
# 获取归一化变换 | ([标准差], [均值])
transforms.Normalize([x, x, x], [x, x, x])
# 获取 Resize 变换
transforms.Resize((512, 512))
# 获取组合变换
transforms.Compose([<trans0>, <trans1>])
# 获取随机裁剪变换
transforms.RandomCrop(512)
```

#### 神经网络骨架

- Container: 容器 | 骨架
    - Module: 所有神经网络的基类
    - Conv2d: 二维卷积 | convolution2D

```Python
from torch import nn
class MyNetwork(nn.Module):
    def __init__(self):
        super(MyNetwork, self).__init__()
        <初始处理>

    def forward(self, input):
        return <处理后的input>
```

#### nn.Conv2d

```Python
from torch import nn
conv = nn.Conv2d(
    3,  # in_channels: 输入图像的通道数
    3,  # out_channels: 输出图像的通道数
    3,  # kernel_size: 卷积核的大小
    stride=1,  # 卷积核的步进大小
    padding=0  # 边距
    pading_mode='zeros'  # 以这个值填充padding,
    bias=True  # 添加偏置
)
```

#### F.conv2d

```Python
import torch
import torch.nn.functional as F
# 输入
input = torch.Tensor([
[1, 2, 0, 3],
[0, 1, 2, 3],
[1, 2, 1, 0],
[5, 2, 3, 1]
])
# 卷积核
kernel = torch.Tensor([
[1, 2, 1],
[0, 1, 0],
[2, 1, 0]
])
# 计算卷积
conv = F.conv2d(
    input,  # 输入
    kernel,  # 卷积核
    stride=1,  # 卷积核每次移动的步数
    padding=1  # 计算卷积时为原数据增加边距
)
output = conv(input)
```
