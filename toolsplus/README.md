## 📦 ToolsPlus 项目

![status](https://img.shields.io/badge/状态-推进中-success)
![version](https://img.shields.io/badge/version-3.1.3-blue)
![vue](https://img.shields.io/badge/Vue-3.x-brightgreen)
![python](https://img.shields.io/badge/Python-3.8+-blue)

> 轻量级多设备定时任务管理、通知 + 个人便签速记

[![GitHub](https://img.shields.io/badge/项目源码-181717?logo=github)](https://github.com/upchr/FnDepot/tree/main/toolsplus)

### 🎯 项目背景
基于 Vue 3 和 Python3 构建，为开发者和多设备用户提供集中式定时任务管理及个人备忘服务，解决跨设备任务同步和通知问题。

### 多平台支持
- x86
- arm
  
### 📊 版本规划

| 版本 | 状态 | 主要功能 | 预计时间 |
|------|------|----------|----------|
| v3.1.3 | ✅ 已发布 | 便签 + 定时任务、通知等 | 已完成 |
| v4.0 | 🚧 开发中 | SSL续签 + 性能优化 |  |
| 未来版本 | 📝 规划中 | 小程序、APP |  |

### ✨ 功能特性
#### ✅ 已实现
- 📝 **便签速记**
  - 快速创建/编辑/删除便签
  - 支持文本域
  
- ⏱️ **多设备定时任务**
  - 多设备任务收集管理
  - Cron表达式支持
  - 任务执行日志
  
- 🔔 **智能通知**
  - 连续失败预警机制
  - 多通道通知（企业微信/bark/Webhook等）
  
- 💽 **数据安全**
  - 模块级备份还原
  - 手动导出/导入
  - 加密存储
  
- 🔄 **系统版本**
  - 提供升级脚本
  - 版本升级推送

#### 🚧 开发中
- [x] **SSL证书管理**
  - 自动申请（Let's Encrypt）
  - 到期自动续签
  - 多域名支持
  
- [ ] **功能优化**
  - 内存、安全优化等
- [ ] **需求收集**
  - issues提交

### 📌 相关链接

- [📖 使用文档](https://github.com/upchr/mytool/blob/master/Readme.md)
- [🐛 Bug反馈](https://github.com/upchr/FnDepot/issues)
- [💬 讨论区](https://github.com/upchr/FnDepot/issues/new)



# 更多介绍参考
[ToolsPlus Github](https://github.com/upchr/mytool)
## 🐳 Docker 部署
### 构建镜像
```bash
git clone git@github.com:upchr/mytool.git
cd mytool && docker build -t toolsplus:latest -f docker/Dockerfile .
```
### 启动容器
```bash
docker run -d \
  --name toolsplus \
  -p 80:80 \
  -v ./data:/data \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  chrplus/toolsplus:latest
```
```bash
services:
  note-app:
    image: chrplus/toolsplus:latest
    container_name: toolsplus
    ports:
      - "80:80"
    volumes:
      - ./data:/data
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```
