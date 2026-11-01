# 建立正式 GitHub Repository

目前交付為本機上傳包，尚未建立遠端 GitHub 儲存庫。

## 網頁方式

1. 在你的 GitHub 帳號建立名為 `tb3-navigation-agent` 的 Repository，公開或私人依老師規定。
2. 開啟儲存庫的 Upload files，把此專案資料夾「裡面」的內容上傳。
3. 確認首頁直接顯示 README.md、DESIGN.md、PROPOSAL.md 和 src；不要多包一層同名資料夾。
4. 把 TB3_Proposal.pdf 和 TB3_Proposal.pptx 放入 docs/，可編輯 Word 視需要加入。
5. 填入組別、五位組員姓名與學號，在 README 補上正式 URL。
6. 複製儲存庫首頁 URL，依 iLearning 作業欄位繳交；不是 GitHub Pages 網址。

## Git 方式

先在 GitHub 建空的儲存庫，不預先加入 README。從本專案根目錄執行：

```bash
git init
git add .
git commit -m "Add TurtleBot3 navigation agent proposal"
git branch -M main
git remote add origin YOUR_REPOSITORY_URL
git push -u origin main
```

將 YOUR_REPOSITORY_URL 換成你實際建立的 HTTPS URL。認證透過 GitHub 正式流程，不把密碼或 token 寫進檔案。
