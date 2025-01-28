## 导包

```Python
from selenium import webdriver
driver = webdriver.Chrome()
```

## 元素定位/操作

```Python
from selenium.webdriver.com.by import By
element = driver.find_element(By.ID, '<元素ID>')
elememt = driver.find_element(By.CLASS_NAME, '<类名>') # 匹配符合的第一个元素
elements = driver.find_elements(By.CLASS_NAME, '<类名>') # 匹配所有元素返回列表
element = driver.find_element(By.TAG_NAME, '<tag_name>') # 匹配标签
element = driver.find_element(By.NAME, '<name>') # 匹配name属性
element = driver.find_element(By.LINK_TEXT, '<text>') # 以<a>标签的文本匹配
element = driver.find_element(By.PARTIAL_LINK_TEXT, '<text>') # 以<a>标签的文本模糊匹配
element = driver.find_element(By.CSS_SELECTOR., '...')
element = driver.find_element(By.XPATH, '...')
element.send_keys('message') # 模拟键盘输入
element.submit() # 模拟回车
element.click() # 模拟点击
select = Select(driver.find_element(By.ID, '<id>')) # select元素的匹配
select.select_by_index(<index>)  # 根据索引选择
select.select_by_value('<value>') # 根据值选择
select.select_by_]=-=visible_text('<text>') # 根据可以看见的值选择
########## 弹窗相关 ##########
print(driver.switch_to.alert.text) # 获取弹窗的文字
driver.switch_to.alert.accept() # 点击确定
driver.switch_to.alert.dismiss() # 点击取消
driver.switch_to.alert.send_keys('<content>') # 往弹窗输入内容
########## 文件相关 ##########
# upload是获取到的文件上传按钮
upload.send_keys('<file_path>') # 上传文件
########### 选项 #############
from selenium.webdriver.support.select import Select
########## 下载相关 ##########
## options还可以用来指定设置UA、Cookie等
chromeOptions = webdriver.ChromeOptions()
preference = {'download.default_directory': '<dir>'} # 设置文件下载路径
chromeOptions.add_experimental_option('prefs', preference)
driver = webdriver.Chrome()
#### 框架等特殊元素的定位 ####
driver.switch_to.frame(0) # 可用name、id以及第几个(index)frame来定位
driver.switch_to.frame(driver.find_element(...)) # 通过元素
driver.switch_to.parent_frame() # 返回上一级
driver.switch_to.default_content() # 返回主界面
```

## 等待元素出现

```Python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions
browser = webdriver.Chrome()
# 打开京东网页
browser.get('https://www.jd.com/')
# 等待加载完成并定位到input
input = WebDriverWait(browser, 5).until(
    expected_conditions.presence_of_element_located((By.CSS_SELECTOR, "#key"))
)
# 等待页面中加载了搜索按钮
submit = WebDriverWait(browser, 5).until(
expected_conditions.element_to_be_clickable((By.CSS_SELECTOR, '#search > div > div.form > button'))
)
# 在输入框中输入“美食”关键字
input.send_keys('美食')
# 点击搜索按钮
submit.click()
```

## 其他

```Python
driver.quit() # 退出浏览器
driver.maximize_window() # 窗口最大化
driver.execute_script('console.log("Hello")') # 执行js脚本
from selenium.common.exceptions import TimeoutException # 使用TimeoutException让程序更健壮
```
