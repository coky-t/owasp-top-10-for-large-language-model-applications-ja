## LLM03: データおよびモデルポイズニング (Data and Model Poisoning)

### 説明

あらゆる機械学習アプローチの出発点は訓練データ、つまり単なる "raw text" です。高い能力 (言語や世界の知識など) を得るには、このテキストは幅広いドメイン、ジャンル、言語にまたがる必要があります。大規模言語モデルはディープニューラルネットワークを使用して、訓練データから学習したパターンに基づいて出力を生成します。したがって、大規模言語モデル (LLM) が成功する出力を生成するには、膨大な量の多様な訓練データに大きく依存します。

データポイズニングはモデルのセキュリティ、有効性、倫理的行動を損なう可能性のある脆弱性 (すべては固有の、場合によっては共有された攻撃ベクトルを持ちます)、バックドア、バイアスを導入するために、事前訓練データや、ファインチューニングや埋め込みプロセスに含まれるデータを操作することを指します。汚染された情報はユーザーに公開されたり、パフォーマンス低下、ダウンストリームソフトウェア悪用、風評被害などの他のリスクを引き起こす可能性があります。たとえユーザーが問題のある AI 出力に不信感を抱いたとしても、モデル機能の低下やブランドの評判を損なう可能性などのリスクが残ります。

- 事前訓練データとはタスクやデータセットに基づいてモデルを訓練するプロセスを指します。
- ファインチューニングはすでに訓練された既存のモデルを取得し、監修されたデータセットを使用して訓練することで、より狭い対象やより焦点を絞った目標に適応することを含みます。このデータセットには一般的に入力とそれに対応する望ましい出力の例を含みます。
- 埋め込みプロセスはカテゴリデータ (多くの場合テキスト) を言語モデルの訓練に使用できる数値表現に変換するプロセスです。埋め込みプロセスにはテキストデータの単語やフレーズを連続ベクトル空間内のベクトルとして表現することが含まれます。ベクトルは一般的に大規模テキストコーパスで訓練されたニューラルネットワークにテキストデータを入力することで生成されます。

このようなモデル開発ライフサイクルのユニークなステージを理解して、攻撃の性質や攻撃対象に応じて、データポイズニングが発生する場所と発生源を特定することが不可欠です。データポイズニングは完全性攻撃とみなされ、訓練データの改竄が正しい予測を出力するモデルの能力に影響を与えます。当然ながら、外部データソースはモデル作成者がデータを管理できず、コンテンツにバイアス、改竄された情報、不適切なコンテンツが含まれていないという高いレベルの信頼性がないため、リスクがより高くなります。データポイズニングは、モデルのパフォーマンスを低下し、偏ったコンテンツや有害なコンテンツを導入し、ダウンストリームシステムを悪用する可能性があります。これらのリスクは、検証されないコンテンツや悪意のあるコンテンツを含む可能性がある外部データソースで特に高くなります。

モデルは、共有モデルリポジトリやオープンソースプラットフォームを通じて成果物として配布されることが多く、継承された脆弱性の影響を受けやすくなります。さらに、モデルはソフトウェアとして実装され、インフラストラクチャと統合されるため、これらの環境が組み込まれると、バックドアやコンピュータウィルスなどのリスクをもたらす可能性があります。

LLM の開発者、クライアント、一般ユーザーのいずれであれ、非プロプライエタリモデルとのやり取りに伴うリスクを理解することが重要です。これらの脆弱性は訓練手順によりモデル出力の正当性に影響を与える可能性があります。特に開発者は、ファインチューニングや埋め込みに使用される内部データまたはサードパーティデータに対する直接的または間接的な攻撃のリスクに直面する可能性があり、最終的には LLM のすべてのユーザーに影響を与える可能性があります。

### 脆弱性の一般的な例

1. 悪意のある人物はモデルの訓練セットに不正確なデータや有害なデータを意図的に導入します。これは [Split-View Data Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%201%20Split-View%20Data%20Poisoning.jpeg) や [Frontrunning Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%202%20Frontrunning%20Data%20Poisoning.jpeg) などの技法によって実現できます。
   - 被害者モデルは利用者への生成 AI プロンプトの出力に反映される偽情報を使用して訓練します。
