### 快捷键

|||
|-|-|
|F4|在同名的头文件和源程序文件之间切换|
|F2|变量：跳转到声明；函数：声明和定义切换|
|Ctrl+Shift+R|改名称，将替换所有用到这个符号的地方|
||为函数原型在cpp文件中生成函数体|
|Ctrl+l|为选择的文字自动进行缩进|
|Ctrl+/|注释或取消注释|
|F1|为光标所在的符号显式帮助|
|Ctrl+Shift+S|文件全部保持|
|Ctrl+F|调出查找/替换对话框|
|F3|查找下一个|
|Ctrl+B|编译当前项目|
|F5|开始调试|
|F10|单步调试，执行当前程序语句|

### 知识点

- 信号/槽 要有参数类型

    - connect(sender, SIGNAL(A(int, bool, etc)), receiver, SLOT(B(int, etc)))

- 信号/操函数不存在命名冲突时可以省略参数类型

- 信号/槽函数出现重载的情况，X有两个重载，使用qOverload

    - connect(发送, &QCheckBox::clicked, this, qOverload<bool>(&Widget::X))

    - connect(发送, &QCheckBox::clicked, this, qOverload<>(&Widget::X))

- connect 函数中的缺省参数

    - Qt::AutoConnection（默认）：自动确定关联方式

    - Qt::DirectConnection：信号被发射时，槽立即执行，槽函数与信号在同一线程

    - Qt::QueuedConnection：事件循环回到接收者线程后执行槽，槽与信号在不同线程

    - Qt::BlockingQueueConnection：与Qt:QueuedConnection相似，信号线程会被阻塞直到槽执行完毕。当槽函数与信号在同一线程，会造成死锁

- connect函数还有一个成员函数版本

    - this→connect(spinNum, SI GNAL(..), SLOT(..))

- disconnect 函数，用于解除链接

    - 解除与一个sender的所有连接

        - disconnect(myobject,nullptr,nullptr,nullptr);// 静态函数

        - myobject->disconnect(); // 成员函数

    - 解除与一个特定signal的所有连接

        - disconnect(myObject, SIGNAL(mySignal()), nullptr, nullptr);

        - myObject->disconnect(SIGNAL(mySignal()));

    - 解除与一个特定receiver的所有连接

        - disconnect(myobject, nullptr, myReceiver, nullptr);

        - myobject->disconnect(myReceiver) ;

    - 解除一对特定的信号和槽的连接

        - disconnect(lineEdit, &QLineEdit:textChanged,label, &QLabel:setText);

- 在槽函数里，使用QObject:sender()可以获取信号发射者的指针

    - QSpinBox * spinbox = qobject_cast<QSpinBox *>(sender());

- 自定义信号

    - 在类的 signals: 下声明即可，不用定义，必须为void，可以有参数

- 使用 emit 关键字发送信号

- 在槽函数中使用 sender 获取发送信号者的指针

- 获取分组框的子对象列表

    - QObjectList objList = ui->groupBox_btns->children()

- window 的函数和属性

    - show, hide, raise, lower, close

    - window contents: update, repaint, scroll

    - top-level windows: windowModified, windowTitle等

- QStackedLayout(堆叠布局)是一个在 creator 中没有体现的布局，可以通过代码实现

- 下拉列表： ComboBox

- 函数中不用的参数在函数体内：Q_UNUSED(arg)