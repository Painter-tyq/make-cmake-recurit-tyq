# Coding Agent 实验笔记
> 实验环境：Windows + WSL2(Ubuntu) + Cursor IDE

## 任务1
- 使用的Agent：Cursor内置 Coding Agent（Cursor Grok 4.6 Medium）
- 安装和配置过程：
  1. Windows 系统安装 Cursor IDE；
  2. 在 Cursor 中启用 WSL 远程连接能力；
  3. 使用 `WSL: Connect to WSL in New Window` 连接 WSL Ubuntu；
  4. 在 WSL 环境打开已有的 Git 仓库 `make-cmake-recurit-tyq`，完成 Coding Agent 的环境配置。
- 交给Agent的任务：
  1. 只读方式阅读整个仓库，介绍仓库内容，不修改任何文件；
  2. 整理优化仓库内 README.md，优化排版，补充仓库简介，不改动其他源码文件。
- Agent做了哪些修改：仅修改 README.md；补充仓库介绍，说明仓库内 `make-task/`（手写Makefile构建）、`cmake-task/`（CMake构建）两个独立项目；整理目录说明，完善 check.sh 自检脚本的描述；其余源码文件保持不变。
- git diff 中看到了什么：`git diff` 只展示 README.md 的改动，绿色加号代表新增内容，没有其他文件变更；直接执行 git diff 会进入 less 分页器，出现 `:...skipping...`，使用 `git --no-pager diff` 关闭分页器，完整查看改动内容。
- 最终结果是否符合预期：符合预期。Agent 只修改 README.md，没有误改源码，文档排版优化完成。

## 任务2
1. Agent 为什么能够读取文件、修改代码，而普通聊天 AI 通常不能？

- 普通聊天AI只具备文本对话能力，无法访问本地文件系统，不能读写磁盘文件。Coding Agent 挂载了工具（Tool），可以调用工具读取本地文件、写入文件、执行终端命令，因此可以直接读取、修改项目代码。

2. Tool 在 Agent 中起到了什么作用？

- Tool（工具）是Agent对外的能力接口。大模型本身只能生成文字，依靠Tool，Agent才能完成读文件、写文件、运行shell命令、执行git命令等真实操作；Tool把大模型推理生成的结果落地为实际动作，是Agent和本地项目环境交互的桥梁。

3. 为什么项目需要给 Agent 配置一份类似“员工手册”的规则？

- 这份规则一般是项目内的说明文件（如 AGENTS.md），用来约束Agent行为：告知Agent项目代码规范、目录结构、允许和禁止的操作，避免Agent乱改文件、写出不符合项目风格的代码，减少误操作，让Agent产出结果更贴合项目要求。

4. Agent 为什么可能“忘记”之前说过的内容？Agent的额度怎么样计算？

- Agent的上下文窗口存在token长度上限。当对话内容、读取的文件总量超过上下文容量，最早的内容会被挤出上下文窗口，Agent就会“忘记”前面的信息。
额度按照消耗的token总量计算：输入token（用户提问 + Agent读取的文件内容）+ 输出token（Agent生成的回答、代码），两者都计入消耗，读取大量文件会快速消耗token额度。

5. 如果一个 Agent 可以随便执行任何终端命令，会有什么风险？

- Agent有可能误执行危险命令，例如 `rm -rf` 删除文件、修改系统配置、误推送代码；也可能泄露仓库内文件内容；严重时会破坏WSL环境，造成代码丢失。因此需要限制Agent可执行终端命令的权限。

## 任务3

### 探索记录
本次使用 Cursor IDE，查阅 Cursor 官方文档学习 MCP。Cursor 原生支持 MCP 协议，可以配置 MCP Server，给 Agent 接入外部数据源与工具（如 GitHub MCP，Agent可直接读取GitHub仓库信息）。  
收获：理解 Tools 是最小能力单元，Skill 是封装好的整套任务流程，MCP 是Agent扩展外部能力的标准协议。在科研场景中，MCP 可以让 Agent 对接文献检索、数据库等外部工具，提升科研效率。