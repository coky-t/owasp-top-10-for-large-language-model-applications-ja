## 訓練データポイズニング (Training Data Poisoning)

**説明:**

あらゆる機械学習アプローチの出発点は訓練データ、つまり単なる "raw text" です。高い能力 (言語や世界の知識など) を得るには、このテキストは幅広いドメイン、ジャンル、言語にまたがる必要があります。大規模言語モデルはディープニューラルネットワークを使用して、訓練データから学習したパターンに基づいて出力を生成します。

訓練データポイズニングはモデルのセキュリティ、有効性、倫理的行動を損なう可能性のある脆弱性、バックドア、バイアスを導入するためにデータやファインチューニングプロセスを操作することを指します。汚染された情報はユーザーに公開されたり、パフォーマンス低下、ダウンストリームソフトウェア悪用、風評被害などの他のリスクを引き起こす可能性があります。たとえユーザーが問題のある AI 出力に不信感を抱いたとしても、モデル機能の低下やブランドの評判を損なう可能性などのリスクが残ります。

データポイズニングは完全性攻撃とみなされ、訓練データの改竄が正しい予測を出力するモデルの能力に影響を与えます。当然ながら、外部データソースはモデル作成者がデータを管理できず、コンテンツにバイアス、改竄された情報、不適切なコンテンツが含まれていないという高いレベルの信頼性がないため、リスクがより高くなります。

**脆弱性の一般的な例:**

1. 悪意のある人物、または競合ブランドがモデルの訓練データを対象とした不正確または悪意のあるドキュメントを意図的に作成します。
   - 被害者モデルは利用者への生成 AI プロンプトの出力に反映される偽情報を使用して訓練します。
2. モデルはそのソース、起源またはコンテンツによって検証されていないデータを使用して訓練されます。
3. モデル自体がインフラストラクチャ内に配置されている場合、アクセスが制限されていないか、訓練データとして使用されるデータセットを収集するためのサンドボックス化が不十分であり、生成 AI プロンプトの出力に悪影響を及ぼしたり、管理の観点から制御を失ったりします。

*LLM の開発者、クライアント、一般利用者のいずれであっても、非プロプライエタリ LLM とやり取りする際に、この脆弱性が LLM アプリケーション内のリスクにどのような影響を与える可能性があるかを理解することが重要です。*

**防止方法:**

