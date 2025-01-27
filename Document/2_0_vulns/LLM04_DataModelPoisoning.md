## LLM04: データおよびモデルポイズニング (Data and Model Poisoning)

### 説明

データポイズニングは、事前トレーニング、ファインチューニング、埋め込みデータが操作されて、脆弱性、バックドア、バイアスを持ち込まれたときに発生します。この操作は、モデルのセキュリティ、パフォーマンス、倫理的行動を損ない、有害な出力や能力の低下につながる可能性があります。一般的なリスクには、モデルのパフォーマンス低下、偏ったコンテンツや有害なコンテンツ、ダウンストリームシステムの悪用などがあります。

データポイズニングは、事前トレーニング (一般的なデータからの学習)、ファインチューニング (特定のタスクへのモデルの適応)、エンベディング (テキストから数値ベクトルへの変換)、転移学習 (事前学習済みモデルを新しいタスクで再利用) など、LLM ライフサイクルの様々な段階をターゲットにできます。これらの段階を理解することは、脆弱性がどこから発生するかを特定するのに役立ちます。トレーニングデータの改竄はモデルの正確な予測を行う能力に影響を影響を及ぼすため、データポイズニングは完全性攻撃とみなされます。検証されていないコンテンツや悪意のあるコンテンツが含まれている可能性のある外部データソースでは、リスクが特に高くなります。

さらに、共有リポジトリやオープンソースプラットフォームを通じて配布されるモデルは、データポイズニング以外のリスクを伴う可能性があります。たとえば、悪意のあるピクリングなどの技法で埋め込まれたマルウェアは、モデルがロードされるときに有害なコードを実行する可能性があります。また、ポイズニングはバックドアの実装を許す可能性があることも考慮してください。そのようなバックドアは、特定のトリガーが変更するまで、モデルの動作をそのままにしておくかもしれません。これはそのような変更をテストしたり検出することが難しくなり、事実上、モデルがスリーパーエージェントになる機会を生み出すかもしれません。

### 脆弱性の一般的な例

