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
2. 仅搜索直飞航班：查询时关闭自助中转/中转选项，并只保留 `stops=0` 且实际航段仅包含起点和终点机场的结果；
3. 先按机场对和起飞时间分片查询，收集返回航司后，再按 `select_airlines` 逐航司查询；
4. 对达到连接器返回上限的分片继续缩小时间窗口，记录每个分片的返回数量和饱和状态；
5. 对每个返回结果严格匹配实际起飞机场和降落机场代码，确认无中转，并按 `[start, end)` 过滤当地起飞时间；
6. 逐条记录符合条件的直飞航班，不只保存当天最低价，并按 `departure_date`、`origin_airport`、`destination_airport`、`flight_number`、`departure_time` 和 `stops` 去重；
7. 将价格历史追加写入 `data\prices`，不覆盖已有历史；不完整或失败的查询写入 `data\errors`；
8. 为配置中的每个日期生成按航班号分组的价格走势图，输出到 `reports`。

Kiwi 连接器单次查询可能只返回有限数量的候选结果，因此不能把一次宽查询当作完整结果集。分片全部未饱和时，结果才可视为本次查询范围内完整；如果仍有饱和或连接器异常，必须记录诊断并明确保留“不完整”状态，不臆测缺失航班或价格。

## 配置

所有跟踪航程和时间窗口以 [config/flights.yml](config/flights.yml) 为准。当前时区为 `Asia/Shanghai`，舱等为经济舱，乘客数为 1。

## 当前目录

- `config\flights.yml`：定时任务必需的配置；
- `data\prices`：价格历史输出目录；
- `data\errors`：查询错误输出目录；
- `reports`：走势图输出目录；
- `LICENSE`：项目许可证。

当前已清理本地 Python 源码、脚本、测试、旧报告、价格历史和浏览器档案；定时任务不依赖这些本地源码。
