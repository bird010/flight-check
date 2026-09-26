# Flight Check

机票价格定时跟踪项目。当前使用 Codex 的 `flight-check-kiwi` 自动化任务，读取配置文件并通过 Kiwi.com 航班连接器查询价格。

## 定时任务

- 任务名称：`flight-check-kiwi`
- 状态：ACTIVE
- 频率：每 3 小时一次
- 执行环境：本地 Codex 项目任务
- 项目目录：`E:\code\flight-check`
- 配置文件：`config\flights.yml`
- 数据源：Kiwi.com 航班连接器
- Windows 任务：`FlightCheck-PriceMonitor` 当前未安装

每次运行时，任务会：

1. 读取配置文件中的全部航线、日期、起飞机场、降落机场和当地起飞时间段；
2. 查询每个固定日期航程，并严格匹配机场代码；
3. 按 `[start, end)` 过滤当地起飞时间；
4. 逐条记录符合条件的航班或中转行程，不只保存最低价；
5. 将价格历史写入 `data\prices`，错误写入 `data\errors`；
6. 为配置中的每个日期生成价格走势图，输出到 `reports`。

## 配置

所有跟踪航程和时间窗口以 [config/flights.yml](config/flights.yml) 为准。当前时区为 `Asia/Shanghai`，舱等为经济舱，乘客数为 1。

## 当前目录

- `config\flights.yml`：定时任务必需的配置；
- `data\prices`：价格历史输出目录；
- `data\errors`：查询错误输出目录；
- `reports`：走势图输出目录；
- `LICENSE`：项目许可证。

当前已清理本地 Python 源码、脚本、测试、旧报告、价格历史和浏览器档案；定时任务不依赖这些本地源码。
