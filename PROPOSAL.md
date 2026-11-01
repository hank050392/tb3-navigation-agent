# 以 TurtleBot3 Burger 為平台之室內導航與任務決策 AI Agent

## 專題提案

### 以 TurtleBot3 Burger 為平台之室內導航與任務決策 AI Agent

Indoor Navigation and Task Decision Agent for TurtleBot3 Burger

期中分組 Proposal｜五人專題｜2026 年 10 月 30 日

組別與五位組員姓名／學號：繳交前填入實際資料。

### 一、摘要 Abstract

A 動機：2025 年語言模型與 ROS 2 整合研究已將延遲列為機器人互動的重要問題 [3]；2026 年 BARN 挑戰仍以狹窄環境中的可靠導航為課題 [12]。對剛接觸漢堡小車的使用者而言，完成一次移動還需要理解地點、確認狀態並處理失敗。

B 挑戰：自然語言的地點可能含糊，模型也可能提出環境中不存在的目標；單純轉送指令無法保證導航完成，更無法保證回報正確。

C 解法：本專題提出以 TurtleBot3 Burger 為平台的室內導航與任務決策 AI Agent，讓使用者以中文指定已登錄的地點，並取得有執行依據的任務回報。

D 方法：系統結合 LLM 結構化解析、地點與領域知識檢索、狀態檢查、Nav2 工具呼叫及事件紀錄。底層控制由導航模組執行；Agent 根據回饋澄清、拒絕、派送或回報。

E 評估：預計建立 60 筆中文指令及四類導航場景，固定 Nav2 設定，比較固定介面、直接語言轉目標及完整 Agent。量測任務完成、錯誤派送、回報正確性、延遲、碰撞與到達表現。

F 預期成果：預期減少歧義指令造成的不當派送，提升失敗回報的可追溯性，並完成可重現的實驗與文件。本提案尚未產生導航實驗結果，所有效益均待驗證。

## 二、緒論 Introduction

### 2.1 動機與背景

TurtleBot3 Burger 是本專題的室內移動機器人平台。使用者以已建立的地圖和命名地點指定任務，系統再透過定位、規劃與控制完成移動。研究重點是讓任務從人類語言銜接至可驗證的執行結果，而不是重新發明底層導航演算法。

近期研究 [3] 顯示語言介面與 ROS 2 的整合仍須處理延遲；BARN 2026 持續比較不同導航方法，反映複雜環境仍有可靠性挑戰 [12]。兩者共同支持本專題同時關注互動與物理執行，而不只展示一個能回答問題的介面。

### 2.2 問題定義

目標使用者為實驗室學生與小車操作新手。給定中文任務、已登錄地點、地圖及機器人狀態，系統必須決定是否需要澄清、能否派送導航，以及最後應回報到達、取消、失敗或逾時。

### 2.3 現有方法的限制

Nav2 解決導航執行，但不直接處理中文指令歧義；LLM 能解析語言，卻不能把語言合理性當成動作可行性的證據 [2][4]。若只做「文字轉座標」，還可能出現不存在的地點、過期狀態、重複派送，以及任務尚未成功卻聲稱到達的問題。這些是本專題要測試的系統風險，不代表所有既有系統都有相同缺陷。

### 2.4 設計特色與預期貢獻

本專題的貢獻定位為教學與實驗室場景的系統整合，不宣稱首次提出 LLM 導航。預計完成：一套經白名單驗證的中文任務介面；一個整合定位與感測檢查的任務管理器；一份以導航回饋為依據的結果紀錄；以及可區分語言層與導航層成效的比較實驗。

### 2.5 範圍

第一版限單台 Burger、單一室內平面、預建地圖與人工命名地點，不辨識任意物體、不執行取放、不訓練端對端 RL。主要障礙物為雷射可偵測的固定物體；動態障礙物與實機長期運行列為後續延伸。

## 三、相關研究與研究缺口

### 3.1 AI 技術研究

AI 文獻以 ReAct、SayCan 與 2025 年 ROS 2 語言互動研究為主 [1][2][3]。本組借鏡工具行動與觀測交替、依機器人能力限制任務，以及量測整合延遲的設計。LLM 初版採既有模型，不自行訓練；模型只輸出符合 schema 的任務與工具參數。

### 3.2 領域知識研究

