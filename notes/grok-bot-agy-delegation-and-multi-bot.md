# Grok Bot 多 Agent 调度：额度别烧在一个地方

最近 Antigravity（`agy`）封控、限 IP 挺狠，本机经常登不上或用不稳。我们把 CLI 装进 **Grok Bot 自带的云主机**里登录、调用——机器在云端，认证和日常跑通反而稳。

这篇文章说的是另一件事：**别让一个 Bot 把活干完。** 手上有 SuperGrok / Google AI Pro / 别的订阅时，该谁出额度就谁出，调度和执行拆开。

文里的安装、截图、录屏、改例行、发 GitHub 和站点，基本都是 Bot 自动做的；中间只打断过你几次（Google 登录授权之类）。

![调度示意](../assets/images/workflow.png)

---

## 角色怎么分

| 谁 | 干什么 | 烧什么 |
|---|---|---|
| 大管家 bot | 听需求、拆任务、派人、验收 | Grok Bot 额度（轻量） |
| topstream 等专职 bot | 例行、发布、自愈、守自己的站 | 同上；重活再往下派 |
| `agy` | 研究、长草稿、明确的执行 | Google AI Pro |
| 以后别的 CLI / API | 可替换的执行层 | 你自己的低成本订阅 |

**Bot 额度**适合：对话、判断、协调、点一下发布脚本。  
**agy（或别的执行端）**适合：目标清楚、会啃很多 token 的活。  
**碰登录、OAuth、隧道、密钥**：专职 bot 自己来，别派 agy。

---

## 为啥要塞进 Grok Bot 云主机

本机跑 `agy`，IP / 环境一变就容易卡在认证。Grok Bot 每台 bot 有持久 Linux 环境：装一次、登一次，后面调度端直接：

```bash
export PATH="$HOME/.local/bin:$PATH"
agy -p "自包含任务" --output-format json --print-timeout 15m
```

我们装的是 1.1.27，Google AI Pro 登录后 `agy models` 和无头 `-p` 都通了。

![agy 模型列表](../assets/images/agy-models.png)

![无头 JSON 回执](../assets/images/agy-headless-json.png)

---

## 什么时候用 Bot，什么时候派 agy

实操里就四条：

1. **还没想清楚** → 先跟大管家聊，别开 `agy`。
2. **已经能写成一段完整指令**（路径、成功标准、别做什么）→ 派 `agy -p`。
3. **要发站、改例行、修隧道** → topstream（或对应专职 bot）；草稿可以 agy 写，发布脚本本机跑。
4. **只要人点确认**（登录、2FA）→ 把桌面交还给你，Bot 不碰密码。

TopStream「每日 X 精选」改过一版：以前研究+写作全压在 bot 身上，额度烫；现在 **agy 出研究草稿，topstream 验收、去重、发布**。入口自愈仍是 topstream 自己跑，不外包。

![精选笔记页](../assets/images/topstream-daily-note.png)

---

## 可扩展的用法

执行层不必绑死 `agy`。同一套调度可以把重活指到：

- 别的官方 CLI（有 headless / print 就行）
- 便宜的 API 额度、按量模型
- 本机脚本 + 小模型做机械整理

原则不变：**调度留在 Grok Bot，执行花「更合适」的那份订阅。**

---

## 这篇是怎么出来的

大管家起草 → 桌面截图/录屏 → topstream 推到 `topmindspace/topstream` 并同步站点。你主要参与的是登录授权。录屏在同目录 `agy-demo.mp4`。

- GitHub：`notes/grok-bot-agy-delegation-and-multi-bot.md`
- 站内也会更新同一篇

![广场](../assets/images/topstream-plaza.png)
