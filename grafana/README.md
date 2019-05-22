# grafana

主要配置两个组件：
- Datasource
  是 grafana 获取 metrics 的后端（此工程是 prometheus）。

- Dashboard
  datasource 的可视化组件。目前没有统一的方式用代码去创建。这里使用 python 的一个库 `grafanalib` 创建它的初始化格式数据（模板）。然后生成 `dashboard.json` 文件：
  > generate-dashboard -o dashboard.json service-dashboard.py
