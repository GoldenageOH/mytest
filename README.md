#include<reg52.h>
#include <stdio.h>              //printf头文件  、
#include <iostream>
#define uchar unsigned char   
#define uint unsigned int         
unsigned char receiveData;
uchar i,j,b;
void init()
{
    TMOD = 0x20; //定时器工作方式，选择了定时器1，工作方式2 八位初值自动重装的8位定时器。
    TH1 = 0xfd;  //定时器1初值  ,设置波特率为9600 晶振11.0529MHZ?
    TL1 = 0xfd;
    TR1 = 1; //开启定时器1
    SM0 = 0;
    SM1 = 1; //10位异步接收，（8位数据）波特率可变
    REN = 1; //允许串行口接收位
    EA = 1;  //允许中断（总闸）
    ES = 1;  //允许串口中断
}

void ser() interrupt 4
{
    receiveData = SBUF; //出去接收到的数据
    RI = 0;             //清除接收中断标志位
    SBUF = receiveData; //将接收到的数据放入到发送寄存器
    while (!TI);   //等待发送数据完成
    TI = 0; //清除发送完成标志位
}

uchar code table0[7][18]={{0x00,0x00,0x07,0xF0,0x08,0x08,0x10,0x04,0x10,0x04,0x08,0x08,0x07,0xF0,0x00,0x00,0x00,0x00}/*0*/,{0x00,0x00,0x00,0x00,0x08,0x04,0x08,0x04,0x1F,0xFC,0x00,0x04,0x00,0x04,0x00,0x00,0x00,0x00},{0x00,0x00,0x0E,0x0C,0x10,0x14,0x10,0x24,0x10,0x44,0x10,0x84,0x0F,0x0C,0x00,0x00,0x00,0x00},{0x00,0x00,0x0C,0x18,0x10,0x04,0x10,0x84,0x10,0x84,0x11,0x44,0x0E,0x38,0x00,0x00,0x00,0x00},/*"3",3*/{0x00,0x00,0x00,0x60,0x01,0xA0,0x02,0x24,0x0C,0x24,0x1F,0xFC,0x00,0x24,0x00,0x24,0x00,0x00},/*"4",4*/{0x00,0x00,0x1F,0x98,0x11,0x04,0x11,0x04,0x11,0x04,0x10,0x88,0x10,0x70,0x00,0x00,0x00,0x00},{0x00,0x00,0x07,0xF0,0x08,0x88,0x11,0x04,0x11,0x04,0x09,0x04,0x00,0xF8,0x00,0x00,0x00,0x00}};

uchar code table1[64][2]={0xFF,0x0F,0xFF,0x07,0xFF,0x03,0xFF,0x01,
0xFF,0x00,0xFF,0x00,0x7F,0x00,0x7F,0x00,
0x7F,0x00,0x7F,0x00,0x7F,0x00,0xFF,0x00,
0xFF,0x00,0xFF,0x01,0xFF,0x01,0xFF,0x03,
0xFF,0x07,0xFF,0x03,0xFF,0x01,0xFF,0x01,
0xFF,0x00,0xFF,0x00,0x7F,0x00,0x7F,0x00,
0x7F,0x00,0x7F,0x00,0x7F,0x00,0xFF,0x00,
0xFF,0x00,0xFF,0x01,0xFF,0x03,0xFF,0x07,
0xFF,0x0F,0xFF,0x1F,0xFF,0x3F,0xFF,0x7F,
0xFF,0x7F,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
0xFF,0xFF,0xFF,0x7F,0xFF,0x3F,0xFF,0x1F,
0xFF,0x0F,0xFF,0x07,0xFF,0x01,0xFF,0x00,
0x7F,0x00,0xFF,0x00,0xFF,0x01,0xFF,0x07,
0xFF,0x0F,0xFF,0x1F,0xFF,0x3F,0xFF,0x7F,
0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
0xFF,0x7F,0xFF,0x7F,0xFF,0x3F,0xFF,0x1F};

uchar code table12[4][32]={0x00,0x00,0x00,0x00,0x0F,0xFC,0x00,0x04,0x00,0x04,0x00,0x04,0x00,0x04,0xFF,0xFC,
0x00,0x04,0x00,0x04,0x00,0x04,0x00,0x04,0x0F,0xFE,0x00,0x00,0x00,0x00,0x00,0x00,/*山*/
0x00,0x00,0x00,0x04,0x11,0x88,0x12,0x90,0x14,0xA0,0x18,0x82,0xF0,0x81,0x17,0xFE,
0x10,0x80,0x10,0x80,0x10,0xA0,0x10,0x90,0x10,0x88,0x00,0x04,0x00,0x00,0x00,0x00,/*东*/
0x00,0x01,0x04,0x01,0x04,0x02,0x04,0x04,0x04,0x08,0x04,0x30,0x04,0xC0,0xFF,0x00,
0x04,0xC0,0x04,0x30,0x04,0x08,0x04,0x04,0x04,0x02,0x04,0x01,0x00,0x01,0x00,0x00,/*大*/
0x02,0x20,0x0C,0x20,0x88,0x20,0x69,0x20,0x09,0x20,0x09,0x22,0x89,0x21,0x69,0x7E,
0x09,0x60,0x09,0xA0,0x19,0x20,0x28,0x20,0xC8,0x20,0x0A,0x20,0x0C,0x20,0x00,0x00,/*学*/};

