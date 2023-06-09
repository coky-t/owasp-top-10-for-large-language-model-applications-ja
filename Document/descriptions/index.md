## OWASP Top 10 リスト 大規模言語モデルバージョン 0.1
これは大規模言語モデル (LLM) 上に構築された人工知能 (AI) アプリケーションの重要な脆弱性タイプのドラフトリストです。

### [LLM01:2023 - プロンプトインジェクション (Prompt Injections)](Prompt_Injection.md)
**説明:**
慎重に作成されたプロンプトを使用し、フィルタをバイパスしたり LLM を操作して、モデルが以前の指示を無視したり意図しないアクションを実行します。


### [LLM02:2023 - データ漏洩 (Data Leakage)](Data_Leakage.md)
**説明:**
LLM のレスポンスを通じて、機密情報、非公開アルゴリズム、その他の機密事項を誤って漏洩します。

### [LLM03:2023 - 不十分なサンドボックス化 (Inadequate Sandboxing)](Inadequate_Sandboxing.md)
**説明:**
LLM が外部リソースや機密性の高いシステムにアクセスする際、LLM を適切に分離できずに、悪用や認可されていないアクセスを許します。

### [LLM04:2023 - 認可されていないコード実行 (Unauthorized Code Execution)](Unauthorized_Code_Execution.md)
**説明:**
LLM を悪用して、自然言語プロンプトを通じて基盤となるシステム上で悪意のあるコード、コマンド、アクションを実行します。

### [LLM05:2023 - SSRF 脆弱性 (SSRF Vulnerabilities)](SSRF.md)
**説明:**
LLM を悪用して、意図しないリクエストを実行したり、内部のサービス、API、データストアなどの制限されたリソースにアクセスします。

### [LLM06:2023 - LLM が生成したコンテンツへの過度の依存 (Overreliance on LLM-generated Content)](Overreliance.md)
**説明:**
人間の監督なしに LLM が生成したコンテンツに過度に依存すると、悪影響を及ぼす結果となる可能性があります。

### [LLM07:2023 - 不十分な AI 調整 (Inadequate AI Alignment)](Inadequate_AI_Alignment.md)
**説明:**
LLM の目的と動作が意図したユースケースと合致していることを確認できないと、望ましくない結果や脆弱性につながります。

### [LLM08:2023 - 不十分なアクセス制御 (Insufficient Access Controls)](Insufficient_Access_Control.md)
**説明:**
アクセス制御や認証を適切に実装しないと、認可されていないユーザーが LLM とやり取りし、脆弱性を悪用する可能性があります。

### [LLM09:2023 - 不適切なエラー処理 (Improper Error Handling)](Improper_Error_Handling.md)
**説明:**
機密情報、システム詳細、潜在的な攻撃ベクトルを明らかにするエラーメッセージやデバッグ情報を公開します。

### [LLM10:2023 - 訓練データポイズニング (Training Data Poisoning)](Training_Data_Poisoning.md)
**説明:**
訓練データや微調整手順を悪意を持って操作し、LLM に脆弱性やバックドアを導入します。
