# อธิบายโปรแกรม
## ex1 serial- monitor แสดงผลการรันโปรแกรม
~~~
int cnt = 0;

void setup()

{

	Serial.begin(115200);
	
}

void loop()

{
	cnt++;
	
	Serial.printf("PATTANI :%d\n",cnt);
	
	int s = cnt % 5 + 1;
	
	int d = s * 1000;
	
	delay(d);
	
}
~~~
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

