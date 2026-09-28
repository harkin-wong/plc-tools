> 适用范围: ST / GXW3 / 伺服 / 传感器
> 使用规则: **仅白名单内缩写允许使用; 禁止自创缩写**; 缩写保持大小写跟随命名风格
> 例：snake_case → `idx`；SCREAMING_SNAKE_CASE → `IDX`；PascalCase 里写 `Idx`

| 缩写  | 全称                   | 中文含义                     |
| ----- | ---------------------- | ---------------------------- |
| mot   | motor                  | 电机                         |
| conv  | conveyor               | 输送机                       |
| valv  | valve                  | 阀门                         |
| sens  | sensor                 | 传感器                       |
| act   | actuator               | 执行器                       |
| sp    | set point              | 设定值                       |
| pv    | process value          | 过程实际值                   |
| rpm   | revolutions per minute | 转速（转 / 分钟）            |
| hz    | hertz                  | 赫兹                         |
| ms    | millisecond            | 毫秒                         |
| s     | second                 | 秒                           |
| min   | minute                 | 分钟                         |
| hr    | hour                   | 小时                         |
| estop | emergency stop         | 急停                         |
| flt   | fault                  | 故障                         |
| alm   | alarm                  | 报警                         |
| ok    | okay                   | 正常就绪                     |
| fb    | function block         | 功能块（仅用于 fb_前缀）     |
| fc    | function               | 函数                         |
| pos   | position               | 位置                         |
| vel   | velocity               | 速度                         |
| acc   | acceleration           | 加速度                       |
| dec   | deceleration           | 减速度                       |
| pulse | pulse                  | 脉冲（不缩写）               |
| enc   | encoder                | 编码器                       |
| pls   | pulse                  | 脉冲（备选，二选一项目固定） |
| dir   | direction              | 方向                         |
| cmd   | command                | 指令                         |
| di    | digital input          | 数字输入                     |
| do    | digital output         | 数字输出                     |
| ai    | analog input           | 模拟量输入                   |
| ao    | analog output          | 模拟量输出                   |

### ⚠️ 缩写使用强制约定

1. 项目二选一, 不可混用同类缩写:
	- 计数: **cnt /num 二选一**
	- 脉冲: **pulse(完整单词)或 pls, 项目固定**
2. 单词较短时**禁止缩写**: speed 不要写成 spd; run 不要写成 rn
3. 缩写只放在单词尾部, 不拆中间单词 ✅ `mot_rpm`; ❌ `mtrpm`
4. 布尔变量尽量不用缩写, 可读性优先: `is_fault` 而不是 `is_flt`(flt 仅状态变量)