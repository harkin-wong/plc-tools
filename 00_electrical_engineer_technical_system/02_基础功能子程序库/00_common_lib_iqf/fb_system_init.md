# 一.变量声明

|标签名|数据类型|类|标签注释|
|:----:|:----:|:----:|:----:|
|   i_is_manual_reset |     位     | VAR_INPUT  |  手动复位上升沿触发   |
|i_m_start|字[有符号]|VAR_INPUT|M起始位置|
| i_m_count |字[有符号]|VAR_INPUT|M复位点数, 0=跳过复位|
| i_s_start |字[有符号]|VAR_INPUT|S起始位置|
| i_s_count |字[有符号]|VAR_INPUT|S复位点数, 0=跳过复位|
| i_c_start |字[有符号]|VAR_INPUT|C起始位置|
|i_c_count|字[有符号]|VAR_INPUT|C复位点数, 0=跳过复位|
|i_d_start|字[有符号]|VAR_INPUT|D起始位置|
| i_d_count | 字[有符号] | VAR_INPUT | D复位点数, 0=跳过复位 |
|    o_is_init_done    | 位 | VAR_OUTPUT | 初始化完成标志 |
|    o_is_init_active    | 位 | VAR_OUTPUT | 初始化执行中 |
|  r_trig_manual_reset  |R_TRIG|VAR|手动复位上升沿检测|

# 二. 代码

```iecst
/*
FB名称：fb_system_init
平台：FX5S / FX5U iQ‑F
版本：V1.0
变更：
功能：
	1. SM402 STOP→RUN上电首次扫描执行初始化
	2. i_is_manual_reset上升沿触发手动复位
	3. ZRST批量复位M/S/C位元件；FMOV对D寄存器批量写入0
接口说明：
	输入：
		i_is_manual_reset：手动复位触发信号，上升沿有效
		i_m_start/i_m_count：M复位起始、点数；Count=0则跳过
		i_s_start/i_s_count：S状态器复位起始、点数；Count=0跳过
		i_c_start/i_c_count：C计数器复位起始、点数；Count=0跳过
		i_d_start/i_d_count：D寄存器清零起始、点数；Count=0跳过
	输出：
		o_is_init_done：初始化完成输出
		o_is_init_active：初始化正在执行
调用示例：
	fbSystemInit_01(
		i_is_manual_reset:=SM402,
		i_m_start:=0,
		i_m_count:=512,
		i_s_start:=0,
		i_s_count:=1000,
		i_c_start:=0,
		i_c_count:=200,
		i_d_start:=0,
		i_d_count:=500,
		o_is_init_done:=M700,
		o_is_init_active:=M701
	);
*/

r_trig_manual_reset(CLK := i_is_manual_reset);

IF SM402 OR r_trig_manual_reset.Q THEN
	o_is_init_active := TRUE;
	o_is_init_done := FALSE;
	
	IF i_m_count > 0 THEN
		Z0 := i_m_start;
		Z1 := i_m_count - 1;
		ZRST(M0Z0, M0Z1);
	END_IF;
	
	IF i_s_count > 0 THEN
		Z0 := i_s_start;
		Z1 := i_s_count - 1;
		ZRST(S0Z0, S0Z1);
	END_IF;
	
	IF i_c_count > 0 THEN
		Z0 := i_s_start;
		Z1 := i_c_count - 1;
		ZRST(C0Z0, C0Z1);
	END_IF;
	
	IF i_d_count > 0 THEN
		Z0 := i_d_start;
		FMOV(0,i_d_count,D0Z0);
	END_IF;
	
	o_is_init_active := FALSE;
	o_is_init_done := TRUE;
ELSE
	o_is_init_active := FALSE;
END_IF;
```

