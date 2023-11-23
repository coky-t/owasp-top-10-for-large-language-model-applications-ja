## サプライチェーン脆弱性 (Supply-Chain Vulnerabilities)

**説明:** 

LLM のサプライチェーンは脆弱である可能性があり、訓練データ、ML モデル、デプロイメントプラットフォームの完全性に影響を与えます。これらの脆弱性はバイアスのある結果、セキュリティ侵害、さらには完全なシステム障害につながる可能性があります。従来、脆弱性はソフトウェアコンポーネントに焦点を当てていますが、機械学習では改竄やポイズニング攻撃の影響を受けやすいサードパーティが提供する事前学習済みモデルや訓練データでこれを拡張します。

最後に、LLM プラグイン拡張機能はそれ自体の脆弱性をもたらす可能性があります。これらは LLM - 安全でないプラグイン設計 (Insecure Plugin Design) で説明されており、LLM プラグインの書き方をカバーし、サードパーティプラグインを評価するのに役立つ情報を提供しています。

**脆弱性の一般的な例:**

1. 従来のサードパーティパッケージの脆弱性 (古いコンポーネントや非推奨のコンポーネントを含む) 。
2. 脆弱な事前訓練モデルを使用してファインチューニングします。
3. 汚染されたクラウドソーシングデータを訓練に使用します。
4. セキュリティ問題につながる保守されていない古いモデルや非推奨のモデルを使用します。
5. モデル運用者の利用規約やデータプライバシーポリシーが不明確なため、アプリケーションの機密データがモデルの訓練とその後の機密情報の流出につながります。これはモデル提供者が著作権で保護された素材を使用することによるリスクにも当てはまることがあります。

**防止方法:**

1. データソースとサプライヤー (利用規約とプライバシーポリシーを含む) を慎重に精査し、信頼できるサプライヤーのみを使用します。独立して監査された適切なセキュリティが配備されていること、モデルオペレータのポリシーがデータ保護ポリシーに沿っていること、つまり、モデルの訓練にデータが使用されていないことを確保します。同様に、モデル保守者から著作権で保護された素材の使用に対する保証と法的緩和を求めます。
2. 信頼できるプラグインのみを使用し、アプリケーション要件についてテストされていることを確保します。LLM-Insecure Plugin Design ではサードパーティプラグインの使用によるリスクを軽減するためにテストすべきである、安全でないプラグイン設計の LLM の側面に関する情報を提供します。
3. OWASP Top 10 の [A06:2021 – 脆弱で古くなったコンポーネント (Vulnerable and Outdated Components)](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/) にある緩和策を理解し、適用します。これには脆弱性スキャン、管理、コンポーネントのパッチ適用が含まれます。機密データにアクセスできる開発環境では、それらの環境にもこれらのコントロールを適用します。
4. ソフトウェア部品表 (Software Bill of Materials, SBOM) を使用してコンポーネントの最新のインベントリを維持し、デプロイされたパッケージの改竄を防ぐために、最新かつ正確で署名されたインベントリを確保します。SBOM を使用して、新しいゼロデイ脆弱性を迅速に検出して警告できます。
5. 執筆時点では、SBOM はモデル、そのアーティファクト、データセットをカバーしていません。LLM アプリケーションが独自のモデルを使用する場合、MLOps のベストプラクティスと、データ、モデル、実験を追跡する安全なモデルリポジトリを提供するプラットフォームを使用すべきです。
6. また、外部モデルとサプライヤーを使用する場合は、モデル署名とコード署名を使用すべきです。
7. 提供されたモデルとデータに対する異常検出と敵対的ロバストネステストは [訓練データポイズニング (Training Data Poisoning)](LLM03_Training_Data_Poisoning.md) で説明されているように、改竄やポイズニングの検出に役立ちます。理想的には、MLOps パイプラインの一部であるべきです。しかし、これらは新たな技法であり、レッドチームエクササイズの一部として実装するほうが簡単かもしれません。
8. コンポーネントと環境の脆弱性スキャン、認可されていないプラグインの使用、モデルとそのアーティファクトを含む古いコンポーネントをカバーするために十分な監視を実装します。
9. パッチ適用ポリシーを実装して、脆弱なコンポーネントや古いコンポーネントを軽減します。アプリケーションが維持している API と基盤となるモデルに依存していることを確保します。
10. サプライヤーのセキュリティとアクセスを定期的にレビューおよび監査して、セキュリティ態勢や利用規約に変更がないことを確認します。

