# Orca —— Why / What / How（中文版）

> 本篇是 [philosophy.md](philosophy.md) 的中文翻譯，英文版為正本（repo 慣例：`traces/` 底下的 prose 以英文為主）。若兩者有出入，以英文版為準。

方法論：用三種來源互相印證，因為每種回答的問題不一樣。文件站（onorca.dev/docs）給的是「**官方宣稱的** why」——那是廠商自己的敘事，要當成一種主張，不是證據。原始碼（`AGENTS.md`、`docs/*.md`、模組結構）給的是「how」——實際蓋出來的東西。Git log / release notes 隨時間的變化給的是「什麼才是真正重要的事」——不管文件怎麼講，數個月的 commit 投在哪裡，才是誠實訊號。

## Why（為什麼做這個）

文件明確講出目標使用者：

> "Orca is designed for people who already write code for a living and want to use AI as leverage — not as a replacement. It assumes you read diffs, care about commits, and keep a worktree tidy."
> （Orca 是為那些本來就靠寫程式維生、想把 AI 當「槓桿」而不是「替代品」的人設計的。它假設你會看 diff、在乎 commit、也會把 worktree 收拾乾淨。）

這是刻意站在「vibe coding」（給非工程師用的 AI 全自動工具）對立面的立場——Orca 把自己定位給那些本來就會 review diff、掌握 git workflow 的人。背後的賭注是：開發者早就同時付錢訂閱好幾個 agent CLI（Claude Code、Codex、Cursor……），Orca 的工作是「編排你已經擁有的東西」，而不是自己下場做一個模型/產品跟這些 agent 競爭。

文件自己在講 worktree 模型時，哲學論證其實蠻單薄，就一句：*"This is what makes parallel agents safe — they never step on each other's files."*（這就是讓多個 agent 平行跑還安全的原因——它們永遠不會互相踩到對方的檔案。）「為什麼選 worktree」的理由是「隔離帶來安全」，講得很直白，完全沒去比較 container / branch / 其他方案——這暗示這個選擇是務實考量（git-native、成本低、開發者本來就熟）而不是經過一番天人交戰。

## What（做出來的東西長什麼樣）

文件自己在「The Orca Model」這節講的最小單位：**一個 worktree = 一個 agent terminal = 一個 browser tab。** 其他所有東西都是疊在這個單位上的一層：

- **同時賽跑多個 agent** —— 招牌用例（"three agents trying the same bug in parallel"，三個 agent 同時試著修同一個 bug），本質是在對沖單一 agent 輸出的不確定性，而不是相信任何一個 agent 第一次就會給對答案。
- **以 review 為核心的介面** —— 大量投資在 diff viewer、"Annotate AI Diff"、attribution（歸因）、commit/push、託管的 PR/issue/Actions review。這個哲學假設「人永遠會在上線前 review」，所以產品優化的是 review 這一步，不是生成那一步。
- **跨環境觸及** —— SSH worktree、遠端 Orca server、WSL 支援。「槓桿」這件事只有在 agent 能跑在開發者真正的 repo 和 infra 上才成立，不是一個被關起來的沙盒 checkout。
- **信任建立後才開放的自動化** —— CLI、orchestration（task DAG、decision gate）、排程自動化、computer use、skills registry/MCP。這一層是給那些已經過了「監督單一 agent」階段、想在 fleet 層級組裝工作流的使用者。
- **手機伴侶 app** —— 是「監督」不是「保母」。開發者離開時 fleet 還在跑；手機是拿來收通知、回頭確認進度，不是拿來操作的。

## How（怎麼做到的）

- **用 git worktree 當隔離的基本單位**（不是 container/VM）—— 建立/刪除都很便宜，而且是 git-native，diff/branch/merge 完全不需要一層轉譯。信任邊界是「一份專屬 checkout 的檔案系統」，不是被沙盒化的 runtime。
- **relay process 當作協定邊界**（`src/relay/*-handler.ts`）—— 不管 worktree 是本機、WSL、還是 SSH 遠端，PTY、git、檔案系統操作都走同一套 socket 協定。這是「隨處可用的槓桿」背後具體的實作機制。
- **每個 agent provider 各自一個模組**（`src/main/{claude,codex,gemini,grok,cursor,devin,...}`）—— 加一個新 agent 是「加一個平行目錄」，不是「重新架構」。結構上就承諾了要保持 agent-agnostic（不綁死單一 agent），而不是賭某一個 agent 會贏。
- **內建的 skill / orchestration 系統**（`skills/orchestration`、`skills/orca-cli`）—— 把產品從「N 個各自獨立的 agent」推進到「一個能自己互相交接工作的 fleet」，讓使用者在對單一 agent 建立起信任之後，可以組出更大的工作流。這套 frontmatter description 路由的模式，跟 [[superpowers]] 裡看到的如出一轍，只是用途從「單一 agent 選自己的 skill」變成「agent 之間互相協調」。
- **把貢獻者規範寫成程式碼，而不是說明文字**（`AGENTS.md` + lint gate）—— 有一份真的按 Git 版本/host 分的相容性矩陣、禁止用含糊檔名的規則、還有一條「不准關掉 max-lines」的強制 lint gate。這就是一個 6,887 個 commit、才活了大約 4 個月、幾乎每天在 ship 的 codebase，還能維持可讀性的原因。

## 從 git / release history 看到的證據（真正花時間做的事）

Repo 起點：第一個 commit 在 2026-03-16，到釘住的 `v1.4.147` tag 為止累積 6,887 個 commit —— 平均下來每天 50 幾個 commit，跟第一輪追蹤時查到的「849 次 release / 幾乎每天在 ship」互相印證。

抽樣幾個小版號的 release notes，可以看到產品的重心隨時間在移動——這是文件本身不會直接講出來的訊號：

- **v1.1.0** —— 單一 agent 的體驗打磨："unified tabs system"（統一分頁系統）、"polish markdown preview and rich editor"（打磨 markdown 預覽和富文字編輯器）。此時產品主要還是在把「一個 agent 的工作區」做好。
- **v1.2.0** —— 可靠度硬化：split-group terminal 生命週期修正、SSH 連線硬化、"plug memory leaks, unbounded caches, and disk accumulation"（補記憶體洩漏、無上限快取、磁碟累積的洞）。這是「讓單一 agent 的 session 在真實使用下站得住」的階段。
- **v1.4.0** —— fleet 可視化："Show running agents in Activity threads"（在 Activity 討論串裡顯示正在跑的 agent）、"Make Activity default-on"（Activity 預設開啟）、"Improve Orca Mobile discoverability"（改善手機 app 的可發現性）。難題從「怎麼生出 agent」變成「不用死盯每個 pane 也能知道你的 fleet 在幹嘛」——一旦平行跑多個 agent 從新鮮事變成日常，這是自然會冒出來的下一個問題。

這個「單一 agent 打磨 → 可靠度 → fleet 可視化」的演進，是哲學透過 commit history 自己顯現出來的，不是透過行銷文案講出來的——它精準對應到「worktree = agent = tab」這個最小單位被大量複製之後，會壞掉什麼、會讓人搞不清楚什麼。

## 方法論的補充說明

這裡抽樣的 release notes 只有少數幾個小版號 tag（v1.0.12、v1.1.0、v1.2.0、v1.4.0——v1.3.0 在 GitHub 上沒有對應的 release 紀錄，可能是跳過或被撤下了）。更完整的做法應該抽更多小版號，並且比對文件站上 `docs/model/worktrees` 這類頁面隨時間的變化（如果文件站有版本紀錄的話），看看「宣稱的哲學」本身有沒有跟著變，而不只是功能集在變。
