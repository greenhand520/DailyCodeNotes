# AD转换

示例程序

```c
#include <reg51.h>
#include <intrins.h>
//stdio.h,string.h用于printf函数原型
#include <stdio.h>

typedef unsigned char uchar;
typedef unsigned int uint;

/* Declare SFR associated with the ADC*/
sfr ADC_CONTR =0xBC;	//ADC control register
sfr ADC_RES=0xBD;	//ADC hight 8-bit result register
sfr ADC_LOW2=0xBE;	//ADC low 2-bit result register
sfr PlASF=0x9D;		//P1 secondary function control register
/* Define ADC operation const for ADC CONTR*/
# define ADC_POWER 0x80	//ADC power control bit
# define ADC_FLAG 0x10	//ADC complete flag
# define ADC_START 0x08	//ADC start control bit
# define ADC_SPEEDLL 0x00	//540 clocks
# define ADC_SPEEDL 0x20		//360 clocks# define ADC SPEEDH0X40//180 clocks
#define ADC_SPEEDH  0X40
# define ADC_SPEEDHH 0x60	//90 clocks 
uchar ch=0;	//ADC channel No.
int i;
#define VCC 4.61

void Delay(uint n) {
	uint x;
	while(n--) {
		x=5000;
		while(x--);
	}
}


void delay_xms(uchar x) {

  uint i,j;
  for(i=x;i>0;i--)
  for(j=110;j>0;j--);
}

void InitADC()
{
	PlASF = 0XF0;
	ADC_RES = 0;
	ADC_CONTR = ADC_POWER|ADC_SPEEDLL;
	Delay(2);
}

uint GetADCResult(uchar ch){
	ADC_CONTR=ADC_POWER|ADC_SPEEDLL|ch|ADC_START;
	_nop_();
	_nop_();
	_nop_();
	_nop_();
	while(!(ADC_CONTR&ADC_FLAG));
	ADC_CONTR&=~ADC_FLAG;

	return ADC_RES*4+ADC_LOW2;
}

void InitUart() {
	
	SCON=0X50;			//设置为工作方式1
	TMOD=0X20;			//设置计数器工作方式2
	PCON=0X00;			//波特率不加倍
	TH1=0XFd;				//计数器初始值设置，注意波特率是9600的
	TL1=0XFd;
	ES=1;						//打开接收中断
	EA=1;						//打开总中断
	TR1=1;					//打开计数器
	TI = 1;
}
void SendData(uchar dat) {
	SBUF=dat;//Send current data
	while(!TI);//Wait for the previous data is sent
	TI=0;//Clear TI flag
}

void adc_isr() interrupt 5 using 1 {
	ADC_CONTR &=! ADC_FLAG; //Clear ADC interrupt flag
	SendData(ch); //Show Channel NO.
	SendData(ADC_RES); //Get ADC high 8-bit result and Send to UART
	//if you want show 10-bit result, uncomment next line
	//SendData(ADC_LOW2);//Show ADC low 2-bit result
	if(++ch>7) ch=0;//switch to next channel
	ADC_CONTR=ADC_POWER|ADC_SPEEDLL|ADC_START|ch;
	i ++;
	if (i == 500) {
		P0 = 0x00;
	}
}

void serial() interrupt 4 {
	RI = 0;//清除接收中断标志位
}

void main() {
	float vin;
	uint ad_value;
	InitUart();
	InitADC();
	 printf("串口初始化完毕");

	while(1) {
		ad_value = GetADCResult(0);
		vin = VCC * ad_value / 1023;
		printf("%.3f", vin);
		delay_xms(1000);
	}
}
```

