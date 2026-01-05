# 🎬 plot-yaml 剧情导演系统

欢迎各位作家、编剧老师！👋

这里是我们游戏的**剧情中枢**。如果说对话文本（Dialog）是角色的台词，场景里该发生什么，那么这个仓库里的脚本就是**导演的剧本**。

在这个仓库里，我们不写具体的“你好，吃饭了吗”，我们决定**什么时候**说这句话，玩家要干些什么，以及**剧情分支**走向哪里。

## 🌟 它是做什么的？

这个系统是为了让我们摆脱复杂的程序代码，用最直观的方式组织剧情。它主要负责：

*   **场务**：加载具体的文案资源。绝对不要一股脑加载所有对话资源，首先这会让文件变得过于冗长，并且Godot不太擅长处理大量的瞬时资源加载，理论上可以用按帧加载优化，但是目前我们没有这个精力。
*   **导演**：决定下一段对话是谁说的（引用 `.dialog` 文件中的Snippet）。
*   **逻辑**：根据好感度 (`isLove`) 或生死状态 (`isDeath`) 决定玩家进入哪个结局。

---

## 🚀 如何开始工作 (给作家组)

不用担心，你不需要懂编程！我已经配置好了环境，你只需要像填表一样写东西。

### 1. 准备工具
下载 **VS Code** (Visual Studio Code)。IntelliJ和Sublime Text正在WIP，不过估计直到游戏上线你们都看不到它了。
*   虽然记事本也能写，但 VS Code 会给你**颜色高亮**和**智能提示**，还有自动补全，能帮你检查 90% 的错误。
*   需要下载RedHat的YAML插件，另外说一句这家伙很严，极其严格的遵守YAML标准，我推荐你听它的话，但是理论上稍微不遵守一点也无事发生。

### 2. 打开项目
将本仓库克隆/下载到本地，用编辑器打开文件夹。

### 3. 开始编写
新建一个 `.yaml` 文件（或者复制现有的），尝试输入 `SceneGroup:` 然后按回车。
*   ✨ **神奇的事情发生了**：编辑器会自动弹出提示，告诉你接下来该填什么。
*   🚨 **如果报错**：如果你拼错了单词，或者格式不对，编辑器会标红。

### 🌰 举个栗子
```yaml
## Scene
GlobalConfig: ## Required
  ## 名字，目前没有用途
  name: "艾玛死掉啦"
  hasBranch: 

SceneGroup: ## Required
  - useLocalVariable: { ## Required
      isLove: false,
      isDeath: true,
      isrobot: false,
      var123456spam: false, ## use as many as u want
    }
    useDialogFile: [ ## Required
      ./dialoga.dialog,
      ./dialogb.dialog,
      ./intro.dialog,
      ./outro.dialog,
    ]
    onSetup:
      charSetup:
        -
          char: ema ## Required
          position: "0,0"
          defaultDialog: null
          dialoglist: [
            dialoga,
            dialogb,
          ]
        -
          char: hiro ## Required

    onIntro:
      playDialog: intro

    onFreeMove: ## Required
      progressSchema: by-match-variables ## Required
      matchSchema: {
        isLove: false,
        isDeath: true,
        isrobot: false
      }
    onOutro:
      playDialog: outro
  - # Scene #2
    useLocalVariable: {
      isLove: false,
      isDeath: true,
      isrobot: false,
    }
    useDialogFile: [ ## Required
      ./dialoga.dialog,
      ./dialogb.dialog,
      ./intro.dialog,
      ./outro.dialog,
    ]
    onFreeMove:
      progressSchema: manual

    

```

---

## 🚧 当前开发进度 (必读！)

我知道大家很急着想看到画面动起来，但作为唯一的程序架构师，我需要向大家说明目前的状况：

### ✅ 现在能做的
*   **写剧情逻辑**：现在的 DSL 语法规范已经确定。
*   **语法检查**：编辑器已经可以帮大家检查拼写错误和结构错误。
*   **数据录入**：大家现在写的每一行脚本，未来都会直接被游戏引擎读取。

### ❌ 暂时还没做好的 (请不要催我 😭)
1.  **还没有“运行”按钮**：目前游戏引擎（Runtime）还在开发中，所以写完脚本后**无法立即在游戏里玩到**。
2.  **还没有逻辑检查器 (Linter)**：编辑器只能检查格式（比如你是不是少写了冒号），但无法检查逻辑（比如你跳转到了一个不存在的章节）。这部分我会尽快补上。
3.  **还没有流程图 (Flow map)**：目前只能看文字，还没法自动生成可视化的连线图。

---

## 📅 Roadmap:

开发计划如下：

1.  **阶段一（进行中）**：确定 DSL 语法，让作家们能先动笔写起来。（❓ 达成~~大概~~）
2.  **阶段二（下周目标）**：发布一个简单的检查工具Linter，如果你写了循环依赖或者没妈的孩子，它会报警。
3.  **阶段三（近期目标）**：开发 **可视化流程图生成器**，把你们写的脚本自动变成一张图，方便导演审视剧情结构。它会和Linter结合起来并把Linter单独发布为可独立运行的命令行工具。
4.  **阶段四（最终目标）**：接入游戏引擎，让文字变成真正的游戏画面！

---

## ❓ 常见问题

**Q: 我写错了怎么办？**
A: 如果编辑器标红了，把鼠标放上去看看提示。如果实在搞不定，截图发群里@我。

**Q: 为什么不用连线（Aka Unreal蓝图&Blender节点）的方式做剧情？**
A: 这是一个恐怖的工作量。

**Q: 我有一些复杂的想法，目前的语法不支持怎么办？**
A: 在群里提需求！我会评估后扩展这个 DSL 的功能。