1. 悪意のある行為者はトレーニング時に有害なデータを導入し、偏った出力をもたらします。"Split-View Data Poisoning" や "Frontrunning Poisoning" などの技法はモデルトレーニングダイナミクスを悪用してこれを実現します。
  (参照リンク: [Split-View Data Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%201%20Split-View%20Data%20Poisoning.jpeg))
  (参照リンク: [Frontrunning Poisoning](https://github.com/GangGreenTemperTatum/speaking/blob/main/dc604/hacker-summer-camp-23/Ads%20_%20Poisoning%20Web%20Training%20Datasets%20_%20Flow%20Diagram%20-%20Exploit%202%20Frontrunning%20Data%20Poisoning.jpeg))
2. 攻撃者は有害なコンテンツをトレーニングプロセスに直接注入し、モデルの出力品質を損なう可能性があります。
3. ユーザーはインタラクション時に機密情報やプロプライエタリ情報を無意識のうちに注入し、その後の出力で開示される可能性があります。
4. 検証されていないトレーニングデータは、偏った出力や誤った出力のリスクを高めます。
5. リソースアクセス制限がないと、安全でないデータの取り込みを許可し、偏った出力となる可能性があります。

### 予防および緩和戦略

1. OWASP CycloneDX や ML-BOM などのツールを使用してデータの起源と変化を追跡し、[Dyana](https://github.com/dreadnode/dyana) などのツールを活用してサードパーティソフトウェアの動的解析を実行します。すべてのモデル開発段階においてデータの正当性を検証します。
2. データベンダーを厳密に審査し、信頼できるソースに対してモデル出力を検証して、ポイズニングの兆候を検出します。
3. 厳密なサンドボックスを実装して、検証されていないデータソースへのモデルの開示を制限します。異常検出技法を使用して、敵対的なデータを除外します。
4. ファインチューニング用の特定のデータセット使用して、さまざまなユースケースに合わせてモデルを調整します。これは定義された目標に基づいてより正確な出力を生成するのに役立ちます。
5. 十分なインフラストラクチャコントロールを確保し、モデルが意図しないデータソースにアクセスすることを防ぎます。
6. データバージョンコントロール (DVC) を使用して、データセットの変更を追跡し、操作を検出します。バージョン管理はモデルの完全性を維持するために極めて重要です。
7. ユーザーが提供した情報をベクトルデータベースに保存し、モデル全体を再トレーニングせずに調整できるようにします。
8. レッドチームキャンペーンや、連合学習などの敵対的技法を使用してモデルの堅牢性をテストし、データ摂動の影響を最小限に抑えます。
9. トレーニングの損失を監視し、モデルの動作を分析して、ポイズニングの兆候を探します。閾値を使用して異常な出力を検出します。
10. 推論時に、検索拡張生成 (Retrieval-Augmented Generation, RAG) とグラウンディング技法を統合して、幻覚のリスクを軽減します。

### 攻撃シナリオの例

#### シナリオ #1
  攻撃者は、トレーニングデータを操作したり、プロンプトインジェクション技法を使用して、モデルの出力に偏りを与え、誤った情報を拡散します。
#### シナリオ #2
  適切なフィルタリングが行われていない有害なデータは、有害な出力や偏った出力につながり、危険な情報を伝播する可能性があります。
#### シナリオ # 3
  悪意のある行為者や競合他社がトレーニング用に偽造されたドキュメントを作成し、その結果としてこれらの不正確さを反映したモデル出力が生まれます。
#### シナリオ #4
  不適切なフィルタリングは、攻撃者がプロンプトインジェクションを介して誤解を招くデータを挿入し、侵害された出力につながる可能性があります。
#### シナリオ #5
  攻撃者がポイズニング技法を使用して、モデルにバックドアトリガーを挿入します。これは、認証のバイパス、データの流出、隠しコマンドの実行を許してしまう可能性があります。

### 参考情報リンク

1. [How data poisoning attacks corrupt machine learning models](https://www.csoonline.com/article/3613932/how-data-poisoning-attacks-corrupt-machine-learning-models.html): **CSO Online**
2. [MITRE ATLAS (framework) Tay Poisoning](https://atlas.mitre.org/studies/AML.CS0009/): **MITRE ATLAS**
3. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/): **Mithril Security**
4. [Poisoning Language Models During Instruction](https://arxiv.org/abs/2305.00944): **Arxiv White Paper 2305.00944**
5. [Poisoning Web-Scale Training Datasets - Nicholas Carlini | Stanford MLSys #75](https://www.youtube.com/watch?v=h9jf1ikcGyk): **Stanford MLSys Seminars YouTube Video**
6. [ML Model Repositories: The Next Big Supply Chain Attack Target](https://www.darkreading.com/cloud-security/ml-model-repositories-next-big-supply-chain-attack-target) **OffSecML**
7. [Data Scientists Targeted by Malicious Hugging Face ML Models with Silent Backdoor](https://jfrog.com/blog/data-scientists-targeted-by-malicious-hugging-face-ml-models-with-silent-backdoor/) **JFrog**
8. [Backdoor Attacks on Language Models](https://towardsdatascience.com/backdoor-attacks-on-language-models-can-we-trust-our-models-weights-73108f9dcb1f): **Towards Data Science**
9. [Never a dill moment: Exploiting machine learning pickle files](https://blog.trailofbits.com/2021/03/15/never-a-dill-moment-exploiting-machine-learning-pickle-files/) **TrailofBits**
10. [arXiv:2401.05566 Sleeper Agents: Training Deceptive LLMs that Persist Through Safety Training](https://www.anthropic.com/news/sleeper-agents-training-deceptive-llms-that-persist-through-safety-training) **Anthropic (arXiv)**
11. [Backdoor Attacks on AI Models](https://www.cobalt.io/blog/backdoor-attacks-on-ai-models) **Cobalt**

### 関連するフレームワークと分類

インフラストラクチャデプロイメント、適用される環境制御、その他のベストプラクティスに関する包括的な情報、シナリオ戦略については、このセクションを参照してください。

- [AML.T0018 | Backdoor ML Model](https://atlas.mitre.org/techniques/AML.T0018) **MITRE ATLAS**
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework): Strategies for ensuring AI integrity. **NIST**
- [ML07:2023 Transfer Learning Attack](https://owasp.org/www-project-machine-learning-security-top-10/docs/ML07_2023-Transfer_Learning_Attack) **OWASP Machine Learning Security Top Ten**
