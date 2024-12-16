## LLM02:2025 機密情報の開示 (Sensitive Information Disclosure)

### 説明

機密情報は LLM とそのアプリケーションコンテキストの両方に影響を及ぼす可能性があります。これは、個人を特定できる情報 (PII)、資産情報、医療記録、ビジネス上の機密データ、セキュリティクレデンシャル、法的文書を含みます。プロプライエタリモデルは、特にクローズドモデルやファウンデーションモデルにおいて、機密とみなされる独自のトレーニング方法やソースコードを有することもあります。

LLM は、特にアプリケーションに組み込まれている場合、その出力を通じて機密データ、プロプライエタリアルゴリズム、機密情報を開示するリスクがあります。これは、不正なデータアクセス、プライバシー侵害、知的財産侵害を引き起こす可能性があります。利用者は LLM と安全にやり取りする方法を意識すべきです。利用者は意図せずに機密データを提供するリスクを理解する必要があります。機密データが後にモデルの出力で開示される可能性があります。

このリスクを軽減するには、LLM アプリケーションは適切なデータサニタイゼーションを実行し、ユーザーデータがトレーニングモデルに入力されることを防ぐべきです。また、アプリケーション所有者は明確な利用規約を提供し、ユーザーがトレーニングモデルに含まれるそれらのデータをオプトアウトできるようにすべきです。システムプロンプト内に LLM が返すべきデータの種類に関する制限を追加することで、機密情報の開示を緩和できます。ただし、このような制限は常に守られるとは限らず、プロンプトインジェクションやその他の方法で回避される可能性があります。

### 脆弱性の一般的な例

#### 1. PII 漏洩
  個人を特定できる情報 (PII) は LLM とのやり取りの中で開示される可能性があります。
#### 2. プロプライエタリアルゴリズムの露出
  不適切に設定されたモデル出力はプロプライエタリアルゴリズムやデータを露出する可能性があります。トレーニングデータが露出すると、モデルを反転攻撃にさらす可能性があり、攻撃者は機密情報を抽出したり、入力を再構築します。たとえば、'Proof Pudding' 攻撃 (CVE-2019-20634) で実証されたように、開示されたトレーニングデータによってモデルの抽出と反転が容易になり、攻撃者は機械学習アルゴリズムのセキュリティコントロールを回避し、電子メールフィルタをバイパスできます。
#### 3. ビジネス上の機密データの開示
  生成されたレスポンスにはビジネス上の機密情報が不注意で含まれるかもしれません。

### 予防および緩和戦略

#### サニタイゼーション:

#### 1. データサニタイゼーション技法を統合する
  データサニタイゼーションを実装して、ユーザーデータがトレーニングモデルに入力されることを防ぎます。これは、トレーニングに使用する前に機密コンテンツを削除またはマスクすることを含みます。
#### 2. 堅牢な入力バリデーション
  厳密な入力バリデーションを適用して、潜在的に有害または機密性の高いデータ入力を検出して除外し、モデルを危険にさらさないようにします。

#### アクセス制御:

#### 1. 厳格なアクセス制御を実施する
  最小権限の原則に基づいて機密データへのアクセスを制限します。特定のユーザーまたはプロセスに必要なデータへのアクセスのみを許可します。
#### 2. データソースの制限
  外部データソースへのモデルアクセスを制限し、実行時データオーケストレーションが安全に管理され、意図しないデータ漏洩を回避するようにします。

#### 連合学習とプライバシー技法:

#### 1. 連合学習を活用する
  複数のサーバーやデバイスに分散して保存されているデータを使用してモデルをトレーニングします。このアプローチは一元的なデータ収集の必要性を最小限に抑え、露出リスクを軽減します。
#### 2. 差分プライバシーを組み込む
  データや出力にノイズを加える技法を適用して、攻撃者が個々のデータポイントをリバースエンジニアすることを困難にします。

#### ユーザー教育と透明性:

#### 1. LLM の安全な使用方法についてユーザーを教育する
  機密情報の入力を避けるためのガイダンスを提供します。LLM を安全に利用するためのベストプラクティスについてのトレーニングを提供します。
#### 2. データ使用の透明性を確保する
  データの保持、使用、削除に関する明確なポリシーを維持します。ユーザーがトレーニングプロセスに含まれるデータをオプトアウトできるようにします。

#### 安全なシステム構成:

#### 1. システムプリアンブルを隠す
  ユーザーがシステムの初期設定を上書きしたりアクセスする機能を制限し、内部構成が開示されるリスクを軽減します。
#### 2. セキュリティ設定ミスのベストプラクティスを参照する
  "OWASP API8:2023 Security Misconfiguration" などのガイダンスに従って、エラーメッセージや設定の詳細から機密情報が漏洩することを防ぎます。
  (参照リンク:[OWASP API8:2023 Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/))

#### 高度な技法:

#### 1. 準同型暗号
  準道警暗号を使用して、安全なデータ分析とプライバシー保護された機械学習を可能にします。これにより、モデルによって処理される間、データの機密性を維持します。
#### 2. トークン化と編集
  トークン化を実装し、機密情報を前処理してサニタイズします。パターンマッチングなどの技法により、機密コンテンツを処理前に検出して編集できます。

### 攻撃シナリオの例

#### Scenario #1: Unintentional Data Exposure
  A user receives a response containing another user's personal data due to inadequate data sanitization.
#### Scenario #2: Targeted Prompt Injection
  An attacker bypasses input filters to extract sensitive information.
#### Scenario #3: Data Leak via Training Data
  Negligent data inclusion in training leads to sensitive information disclosure.

### 参考情報リンク

1. [Lessons learned from ChatGPT’s Samsung leak](https://cybernews.com/security/chatgpt-samsung-leak-explained-lessons/): **Cybernews**
2. [AI data leak crisis: New tool prevents company secrets from being fed to ChatGPT](https://www.foxbusiness.com/politics/ai-data-leak-crisis-prevent-company-secrets-chatgpt): **Fox Business**
3. [ChatGPT Spit Out Sensitive Data When Told to Repeat ‘Poem’ Forever](https://www.wired.com/story/chatgpt-poem-forever-security-roundup/): **Wired**
4. [Using Differential Privacy to Build Secure Models](https://neptune.ai/blog/using-differential-privacy-to-build-secure-models-tools-methods-best-practices): **Neptune Blog**
5. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)

### 関連するフレームワークと分類

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [AML.T0024.000 - Infer Training Data Membership](https://atlas.mitre.org/techniques/AML.T0024.000) **MITRE ATLAS**
- [AML.T0024.001 - Invert ML Model](https://atlas.mitre.org/techniques/AML.T0024.001) **MITRE ATLAS**
- [AML.T0024.002 - Extract ML Model](https://atlas.mitre.org/techniques/AML.T0024.002) **MITRE ATLAS**