**攻撃シナリオの例:**

1. 攻撃者は脆弱な Python ライブラリを悪用して、システムを侵害します。これは最初の Open AI データ侵害で発生しました。

2. 攻撃者はフライトを検索するための LLM プラグインを提供して、プラグインユーザーの詐欺につながる偽のリンクを生成します。

3. 攻撃者は PyPi パッケージレジストリを悪用して、モデル開発者を騙し、危殆化したパッケージをダウンロードさせ、モデル開発環境でデータ窃取や権限昇格を行います。これは実際の攻撃でした。

4. 攻撃者は経済分析や社会調査に特化した一般に公開されている事前訓練モデルを汚染し、誤情報やフェイクニュースを生成するバックドアを作成します。攻撃者はそれをモデルマーケットプレイス (HuggingFace など) にデプロイし、被害者が使用します。

5. 攻撃者は一般に公開されているデータセットを汚染し、モデルをファインチューニングする際にバックドアの作成を支援します。バックドアはさまざまなマーケットで特定の企業を巧妙に優遇します。

6. サプライヤー (アウトソーシング開発者、ホスティング会社など) の危殆化した従業員がデータ、モデル、コードを抽出し、知的財産を盗みます。

7. LLM オペレータが利用規約とプライバシーポリシーを変更し、モデルトレーニングのためにアプリケーションデータを使用することを明示的にオプトアウトする必要があるため、機密データのメモ化につながります。


**参考情報リンク:**

1. [ChatGPT Data Breach Confirmed as Security Firm Warns of Vulnerable Component Exploitation](https://www.securityweek.com/chatgpt-data-breach-confirmed-as-security-firm-warns-of-vulnerable-component-exploitation/): redis-py の脆弱性の悪用により、初の OpanAI データ侵害と障害が発生します。
2. [Plugin review process](https://platform.openai.com/docs/plugins/review): OpenAI のプラグインレビュープロセス。
3. [Compromised PyTorch-nightly dependency chain](https://pytorch.org/blog/compromised-nightly-dependency/): 攻撃者は PyPi での依存関係チェーン攻撃を成功させ、PyTorch 開発者を騙して PyTorch 依存関係を装ったデータ流出マルウェアをダウンロードさせます。
4. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/)
5. [Army looking at the possibility of ‘AI BOMs](https://defensescoop.com/2023/05/25/army-looking-at-the-possibility-of-ai-boms-bill-of-materials/)
6. [Failure Modes in Machine Learning](https://learn.microsoft.com/en-us/security/engineering/failure-modes-in-machine-learning): サプライチェーン攻撃の例を含む Microsoft の AI/ML 脅威リスト。
7. [ML Supply Chain Compromise](https://atlas.mitre.org/techniques/AML.T0010/): MITRE ATLAS による ML サプライチェーン侵害とその対策についての報道。
8. [Transferability in Machine Learning: from Phenomena to Black-Box Attacks using Adversarial Samples](https://arxiv.org/pdf/1605.07277.pdf): データポイズニングが転移学習を介して転移可能であるという証跡。
9. [BadNets: Identifying Vulnerabilities in the Machine Learning Model Supply Chain](https://arxiv.org/abs/1708.06733) 改竄された可能性がある事前構築済みモデル (BadNets) を使用して、セキュリティ問題を浮き彫りにした 2017 年の重大な論文。
10. [VirusTotal Poisoning](https://atlas.mitre.org/studies/AML.CS0002): サプライチェーンを介したデータポイズニングの例; McAfee の報告によると、攻撃者はランサムウェアの変異合成亜種を VirusTotal に埋め込み、アンチウィルス ML システムを混乱させ、ランサムウェアを良性として誤分類します。
