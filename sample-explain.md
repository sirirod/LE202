# อธิบายโปรแกรม
## ex1 serial- monitor เขียนโปรแกรมลงบน microcontroller
~~~
#include <Arduino.h>
int cnt = 0;

void setup()
{
	Serial.begin(115200);	
}

void loop()
{
	cnt++;
	Serial.printf("A :%d\n",cnt);		
	delay(300);	
}
~~~
+ เริ่มต้น โปรแกรมจะต้องกำหนดความเร็วเริ่มต้นให้ตรงกัน

+ โปรแกรมจะวนลูปการแสดงผลไปเรื่อยๆ โดย cnt จะเพิ่มขึ้นทีละ1 เป็นจำนวนรอบ

# ex2 scan หา wi-fi
~~~
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	WiFi.mode(WIFI_STA);
	WiFi.disconnect();
	delay(100);
	Serial.println("\n\n\n");
}

void loop()
{
	Serial.println("========== เริ่มต้นแสกนหา Wifi ===========");
	int n = WiFi.scanNetworks();
	if(n == 0) {
		Serial.println("NO NETWORK FOUND");
	} else {
		for(int i=0; i<n; i++) {
			Serial.print(i + 1);
			Serial.print(": ");
			Serial.print(WiFi.SSID(i));
			Serial.print(" (");
			Serial.print(WiFi.RSSI(i));
			Serial.println(")");
			Serial.print(WiFi.channel(i));
			delay(10);
		}
	}
	Serial.println("\n\n");
	delay(10 * 1000);
}
~~~
+ โปรแกรมจะค้นหาสัญญาณ ถ้า n = 0 แสดงว่าไม่มีสัญญาณ wi-fi
+ เมื่อค้นพบสัญญาณไวไฟ จะแสดงชื่อของไวไฟนั้น
# ex3 เอ้าพุทสัญญาณดิจิทัล
~~~
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, OUTPUT);
	Serial.println("\n\n\n");
}

void loop()
{
	cnt++;
	if(cnt % 2) {
		Serial.println("========== ON ===========");
		digitalWrite(0, HIGH);
	} else {
		Serial.println("========== OFF ===========");
		digitalWrite(0, LOW);
	}
	delay(500);
}
~~~
+ ใช้ ESP01 มีพอร์ท 0 และ พอร์ท 1
+ เริ่มต้นโปรแกรมจะเซตให้พอร์ท 0 เป็นพอร์ท output
+ โปรแกรมจะนับวนลูปไปเรื่อยๆ ถ้านับเป็นเลขคู่ให้ on ถ้านับเป็นเลขคี่ให้ off ไปที่พอร์ท 0
# ex4 อินพุทสัญญาณดิจิทัล
~~~
#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, INPUT);
	pinMode(2, OUTPUT);
	Serial.println("\n\n\n");
}

void loop()
{
	int val = digitalRead(0);
	Serial.printf("======= read %d\n", val);
	if(val==1) {
		digitalWrite(2, LOW);
	} else {
		digitalWrite(2, HIGH);
	}
	delay(100);
}
~~~
+ เซตพอร์ท 0 เป็น input
+ เซตพอร์ท 2 เป็น output
+ โปรแกรมจะรับสัญญาณที่พอร์ท 0 ถ้า = 1 ที่พอร์ท 2 ไปจะไม่ติด
+ ถ้า = 0 ไฟที่พอร์ท 2 จะติด
# ex5 เชื่อมต่อไวไฟและเว็บเซอร์เวอร์
~~~
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "HI_BMFWIFI_2.4G";
const char* password = "0819110933";

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.mode(WIFI_STA);
	WiFi.begin(ssid, password);
	while (WiFi.status() != WL_CONNECTED) {
		delay(500);
		Serial.print(".");
	}
	Serial.print("\n\nIP address: ");
	Serial.println(WiFi.localIP());

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	/// http://192.0.0.1/ = Hello cnt: ???
	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/on = Hello cnt: ???
	server.on("/on", []() {
		cnt++;
		String msg = "Switch on ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/off = Switch off: ???
	server.on("/off", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
~~~
+ จะต้องเชื่อมต่อไวไฟ และใส่รหัสไวไฟ
+ โดยส่วน set up จะเป็นการเชื่อมต่อไวไฟที่เราเลือก
+ ทำการ set up web surver เมื่อเชื่อมต่อเสร็จจะแสดงผลเป็น Hello จำนวนครั้งเพิ่มขึ้น
# ex6 สร้างไวไฟแอคเซสพอยต์ (Wifi AP)
~~~
#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "MY-ESP8266";
const char* password = "choompol";

IPAddress local_ip(192, 168, 1, 1);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.softAP(ssid, password);
	WiFi.softAPConfig(local_ip, gateway, subnet);
	delay(100);

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
~~~
+ กำหนดชื่อไวไฟและรหัส
+ กำหนด IP address
+ เมื่อเชื่อมต่อ server แล้ว โปรแกรมจะให้ใส่รหัส
