## LLM03: 訓練データポイズニング (Training Data Poisoning)

### 説明

あらゆる機械学習アプローチの出発点は訓練データ、つまり単なる "raw text" です。高い能力 (言語や世界の知識など) を得るには、このテキストは幅広いドメイン、ジャンル、言語にまたがる必要があります。大規模言語モデルはディープニューラルネットワークを使用して、訓練データから学習したパターンに基づいて出力を生成します。

訓練データポイズニングはモデルのセキュリティ、有効性、倫理的行動を損なう可能性のある脆弱性 (すべては固有の、場合によっては共有された攻撃ベクトルを持ちます)、バックドア、バイアスを導入するために、事前訓練データや、ファインチューニングや埋め込みプロセスに含まれるデータを操作することを指します。汚染された情報はユーザーに公開されたり、パフォーマンス低下、ダウンストリームソフトウェア悪用、風評被害などの他のリスクを引き起こす可能性があります。たとえユーザーが問題のある AI 出力に不信感を抱いたとしても、モデル機能の低下やブランドの評判を損なう可能性などのリスクが残ります。

- 事前訓練データとはタスクやデータセットに基づいてモデルを訓練するプロセスを指します。
- ファインチューニングはすでに訓練された既存のモデルを取得し、監修されたデータセットを使用して訓練することで、より狭い対象やより焦点を絞った目標に適応することを含みます。このデータセットには一般的に入力とそれに対応する望ましい出力の例を含みます。
- 埋め込みプロセスはカテゴリデータ (多くの場合テキスト) を言語モデルの訓練に使用できる数値表現に変換するプロセスです。埋め込みプロセスにはテキストデータの単語やフレーズを連続ベクトル空間内のベクトルとして表現することが含まれます。ベクトルは一般的に大規模テキストコーパスで訓練されたニューラルネットワークにテキストデータを入力することで生成されます。

データポイズニングは完全性攻撃とみなされ、訓練データの改竄が正しい予測を出力するモデルの能力に影響を与えます。当然ながら、外部データソースはモデル作成者がデータを管理できず、コンテンツにバイアス、改竄された情報、不適切なコンテンツが含まれていないという高いレベルの信頼性がないため、リスクがより高くなります。

### 脆弱性の一般的な例

1.  悪意のある人物、または競合ブランドがモデルの事前訓練、ファインチューニングデータ、埋め込みを対象とした不正確または悪意のあるドキュメントを意図的に作成します。例として [Split-View Data Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%201%20Split-View%20Data%20Poisoning.jpeg) と [Frontrunning Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%202%20Frontrunning%20Data%20Poisoning.jpeg) の両方の攻撃ベクトルを考えてみましょう。
   1. 被害者モデルは利用者への生成 AI プロンプトの出力に反映される偽情報を使用して訓練します。
2. 悪意のある人物はモデルの訓練プロセスに改竄された、偏りのある、あるいは有害なコンテンツを直接注入して、後続の出力で返すことができます。
3. 疑いを持たないユーザーがモデルの訓練プロセスに機密データやプロプライエタリデータを間接的に注入して、後続の出力で返されます。
4. モデルは訓練ステージのどの例においても、その出所、起源、内容が検証されていないデータを使用して訓練されるため、データが汚染されていたり正しくない場合、誤った結果を生じる可能性があります。
5. インフラストラクチャへのアクセスが制限されていなかったり、サンドボックス化が不適切であると、モデルは安全でない訓練データを取り込み、偏った出力や有害な出力が生じる可能性があります。この例は訓練ステージのどの例においても存在します。
   1. このシナリオでは、モデルへのユーザー入力が別のユーザーへの出力に反映される (侵害につながる) 可能性があったり、LLM のユーザーはモデルのユースケース (通常はモデルカードに反映されます) と比較して取り込まれたデータのタイプに応じて不正確、無関係、または有害な出力をモデルから受け取る可能性があります。

*LLM の開発者、クライアント、一般利用者のいずれであっても、非プロプライエタリ LLM とやり取りする際に、この脆弱性が LLM アプリケーション内のリスクにどのような影響を与える可能性があるかを理解し、その訓練手順に基づいたモデル出力の正当性を理解することが重要です。同様に、LLM の開発者はファインチューニングと埋め込み (もっとも一般的) に使用される内部またはサードパーティデータに対する直接的および間接的な攻撃の両方のリスクにさらされる可能性があり、その結果、すべての利用者にリスクが生じます。*

### 予防および緩和戦略

