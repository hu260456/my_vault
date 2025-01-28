## WLAN | 使用网络API案例

```C++
// Arduino
#include <WiFi.h>
#include <HTTPClient.h>
// 第三方
#include <ArduinoJson.h>

#define LED 2
const char *ssid = "6A";
const char *pwd = "13631698818";

void setup()
{
    pinMode(LED, OUTPUT);
    Serial.begin(9600);
    // 连接WiFi
    Serial.print("正在连接WiFi：");
    WiFi.begin(ssid, pwd);
    //  Wifi.status() != WL_CONNECED?
    while (!WiFi.isConnected())
    {
        Serial.print(".");
        delay(500);
    }
    Serial.println("wifi连接成功");

    for (int i = 0; i < 3; i++)
    {
        digitalWrite(LED, HIGH);
        delay(500);
        digitalWrite(LED, LOW);
        delay(500);
    }

    // 创建HTTPClient对象
    HTTPClient http;

    String url = "http://v.juhe.cn/joke/content/text.php";
    String key = "4cde2e683864a96924d2d8f23e38c591";
    // 访问API接口
    http.begin(url + "?key=" + key);

    // 接受HTTP响应
    Serial.printf("HTTP响应状态码：%d", http.GET());

    // 获取响应
    String response = http.getString();
    Serial.println("响应数据：\n" + response);

    // 关闭连接
    http.end();

    Serial.println("下面获取第一条joke正文：");
    // 此对象存储和处理JSON数据
    DynamicJsonDocument doc(1024);
    deserializeJson(doc, response);
    String joke = doc["result"]["data"][0]["content"].as<String>();
    Serial.println(joke);
}

void loop()
{
    // code
}
```



