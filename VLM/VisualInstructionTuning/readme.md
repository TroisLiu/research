# Visual Instruction Tuning
## 定義
- Visual Instruction Tuning（視覺指令微調） 是一種用於 多模態（視覺 + 語言）大模型訓練的方法
- 傳統的指令微調（Instruction Tuning） 是在純語言大模型上訓練模型去「理解人類給的文字指令並跟隨它們去完成任務」。
- Visual Instruction Tuning 讓模型能“一邊看圖一邊依照語言指令操作"
## 目標
- 將大型語言模型（LLM）與視覺模組結合，讓模型能：
  - 理解帶有圖片的指令（例如：「這張照片裡有什麼？為什麼這些物品一起出現？」）
  - 輸出與圖像內容相關的文字回答、描述、推理等
## 說明
- VQA是VIT的子集合