1. "SBOM" (Software Bill of Materials) 方法論と同様に、特に証明書を維持するだけでなく外部から調達する場合にも、訓練データのサプライチェーンを検証します。
2. トレーニングとファインチューニングの両方のステージで取得した、対象となるデータソースとそれに含まれるデータの正当性を検証します。
3. LLM のユースケースと、それが統合されるアプリケーションを検証します。個別の訓練データからさまざまなモデルを作成したり、さまざまなユースケースごとにファインチューニングして、定義されたユースケースに従って、より詳細で正確な生成 AI 出力を作成します。
4. 機械学習の出力を妨げる可能性のある意図しないデータソースをモデルがスクレイピングしないように、十分なサンドボックスが存在することを確保します。
5. 特定の訓練データまたはデータソースのカテゴリに厳密な検査または入力フィルタを使用して、改竄されたデータの量を制御します。統計的外れ値検出や異常値検出手法などの技法を使用したデータサニタイゼーションにより、ファインチューニングプロセスに供給される可能性のある敵対的データを検出して削除します。
6. 連合学習や、外れ値の影響を最小限にする制約、あるいは訓練データの最悪のケースでの摂動に対して強力である敵対的訓練などの敵対的ロバストネス技法。
   1. "MLSecOps" アプローチでは自動ポイズニング技法を使用して訓練ライフサイクルに敵対的ロバストネスを持たせることができます。
   2. このリポジトリの例は [Autopoison](https://github.com/azshue/AutoPoison) テストです。これにはこのアプローチで実現できるコンテンツインジェクション攻撃 (「LLM レスポンスにブランドを注入する方法」) や拒否攻撃 (「常にモデルのレスポンスを拒否させる」) のような攻撃を含みます。
7. テストと検出。訓練段階での損失を計測し、訓練したモデルを解析して、特定のテスト入力に対するモデルの動作を解析することで、ポイズニング攻撃の兆候を検出します。
   1. 閾値を超えた偏ったレスポンスの数を監視して警告します。
   2. ヒューマンループを使用して、レスポンスのレビューと監査を行います。
   3. 専用 LLM を実装して、望ましくない結果に対するベンチマークを行い、[強化学習技法](https://wandb.ai/ayush-thakur/Intro-RLAIF/reports/An-Introduction-to-Training-LLMs-Using-Reinforcement-Learning-From-Human-Feedback-RLHF---VmlldzozMzYyNjcy) を使用して他の LLM を訓練します。
   4. LLM のライフサイクルのテスト段階で LLM ベースの [レッドチームエクササイズ](https://www.anthropic.com/index/red-teaming-language-models-to-reduce-harms-methods-scaling-behaviors-and-lessons-learned) や [LLM 脆弱性スキャン](https://github.com/leondz/garak) を実行します。

**攻撃シナリオの例:**

1. LLM 生成 AI プロンプト出力はアプリケーションのユーザーを誤解させる可能性があり、偏った意見や支持、さらにはヘイトクライムなどにつながる可能性があります。
2. 訓練データが正しくフィルタやサニタイズされていない場合、アプリケーションの悪意のあるユーザーは偏ったデータや偽のデータに適応されるために、有害なデータを注入し、影響を与えようとするかもしれません。
3. 悪意のある人物や競合他社が、入力に基づいて同時にモデルを訓練する、モデルの訓練データをターゲットにして、不正確なドキュメントや悪意のあるドキュメントを意図的に作成します。被害を受けるモデルはこの偽の情報を使用して訓練し、利用者に対する生成 AI プロンプトの出力に反映されます。
4. 脆弱性 [プロンプトインジェクション (Prompt Injection)](LLM01_PromptInjection.md) は、LLM アプリケーション入力のクライアントを使用してモデルを訓練する際、サニタイゼーションやフィルタリングの実行が不十分である場合、この脆弱性への攻撃ベクトルとなる可能性があります。つまり、プロンプトインジェクション技法の一部として悪意のあるデータや偽のデータがクライアントからモデルに入力された場合、これは本質的にモデルデータに描写される可能性があります。

**参考情報リンク:**

1. [Stanford Research Paper:CS324](https://stanford-cs324.github.io/winter2022/lectures/data/) 大規模言語モデルの背後にあるデータについて語ります。
2. [How data poisoning attacks corrupt machine learning models](https://www.csoonline.com/article/3613932/how-data-poisoning-attacks-corrupt-machine-learning-models.html) データポイズニング攻撃と注意が必要な理由について、高レベルの概要がわかりやすく説明されています。
3. [MITRE ATLAS (framework) Tay Poisoning](https://atlas.mitre.org/studies/AML.CS0009/) MITRE ATLAS フレームワークは Microsoft "Tay" チャットボットプロジェクトに関するケーススタディを公表しました。これはレッドチームのスキルが未熟で、モデルにアクセスできる個人の労力によって、最終的に他の脆弱性や懸念につながる汚染されたデータで訓練できた様子を描いています。
4. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/) オープンソースモデルを外科的に改変し、それをプロバイダにアップロードして、標準ベンチマークで検出されずに誤った情報を広めることができることを詳述した記事。
5. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf/) プロンプトインジェクション ([LLM01:2023 - Prompt Injections](LLM01_PromptInjection.md)) 技法がスクレイピング時に訓練データポイズニングを引き起こす可能性もあることの証跡です。
6. [Backdoor Attacks on Language Models](https://towardsdatascience.com/backdoor-attacks-on-language-models-can-we-trust-our-models-weights-73108f9dcb1f) モデルの重みを信頼できますか？言語モデルにおけるデータポイズニングとバックドアの入門書。
7. [Poisoning Language Models During Instruction](https://arxiv.org/abs/2305.00944) モデルファインチューニングプロセスにおけるデータポイズニングを実証するチューニング研究論文。
8. [FedMLSecurity:arXiv:2306.04959](https://arxiv.org/abs/2306.04959) 連合学習と LLM における攻撃と防御のベンチマーク: LLM に対するデータポイズニング攻撃に対する連合学習の有効性を実証する研究。
9. [The poisoning of ChatGPT](https://softwarecrisis.dev/letters/the-poisoning-of-chatgpt/) LLM におけるデータポイズニングのリスクについて物議を醸したが有益な記事。