領域研究以 Nav2、SLAM Toolbox 與 BARN 的導航實作及測試為主 [4][5][6]。重點包括差速小車的車身範圍、地圖與定位、雷射限制、規劃與控制分工，以及狹窄環境的評估。AI 與領域文獻各三篇，後兩頁以相同八欄整理。

### 3.3 證據強度與限制

文獻表區分作者已報告的成果與本組推論的適用限制。官方技術文件 [8]–[11] 補充介面與工程設定，不當成獨立實驗論文。NavBench 2025 [7] 作為跨平台評估的補充；其結果不能直接代表 Burger。較舊的基礎論文保留以交代方法來源，研究動機另納入 2025–2026 的資料 [3][7][12]。

### 3.4 Existing Research → Limitations → Research Gap → Our Project

| 層次 | 本專題歸納 |
| --- | --- |
| 現有研究 | 已有語言工具代理、技能可行性約束、模組化導航與標準化測試。 |
| 適用限制 | 平台、任務與評估各異，不能直接得到本實驗室中文任務的可靠性結論。 |
| 研究缺口 | 在固定 Burger／Nav2 條件下，任務澄清、狀態檢查與證據回報的增益尚待本組量測。 |
| 本專題 | 以受限工具 Agent 串接上述流程，進行三組比較與兩項消融，揭露效益與失敗。 |

研究問題 RQ1：Agent 是否降低歧義／未知目標的不當派送？RQ2：狀態檢查是否降低故障情境下的派送？RQ3：依事件紀錄生成回報，是否比直接語言轉目標更準確？

## 3.5 Article Summary Table  AI 技術

前三篇為 AI 技術文獻。Findings 為原研究的摘要；Limitation 為本組對本專題適用性的界定，不是宣稱原研究沒有其他能力。

| Ref. | Year | Problem | AI Method | Domain Knowledge | Dataset | Findings | Limitation |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [1] | 2023 | 語言推理與外部行動分離 | ReAct；交替工具行動與觀測 | 依環境回饋更新任務 | HotpotQA、FEVER、ALFWorld、WebShop | 展示工具互動對任務處理的效益 | 非 Burger 實機導航；不能推論實體安全 |
| [2] | 2022 | 語言計畫可能無法執行 | LLM 結合技能可行性／價值函數 | 動作必須符合機器人能力 | 移動操作機器人的真實任務 | 以技能能力約束語言規劃 | 依賴已有技能；非本專題差速小車場景 |
| [3] | 2025 | 語言控制的通訊延遲 | ChatGPT 與 ROS 2 整合架構 | 任務解析與機器人執行的介面 | Gazebo 模擬移動機器人 | 報告其架構的延遲改善 | 模擬與特定整合條件；非本專題實機結果 |

採用方式：本專題以 [1] 的觀測回饋迴圈、[2] 的動作可行性約束及 [3] 的延遲量測作為設計依據。初版不重製這些論文的訓練程序或直接沿用其實驗數字。

## 3.6 Article Summary Table  領域知識

後三篇為室內導航與建圖的領域文獻。與前表合計六篇，AI 與領域知識各占一半。

| Ref. | Year | Problem | AI Method | Domain Knowledge | Dataset | Findings | Limitation |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [4] | 2020 | 導航模組的協調與執行 | Nav2 行為樹；非 LLM | 定位、規劃、控制與恢復 | 校園長距離導航實驗 | 提供模組化 ROS 2 導航系統 | 不直接解決中文地點歧義與自然語言任務 |
| [5] | 2021 | 室內建圖與地圖維護 | 圖最佳化 SLAM；非 LLM | 雷射、里程計與地圖一致性 | 軟體與應用案例；無統一語言測試集 | 提供建圖、定位及地圖維護工具 | 不是語意任務規劃或完整避障系統 |
| [6] | 2022 | 擁擠狹窄環境的可靠導航 | 比較參賽導航系統；非單一 AI | 差速運動與真實通行限制 | BARN 模擬與實體障礙場地 | 顯示狹窄場景仍有失敗與部署挑戰 | 平台及場景不同；不可直接移用分數至 Burger |

採用方式：以 [4] 的模組化導航作為基準、[5] 支持地圖工作流程、[6] 支持多情境與實體驗證。BARN 原平台及地圖尺度與 Burger 不同；如使用其環境，需先確認車身比例、解析度與測試條件，不能直接比較排行榜。

## 四、Deep Research 五個核心思維

