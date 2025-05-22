## LLM01:2025 プロンプトインジェクション (Prompt Injection)

### 説明

プロンプトインジェクション脆弱性はユーザープロンプトが LLM の動作や出力を意図しない方法で変更する際に発生します。これらの入力は人間に近くできない場合でもモデルに影響を及ぼす可能性があるため、コンテンツがモデルによって解析される限り、プロンプトインジェクションは人間が可視/可読である必要はありません。

プロンプトインジェクション脆弱性は、モデルがプロンプトを処理する方法と、入力によってモデルがモデルの他の部分に誤って渡す方法に存在し、ガイドラインに違反したり、有害なコンテンツを生成したり、不正アクセスを可能にしたり、重要な決定に影響を及ぼす可能性があります。検索拡張生成 (Retrieval Augmented Generation, RAG) やファインチューニングなどの技法は LLM 出力をより適切で正確なものにすることを目的としていますが、プロンプトインジェクション脆弱性を完全に緩和するものではないことが研究によって示されています。

プロンプトインジェクションとジェイルブレーキングは LLM セキュリティの関連概念ですが、しばしば同じ意味で使用されます。プロンプトインジェクションは特定の入力によってモデルのレスポンスを操作して、その動作を変更します。それには安全対策のバイパスを含む可能性があります。ジェイルブレーキングはプロンプトインジェクションの一形態であり、攻撃者がモデルの安全プロトコルを完全に無視させる入力を提供します。開発者はシステムプロンプトと入力処理にセーフガードを組み込み、プロンプトインジェクション攻撃の軽減しますが、ジェイルブレーキングを効果的に防止するには、モデルのトレーニングと安全メカニズムを継続的に更新する必要があります。

### プロンプトインジェクション脆弱性の種類

#### 直接プロンプトインジェクション
  直接プロンプトインジェクションはユーザーのプロンプト入力が意図しない方法や予期しない方法でモデルの動作を直接変更する際に発生します。入力には意図的な (つまり、悪意のある行為者がモデルを悪用するためにプロンプトを作成する) ものと意図的でない (つまり、ユーザーが予期しない動作を引き起こす入力を誤って提供する) ものがあります。

#### 間接プロンプトインジェクション
  間接プロンプトインジェクションは LLM がウェブサイトやファイルなどの外部ソースからの入力を受け入れる際に発生します。外部ソースは、モデルによって解釈される際に、意図しない方法や予期しない方法でモデルの動作を変更するコンテンツデータを有する可能性があります。直接インジェクションと同様に、間接インジェクションにも意図的なものと意図的でないものがあります。

プロンプトインジェクション攻撃が成功した場合の影響の重大性と性質は大きく異なる可能性があり、モデルが動作するビジネスコンテキストと、モデルが設計されたエージェンシーの両方に大きく依存します。しかし、一般的にプロンプトインジェクションは意図しない結果につながる可能性があり、以下を含みますがそれに限定されません。

- 機密情報の開示
- AI システムインフラストラクチャやシステムプロンプトに関する機密情報の漏洩
- 誤った出力や偏った出力につながるコンテンツ操作
- LLM で利用可能な機能への不正アクセスの提供
- 接続されたシステムでの任意のコマンドの実行
- 重要な意思決定プロセスの操作

複数のデータタイプを同時に処理するマルチモーダル AI の台頭は、プロンプトインジェクション特有のリスクをもたらします。悪意のある行為者は、無害なテキストに付随する画像に指示を隠すなど、モダリティ間のやり取りを悪用する可能性があります。これらのシステムの複雑さは攻撃対象領域を拡大します。マルチモーダルモデルは、現在の技術では検出や軽減が難しい、新しいクロスモーダル攻撃の影響を受けやすくなる可能性もあります。堅牢なマルチモーダル固有の防御はさらなる研究開発の重要な分野です。

### 予防および緩和戦略

プロンプトインジェクション脆弱性は生成 AI の性質上、発生する可能性があります。モデルの動作方法の中核にある確率的な影響を考えると、プロンプトインジェクションを防ぐ確実な方法があるかどうかは不明です。ただし、以下の対策はプロンプトインジェクションの影響を緩和できます。

#### 1. モデルの動作を制限する
  システムプロンプト内でモデルの役割、能力、制限について具体的な指示を与えます。コンテキストの厳格な遵守を強制し、レスポンスを特定のタスクやトピックに制限し、中核となる指示を修正しようとする試みを無視するようにモデルに指示します。
#### 2. 期待する出力形式を定義して検証する
  明確な出力形式を指定し、詳細な理由とソース引用を要求し、決定論的コードを使用してこれらの形式に準拠しているかどうかを検証します。
#### 3. 入出力フィルタリングを実装する
  センシティブのカテゴリを定義し、そのようなコンテンツを識別して処理するためのルールを構築します。  セマンティックフィルタを適用し、文字列チェックを使用して、許可されていないコンテンツをスキャンします。  RAG トライアドを使用してレスポンスを評価します。コンテキストの関連性、根拠、質問/回答の関連性を評価して、潜在的に悪意のある出力を特定します。
#### 4. 権限制御と最小権限アクセスを強制する
  拡張可能な機能のためにアプリケーションに独自の API トークンを提供し、これらの機能をモデルに提供するのではなくコードで処理します。モデルのアクセス権限を、意図した操作に必要な最小限に制限します。