2. 悪意のある人物はモデルの訓練プロセスに改竄された、偏りのある、あるいは有害なコンテンツを直接注入して、後続の出力で返すことができます。
3. ユーザーはモデルのインタラクションの際に機密情報やプロプライエタリ情報を意図せずに注入して、後続の出力に反映される可能性があります。
4. モデルは訓練ステージのどの例においても、その出所、起源、内容が検証されていないデータを使用して訓練されるため、データが汚染されていたり正しくない場合、誤った結果を生じる可能性があります。
5. リソースへのアクセスが制限されていなかったり、サンドボックス化が不適切であると、モデルは安全でないデータを取り込み、偏った出力や有害な出力が生じる可能性があります。
   - 例のシナリオはファインチューニングプロセスで発生する可能性があり、LLM クライアントからの推論呼び出しが、意図的に、あるいは意図せずに、機密情報をモデルのデータストアに取り込む可能性があります。この機密データは、生成された出力を通じて、他の無防備なクライアントに公開されるかもしれません。
   - もう一つの例は、検証されていないソースからリモートリソースをウェブスクレイピングして、モデルのライフサイクルの訓練やファインチューニングの要素として使用されるデータを取得することです。

### 予防および緩和戦略

1. データの起源と変換の詳細な記録を保持します。"ML-BOM" (Machine Learning Bill of Materials, 機械学習部品表) IE, OWASP CycloneDX などのツールを使用して、データサプライチェーンを追跡します。サードパーティモデルを継承する場合は、モデルデータセットの収集と訓練フェーズ、および関連するユースケースに関する透明性のために、開発者モデルカードを調査することを検討してください。
2. 事前訓練、ファインチューニング、埋め込みのそれぞれのステージで取得した、対象となるデータソースとそれに含まれるデータの正当性を検証します。モデルの訓練データ、その起源、関連性を追跡できるツールを開発し、評判の良いセキュリティベンダーと協力して、データポイズニングや悪意のあるコンテンツに対抗する追加のプロトコルを開発します。
3. データを保護するためにデータベンダーのオンボーディング要求を精査し、安全で信頼できるパートナーのみがサプライチェーンに統合されるようにします。モデル出力を信頼できる外部データソースに対して検証し、不整合やポイズニングの兆候を検出します。
4. LLM のユースケースと、それが統合されるアプリケーションを検証します。個別の訓練データからさまざまなモデルを作成したり、さまざまなユースケースごとにファインチューニングして、定義されたユースケースに従って、より詳細で正確な生成 AI 出力を作成します。
5. 訓練プロセスを妨げる可能性のある意図しないデータソースをモデルがスクレイピングしないように、インフラストラクチャコントロールを通じて十分なサンドボックスが存在することを確保します。
6. 特定の訓練データまたはデータソースのカテゴリに厳密な入力フィルタと分類器を使用して、改竄されたデータの量を制御します。統計的外れ値検出や異常値検出手法などの技法を使用したデータサニタイゼーションにより、ファインチューニングプロセスに供給される可能性のある敵対的データを検出して削除します。
7. データバージョン管理 (Data Version Control, DVC) を使用して、操作、削除、追加されてポイズニングにつながった可能性のあるデータセットの部分を厳密に特定して追跡します。バージョン管理はソフトウェア開発だけでなく、ML モデルの開発でも重要であり、ソースコードと、データセットやモデルなどのアーティファクトの両方の変更を追跡および管理する必要があります。ML では、データセットは訓練プロセスの入力アーティファクトとして機能し、モデルは出力アーティファクトであるため、開発プロセスの完全性と再現性を維持するにはデータセットのバージョニングが不可欠になります。
8. ベクターデータベースを使用して、ユーザーが提供する情報を保存および管理することで、他のユーザーへのポイズニングを防ぎ、モデル全体を再訓練する必要なく運用での調整を可能にします。
9. レッドチームキャンペーンを運用して、データポイズニングに対するモデルと環境のセーフガードの機能をテストします。連合学習や制約などの敵対的ロバストネス技法での対抗は、外れ値の影響を最小限に抑えたり、敵対的訓練で訓練データの最悪ケースの摂動に威力を発揮するのに有利になります。
10. テストと検出。訓練段階での損失を計測し、訓練したモデルを解析して、特定のテスト入力に対するモデルの動作を解析することで、ポイズニング攻撃の兆候を検出します。
   - 閾値を超えた偏ったレスポンスの数を監視して警告します。
   - ヒューマンループを使用して、レスポンスのレビューと監査を行います。
   - 専用 LLM を実装して、望ましくない結果に対するベンチマークを行い、[強化学習技法](https://wandb.ai/ayush-thakur/Intro-RLAIF/reports/An-Introduction-to-Training-LLMs-Using-Reinforcement-Learning-From-Human-Feedback-RLHF---VmlldzozMzYyNjcy) を使用して他の LLM を訓練します。
11. 推論時に、検索拡張生成 (Retrieval Augmentation Generation, RAG) と信頼できるデータエンティティのグラウンディング技法を統合して、事実に基づいた正確な言語の知識ソースや定義を提供することで、モデルデータパイプラインに入るリスクをもたらす可能性のあるハルシネーションや不正確な生成のリスクを軽減できます。

### 攻撃シナリオの例

1. 誤解を招く出力: 攻撃者は訓練データを操作したり、プロンプトインジェクション技法を使用して、LLM の出力にバイアスをかけます。その結果、モデルは誤解を招くレスポンスやバイアスのあるレスポンスを生成し、ユーザーの意見を形成したり、誤った情報を広めたり、ヘイトスピーチなどの有害な行為を扇動する可能性があります。
2. 有害なデータインジェクション: 適切なデータフィルタリングとサニタイゼーションがないと、悪意のあるユーザーが訓練セットに有害なデータを導入する可能性があります。このデータはモデルが有害なバイアスや誤った情報を学習して伝播し、生成された出力を通じて他のユーザーに拡散される可能性があります。
3. 意図的な改竄: 悪意のある行為者や競合他社が、意図的に改竄されたドキュメントや有害なドキュメントを作成し、モデルの訓練データに入力します。十分な精査メカニズムがないモデルでは、このような誤った情報を組み込むため、これらの不正確さを反映した出力につながり、ユーザーに損害を与えたり、誤解を招く可能性があります。
4. プロンプトインジェクション攻撃: 不適切なサニタイゼーションとフィルタリングにより、攻撃者はプロンプトインジェクションを介して有害なデータや誤解を招くデータをモデルに挿入できます。この攻撃は、モデルが誤って訓練データに組み込んだユーザー入力を利用して、侵害された出力やバイアスのある出力を後続のユーザーに拡散することになります。

### 参考情報リンク

1. [How data poisoning attacks corrupt machine learning models](https://www.csoonline.com/article/3613932/how-data-poisoning-attacks-corrupt-machine-learning-models.html): **CSO Online**
2. [MITRE ATLAS (framework) Tay Poisoning](https://atlas.mitre.org/studies/AML.CS0009/): **MITRE ATLAS**
3. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/): **Mithril Security**
4. [Poisoning Language Models During Instruction](https://arxiv.org/abs/2305.00944): **Arxiv White Paper 2305.00944**
5.  [Poisoning Web-Scale Training Datasets - Nicholas Carlini | Stanford MLSys #75](https://www.youtube.com/watch?v=h9jf1ikcGyk): **Stanford MLSys Seminars YouTube Video**
6.  [ML Model Repositories: The Next Big Supply Chain Attack Target](https://www.darkreading.com/cloud-security/ml-model-repositories-next-big-supply-chain-attack-target) **OffSecML**
7.  [Data Scientists Targeted by Malicious Hugging Face ML Models with Silent Backdoor](https://jfrog.com/blog/data-scientists-targeted-by-malicious-hugging-face-ml-models-with-silent-backdoor/) **JFrog**
8.  [Backdoor Attacks on Language Models](https://towardsdatascience.com/backdoor-attacks-on-language-models-can-we-trust-our-models-weights-73108f9dcb1f): **Towards Data Science**
9.  [Can you trust ChatGPT’s package recommendations?](https://vulcan.io/blog/ai-hallucinations-package-risk) **VULCAN**