本節整理廣泛採用的導航設計原則，不將「所有專家一致同意」當成可證明的事實。每項依序列出意義、證據與系統設計。

### 4.1.1 定位是一個帶有不確定性的估計

意義：位置與朝向不是完全可靠的真值。輪子打滑、環境重複或資料延遲，都可能使估計與真實狀態不同。

證據：Nav2 需要定位資訊；SLAM Toolbox 處理建圖及姿態估計 [4][5]。

設計：任務前檢查定位與 TF 時效；不確定時暫停導航並請使用者處理。初版不把單一 covariance 門檻當作正確定位的保證。

### 4.1.2 感測不到不代表沒有障礙物

意義：2D LiDAR 只觀測掃描平面，且有遮蔽與量測限制；零值、無限值和資料中斷須依驅動語義處理。

證據：Burger 使用雷射、IMU 與編碼器等感測設備，實際 LDS 型號須核對 [8]。

設計：感測過期或無效時禁止啟動；實驗使用掃描平面可偵測的障礙物，並記錄盲區限制。

### 4.1.3 路徑可行性必須考慮車身與運動限制

意義：小車不是沒有體積的點。即使中心線無碰撞，車身、轉動空間或速度限制仍可能使路線不可執行。

證據：Nav2 調校指南說明 footprint 與碰撞檢查的重要性 [9]。

設計：採用核對後的車身尺寸；測試窄通道；安全距離與速度設定經實驗校正，不任意縮小車身以提高到達率。

## 4.1 核心思維與 Agent 設計

### 4.1.4 把語意任務與即時控制分層處理

意義：Agent 決定去哪裡、是否需要澄清與如何回報；導航模組決定可行路徑和即時運動。

證據：SayCan 強調語言需受到技能能力約束；Nav2 提供導航模組 [2][4]。

設計：LLM 只能提出白名單工具呼叫；經規則驗證後才派送已登錄地點，不能直接輸出輪速或任意座標。

### 4.1.5 可靠性來自重複測試與失敗分析

意義：單次成功不能代表換地圖、加入障礙物或定位變差後仍可靠。整體任務要拆成語言、導航與回報三層評估。

證據：BARN 和 NavBench 提倡標準化場景與可比較的導航評估 [6][7][12]。

設計：固定基準設定、保留失敗與逾時，報告成功率及延遲分布；明確區分模擬與實機結果。

### 4.1.6 原則如何落實

| 原則 | 預計程式責任 | 驗證方式 |
| --- | --- | --- |
| 定位不確定 | status_adapter 與 guard | 過期定位、TF 中斷注入 |
| 感測有限 | status_adapter 與底層停止 | 雷射中斷、遮蔽情境 |
| 車身限制 | Nav2 設定與 map registry | 窄通道與 footprint 核對 |
| 分層處理 | intent parser 與 tool gateway | 非法工具／座標拒絕 |
| 重複評估 | logger 與 evaluation | 固定案例、重複與失敗統計 |

上表為期末預計模組與驗證；隨附離線範例僅展示結構化任務驗證與模擬回報，不代表 ROS 2 或實機整合已完成。

## 4.2.1 研究爭論 模組化導航與端對端學習

這是具代表性的設計取捨，沒有可客觀驗證的「爭論最激烈」排名。

### 觀點 A

模組化系統讓定位、規劃和控制的責任清楚，較容易檢查錯誤並使用現有工具。

### 觀點 B

端對端學習希望由資料學到感測與動作之間的關係，在某些複雜場景減少人工設計。

### 雙方證據與可比較性

A 的實際支持是 Nav2 的校園導航實驗 [4]；B 的支持是 NavBench 2025 展示 RL 策略跨多種平台的模擬及實體轉移 [7]。兩者使用不同任務，不能據此直接宣稱任一方全面勝出。BARN 2026 提供傳統、學習與混合方法共同測試的框架 [12]。

### 目前可採納的共同立場

較穩健的共同評估原則是：以相同任務、算力與測試場景比較成功、碰撞和延遲；這是本組依文獻歸納的立場，不是全體專家投票結果。

### 尚未解決的問題

未知環境的泛化、學習策略的失敗界線，以及低成本實機驗證仍需研究。

### 本專題的設計決定

本專題先用 Nav2 作為固定底層，研究 Agent 在任務澄清與失敗回報上的效益，初版不重新訓練 RL。

## 4.2.2 研究爭論 預先地圖導航與無地圖導航

