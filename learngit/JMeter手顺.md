# 生成测试报告

```
jmeter -n -t test.jmx -l test.jtl -e -o /path
# -n：以非GUI形式运行Jmeter 
# -t：source.jmx 脚本路径 
# -l：result.jtl 运行结果保存路径（.jtl）,此文件必须不存在 
# -e：在脚本运行结束后生成html报告 
# -o：用于存放html报告的目录
```

# 关于测试报告

## 1、APDEX（Application Performance Index）：应用性能指标

APEDEX 的取值范围为0-1

### 1.1、术语

Apdex 定义了应用响应时间的最优门槛为 T，另外根据应用响应时间结合 T 定义了三种不同的性能表现：

- **Satisfied（满意）**：应用响应时间低于或等于 T（T 由性能评估人员根据预期性能要求确定），比如 T 为 1.5s，则一个耗时 1s 的响应结果则可以认为是 satisfied 的。

- **Tolerating（可容忍）**：应用响应时间大于 T，但同时小于或等于 4T。假设应用设定的 T 值为 1s，则 4 * 1 = 4 秒极为应用响应时间的容忍上限。

- **Frustrated（烦躁期）**：应用响应时间大于 4T。

### 1.2、公式

```
Apdext = (Satisfied Count + Tolerating Count / 2) / Total Samples
```

APDEX = （满意数目 + 可容忍数目 / 2） / 总采样数

