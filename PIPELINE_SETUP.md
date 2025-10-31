# E2E测试流水线设置指南

## 📋 概述

本项目已配置完整的E2E业务流程测试流水线，基于GitHub Actions实现。每次代码提交都会自动触发完整的电商业务流程测试，并通过飞书发送测试结果通知。

## 🚀 流水线功能

### 触发条件
- 推送到 `main`、`master`、`develop` 分支
- 创建针对 `main`、`master` 分支的 Pull Request

### 测试内容
- **测试用例**: `testcases/test_e2e_purchase.yaml`
- **覆盖流程**: 用户注册 → 登录 → 浏览商品 → 加购物车 → 创建订单 → 验证库存
- **测试步骤**: 12个完整业务步骤

### 通知功能
- 🚀 测试开始通知（蓝色卡片）
- ✅ 测试成功通知（绿色卡片）
- ❌ 测试失败通知（红色卡片，包含错误详情）

## 🔧 配置步骤

### 1. 飞书机器人设置

#### 创建飞书机器人
1. 在飞书群聊中添加自定义机器人
2. 选择"自定义机器人"类型
3. 设置机器人名称和头像
4. 配置安全设置（建议选择"自定义关键词"：E2E测试）
5. 复制生成的 Webhook URL

#### 配置 GitHub Secrets
1. 进入GitHub仓库设置页面
2. 选择 "Secrets and variables" → "Actions"
3. 点击 "New repository secret"
4. 添加以下Secret：
   ```
   Name: FEISHU_WEBHOOK_URL
   Value: [你的飞书机器人Webhook URL]
   ```

### 2. 环境配置确认

确保项目中存在 `.env` 文件，包含以下配置：
```env
# API 基础地址
BASE_URL=http://110.40.159.145:9099

# MySQL 数据库配置
MYSQL_MAIN__DEFAULT__DSN=mysql://root:password@110.40.159.145:8020/ecommerce
MYSQL_MAIN__DEFAULT__CHARSET=utf8mb4

# 测试用户凭证
USER_PASSWORD=Test@123456
ADMIN_USERNAME=admin
ADMIN_PASSWORD=Admin@123456
```

### 3. Drun框架安装

流水线会自动安装Drun框架，如果遇到问题，请确保：
- 网络连接正常
- PyPI源可访问
- 如果需要，可配置私有PyPI源

## 📊 测试报告

### 报告生成
- **HTML报告**: 可视化测试结果，包含详细步骤和截图
- **JSON报告**: 机器可读格式，便于CI/CD集成
- **GitHub Summary**: 在Actions页面显示测试摘要

### 报告访问
1. 进入GitHub仓库的Actions页面
2. 点击对应的workflow运行记录
3. 在 "Artifacts" 部分下载 `e2e-test-reports`
4. 解压后查看 `reports/e2e_*/e2e_report.html`

## 🔔 通知示例

### 测试开始通知
```
🚀 E2E业务流程测试开始
测试项目: E-commerce API E2E测试
触发方式: push
分支: main
提交者: your-username
提交信息: feat: add new feature
测试用例: test_e2e_purchase.yaml
```

### 测试成功通知
```
✅ E2E业务流程测试通过
测试结果: 全部通过 ✅
测试用例: test_e2e_purchase.yaml
执行时间: 2025-01-01 10:30:00
分支: main
提交者: your-username
```

### 测试失败通知
```
❌ E2E业务流程测试失败
测试结果: 失败 ❌
测试用例: test_e2e_purchase.yaml
失败时间: 2025-01-01 10:30:00
分支: main
提交者: your-username

请及时检查测试失败原因并修复问题！
```

## 🛠️ 故障排查

### 常见问题

#### 1. 飞书通知发送失败
**原因**: Webhook URL配置错误或机器人权限问题
**解决方案**:
- 检查 `FEISHU_WEBHOOK_URL` Secret是否正确
- 确认飞书机器人在群聊中有发送消息权限
- 检查网络连接是否正常

#### 2. 测试执行失败
**原因**:
- Drun框架安装失败
- .env文件缺失或配置错误
- 测试环境连接问题

**解决方案**:
- 检查workflow日志中的详细错误信息
- 确认.env文件包含正确的配置
- 验证API服务是否正常运行

#### 3. 报告生成失败
**原因**: 权限问题或磁盘空间不足
**解决方案**:
- 检查reports目录创建权限
- 确认GitHub Actions有足够磁盘空间

### 调试步骤

1. **查看Workflow日志**
   ```
   GitHub仓库 → Actions → 选择对应运行记录 → 查看详细日志
   ```

2. **本地验证测试**
   ```bash
   # 确保本地环境可以正常运行
   drun run testcases/test_e2e_purchase.yaml
   ```

3. **检查环境变量**
   ```bash
   # 验证.env文件内容
   cat .env
   ```

## 📈 最佳实践

### 1. 提交规范
- 使用清晰的提交信息
- 避免在单次提交中包含过多变更
- 重要功能变更建议创建PR进行代码审查

### 2. 分支管理
- `main/master`: 生产环境代码
- `develop`: 开发环境代码
- 功能分支: `feature/功能名称`

### 3. 监控告警
- 及时关注飞书通知
- 定期检查测试报告
- 建立失败响应机制

## 🔄 更新维护

### 流水线配置更新
- 编辑 `.github/workflows/e2e-pipeline.yml`
- 提交变更后自动生效
- 建议先在测试分支验证

### 测试用例更新
- 修改 `testcases/test_e2e_purchase.yaml`
- 确保测试步骤与业务流程一致
- 定期review和优化测试用例

---

## 📞 技术支持

如遇到问题，请：
1. 查看GitHub Actions日志
2. 检查飞书机器人配置
3. 验证环境配置文件
4. 联系项目维护团队