這是具代表性的設計取捨，沒有可客觀驗證的「爭論最激烈」排名。

### 觀點 A

預先地圖提供全域空間關係，有利於命名地點、長距離規劃及死巷判斷。

### 觀點 B

無地圖方法可根據局部感測行動，減少事先建圖需求，適合探索或環境變化情境。

### 雙方證據與可比較性

A 的實作依據是 SLAM Toolbox 與 Nav2 的地圖式流程 [4][5]；B 的可行性由 BARN 的局部導航測試設定支持：使用標準感測與目標評估導航 [6][12]。這證明可研究局部感測導航，不代表所有參賽方法均完全沒有內部地圖或記憶。

### 目前可採納的共同立場

地圖可能過時，局部感測也缺少全域資訊；應依任務選擇資訊來源，不能把未知區域直接視為安全通道。

### 尚未解決的問題

長期地圖更新、感測盲區與局部策略陷入死巷，仍需要有效處理。

### 本專題的設計決定

採預建地圖與命名地點表，配合即時代價地圖偵測新障礙物；陌生地點由 Agent 澄清，不讓 LLM 猜座標。

## 4.2.3 研究爭論 保守安全距離與通行效率

這是具代表性的設計取捨，沒有可客觀驗證的「爭論最激烈」排名。

### 觀點 A

較低速度與較大距離可容納定位誤差、控制誤差和反應延遲。

### 觀點 B

過於保守會讓可通行的窄路被排除，增加繞行、停滯與任務失敗。

### 雙方證據與可比較性

Nav2 的調校指南同時討論障礙物膨脹代價與正確車身形狀對狹窄通行的影響 [9]；BARN 實體挑戰則提供狹窄環境仍難以兼顧速度與無碰撞的案例 [6]。這些是機制與場景證據，尚不是對本小車最佳參數的量化證明。

### 目前可採納的共同立場

以碰撞、到達率、完成時間與最小距離共同評估，不能只追求最快，也不能以完全不移動冒充安全成功。

### 尚未解決的問題

如何依定位誤差、感測延遲與通道寬度自動調整速度及距離，仍是設計問題。

### 本專題的設計決定

先固定一組經實測的保守底層設定；參數敏感度另列選配實驗。Agent 不在行駛中任意修改 footprint 或安全參數。

## 4.3 深度理解問題 1 至 5

問題用來檢查推理與實作判斷。每題可依「是否解釋原因、提出可觀察證據、說明取捨」評為 0–2 分；十題共 20 分，作為組內理解檢查，不是導航實驗成績。

### Q1 輪子轉動但小車被卡住時，里程計和真實位置可能如何不同？

判斷重點：輪式里程計可能累積位移；應交叉查看雷射、定位、TF 和實際觀察。無進度不能只靠輪子速度判定。

### Q2 地圖正確但車子沿牆偏移，如何區分定位錯誤與控制問題？

判斷重點：先看時間戳、map/odom/base_link 關係與雷射對齊，再檢查 footprint、代價地圖及控制回饋；用紀錄逐步排除。

### Q3 LiDAR 顯示沒有障礙物，為什麼仍可能撞到桌子？

判斷重點：桌面或低矮物體可能不在掃描平面；也可能是遮蔽、量測無效或資料過期。

### Q4 中心線能穿過窄門，為何規劃器仍拒絕？

判斷重點：車身尺寸、姿態、地圖解析度及障礙物代價都影響可行性；需量測實際淨寬，不能直接縮小 footprint。

### Q5 已完成全域規劃後出現新箱子，哪個模組應先反應？

判斷重點：即時感測與底層控制／停止機制先處理，Nav2 視條件重新規劃；Agent 監控並依結果處理任務，不等待 LLM 才停。

## 4.3 深度理解問題 6 至 10

問題用來檢查推理與實作判斷。每題可依「是否解釋原因、提出可觀察證據、說明取捨」評為 0–2 分；十題共 20 分，作為組內理解檢查，不是導航實驗成績。

### Q6 擴大 inflation_radius 是否必然更安全？

判斷重點：它改變代價場，並不直接保證實際淨距；可能造成無路可走。要測碰撞、淨距、到達率與時間。

### Q7 較快方法偶爾碰撞，較慢方法沒有觀察到碰撞，如何比較？

判斷重點：需固定任務和樣本數、揭露碰撞率與區間；沒有觀察到不代表零風險，安全要求依應用決定。

