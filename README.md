# WorkBuddy Local Dev

一个用于 Codex + WorkBuddy 协作开发的本地 Skill。

## 它解决什么问题？

当你希望由 Codex 负责需求分析、技术规划和最终审查，再让电脑上的 WorkBuddy 负责真正编写代码时，这个 Skill 会把整个流程固定下来。

它优先使用 WorkBuddy 自带的 headless CLI，直接操作 Windows 本地项目目录，不依赖反复截图、鼠标点击或浏览器沙箱。因此更快、更省 Token，也能避免代码被写入远程临时环境。

## 分工方式

```text
用户需求
   ↓
Codex：分析需求、拆解任务、制定验收标准
   ↓
WorkBuddy：在本机项目目录中创建或修改代码
   ↓
Codex：检查真实文件、运行测试/构建、审查质量
   ↓
交付结果与启动说明
```

## 调用方式

在 Codex 中调用：

```text
$workbuddy-local-dev
```

也可以直接描述任务，例如：

```text
使用 WorkBuddy 帮我修改 E:\\AIWork\\demo-app，完成后由你检查构建和代码质量。
```

Skill 会自动识别本地 WorkBuddy CLI，通常使用以下入口：

```text
D:\\WorkBuddy\\resources\\app.asar.unpacked\\cli\\bin\\codebuddy
```

## 标准工作流程

1. 确认项目目录和现有技术栈。
2. 由 Codex 整理实现 brief、交互要求和验收标准。
3. 通过 WorkBuddy headless CLI 执行编码任务。
4. 直接检查项目目录中的真实文件，而不是只相信 Agent 的文字总结。
5. 运行项目已有的测试、Lint、类型检查、自检脚本或生产构建。
6. 发现问题时，优先发送精确的修复任务；必要时由 Codex 直接修正。
7. 汇报变更文件、验证结果、启动方式和剩余限制。

## 适用场景

- 创建网页、后台、工具和小型应用
- 修改 React、Vue、HTML/CSS/JS、Node.js 项目
- 修复 Bug、补测试、运行构建和检查代码质量
- 让 WorkBuddy 批量处理本地文件，同时由 Codex 做技术把关

## 设计原则

- 只修改用户明确指定的项目目录。
- 不把远程沙箱中的“已完成”当作本地完成证据。
- 不使用高频桌面截图作为主要编码方式。
- 不执行未经用户授权的删除、重置或外部发布操作。
- 对网页任务同时检查功能、响应式布局、可访问性和静态资源路径。

## Skill 文件

- [SKILL.md](./skills/workbuddy-local-dev/SKILL.md)：完整工作流定义
- [workbuddy-local-dev.skill](./workbuddy-local-dev.skill)：可安装的 Skill 包
- [evals.json](./skills/workbuddy-local-dev/evals/evals.json)：基础测试用例

## 说明

这是个人开发效率工具，不属于腾讯、WorkBuddy 或 OpenAI 官方发布的软件包。使用时请确认本机账户、项目权限和代码变更范围。
