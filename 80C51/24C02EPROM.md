示例程序

实验现象：

实现现象：下载程序后数码管后4位显示0，按K1保存显示的数据，按K2读取上次保存的数据，
		  按K3显示数据加一，按K4显示数据清零。最大能写入的数据是255.
注意事项：由于P3.2口跟红外线共用，所以做按键实验时为了不让红外线影响实验效果，最好把红外线先取下来。

i2c.c

```c
#include"i2c.h"
void Delay10us() {
	unsigned char a,b;
	for(b=1; b>0; b--)
		for(a=2; a>0; a--);

}
/*起始信号：在SCL时钟信号在高电平期间SDA信号产生一个下降沿
* 备注           : 起始之后SDA和SCL都为0*/
void I2cStart() {
	SDA=1;
	Delay10us();
	SCL=1;
	Delay10us();//建立时间是SDA保持时间>4.7us
	SDA=0;
	Delay10us();//保持时间是>4us
	SCL=0;
	Delay10us();
}
/*终止信号：在SCL时钟信号高电平期间SDA信号产生一个上升沿
* 备注           : 结束之后保持SDA和SCL都为1；表示总线空闲*/
void I2cStop() {
	SDA=0;
	Delay10us();
	SCL=1;
	Delay10us();//建立时间大于4.7us
	SDA=1;
	Delay10us();
}
/*通过I2C发送一个字节。在SCL时钟信号高电平期间，保持发送信号SDA保持稳定
* 输入           : num
* 输出         	 : 0或1。发送成功返回1，发送失败返回0
* 备注           : 发送完一个字节SCL=0,SDA=1*/
unsigned char I2cSendByte(unsigned char dat) {
	unsigned char a=0,b=0;//最大255，一个机器周期为1us，最大延时255us。
	for(a=0; a<8; a++) { //要发送8位，从最高位开始
		SDA=dat>>7;	 //起始信号之后SCL=0，所以可以直接改变SDA信号
		dat=dat<<1;
		Delay10us();
		SCL=1;
		Delay10us();//建立时间>4.7us
		SCL=0;
		Delay10us();//时间大于4us
	}
	SDA=1;
	Delay10us();
	SCL=1;
	while(SDA) { //等待应答，也就是等待从设备把SDA拉低
		b++;
		if(b>200) { //如果超过2000us没有应答发送失败，或者为非应答，表示接收结束
			SCL=0;
			Delay10us();
			return 0;
		}
	}
	SCL=0;
	Delay10us();
	return 1;
}
/*使用I2c读取一个字节
* 备注           : 接收完一个字节SCL=0,SDA=1*/
unsigned char I2cReadByte() {
	unsigned char a=0,dat=0;
	SDA=1;			//起始和发送一个字节之后SCL都是0
	Delay10us();
	for(a=0; a<8; a++) { //接收8个字节
		SCL=1;
		Delay10us();
		dat<<=1;
		dat|=SDA;
		Delay10us();
		SCL=0;
		Delay10us();
	}
	return dat;
}
/*往24c02的一个地址写入一个数据*/
void At24c02Write(unsigned char addr,unsigned char dat) {
	I2cStart();
	I2cSendByte(0xa0);//发送写器件地址
	I2cSendByte(addr);//发送要写入内存地址
	I2cSendByte(dat);	//发送数据
	I2cStop();
}
/*读取24c02的一个地址的一个数据*/
unsigned char At24c02Read(unsigned char addr) {
	unsigned char num;
	I2cStart();
	I2cSendByte(0xa0); //发送写器件地址
	I2cSendByte(addr); //发送要读取的地址
	I2cStart();
	I2cSendByte(0xa1); //发送读器件地址
	num=I2cReadByte(); //读取数据
	I2cStop();
	return num;
}
```

main.c

```c
#include "reg52.h"			 //此文件中定义了单片机的一些特殊功能寄存器
#include "i2c.h"

typedef unsigned int u16;	  //对数据类型进行声明定义
typedef unsigned char u8;
sbit SCL=P2^1;
sbit SDA=P2^0;
sbit LSA=P2^2;
sbit LSB=P2^3;
sbit LSC=P2^4;

sbit k1=P3^1;	//读取
sbit k2=P3^0;	//保存
sbit k3=P3^2;	//累加
sbit k4=P3^3;	//清空

char num=0;
u8 disp[4];
u8 code smgduan[10]= {0x3f,0x06,0x5b,0x4f,0x66,0x6d,0x7d,0x07,0x7f,0x6f};

/* 函数功能		   : 延时函数，i=1时，大约延时10us*/
void delay(u16 i) {
	while(i--);
}

/*按键处理函数*/
void Keypros() {
	if(k1==0) {
		delay(1000);  //消抖处理
		if(k1==0) {
			At24c02Write(1,num);   //在地址1内写入数据num
		}
		while(!k1);
	}
	if(k2==0) {
		delay(1000);  //消抖处理
		if(k2==0) {
			num=At24c02Read(1);	  //读取EEPROM地址1内的数据保存在num中
		}
		while(!k2);
	}
	if(k3==0) {
		delay(100);  //消抖处理
		if(k3==0) {
			num++;	   //数据加1
			if(num>255)num=0;
		}
		while(!k3);
	}
	if(k4==0) {
		delay(1000);  //消抖处理
		if(k4==0) {
			num=0;		 //数据清零
		}
		while(!k4);
	}
}
/*数据处理函数*/
void datapros() {
	disp[0]=smgduan[num/1000];//千位
	disp[1]=smgduan[num%1000/100];//百位
	disp[2]=smgduan[num%1000%100/10];//个位
	disp[3]=smgduan[num%1000%100%10];
}
void DigDisplay() {
	u8 i;
	for(i=0; i<4; i++) {
		switch(i) { //位选，选择点亮的数码管，
		case(0):LSA=0;LSB=0;LSC=0;break;//显示第0位
		case(1):LSA=1;LSB=0;LSC=0;break;//显示第1位
		case(2):LSA=0;LSB=1;LSC=0;break;//显示第2位
		case(3):LSA=1;LSB=1;LSC=0;break;//显示第3位
	}
		P0=disp[3-i];//发送数据
		delay(100); //间隔一段时间扫描
		P0=0x00;//消隐
	}
}
void main() {
	while(1) {
		Keypros();	 //按键处理函数
		datapros();	 //数据处理函数
		DigDisplay();//数码管显示函数
	}
}
```