### Q8 Gazebo 成功但實機失敗，要先檢查什麼？

判斷重點：感測頻率、延遲、里程計誤差、打滑、車身尺寸與時間來源；模擬時計設定不可直接照搬到實機。

### Q9 如何證明 Agent 有幫助，而不是 Nav2 本來就會導航？

判斷重點：固定 Nav2，比較固定介面、直接語言轉目標與完整 Agent；分開量測地點解析、澄清、不當派送、任務完成及回報正確性。

### Q10 使用者說去門口但有兩個門口，或途中取消，系統該怎麼辦？

判斷重點：歧義時先詢問，不派送；取消優先處理，確認導航取消回饋，不能把任務結束或取消當成到達。

## 五、Proposed Scheme 系統架構

系統以任務層 Agent 串接資料取得、領域規則、導航工具與回饋。圖中箭頭為主要流程，導航層的執行結果回傳 Agent；低延遲停止不依賴 LLM。

![圖 1  八個主要模組與導航執行回饋](assets/architecture.png)

| 編號與模組 | 功能與責任 |
| --- | --- |
| ① 使用者與應用情境 | 實驗室室內移動；中文指定命名目的地。 |
| ② 任務輸入與解析 | LLM 轉為 intent、landmark_id；模糊地點先詢問。 |
| ③ Planner 與任務 Agent | 依狀態選擇讀取、澄清、派送、取消或回報。 |
| ④ 資料取得 | 讀地點表、地圖、定位、雷射、TF 與導航回饋。 |
| ⑤ Analytics Engine | 計算時效、進度、延遲、路徑與實驗指標。 |
| ⑥ 領域知識與驗證 | 白名單、車身／地圖規則、逾時與有限重試。 |
| ⑦ 決策與導航工具 | 經驗證後呼叫 Nav2，監控與取消任務。 |
| ⑧ 輸出與互動 | 根據已觀察事件產生結果與證據，必要時詢問。 |

## 5.1 Flowchart 與失敗處理

![圖 2  任務輸入至輸出的完整處理流程](assets/flowchart.png)

拒絕、澄清、故障與無路可走都是合法輸出，但不能計為成功到達。新移動任務與既有任務互斥；取消優先處理。導航層先完成其既有恢復行為，Agent 僅在失敗後、狀態合格且路徑有效時最多重新派送一次。

取消請求被接受不代表機器人已停止。需持續觀察 action 結果與速度／里程計；若通訊失效導致無法確認，回報「停止狀態未確認」並交由現場操作員與既有停止機制處理。

## 5.2 核心演算法 Pseudocode

```text
Algorithm 1  Evidence grounded indoor navigation agent
Input: query Q, landmark registry K, robot status S
Output: mission result R with evidence E
01: if Q is an explicit cancel command: cancel active goal; return status
02: reject a new movement request while another mission is active
03: read K and retrieve relevant domain rules with source IDs
04: parse Q into a schema constrained intent using the LLM
05: if parsing fails or the service times out: return clarification/error
06: if intent is ambiguous: ask user; return CLARIFY
07: validate tool name, landmark ID and schema against allowlists
08: if validation fails: return REJECTED without sending a goal
09: read scan, localization, TF and navigation lifecycle status
10: if data are stale or checks fail: return NOT_READY with evidence
11: resolve landmark ID into a registered pose in the map frame
12: request a path feasibility check with a bounded timeout
13: if no valid path: return NO_PATH; do not claim the goal is reached
14: send the validated goal through the navigation adapter
15: while mission deadline has not expired:
16:     read navigation feedback and fresh status; append evidence
17:     if user cancels or status is unsafe/stale:
18:         cancel goal; verify response; return CANCELED or FAULT
19:     if navigation result is SUCCEEDED:
20:         return ARRIVED with action result and observed final pose
21:     if navigation result is FAILED or CANCELED:
22:         if FAILED and retry budget remains and fresh checks pass:
23:             recheck path; retry once only when a valid path exists
24:         else: return the observed terminal result with evidence
25: cancel goal; verify cancellation or escalate unresolved state
26: return TIMEOUT with log IDs; never substitute an LLM guess
```

演算法紀錄工具名稱、經驗證參數、時間、感測摘要及 action 結果。只需儲存可核對的決策理由與依據，不依賴或要求模型提供內部思考過程。

## 5.3 資料 介面與領域知識

