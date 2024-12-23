## LLM03:2025 サプライチェーン (Supply Chain)

### 説明

LLM サプライチェーンはさまざまな脆弱性の影響を受けやすく、トレーニングデータ、モデル、デプロイメントプラットフォームの完全性に影響を及ぼす可能性があります。これらのリスクは偏った出力、セキュリティ侵害、システム障害を引き起こす可能性があります。従来のソフトウェアの脆弱性はコードの欠陥や依存関係などの問題に焦点を当てていますが、ML ではリスクはサードパティの事前トレーニング済みモデルやデータにも及びます。

これらの外部要素は改竄やポイズニング攻撃によって操作される可能性があります。

LLM の作成は専門的なタスクであり、多くの場合、サードパーティモデルに依存します。オープンアクセス LLM の台頭や、"LoRA" (Low-Rank Adaptation) や "PEFT" (Parameter-Efficient Fine-Tuning) などの新しいファインチューニング手法により、特に Hugging Face などのプラットフォーム上で、新たなサプライチェーンリスクをもたらします。最後に、オンデバイス LLM の出現により、LLM アプリケーションの攻撃対象領域とサプライチェーンリスクが増加します。

ここで説明されているリスクの一部は「LLM04 データおよびモデルポイズニング」でも説明されています。このエントリでは、リスクのサプライチェーンの側面に焦点を当てています。
簡単な脅威モデルは [こちら](https://github.com/jsotiro/ThreatModels/blob/main/LLM%20Threats-LLM%20Supply%20Chain.png) にあります。

### リスクの一般的な例

#### 1. 従来のサードパーティパッケージの脆弱性
  古くなったコンポーネントや非推奨のコンポーネントなどは、攻撃者が悪用して、LLM アプリケーションを侵害できます。これは "A06:2021 – Vulnerable and Outdated Components" に似ていますが、コンポーネントがモデルの開発時やファインチューニング時に使用されるとリスクが高まります。
  (参照リンク: [A06:2021 – Vulnerable and Outdated Components](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/))
#### 2. ライセンスのリスク
  AI 開発にはさまざまなソフトウェアやデータセットのライセンスが関係することがよくあり、適切に管理しないとリスクが生じます。さまざまなオープンソースライセンスとプロプライエタリライセンスはそれぞれ異なる法的要件を課します。データセットライセンスは使用、配布、商利用を制限することがあります。
#### 3. 古くなったモデルや非推奨のモデル
  もはやメンテナンスされていない古くなったモデルや非推奨のモデルを使用すると、セキュリティ問題につながります。
#### 4. 脆弱な事前トレーニング済みモデル
  モデルはバイナリブラックボックスであり、オープンソースとは異なり、静的検査ではセキュリティの保証がほとんどできません。脆弱な事前トレーニング済みモデルには、モデルリポジトリの安全性評価では特定されていない隠されたバイアス、バックドア、その他の悪意のある機能を含む可能性があります。脆弱なモデルは、汚染されたデータセットと、ロボトミーとも呼ばれる ROME などの技法を使用した直接的なモデル改竄の両方によって作成される可能性があります。
#### 5. 弱いモデル来歴
  現在のところ、公開されているモデルには強力な来歴保証がありません。モデルカードと関連ドキュメントはモデル情報を提供し、ユーザーを信頼させますが、モデルの起源についての保証はありません。攻撃者は、モデルリポジトリのサプライヤアカウントを侵害したり、同様のアカウントを作成してソーシャルエンジニアリング技法と組み合わせて、LLM アプリケーションのサプライチェーンを侵害できます。
#### 6. 脆弱な LoRA アダプタ
  LoRA は一般的なファインチューニング技法であり、事前トレーニング済みレイヤを既存の LLM にボルト固定することでモジュール性を高めます。この手法は効率性を高めますが、悪意のある LoRA アダプタが事前トレーニング済みのベースモデルの完全性とセキュリティを損なうという新たなリスクを生み出します。これは、共同モデルマージ環境だけでなく、アダプタをダウンロードしてデプロイ済みモデルに適用できる vLMM や OpenLLM などの一般的な推論デプロイメントプラットフォームから LoRA のサポートを悪用する場合にも発生する可能性があります。
#### 7. 共同開発プロセスの悪用
  共有環境でホストされている共同モデルマージおよびモデル処理サービス (変換など) は、共有モデルの脆弱性をもたらすために悪用される可能性があります。  モデルマージは Hugging Face で非常に人気があり、モデルマージされたモデルは OpenLLM リーダーボードの上位を占めており、レビューをバイパスするために悪用される可能性があります。  同様に、会話ボットなどのサービスは操作に対して脆弱であり、モデルに悪意のあるコードをもたらすことが証明されています。
#### 8. デバイス上の LLM モデルのサプライチェーンの脆弱性
  デバイス上の LLM モデルは、侵害された製造プロセスや、デバイス OS やファームウェアの脆弱性を悪用してモデルを侵害することで、サプライ攻撃対象領域を拡大します。攻撃者が改竄されたモデルでアプリケーションをリバースエンジニアして再パッケージ化できます。
#### 9. 不明確な利用規約とデータプライバシーポリシー
  モデル運営者の利用規約とデータプライバシーポリシーが不明確であると、アプリケーションの機密データがモデルのトレーニングに使用され、その後、機密情報の暴露につながります。これは、モデル供給者が著作権で保護された素材を使用することによるリスクにも当てはまる可能性があります。

### 予防および緩和戦略

1. 信頼できるサプライヤのみを使用して、利用規約やプライバシーポリシーを含む、データソースとサプライヤを慎重に審査します。サプライヤのセキュリティとアクセスを定期的にレビューおよび監査して、セキュリティ態勢や利用規約に変更がないことを確認します。
2. OWASP Top Ten の "A06:2021 – Vulnerable and Outdated Components" に記載されている緩和策を理解して適用します。これは、脆弱性スキャン、管理、パッチ適用コンポーネントを含みます。機密データにアクセスできる開発環境では、その環境にもこれらのコントロールを適用します。
  (参照リンク: [A06:2021 – Vulnerable and Outdated Components](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/))
3. サードパーティのモデルを選択する際には、包括的な AI レッドチームと評価を適用します。Decoding Trust は LLM のための信頼できる AI ベンチマークの一例ですが、モデルは公表されているベンチマークをパスするようにファインチューニングできます。特にモデルを使用する予定のユースケースにおいて、広範な AI レッドチームを使用してモデルを評価します。
4. ソフトウェア部品表 (Software Bill of Materials, SBOM) を使用してコンポーネントの最新インベントリを維持し、最新で正確な署名付きインベントリを確保して、デプロイされたパッケージの改竄を防止します。SBOM を使用して、新しいゼロデイの脆弱性を迅速に検出して警告できます。AI BOM と ML SBOM は新しい分野であり、OWASP CycloneDX を始めとするオプションを評価すべきです。
5. AI ライセンスのリスクを緩和するには、BOM を使用して関連するすべての種類のライセンスのインベントリを作成し、すべてのソフトウェア、ツール、データセットの定期的な監査を実施して、BOM を通じてコンプライアンスと透明性を確保します。自動ライセンス管理ツールを使用してリアルタイムに監視し、ライセンスモデルについてチームをトレーニングします。BOM で詳細なライセンスドキュメントを維持します。
6. 検証可能なソースからのモデルのみを使用し、署名とファイルハッシュによるサードパーティのモデル完全性チェックを使用して、強力なモデルの来歴の欠如を補います。同様に、外部から提供されるコードにはコード署名を使用します。
7. 共同モデル開発環境に対して厳格な監視と監査を実施し、不正使用を防止して迅速に検出します。"HuggingFace SF_Convertbot Scanner" は、使用する自動スクリプトの一例です。
  (参照リンク: [HuggingFace SF_Convertbot Scanner](https://gist.github.com/rossja/d84a93e5c6b8dd2d4a538aa010b29163))
8. 提供されたモデルとデータに対する異常検出と敵対的堅牢性テストは、「LLM04 データおよびモデルポイズニング」で説明されているように改竄とポイズニングの検出に役立ちます。理想的には、これは MLOps と LLM パイプラインの一部であるべきですが、これらは新しい技法であり、レッドチーム演習の一部として実装する方が簡単かもしれません。
9. パッチ適用ポリシーを実装して、脆弱なコンポーネントや古くなったコンポーネントを緩和します。アプリケーションが、保守されているバージョンの API と基盤モデルに依存していることを確保します。
10. AI エッジにデプロイされたモデルを完全性チェックで暗号化し、ベンダー認証 API を使用してアプリやモデルの改竄を防ぎ、認識できないファームウェアのアプリケーションを終了します。

### 攻撃シナリオの例

#### Scenario #1: Vulnerable Python Library
  An attacker exploits a vulnerable Python library to compromise an LLM app. This happened in the first Open AI data breach.  Attacks on  the PyPi package registry  tricked model developers into downloading a compromised PyTorch dependency with malware in a model development environment.  A more sophisticated example of this type of attack is Shadow Ray attack on the Ray AI framework used by many vendors to manage AI infrastructure.  In this attack, five vulnerabilities are believed to have been exploited in the wild affecting many servers.
#### Scenario #2: Direct Tampering
  Direct Tampering and publishing a model to spread misinformation. This is an actual attack with PoisonGPT bypassing Hugging Face safety features by directly changing model parameters.
#### Scenario #3: Finetuning Popular Model
  An attacker finetunes a popular open access model to remove key safety features and perform high in a specific domain (insurance). The model is finetuned to score highly on safety benchmarks but  has very targeted  triggers. They deploy it on Hugging Face for victims to use it exploiting their trust on  benchmark assurances. 
#### Scenario #4: Pre-Trained Models
  An LLM system deploys pre-trained models from a widely used repository without thorough verification. A compromised model introduces malicious code, causing biased outputs in certain contexts and leading to harmful or manipulated outcomes
#### Scenario #5: Compromised Third-Party Supplier
  A compromised third-party supplier provides a vulnerable LorA adapter that is being merged to an LLM using model merge on Hugging Face.
#### Scenario #6: Supplier Infiltration
  An attacker infiltrates a third-party supplier and compromises the production of a LoRA (Low-Rank Adaptation) adapter intended for integration with an on-device LLM deployed using frameworks like vLLM or OpenLLM. The compromised LoRA adapter is subtly altered to include hidden vulnerabilities and malicious code. Once this adapter is merged with the LLM, it provides the attacker with a covert entry point into the system. The malicious code can activate during model operations, allowing the attacker to manipulate the LLM’s outputs.
#### Scenario #7: CloudBorne and CloudJacking Attacks
  These attacks target cloud infrastructures, leveraging shared resources and vulnerabilities in the virtualization layers. CloudBorne involves exploiting firmware vulnerabilities in shared cloud environments, compromising the physical servers hosting virtual instances. CloudJacking refers to malicious control or misuse of cloud instances, potentially leading to unauthorized access to critical LLM deployment platforms. Both attacks represent significant risks for supply chains reliant on cloud-based ML models, as compromised environments could expose sensitive data or facilitate further attacks. 
#### Scenario #8: LeftOvers (CVE-2023-4969)
  LeftOvers exploitation of leaked GPU local memory to recover sensitive data. An attacker can use this attack to exfiltrate sensitive data in production servers and development workstations or laptops.  	
#### Scenario #9: WizardLM
  Following the removal of WizardLM, an attacker exploits the interest in this model and publish a fake version of the model with the same name but containing malware and backdoors.  
#### Scenario #10: Model Merge/Format Conversion Service
  An attacker stages an attack with a model merge or format conversation service to compromise a publicly available access model to inject malware. This is an actual attack published by vendor HiddenLayer.
#### Scenario #11: Reverse-Engineer Mobile App
  An attacker reverse-engineers an mobile app to replace the model with a tampered version that leads the user to scam sites. Users are encouraged to dowload the app directly via social engineering techniques. This is a "real attack on predictive AI" that affected 116 Google Play apps including popular security and safety-critical applications used for as cash recognition, parental control, face authentication, and financial service.
  (Ref. link: [real attack on predictive AI](https://arxiv.org/abs/2006.08131))
#### Scenario #12: Dataset Poisoning
  An attacker poisons publicly available datasets to help create a back door when fine-tuning models. The back door subtly favors certain companies in different markets.
#### Scenario #13: T&Cs and Privacy Policy
  An LLM operator changes its T&Cs and Privacy Policy to require an explicit opt out from using application data for model training, leading to the memorization of sensitive data.

### 参考情報リンク

1. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news)
2. [Large Language Models On-Device with MediaPipe and TensorFlow Lite](https://developers.googleblog.com/en/large-language-models-on-device-with-mediapipe-and-tensorflow-lite/)
3. [Hijacking Safetensors Conversion on Hugging Face](https://hiddenlayer.com/research/silent-sabotage/)
4. [ML Supply Chain Compromise](https://atlas.mitre.org/techniques/AML.T0010)
5. [Using LoRA Adapters with vLLM](https://docs.vllm.ai/en/latest/models/lora.html)
6. [Removing RLHF Protections in GPT-4 via Fine-Tuning](https://arxiv.org/pdf/2311.05553)
7. [Model Merging with PEFT](https://huggingface.co/blog/peft_merging)
8. [HuggingFace SF_Convertbot Scanner](https://gist.github.com/rossja/d84a93e5c6b8dd2d4a538aa010b29163)
9. [Thousands of servers hacked due to insecurely deployed Ray AI framework](https://www.csoonline.com/article/2075540/thousands-of-servers-hacked-due-to-insecurely-deployed-ray-ai-framework.html)
10. [LeftoverLocals: Listening to LLM responses through leaked GPU local memory](https://blog.trailofbits.com/2024/01/16/leftoverlocals-listening-to-llm-responses-through-leaked-gpu-local-memory/)

### 関連するフレームワークと分類

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [ML Supply Chain Compromise](https://atlas.mitre.org/techniques/AML.T0010) -  **MITRE ATLAS**
