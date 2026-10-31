# TurtleBot3 Burger 室內導航與任務決策 AI Agent

以中文任務、命名地點、機器人狀態與導航結果，設計可澄清、驗證、執行及回報的室內導航代理人。

## 目前狀態

本版本是 **期中 Proposal 與離線驗證範例**。報告、架構、流程、演算法、實驗設計及介面規格已建立。
`src/agent_demo.py` 只驗證預先給定的結構化任務，使用 mock 狀態和 mock 導航結果。
**尚未接入 LLM、ROS 2、Nav2 或實體小車，不是完整導航系統，也不代表實驗成效。**

正式儲存庫 URL：https://github.com/hank050392/tb3-navigation-agent
組別：第 7 組。組員：陳至恆、蕭丞皓、林瑞裕、邱原美、官奕愷；學號參閱 `docs/TEAM.md`。

## 問題與使用情境

使用者說「去實驗桌」後，Agent 解析為已登錄地點，檢查資料時效與系統狀態，再透過導航工具執行。
地點含糊時先詢問；失敗、取消和逾時如實回報，不由模型自行宣稱到達。
第一版限單台 Burger、單一室內平面、預建地圖和人工登錄地點。

## 文件

- [提案報告 PDF](docs/TB3_Proposal.pdf)：最新版 A4 直式學術報告。
- [提案簡報 PPTX](docs/TB3_Proposal.pptx)：可編輯簡報。

- `PROPOSAL.md`：完整提案，含摘要、緒論、文獻表、Deep Research、設計與參考文獻。
- `DESIGN.md`：系統責任、資料、狀態、演算法、介面與驗證。
- `docs/EXPERIMENTS.md`：三方法比較、兩項消融、資料切分與指標定義。
- `docs/ARCHITECTURE.mmd`、`docs/FLOWCHART.mmd`：可編輯 Mermaid 圖。
- `docs/GITHUB_UPLOAD.md`：建立及上傳正式儲存庫的步驟。
- `docs/SUBMISSION_CHECKLIST.md`：Proposal 繳交檢查。

## Installation

離線範例使用 Python 3.10 以上，沒有第三方套件。從專案根目錄執行下列命令。

## How to Run

```bash
python3 src/agent_demo.py --request examples/navigate.json --scenario ready
python3 src/agent_demo.py --request examples/ambiguous.json --scenario ready
python3 src/agent_demo.py --request examples/navigate.json --scenario stale
python3 src/agent_demo.py --request examples/navigate.json --scenario blocked
python3 src/agent_demo.py --request examples/navigate.json --scenario failed
python3 -m unittest discover -s tests -v
```

輸出 JSON 包含 `demo_only: true`、狀態、mock evidence 與時間；`ARRIVED` 僅代表 mock 成功。
正式 LLM 應輸出結構化任務；此範例不會理解任意自然語言。

## Docker

```bash
docker build -t tb3-agent-proposal .
docker run --rm tb3-agent-proposal
docker run --rm tb3-agent-proposal python -m unittest discover -s tests -v
```

Dockerfile 僅包裝離線範例，不包含 ROS、Gazebo 或實機裝置權限。
本交付未建置 Docker image；需在有 Docker 的環境驗證。

## 預計 ROS 2 整合

先固定與實機配套的 ROS 2／Nav2 版本，再實作 `docs/INTERFACES.md` 的導航 adapter。
不可把示範地點座標直接發給實機。`config/landmarks.json` 中座標全部是離線示範資料。
詳細啟動步驟需待實機版本和 topic 核對後加入，現階段不提供假裝可用的 bringup 指令。

## Experiments and Results

目前只有離線驗證測試；沒有語言模型、模擬導航或實機比較結果。
實驗規畫見 `docs/EXPERIMENTS.md`；結果 CSV 只有欄位，不填虛構數字。

## AI 使用

AI 協助提案與程式骨架製作；組員須核對來源、理解設計並完成實驗。
參考來源見 `docs/REFERENCES.md`。尚未決定對外發布授權；未附第三方論文全文或程式碼。