| 來源 | 內容 | 取得與檢查 |
| --- | --- | --- |
| landmarks.json | 地點 ID、別名、map frame 的 x/y/yaw | 人工在地圖上登錄，版本化；示範座標不得直接用於實機 |
| 地圖與定位 | OccupancyGrid、姿態、TF | SLAM 建圖後定位；核對 map→odom→base_link 與時間 |
| 感測器 | LaserScan、Odometry、IMU（依配備） | 核對實際 topic、頻率、有效值與時效 |
| 導航 action | 任務受理、feedback、終止結果 | Nav2 adapter；依發行版核對介面 [10] |
| domain_rules.md | 定義、故障處理、原始來源 | 以條目 ID 檢索並記錄引用；初版不需要向量資料庫 |
| JSONL／CSV | run_id、時間、任務、狀態、結果 | logger 與指標程式；去除金鑰與無關個資 |

### 5.3.1 模型與工具介面

預計使用可輸出結構化 JSON 的既有 LLM。正式實驗前固定供應者、完整模型 ID、版本日期、temperature 與 prompt hash；本提案不假設已選定或取得付費模型。輸出只允許 navigate、cancel、clarify、reject 等 intent 與已登錄地點 ID。

工具白名單：list_landmarks、get_robot_status、get_path、navigate_to_landmark、cancel_navigation、read_mission_result。參數經 schema 與規則驗證；未知地點或額外欄位拒絕。模型不能自行更改地圖、安全參數或發送 shell 命令。

### 5.3.2 導航與 Analytics

ROS 2／Nav2 維持單一既定版本；建圖與導航設定以實機配套為準，不能混用 Humble、Jazzy 或不同 Gazebo 教學。分析引擎計算資料年齡、任務進度、路徑長度和回報一致性。雷射最小量測不等於車身淨距；淨距須結合感測器位置與 footprint，在模擬中優先以真值量測。

## 六、實驗設計與預期成果

### 6.1 指令資料與導航情境

建立 60 筆繁體中文指令：明確地點、歧義、未知地點、取消、複合任務、越權／矛盾輸入各 10 筆。由兩位組員獨立標註 intent、可派送目標與預期處理，分歧討論後定稿。20 筆開發、40 筆鎖定測試，依語意模板分組切分，避免只換地名的同句型洩漏。

導航包含空曠、窄通道、路徑新增箱子、狀態異常／目標不可達四類，每類選兩組起終點，每組重複五次，共 40 次／方法。前兩類量測一般導航；後兩類分開計算正確拒絕／停止，不把不移動算成到達。實機先挑選 12 個經檢查的配對案例做三方法比較，場景不刻意造成碰撞。

### 6.2 基準與消融

B0 固定地點選單＋相同 Nav2，提供物理導航及固定介面的參考；B1 LLM 直接解析地點＋基本 schema／地點白名單＋相同 Nav2；B2 完整 Agent，加入澄清、狀態檢查與證據回報。B0 不接受自然語言，因此不和 B1/B2 比語言解析正確率。所有方法保留相同底層停止機制。

消融 A：移除澄清；消融 B：移除 Agent 的前置狀態檢查。兩項先以離線或模擬故障注入評估，不能移除實機的底層停止。固定 Nav2 版本、地圖、參數、起終點、模型與 prompt；方法順序交錯，保留所有失敗。

| 指標 | 定義與比較方式 |
| --- | --- |
| 地點／處理正確率 | 符合人工標註的處理／所有測試指令；歧義須澄清才算正確 |
| 不當派送率 | 不應導航卻送出目標的筆數／不應派送的指令或故障案例 |
| 任務完成率 | 合格可達任務中 action 成功且符合終點條件的比例 |
| 回報正確率 | 回報狀態與實際 action／事件一致的比例；無證據成功另列 |
| 延遲與導航表現 | 解析及派送延遲 p50/p95；時間、路徑、碰撞、最小距離 |

二元比例報告樣本數與 Wilson 95% 信賴區間；延遲報中位數與 p95。語言誤解導致未派送和導航途中失敗分開統計。預期 B2 改善處理與回報，但可能增加互動延遲；尚無結果，不能宣稱準確率或碰撞率改善。

## 七、可行性與交付

### 7.1 實作階段與風險

