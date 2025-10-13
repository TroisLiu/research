# 新結構
## py檔簡介
- models.py（資料模型）
- controls.py（輸入參數）
- grammar.py（BNF 解析）
- fuzzing.py（可選步驟）
- parser.py（迷你語法剖析）
- synth.py（受控合成）
- validate.py（規則檢查與修補）
- mermaid.py（輸出渲染）
- generate.py（管線整合）

## 資料流
1. 先宣告取得以下Object
  - Controls (定義於controls.py)
  - Options (定義於controls.py)
  - BNF (定義於demo.py)

2. 呼叫generate_flowchart_mermaid()
   ├─ fuzzing.fuzz_string_from_bnf()  # 可選：當Options的屬性use_fuzz = True
   ├─ parser.parse_flowchart_text()   # 只驗證
   ├─ SynthContext.from_controls()
   ├─ synth.ast_to_graph()            # 造圖（控制變數主導）
   ├─ validate.validate_and_fix()     # 修到好 + FixReport
   └─ mermaid.to_mermaid()            # 輸出
=> (mermaid_code, FixReport, fuzz_src)

# 舊結構
## Function Dependency
- Demo()
  - 呼叫 generate_flowchart_mermaid()
    - 呼叫 random.seed()
    - 呼叫 fuzz_string_from_bnf() : 以 fuzzingbook 依 BNF 生成語句 (grammar dict → fuzz 字串)
      - 呼叫 bnf_to_fb_grammar() : 將bnf_grammar從plaintext(String)轉換成dict結構  (BNF 文字 → fuzzingbook grammar dict)
      - 呼叫 ensure()：若欠缺就設定預設值
        - 呼叫 .setdefault() 
      - 呼叫 random.Random()
      - 呼叫 random.seed()
      - 宣告 GrammarFuzzer() object 初始化
      - 呼叫 .fuzz()
    - 宣告 SynthContext() object 初始化
    - 呼叫 parse_flowchart_text()
      - 呼叫 parse_flowchart()
    - 呼叫 ast_to_graph()
      - 宣告 Graph() Object初始化
      - 呼叫 .add_node()
      - 呼叫 .add_edge()
      - 呼叫 random.Random()
      - 呼叫 .choice()
      - 呼叫 .predecessors()
      - 呼叫 .nested_branch()
    - 呼叫 FixReport()
    - 呼叫 validate_and_fix()
    - 呼叫 to_mermaid()
