## LLM04: データおよびモデルポイズニング (Data and Model Poisoning)

### 説明

データポイズニングは、事前トレーニング、ファインチューニング、埋め込みデータが操作されて、脆弱性、バックドア、バイアスを持ち込まれたときに発生します。この操作は、モデルのセキュリティ、パフォーマンス、倫理的行動を損ない、有害な出力や能力の低下につながる可能性があります。一般的なリスクには、モデルのパフォーマンス低下、偏ったコンテンツや有害なコンテンツ、ダウンストリームシステムの悪用などがあります。

データポイズニングは、事前トレーニング (一般的なデータからの学習)、ファインチューニング (特定のタスクへのモデルの適応)、エンベディング (テキストから数値ベクトルへの変換) など、LLM ライフサイクルの様々な段階をターゲットにできます。これらの段階を理解することは、脆弱性がどこから発生するかを特定するのに役立ちます。トレーニングデータの改竄はモデルの正確な予測を行う能力に影響を影響を及ぼすため、データポイズニングは完全性攻撃とみなされます。検証されていないコンテンツや悪意のあるコンテンツが含まれている可能性のある外部データソースでは、リスクが特に高くなります。

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

1. Track data origins and transformations using tools like OWASP CycloneDX or ML-BOM. Verify data legitimacy during all model development stages.
2. Vet data vendors rigorously, and validate model outputs against trusted sources to detect signs of poisoning.
3. Implement strict sandboxing to limit model exposure to unverified data sources. Use anomaly detection techniques to filter out adversarial data.
4. Tailor models for different use cases by using specific datasets for fine-tuning. This helps produce more accurate outputs based on defined goals.
5. Ensure sufficient infrastructure controls to prevent the model from accessing unintended data sources.
6. Use data version control (DVC) to track changes in datasets and detect manipulation. Versioning is crucial for maintaining model integrity.
7. Store user-supplied information in a vector database, allowing adjustments without re-training the entire model.
8. Test model robustness with red team campaigns and adversarial techniques, such as federated learning, to minimize the impact of data perturbations.
9. Monitor training loss and analyze model behavior for signs of poisoning. Use thresholds to detect anomalous outputs.
10. During inference, integrate Retrieval-Augmented Generation (RAG) and grounding techniques to reduce risks of hallucinations.

### 攻撃シナリオの例

#### Scenario #1
  An attacker biases the model's outputs by manipulating training data or using prompt injection techniques, spreading misinformation.
#### Scenario #2
  Toxic data without proper filtering can lead to harmful or biased outputs, propagating dangerous information.
#### Scenario # 3
  A malicious actor or competitor creates falsified documents for training, resulting in model outputs that reflect these inaccuracies.
#### Scenario #4
  Inadequate filtering allows an attacker to insert misleading data via prompt injection, leading to compromised outputs.
#### Scenario #5
  An attacker uses poisoning techniques to insert a backdoor trigger into the model. This could leave you open to authentication bypass, data exfiltration or hidden command execution.

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

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [AML.T0018 | Backdoor ML Model](https://atlas.mitre.org/techniques/AML.T0018) **MITRE ATLAS**
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework): Strategies for ensuring AI integrity. **NIST**