先完成模擬地圖、定位與單點導航，再加入任務 schema、工具介面與記錄，最後接入 LLM、失敗處理及實機。現有小車設定與模擬操作只表示平台正在準備，不能取代完整整合測試。若硬體或模型服務受限，保留模擬評估並明確揭露，不能將離線範例當成實機 Demo。

### 7.2 Proposal 至期末里程碑

11 月 4 日前：全組核對題目、姓名學號、提案與簡報，建立正式 GitHub 儲存庫並放入 README.md／DESIGN.md。後續第 1 階段：固定平台版本與導航基準；第 2 階段：Agent 工具與 LLM；第 3 階段：鎖定測試與消融；第 4 階段：實機示範與期末交付。期末實際日期依課程公告安排。

### 7.3 文件與交付標準

Proposal 交付包含本報告、Proposal PPT、正式 GitHub Repository 與 DESIGN.md。隨附專案資料夾已準備上傳內容；遠端 Repository 網址與組員資料須由實際帳號及名單補上。期末另需完整程式、資料來源、可重現執行方式、實驗比較、超過 20 頁的 Final PPT 與超過 10 分鐘的 YouTube 簡報／Demo。

### 7.4 AI 使用與限制

本提案使用 AI 協助文獻整理、設計與文件製作；所列來源可回查，系統成效未經測試。全組須理解架構、資料、演算法與驗證方式。初版不保證任意中文、所有障礙物或長期運作；LLM 無法取代感測器、低延遲控制或現場操作判斷。

## 八、參考文獻

[1] Yao, S., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. ICLR 2023; arXiv:2210.03629.
https://arxiv.org/abs/2210.03629

[2] Ahn, M., et al. (2022). Do As I Can, Not As I Say: Grounding Language in Robotic Affordances. CoRL 2022; arXiv:2204.01691.
https://arxiv.org/abs/2204.01691

[3] Pollini, D., Guterres, B. V., Guerra, R. S., & Grando, R. B. (2025). Reducing Latency in LLM-Based Natural Language Commands Processing for Robot Navigation. arXiv:2506.00075; accepted to INDIN 2025.
https://arxiv.org/abs/2506.00075

[4] Macenski, S., Martín, F., White, R., & Clavero, J. G. (2020). The Marathon 2: A Navigation System. IROS 2020; arXiv:2003.00368.
https://arxiv.org/abs/2003.00368

[5] Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox: SLAM for the dynamic world. Journal of Open Source Software, 6(61), 2783; doi:10.21105/joss.02783.
https://joss.theoj.org/papers/10.21105/joss.02783

[6] Xiao, X., et al. (2022). Autonomous Ground Navigation in Highly Constrained Spaces: Lessons Learned from the BARN Challenge at ICRA 2022. IEEE Robotics & Automation Magazine; arXiv:2208.10473.
https://arxiv.org/abs/2208.10473

網路來源查閱日期：2026 年 10 月 30 日。官方文件會更新；正式實驗需記錄實際套件版本與 commit。

## 八、參考文獻 續

[7] El-Hariry, M., et al. (2025). NavBench: A Unified Robotics Benchmark for Reinforcement Learning-Based Autonomous Navigation. arXiv:2505.14526.
https://arxiv.org/abs/2505.14526

[8] ROBOTIS (官方文件). TurtleBot3 Features and Navigation. 硬體規格與導航流程；實際 LDS 型號依實機確認.
https://emanual.robotis.com/docs/en/platform/turtlebot3/features/

[9] ROS Navigation maintainers (官方文件). Nav2 Tuning Guide. 車身 footprint、代價地圖與障礙物膨脹.
https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/

[10] ROS Navigation maintainers (官方文件). Nav2 Simple Commander API. 任務呼叫與回饋；使用前須核對已安裝 ROS 發行版.
https://docs.nav2.org/jazzy/configuration_and_development/simple_commander_api/simple_commander_api/

[11] ROS Navigation maintainers (官方程式庫). Nav2 Collision Monitor. 感測資料監測與減速／停止；不是安全認證保證.
https://github.com/ros-navigation/navigation2/blob/main/nav2_collision_monitor/README.md

[12] Xiao, X., and BARN organizers (2026). The BARN Challenge 2026. 標準化導航測試、傳統與學習方法基準.
https://people.cs.gmu.edu/~xiao/Research/BARN_Challenge/BARN_Challenge26.html

網路來源查閱日期：2026 年 10 月 30 日。官方文件會更新；正式實驗需記錄實際套件版本與 commit。

