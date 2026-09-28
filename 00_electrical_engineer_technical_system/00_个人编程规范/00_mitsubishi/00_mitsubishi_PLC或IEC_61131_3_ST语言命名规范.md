# PLC / `IEC 61131-3` ST 语言命名规范(三菱 FX5U 适用)

> 适用: ST 语言、FB/FC、变量标签、结构体、枚举、软元件别名;
> **三菱 GX Works3 推荐规范** IEC61131-3 标识符: 支持字母、数字、下划线, **不能以数字开头, 不允许空格、中文、短横线`-`**, 标识符不区分大小写(但编码书写必须统一风格!)

## 一. 核心原则

---

1. **见名知意**: PLC 变量直接关联现场设备, 优先描述**设备 + 动作 / 状态**; 缩写必须团队统一
2. **风格固定**: 推荐两套二选一, **项目内全程统一**, 推荐方案 A(行业主流)
	- 方案 A(推荐, 工控主流): **snake_case**
	- 方案 B: 小驼峰 camelCase(部分日系项目使用, 不混搭)

> 本规范下文示例全部使用 snake_case

1. **区分变量作用域**: 通过前缀区分变量类型(工控常用, PLC 推荐保留)
2. **禁止使用 PLC 保留关键字**: `IF` `THEN` `ELSE` `FOR` `WHILE` `RETURN` `AND` `OR` 等
3. **避免直接使用软元件名作为变量名**: 不推荐直接 `X0`, 使用标签别名 `start_btn`

## 二. 变量前缀约定(重点!工控最常用)

---

表格

| 前缀    | 含义                         | 示例                           |
| ------- | ---------------------------- | ------------------------------ |
| `i_`    | Input 输入变量(FB 输入引脚)  | `i_start_btn`, `i_pulse_100ms` |
| `o_`    | Output 输出变量(FB 输出引脚) | `o_motor_run`, `o_valve_open`  |
| `io_`   | IN_OUT 输入输出引脚          | `io_motor_speed`               |
| `r_`    | Retain 保持型变量            | `r_total_count`                |
| `stat_` | 状态机状态变量               | `stat_idle`, `stat_running`    |
| `tmp_`  | 临时局部变量                 | `tmp_calc_speed`               |
| `cfg_`  | 参数配置(固定参数)           | `cfg_motor_max_speed`          |
| `fb_`   | FB 功能块实例                | `fb_conveyor`, `fb_motor1`     |
| `s_`    | 结构体 Struct                | `s_motor_param`                |
| `e_`    | 枚举 Enum                    | `e_run_status`                 |

> 全局变量: 不加前缀 或 `g_` 前缀, 项目统一即可.

## 三. 各类对象命名细则

---

### 1. FB 功能块、FC 函数

- FB: 大驼峰(PascalCase), 名词, 描述设备 / 功能 `MotorControl`、`ConveyorSpeedCalc`
- FC: 大驼峰(PascalCase), 动词 + 名词, 描述动作 `CalcWeight`、`CheckSensorValid`

### 2. 结构体 STRUCT

- 结构体类型: 大驼峰(PascalCase) `MotorParam`

- 结构体内部成员: 蛇形命名(snake_case)
	```iecst
	TYPE MotorParam :
	STRUCT
	  max_speed : INT;
	  accel_time : REAL;
	  enable : BOOL;
	END_STRUCT
	END_TYPE
	```

### 3. 枚举 ENUM

- 枚举类型: 大驼峰(PascalCase)  `RunStatus`

- 枚举成员: 大写蛇形(SCREAMING_SNAKE_CASE)
	```iecst
	TYPE RunStatus :
	(
	  STATUS_IDLE,
	  STATUS_RUNNING,
	  STATUS_FAULT
	);
	END_TYPE
	```

### 4. 常量 VAR_CONSTANT

- 大写蛇形(SCREAMING_SNAKE_CASE)
	```iecst
	VAR_CONSTANT
	  MAX_MOTOR_SPEED : INT := 3000;
	  TIMEOUT_S : REAL := 5.0;
	END_VAR
	```

### 5. BOOL 布尔变量

- 带状态谓词: `is_fault`、`has_alert`、`enable_auto`、`is_ready`
- ❌ `fault`(分不清是 bool 还是故障代码)

### 6. 数组变量

- `sensor_data_arr`, 后缀`_arr`标识数组, 可选

### 7. 软元件标签别名(GXW3 全局标签)

- X 输入: `start_btn`、`emergency_stop`
- Y 输出: `main_motor_out`
- M 中间继电器: `auto_mode_flag`
- D 寄存器: `product_count`

## 四. ST 代码禁止项 ❌

---

1. 标识符包含 `-`、空格、中文
2. 数字开头 `1_motor_run`
3. 关键字作为变量 `if : BOOL;`
4. 驼峰 + 下划线混搭 `motorRun_speed`
5. 模糊命名 `a, b, data1, data2`
6. 过长名称(超过 4~5 个单词建议拆结构体)

## 五. ST 代码示例(完整可直接复制)

---

```
VAR
  fb_motor1 : MotorControl;
  cfg_motor_max_speed : INT := 2000;
  is_system_ready : BOOL;
  stat_current : RunStatus;
  tmp_calc_rpm : REAL;
END_VAR

fb_motor1.i_enable := is_system_ready;
fb_motor1.i_max_speed := cfg_motor_max_speed;
fb_motor1();
```

## 六. 工程配套约定(GX Works3)

---

1. 全局标签 / 局部标签 严格遵守一套命名风格, 不要混用
2. 注释: 变量注释写在标签备注, FB 引脚添加注释
3. 变量分类: 用文件夹分组(Inputs / Outputs / Params / Status)
4. 导出变量清单时, 命名统一便于上位机(触摸屏、SCADA)映射
5. 触摸屏标签: 尽量和 PLC 变量名保持一致, 减少翻译错误