uchar code table13[64][2]={0x00,0x00,0x00,0x00,0x00,0x00,0x80,0x00,
0x80,0x00,0x98,0x0E,0xB0,0x05,0xA0,0x40,
0x44,0x22,0x3E,0x13,0xA8,0x0E,0x90,0xFC,
0x40,0x82,0x18,0x62,0x30,0x01,0x20,0x01,
0x00,0x00,0x00,0x08,0x00,0x08,0x08,0x08,
0x08,0x04,0x10,0x02,0x00,0x00,0x20,0x9F,
0xC0,0x01,0xC0,0x01,0x40,0x06,0x20,0x0C,
0x20,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
0x00,0x06,0x80,0x04,0x60,0x08,0xD0,0x30,
0x88,0x61,0x04,0x03,0x0C,0x0E,0x18,0x78,
0x70,0x00,0xC0,0x07,0x00,0x00,0x00,0x00,
0x00,0x00,0x00,0x08,0x02,0x08,0xC2,0x09,
0x8C,0x7E,0x78,0x10,0x40,0x27,0x80,0xC3,
0xC0,0x04,0x48,0x12,0x30,0x02,0x30,0x06,
0x10,0x04,0x00,0x00,0x00,0x00,0x00,0x00};

uchar code table14[64][2]={0x32,0x80,0x26,0x88,0x02,0xB0,0x00,0x89,
0xE0,0xB8,0x10,0xAB,0x98,0x92,0x4E,0xBA,
0x06,0xB2,0x12,0xB2,0x0A,0x8A,0x0B,0xB2,
0xE3,0xBA,0x01,0xB2,0x75,0x8A,0x65,0xAA,
0x05,0x82,0xF5,0xBA,0x95,0x92,0x01,0xAA,
0xC3,0x92,0x2B,0xAA,0x0A,0x92,0x12,0xAA,
0x26,0xA2,0x4E,0x8A,0x88,0xB2,0x10,0xAA,
0xF0,0x92,0x00,0x83,0x00,0xBA,0x02,0x82,
0x33,0x80,0x13,0x82,0xF3,0x82,0x7F,0x92,
0x1F,0xF2,0x1F,0x92,0x1B,0x82,0x13,0x82,
0x1F,0x82,0x3E,0x82,0x0E,0x82,0x06,0xAA,
0x06,0xE2,0x0C,0x82,0x18,0x82,0x39,0x82,
0xE1,0x82,0x31,0x82,0x18,0x82,0x0C,0x8A,
0x0E,0xBA,0x06,0xAA,0x06,0x82,0x3E,0x82,
0x1F,0x82,0x13,0x82,0x13,0x82,0x13,0x80,
0x13,0xFC,0x13,0x88,0x1F,0x80,0xFF,0x80};
uchar code table15[64][2]={
0xFF,0x00,0x00,0x00,0xF8,0x01,0xFC,0x01,
0x7C,0x20,0x00,0x44,0x00,0x82,0x00,0x12,
0x80,0x4C,0x00,0x39,0x00,0x02,0x40,0x07,
0x80,0x00,0x00,0x01,0xF0,0x03,0x00,0x00,
0xE0,0x03,0x90,0x00,0x90,0x00,0xF0,0x03,
0x00,0x00,0x00,0x03,0x80,0x04,0x40,0x04,
0x00,0x32,0x00,0x99,0x00,0x64,0x00,0x20,
0x7F,0x00,0xFF,0x00,0xFC,0x01,0x00,0x00,
0xFF,0x00,0xFF,0x07,0xFE,0x0F,0xFC,0x1F,
0xC0,0x3F,0x3F,0x00,0xFF,0x0F,0xFF,0x3F,
0xFE,0x7F,0xFC,0x7F,0x00,0x00,0xFF,0x07,
0xFF,0x3F,0xFF,0xFF,0xFF,0xFF,0xFE,0x03,
0x00,0x00,0xF0,0xFF,0xFE,0xFF,0xFF,0x3F,
0xFF,0x0F,0xFF,0x01,0x03,0x00,0xE0,0xFF,
0xFC,0x7F,0xFE,0x3F,0xFF,0x1F,0xFF,0x01,
0x00,0x3F,0xF8,0x1F,0xFE,0x1F,0xFF,0x07};

void delay(uint x)
{
	uint i, j;
	for(i=0;i<x;i++)
		for(j =0;j<197;j++);
}

void ledshuzi()
{
	delay(1);
	for(j=0;j<7;j++)
	{
    	for(i =0;i<9;i++)
    	{
        	P2=table0[j][2*i]; 
			P0=table0[j][2*i+1]; 
			delay(1);
    	}
	}
}

void ledshan()
{
	for(j=0;j<4;j++)
	{
    	for(i =0;i<16;i++)
    	{
        	P2=table12[j][2*i]; 
			P0=table12[j][2*i+1]; 
			delay(1);
    	}
	}
}

void ledxin()
{
	P0 = 0xff;
    P2 = 0xff;
	for(j=0;j<64;j++)
	{
        	P0=table1[j][0]; 
			P2=table1[j][1]; 
			delay(1);
	}
}

void ledshan2()
{
	for(j=0;j<64;j++)
	{
        	P0=table13[j][0]; 
			P2=table13[j][1]; 
			delay(1);
	}
}

 void ledhui()
{
	for(j=0;j<64;j++)
	{
        	P0=table14[j][0]; 
			P2=table14[j][1]; 
			delay(1);
	}
}

void ledhua()
{
	for(j=0;j<64;j++)
	{
        	P0=table15[j][0]; 
			P2=table15[j][1]; 
			delay(1);
	}
}
void main()
{
    init();
    while (1)
    {
        if (receiveData == 'A')
        {
         	ledshuzi();
        }
        if (receiveData == 'B')
        {
            ledxin();
        }
		if (receiveData == 'C')
        {
            ledshan();
        }
		if (receiveData == 'D')
        {
            ledshan2();
        }
		if (receiveData == 'E')
        {
            ledhui();
        }
		if (receiveData == 'F')
        {
            ledhua();
        }
    }
}
