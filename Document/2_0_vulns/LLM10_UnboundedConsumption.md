## LLM10:2025 無制限の消費 (Unbounded Consumption)

### 説明

無制限の消費は、大規模言語モデル (Large Language Model, LLM) が入力クエリまたはプロンプトに基づいて出力を生成するプロセスを指します。推論は LLM の重要な機能であり、学習したパターンや知識を適用して、関連するレスポンスや予測を生成します。

サービスを妨害したり、ターゲットの経済的リソースを枯渇させたり、モデルの動作を複製して知的財産を窃取することを目的とした攻撃はすべて、成功するために共通のセキュリティ脆弱性に依存しています。無制限の消費は、大規模言語モデル (Large Language Model, LLM) アプリケーションによってユーザーが過剰で制御されていない推論を実行できる際に発生し、サービス拒否 (DoS)、経済的損失、モデル窃取、サービス低下などのリスクにつながります。特にクラウド環境では、LLM の計算要求が高いため、リソースの悪用や不正使用に対して脆弱となります。

### 脆弱性の一般的な例

#### 1. 可変長入力フラッド
  攻撃者は、処理の非効率性を悪用して、さまざまな長さの多数の入力で LLM に過負荷をかけることができます。これはリソースを枯渇し、システムが応答しなくなる可能性があり、サービスの可用性に重大な影響を及ぼす可能性があります。
#### 2. ウォレット拒否 (Denial of Wallet, DoW)
  大量のオペレーションを開始することで、攻撃者はクラウドベースの AI サービスの従量課金モデルを悪用し、プロバイダに持続不可能な経済的負担をもたらし、財政破綻のリスクをもたらします。
#### 3. 連続的な入力オーバーフロー
  LLM のコンテキストウィンドウを超える入力を連続的に送信すると、計算リソースを過剰に使用し、サービス低下や運用中断につながる可能性があります。
#### 4. リソース集約型クエリ
  複雑なシーケンスや入り組んだ言語パターンを含む、異常に負荷の高いクエリを送信すると、システムリソースを消費し、処理時間が長引いたり、システム障害が発生する可能性があります。
#### 5. API 経由のモデル抽出
  攻撃者は、注意深く細工した入力とプロンプトインジェクション技法を使用してモデル API をクエリし、部分的なモデルを複製したり、シャドウモデルを作成するのに十分な出力を収集する可能性があります。これは知的財産の盗難リスクをもたらすだけでなく、元のモデルの完全性を損なうことにもなります。
#### 6. 機能モデルの複製
  ターゲットモデルを使用して、模造トレーニングデータを生成することで、攻撃者は他の機能モデルをファインチューンして、機能的に等価なものを作成できます。これは従来のクエリベースの抽出手法を巧みに迂回して、プロプライエタリモデルやテクノロジに重大なリスクをもたらします。
#### 7. サイドチャネル攻撃
  悪意のある攻撃者は、LLM の入力フィルタリング技法を悪用して、サイドチャネル攻撃を実行し、モデルの重みやアーキテクチャ情報を採取する可能性があります。これはモデルのセキュリティを侵害し、さらなる悪用につながる可能性があります。

### 予防および緩和戦略

#### 1. 入力バリデーション
  厳密な入力バリデーションを実装して、入力が適切なサイズ制限を超えないようにします。
#### 2. Logits と Logprobs の開示制限
  API レスポンスでの `logit_bias` と `logprobs` の開示を制限するか難読化します。詳細な確率を明らかにせず、必要な情報のみを提供します。
#### 3. レート制限
  レート制限とユーザークォータを適用して、単一ソースエンティティが一定期間内に実行できるリクエスト数を制限します。
#### 4. リソース割り当て管理
  リソース割り当てを動的に監視および管理して、過剰なリソースを消費する単一のユーザーやリクエストを防ぎます。
#### 5. タイムアウトとスロットル
  リソース集約型操作に対してタイムアウトとスロットルを設定して、リソース消費が長引くのを防ぎます。
#### 6. サンドボックス技法
  LLM のネットワークリソース、内部サービス、API へのアクセスを制限します。
  - これは内部のリスクと脅威を包含するため、すべての一般的なシナリオで特に重要です。さらに、LLM アプリケーションがデータやリソースにアクセスできる範囲を管理して、サイドチャネル攻撃を緩和ないし防御するための重要な制御メカニズムとして機能します。
#### 7. 包括的なログ記録、監視、異常検出
  リソースの使用状況を継続的に監視し、ログ記録を実装して、リソース消費の異常なパターンを検出して対応します。
#### 8. 電子透かし
  電子透かしフレームワークを導入して、埋め込みを行い、LLM 出力の不正使用を検出します。
#### 9. 段階的な劣化
  高負荷時にシステムが段階的に劣化するように設計し、完全な障害ではなく部分的に機能を維持します。
#### 10. キューに入れられるアクションの制限と堅牢なスケール
  キューに入れられるアクションの数とアクションの総数に制限を設けて、動的スケーリングと負荷分散を組み込むことで、さまざまな需要に対応し、一貫したシステムパフォーマンスを確保します。
