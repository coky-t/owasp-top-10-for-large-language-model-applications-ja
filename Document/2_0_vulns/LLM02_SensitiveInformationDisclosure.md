## LLM02:2025 機密情報の開示 (Sensitive Information Disclosure)

### 説明

機密情報は LLM とそのアプリケーションコンテキストの両方に影響を及ぼす可能性があります。これは、個人を特定できる情報 (PII)、資産情報、医療記録、ビジネス上の機密データ、セキュリティクレデンシャル、法的文書を含みます。プロプライエタリモデルは、特にクローズドモデルやファウンデーションモデルにおいて、機密とみなされる独自のトレーニング方法やソースコードを有することもあります。

LLM は、特にアプリケーションに組み込まれている場合、その出力を通じて機密データ、プロプライエタリアルゴリズム、機密情報を開示するリスクがあります。これは、不正なデータアクセス、プライバシー侵害、知的財産侵害を引き起こす可能性があります。利用者は LLM と安全にやり取りする方法を意識すべきです。利用者は意図せずに機密データを提供するリスクを理解する必要があります。機密データが後にモデルの出力で開示される可能性があります。

このリスクを軽減するには、LLM アプリケーションは適切なデータサニタイゼーションを実行し、ユーザーデータがトレーニングモデルに入力されることを防ぐべきです。また、アプリケーション所有者は明確な利用規約を提供し、ユーザーがトレーニングモデルに含まれるそれらのデータをオプトアウトできるようにすべきです。システムプロンプト内に LLM が返すべきデータの種類に関する制限を追加することで、機密情報の開示を緩和できます。ただし、このような制限は常に守られるとは限らず、プロンプトインジェクションやその他の方法で回避される可能性があります。

### 脆弱性の一般的な例

#### 1. PII Leakage
  Personal identifiable information (PII) may be disclosed during interactions with the LLM.
#### 2. Proprietary Algorithm Exposure
  Poorly configured model outputs can reveal proprietary algorithms or data. Revealing training data can expose models to inversion attacks, where attackers extract sensitive information or reconstruct inputs. For instance, as demonstrated in the 'Proof Pudding' attack (CVE-2019-20634), disclosed training data facilitated model extraction and inversion, allowing attackers to circumvent security controls in machine learning algorithms and bypass email filters.
#### 3. Sensitive Business Data Disclosure
  Generated responses might inadvertently include confidential business information.

### 予防および緩和戦略

#### Sanitization:

#### 1. Integrate Data Sanitization Techniques
  Implement data sanitization to prevent user data from entering the training model. This includes scrubbing or masking sensitive content before it is used in training.
#### 2. Robust Input Validation
  Apply strict input validation methods to detect and filter out potentially harmful or sensitive data inputs, ensuring they do not compromise the model.

#### Access Controls:

#### 1. Enforce Strict Access Controls
  Limit access to sensitive data based on the principle of least privilege. Only grant access to data that is necessary for the specific user or process.
#### 2. Restrict Data Sources
  Limit model access to external data sources, and ensure runtime data orchestration is securely managed to avoid unintended data leakage.

#### Federated Learning and Privacy Techniques:

#### 1. Utilize Federated Learning
  Train models using decentralized data stored across multiple servers or devices. This approach minimizes the need for centralized data collection and reduces exposure risks.
#### 2. Incorporate Differential Privacy
  Apply techniques that add noise to the data or outputs, making it difficult for attackers to reverse-engineer individual data points.

#### User Education and Transparency:

#### 1. Educate Users on Safe LLM Usage
  Provide guidance on avoiding the input of sensitive information. Offer training on best practices for interacting with LLMs securely.
#### 2. Ensure Transparency in Data Usage
  Maintain clear policies about data retention, usage, and deletion. Allow users to opt out of having their data included in training processes.

#### Secure System Configuration:

#### 1. Conceal System Preamble
  Limit the ability for users to override or access the system's initial settings, reducing the risk of exposure to internal configurations.
#### 2. Reference Security Misconfiguration Best Practices
  Follow guidelines like "OWASP API8:2023 Security Misconfiguration" to prevent leaking sensitive information through error messages or configuration details.
  (Ref. link:[OWASP API8:2023 Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/))

#### Advanced Techniques:

#### 1. Homomorphic Encryption
  Use homomorphic encryption to enable secure data analysis and privacy-preserving machine learning. This ensures data remains confidential while being processed by the model.
#### 2. Tokenization and Redaction
  Implement tokenization to preprocess and sanitize sensitive information. Techniques like pattern matching can detect and redact confidential content before processing.

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