#### 5. 高リスクのアクションには人間による承認を要求する
  権限操作に人間参加型 (human-in-the-loop) 制御を実装して、不正なアクションを防ぎます。
#### 6. 外部コンテンツを分離して識別する
  信頼できないコンテンツを分離して明示し、ユーザープロンプトへの影響を制限します。
#### 7. 敵対的テストと攻撃シミュレーションを実施する
  定期的なペネトレーションテストと侵害シミュレーションを実施し、信頼できないユーザーとしてモデルを扱い、信頼境界とアクセス制御の有効性をテストします。

### 攻撃シナリオの例

#### シナリオ #1: 直接インジェクション
  攻撃者はプロンプトを顧客サポートチャットボットに注入し、以前のガイドラインを無視して、個人データストアに問い合わせたり、電子メールを送信するように指示し、不正アクセスと権限昇格を引き起こします。
#### シナリオ #2: 間接インジェクション
  ユーザーが LLM を使用して、隠された指示を含むウェブページを要約すると、LLM は URL にリンクする画像を挿入し、プライベートな会話の流出につながります。
#### シナリオ #3: 意図しないインジェクション
  企業は AI が生成した応募書類を特定するための指示を求人情報に記載しています。この指示を知らない応募者は LLM を使用して履歴書を最適化し、不注意にも AI 検出を引き起こします。
#### シナリオ #4: 意図したモデルの影響
  攻撃者は検索拡張生成 (Retrieval-Augmented Generation, RAG) アプリケーションで使用されるリポジトリ内のドキュメントを変更します。ユーザーのクエリが変更されたコンテンツを返すと、悪意のある指示が LLM の出力を変更し、誤解を招く結果を生成します。
#### シナリオ #5: コードインジェクション
  攻撃者は LLM を搭載した電子メールアシスタントの脆弱性 (CVE-2024-5184) を悪用して悪意のあるプロンプトを注入し、機密情報へのアクセスや電子メールコンテンツの操作を可能にします。
#### シナリオ #6: ペイロードの分割
  攻撃者は悪意のあるプロンプトを分割した履歴書をアップロードします。LLM を使用して候補者を評価すると、組み合わされたプロンプトがモデルのレスポンスを操作し、実際の履歴書の内容に関わらず肯定的な推奨をもたらします。
#### シナリオ #7: マルチモーダルインジェクション
  攻撃者は悪意のあるプロンプトを無害なテキストに付随する画像内に埋め込みます。マルチモーダル AI が画像とテキストを同時に処理すると、隠されたプロンプトがモデルの動作を変更し、不正なアクションや機密情報の開示につながる可能性があります。
#### シナリオ #8: 敵対的サフィックス
  攻撃者は一見意味のない文字列をプロンプトに追加し、LLM の出力に悪意のある方法で影響を及ぼし、安全対策を回避します。
#### シナリオ #9: 多言語/難読化攻撃
  攻撃者は複数の言語を使用したり、悪意のある指示をエンコード (Base64 や絵文字を使用するなど) して、フィルタを回避し、LLM の動作を操作します。

### 参考情報リンク

1. [ChatGPT Plugin Vulnerabilities - Chat with Code](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/) **Embrace the Red**
2. [ChatGPT Cross Plugin Request Forgery and Prompt Injection](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) **Embrace the Red**
3. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Arxiv**
4. [Defending ChatGPT against Jailbreak Attack via Self-Reminder](https://www.researchsquare.com/article/rs-2873090/v1) **Research Square**
5. [Prompt Injection attack against LLM-integrated Applications](https://arxiv.org/abs/2306.05499) **Cornell University**
6. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf) **Kai Greshake**
7. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Cornell University**
8. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/) **AI Village**
9. [Reducing The Impact of Prompt Injection Attacks Through Design](https://research.kudelskisecurity.com/2023/05/25/reducing-the-impact-of-prompt-injection-attacks-through-design/) **Kudelski Security**
10. [Adversarial Machine Learning: A Taxonomy and Terminology of Attacks and Mitigations (nist.gov)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2023.pdf)
11. [2407.07403 A Survey of Attacks on Large Vision-Language Models: Resources, Advances, and Future Trends (arxiv.org)](https://arxiv.org/abs/2407.07403)
12. [Exploiting Programmatic Behavior of LLMs: Dual-Use Through Standard Security Attacks](https://ieeexplore.ieee.org/document/10579515)
13. [Universal and Transferable Adversarial Attacks on Aligned Language Models (arxiv.org)](https://arxiv.org/abs/2307.15043)
14. [From ChatGPT to ThreatGPT: Impact of Generative AI in Cybersecurity and Privacy (arxiv.org)](https://arxiv.org/abs/2307.00691)

### 関連するフレームワークと分類

インフラストラクチャデプロイメント、適用される環境制御、その他のベストプラクティスに関する包括的な情報、シナリオ戦略については、このセクションを参照してください。

- [AML.T0051.000 - LLM Prompt Injection: Direct](https://atlas.mitre.org/techniques/AML.T0051.000) **MITRE ATLAS**
- [AML.T0051.001 - LLM Prompt Injection: Indirect](https://atlas.mitre.org/techniques/AML.T0051.001) **MITRE ATLAS**
- [AML.T0054 - LLM Jailbreak Injection: Direct](https://atlas.mitre.org/techniques/AML.T0054) **MITRE ATLAS**