#### 11. 敵対的堅牢性トレーニング
  モデルをトレーニングして、敵対的なクエリや抽出の試みを検出して緩和します。
#### 12. グリッチトークンのフィルタリング
  既知のグリッチトークンのリストを作成して、モデルのコンテキストウィンドウに追加する前に出力をスキャンします。
#### 13. アクセス制御
  ロールベースのアクセス制御 (RBAC) や最小権限の原則などの強力なアクセス制御を実装して、LLM モデルリポジトリやトレーニング環境への不正アクセスを制限します。
#### 14. ML モデルインベントリの一元化
  本番環境で使用されるモデルには一元化された ML モデルインベントリやレジストリを使用して、適切なガバナンスとアクセス制御を確保します。
#### 15. MLOps デプロイメントの自動化
  ガバナンス、追跡、承認ワークフローを備えた自動化された MLOps デプロイメントを実装して、インフラストラクチャ内のアクセスとデプロイメントの制御を強化します。

### 攻撃シナリオの例

#### Scenario #1: Uncontrolled Input Size
  An attacker submits an unusually large input to an LLM application that processes text data, resulting in excessive memory usage and CPU load, potentially crashing the system or significantly slowing down the service.
#### Scenario #2: Repeated Requests
  An attacker transmits a high volume of requests to the LLM API, causing excessive consumption of computational resources and making the service unavailable to legitimate users.
#### Scenario #3: Resource-Intensive Queries
  An attacker crafts specific inputs designed to trigger the LLM's most computationally expensive processes, leading to prolonged CPU usage and potential system failure.
#### Scenario #4: Denial of Wallet (DoW)
  An attacker generates excessive operations to exploit the pay-per-use model of cloud-based AI services, causing unsustainable costs for the service provider.
#### Scenario #5: Functional Model Replication
  An attacker uses the LLM's API to generate synthetic training data and fine-tunes another model, creating a functional equivalent and bypassing traditional model extraction limitations.
#### Scenario #6: Bypassing System Input Filtering
  A malicious attacker bypasses input filtering techniques and preambles of the LLM to perform a side-channel attack and retrieve model information to a remote controlled resource under their control.

### 参考情報リンク

1. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)
2. [arXiv:2403.06634 Stealing Part of a Production Language Model](https://arxiv.org/abs/2403.06634) **arXiv**
3. [Runaway LLaMA | How Meta's LLaMA NLP model leaked](https://www.deeplearning.ai/the-batch/how-metas-llama-nlp-model-leaked/): **Deep Learning Blog**
4. [You wouldn't download an AI, Extracting AI models from mobile apps](https://altayakkus.substack.com/p/you-wouldnt-download-an-ai): **Substack blog**
5. [A Comprehensive Defense Framework Against Model Extraction Attacks](https://ieeexplore.ieee.org/document/10080996): **IEEE**
6. [Alpaca: A Strong, Replicable Instruction-Following Model](https://crfm.stanford.edu/2023/03/13/alpaca.html): **Stanford Center on Research for Foundation Models (CRFM)**
7. [How Watermarking Can Help Mitigate The Potential Risks Of LLMs?](https://www.kdnuggets.com/2023/03/watermarking-help-mitigate-potential-risks-llms.html): **KD Nuggets**
8. [Securing AI Model Weights Preventing Theft and Misuse of Frontier Models](https://www.rand.org/content/dam/rand/pubs/research_reports/RRA2800/RRA2849-1/RAND_RRA2849-1.pdf)
9. [Sponge Examples: Energy-Latency Attacks on Neural Networks: Arxiv White Paper](https://arxiv.org/abs/2006.03463) **arXiv**
10. [Sourcegraph Security Incident on API Limits Manipulation and DoS Attack](https://about.sourcegraph.com/blog/security-update-august-2023) **Sourcegraph**

### 関連するフレームワークと分類

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [MITRE CWE-400: Uncontrolled Resource Consumption](https://cwe.mitre.org/data/definitions/400.html) **MITRE Common Weakness Enumeration**
- [AML.TA0000 ML Model Access: Mitre ATLAS](https://atlas.mitre.org/tactics/AML.TA0000) & [AML.T0024 Exfiltration via ML Inference API](https://atlas.mitre.org/techniques/AML.T0024) **MITRE ATLAS**
- [AML.T0029 - Denial of ML Service](https://atlas.mitre.org/techniques/AML.T0029) **MITRE ATLAS**
- [AML.T0034 - Cost Harvesting](https://atlas.mitre.org/techniques/AML.T0034) **MITRE ATLAS**
- [AML.T0025 - Exfiltration via Cyber Means](https://atlas.mitre.org/techniques/AML.T0025) **MITRE ATLAS**
- [OWASP Machine Learning Security Top Ten - ML05:2023 Model Theft](https://owasp.org/www-project-machine-learning-security-top-10/docs/ML05_2023-Model_Theft.html) **OWASP ML Top 10**
- [API4:2023 - Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/) **OWASP Web Application Top 10**
- [OWASP Resource Management](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/) **OWASP Secure Coding Practices**
