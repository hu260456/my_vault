#### 接口

```Python
class X:  # interface
    def func(self):
        raise NotImplementedError  # 强制此方法需要实现
# 最好使用抽象类
from abc import ABCMeta, abstractmethod
class X(metaclass=ABCMeta):
    @abstarctmethod
    def func(self):
        ...
```

#### pip

```Shell
# 列举本地安装的依赖，比pip list少pip、wheel、setuptools等
pip freeze
# 安装requirements.txt中的包
pip install -r requirements.txt
# 更新某个模块
pip install -U 模块
```

#### 内置函数

```Python
# 列出包内容
print(dir())
# 函数的使用帮助
print(help())
```

#### enumerate

```Python
seq = ['apple', 'orange']
for index, item in enumerate(seq)
# 0 apple
# 1 orange
list(enumerate(seq))
# [(0, 'apple'), (1, 'orange')]
```

#### 魔法函数

```Python
class X:
  
    # 注意第一参数是 cls
    def __new__(cls, var):
        ...
        
    # 使实例能够被 print
    def __repr__(self):
        return f'{self.var}'
      
    # 实现实例的 len(x)
    def __len__(self):
        return len(self.var)
    
########## 运算符重载类 ##########
    # 实现 lt, gt, eq等
    def __lt__(self, x:X):
        return self.var < x.var
```

#### 打印当前解释器路径

```Python
import sys
print(sys.executable)
```
