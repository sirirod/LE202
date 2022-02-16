# อธิบายโปรแกรม
## ex1 serial- monitor แสดงผลการรันโปรแกรม

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

# ex2 
