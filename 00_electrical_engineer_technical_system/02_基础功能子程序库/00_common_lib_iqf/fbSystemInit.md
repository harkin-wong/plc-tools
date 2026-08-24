# 一.变量声明

|标签名|数据类型|类|标签注释|
|:----:|:----:|:----:|:----:|
|   b_Manual_Reset    |     位     | VAR_INPUT  |  手动复位上升沿触发   |
|w_M_Start|字[有符号]|VAR_INPUT|M起始位置|
| w_M_Count |字[有符号]|VAR_INPUT|M复位点数, 0=跳过复位|
| w_S_Start |字[有符号]|VAR_INPUT|S起始位置|
| w_S_Count |字[有符号]|VAR_INPUT|S复位点数, 0=跳过复位|
| w_C_Start |字[有符号]|VAR_INPUT|C起始位置|
|w_C_Count|字[有符号]|VAR_INPUT|C复位点数, 0=跳过复位|
|w_D_Start|字[有符号]|VAR_INPUT|D起始位置|
| w_D_Count | 字[有符号] | VAR_INPUT | D复位点数, 0=跳过复位 |
|    b_Init_Done    | 位 | VAR_OUTPUT | 初始化完成标志 |
|    b_Init_Active    | 位 | VAR_OUTPUT | 初始化执行中 |
|  r_Trig_Manual_Reset  |R_TRIG|VAR|手动复位上升沿检测|

# 二. 代码

```iecst
/*
FB名称：fbSystemInit
平台：FX5S / FX5U iQ‑F
版本：V1.0
变更：
功能：
	1. SM402 STOP→RUN上电首次扫描执行初始化
	2.b_Manual_Reset上升沿触发手动复位
	3. ZRST批量复位M/S/C位元件；FMOV对D寄存器批量写入0
接口说明：
	输入：
		b_Manual_Reset：手动复位触发信号，上升沿有效
		w_M_Start/w_M_Count：M复位起始、点数；Count=0则跳过
		w_S_Start/w_S_Count：S状态器复位起始、点数；Count=0跳过
		w_C_Start/w_C_Count：C计数器复位起始、点数；Count=0跳过
		w_D_Start/w_D_Count：D寄存器清零起始、点数；Count=0跳过
	输出：
		b_Init_Done：初始化完成输出
		b_Init_Active：初始化正在执行
调用示例：
	fbSystemInit_01(
		b_Manual_Reset:=SM402,
		w_M_Start:=0,
		w_M_Count:=512,
		w_S_Start:=0,
		w_S_Count:=1000,
		w_C_Start:=0,
		w_C_Count:=200,
		w_D_Start:=0,
		w_D_Count:=500,
		b_Init_Done=M700,
		bInitActive=M701
	);
*/

r_Trig_Manual_Reset(CLK := b_Manual_Reset);

IF SM402 OR r_Trig_Manual_Reset.Q THEN
	b_Init_Active := TRUE;
	b_Init_Done := FALSE;
	
	IF w_M_Count > 0 THEN
		Z0 := w_M_Start;
		Z1 := w_M_Count - 1;
		ZRST(M0Z0, M0Z1);
	END_IF;
	
	IF w_S_Count > 0 THEN
		Z0 := w_S_Start;
		Z1 := w_S_Count - 1;
		ZRST(S0Z0, S0Z1);
	END_IF;
	
	IF w_C_Count > 0 THEN
		Z0 := w_S_Start;
		Z1 := w_C_Count - 1;
		ZRST(C0Z0, C0Z1);
	END_IF;
	
	IF w_D_Count > 0 THEN
		Z0 := w_D_Start;
		FMOV(0,w_D_Count,D0Z0);
	END_IF;
	
	b_Init_Active := FALSE;
	b_Init_Done := TRUE;
ELSE
	b_Init_Active := FALSE;
END_IF;
```