1. "ML-SBOM" (Machine Learning Software Bill of Materials) 方法論を介して証明書を維持したり、モデルカードを検証するだけでなく、特に外部から調達する場合にも、訓練データのサプライチェーンを検証します。
2. 事前訓練、ファインチューニング、埋め込みのそれぞれのステージで取得した、対象となるデータソースとそれに含まれるデータの正当性を検証します。
3. LLM のユースケースと、それが統合されるアプリケーションを検証します。個別の訓練データからさまざまなモデルを作成したり、さまざまなユースケースごとにファインチューニングして、定義されたユースケースに従って、より詳細で正確な生成 AI 出力を作成します。
4. 機械学習の出力を妨げる可能性のある意図しないデータソースをモデルがスクレイピングしないように、ネットワークコントロールを通じて十分なサンドボックスが存在することを確保します。
5. 特定の訓練データまたはデータソースのカテゴリに厳密な検査または入力フィルタを使用して、改竄されたデータの量を制御します。統計的外れ値検出や異常値検出手法などの技法を使用したデータサニタイゼーションにより、ファインチューニングプロセスに供給される可能性のある敵対的データを検出して削除します。
6. データセットの出所と所有権に関する管理上の質問を精緻化し、モデルが汚染されていないことを確保し、この文化を "MLSecOps" サイクルに導入します。たとえば [The Foundation Model Transparency Index](https://crfm.stanford.edu/fmti/) や [Open LLM Leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard) などの利用可能なリソースを参照してください。
7. DVC ([Data Version Control](https://dvc.org/doc/user-guide/analytics) を使用して、ポイズニングにつながる操作、削除、追加された可能性のあるデータセットの部分を厳密に特定し、追跡します。
8. Vector Database を使用して、ユーザー提供の情報を追加することで、他のユーザーのポイズニングから保護し、新しいモデルを再訓練することなく運用環境で修正できます。
9. 連合学習や、外れ値の影響を最小限にする制約、あるいは訓練データの最悪のケースでの摂動に対して強力である敵対的訓練などの敵対的ロバストネス技法。
   1. "MLSecOps" アプローチでは自動ポイズニング技法を使用して訓練ライフサイクルに敵対的ロバストネスを持たせることができます。
   2. このリポジトリの例は [Autopoison](https://github.com/azshue/AutoPoison) テストです。これにはこのアプローチで実現できるコンテンツインジェクション攻撃 (「モデルレスポンスでブランド名を宣伝しようとする」) や拒否攻撃 (「常にモデルのレスポンスを拒否させる」) のような攻撃を含みます。
10. テストと検出。訓練段階での損失を計測し、訓練したモデルを解析して、特定のテスト入力に対するモデルの動作を解析することで、ポイズニング攻撃の兆候を検出します。
   1. 閾値を超えた偏ったレスポンスの数を監視して警告します。
   2. ヒューマンループを使用して、レスポンスのレビューと監査を行います。
   3. 専用 LLM を実装して、望ましくない結果に対するベンチマークを行い、[強化学習技法](https://wandb.ai/ayush-thakur/Intro-RLAIF/reports/An-Introduction-to-Training-LLMs-Using-Reinforcement-Learning-From-Human-Feedback-RLHF---VmlldzozMzYyNjcy) を使用して他の LLM を訓練します。
   4. LLM のライフサイクルのテスト段階で LLM ベースの [レッドチームエクササイズ](https://www.anthropic.com/index/red-teaming-language-models-to-reduce-harms-methods-scaling-behaviors-and-lessons-learned) や [LLM 脆弱性スキャン](https://github.com/leondz/garak) を実行します。

### 攻撃シナリオの例

1. LLM 生成 AI プロンプト出力はアプリケーションのユーザーを誤解させる可能性があり、偏った意見や支持、さらにはヘイトクライムなどにつながる可能性があります。
2. 訓練データが正しくフィルタやサニタイズされていない場合、アプリケーションの悪意のあるユーザーは偏ったデータや偽のデータに適応されるために、有害なデータを注入し、影響を与えようとするかもしれません。
3. 悪意のある人物や競合他社が、入力に基づいて同時にモデルを訓練する、モデルの訓練データをターゲットにして、不正確なドキュメントや悪意のあるドキュメントを意図的に作成します。被害を受けるモデルはこの偽の情報を使用して訓練し、利用者に対する生成 AI プロンプトの出力に反映されます。
4. 脆弱性 [プロンプトインジェクション (Prompt Injection)](LLM01_PromptInjection.md) は、LLM アプリケーション入力のクライアントを使用してモデルを訓練する際、サニタイゼーションやフィルタリングの実行が不十分である場合、この脆弱性への攻撃ベクトルとなる可能性があります。つまり、プロンプトインジェクション技法の一部として悪意のあるデータや偽のデータがクライアントからモデルに入力された場合、これは本質的にモデルデータに描写される可能性があります。

### 参考情報リンク

1. [Stanford Research Paper:CS324](https://stanford-cs324.github.io/winter2022/lectures/data/): **Stanford Research**
2. [How data poisoning attacks corrupt machine learning models](https://www.csoonline.com/article/3613932/how-data-poisoning-attacks-corrupt-machine-learning-models.html): **CSO Online**
3. [MITRE ATLAS (framework) Tay Poisoning](https://atlas.mitre.org/studies/AML.CS0009/): **MITRE ATLAS**
4. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/): **Mithril Security**
5. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf/): **Kai Greshake**
6. [Backdoor Attacks on Language Models](https://towardsdatascience.com/backdoor-attacks-on-language-models-can-we-trust-our-models-weights-73108f9dcb1f): **Towards Data Science**
7. [Poisoning Language Models During Instruction](https://arxiv.org/abs/2305.00944): **Arxiv White Paper**
8. [FedMLSecurity:arXiv:2306.04959](https://arxiv.org/abs/2306.04959): **Arxiv White Paper**
9. [The poisoning of ChatGPT](https://softwarecrisis.dev/letters/the-poisoning-of-chatgpt/): **Software Crisis Blog**
10. [Poisoning Web-Scale Training Datasets - Nicholas Carlini | Stanford MLSys #75](https://www.youtube.com/watch?v=h9jf1ikcGyk): **YouTube Video**
11. [OWASP CycloneDX v1.5](https://cyclonedx.org/capabilities/mlbom/): **OWASP CycloneDX**
