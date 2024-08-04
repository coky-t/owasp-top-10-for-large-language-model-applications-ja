## LLM06: 機密情報の開示 (Sensitive Information Disclosure)

### 説明

機密情報は LLM アプリケーションでのモデルとそのデプロイメントの両方にコンテキスト的に関連しています。この用語は、個人を識別できる情報 (PII)、財務情報、健康記録、ビジネス上の機密データ、セキュリティクレデンシャル、法規制文書を含みますが、これらに限定されません。さらに、プロプライエタリのクローズドモデルやファウンデーションモデルには、機密とみなされる可能性のある独自の訓練手法とソースコードがありますが、これはオープンソースモデルやオープンウエイトモデルではそれほど問題にはなりません。

LLM も、アプリケーション内に組み込まれた場合も、その出力を通じて機密情報、プロプライエタリアルゴリズム、その他の機密情報を明らかにする可能性があります。これにより機密データや知的財産への認可されていないアクセス、プライバシー侵害、その他のセキュリティ侵害を引き起こす可能性があります。LLM アプリケーションの利用者は LLM と安全にやり取りする方法を認識し、意図せずに機密データを入力すると、後に LLM によって他の場所に出力される可能性があるというリスクを認識することが重要です。

このリスクを軽減するには、LLM アプリケーションは適切なデータサニタイゼーションを実行して、ユーザーデータが訓練モデルデータに混入するのを防ぐ必要があります。また、LLM アプリケーションオーナーは適切な利用規約ポリシーを用意し、自分のデータがどのように処理されるか、また、訓練モデルにデータが含まれることをオプトアウトできることを利用者が認識できるようにする必要があります。

利用者と LLM アプリケーションのやり取りは双方向の信頼境界を形成し、私たちは クライアント → LLM 入力 や LLM → クライアント出力 を本質的に信頼できません。この脆弱性は脅威モデリング演習、インフラストラクチャの保護、適切なサンドボックス化など、特定の前提条件がスコープ外であることを仮定していることに注意することが重要です。LLM が返すデータの種類に関する制限をシステムプロンプト内に追加することで、機密情報の漏洩をある程度緩和できますが、LLM の予測不可能な性質により、そのような制限が常に守られるとは限らず、プロンプトインジェクションや他の攻撃によって回避される可能性があります。

### 脆弱性の一般的な例

1. LLM のレスポンスにおいて、機密情報のフィルタリングが不完全または不適切: LLM がその出力から機密情報を適切にフィルタアウトできない場合に発生し、機密データが認可されていないユーザーに開示される可能性があります。
2. LLM のトレーニングプロセスにおける、機密データの過学習やメモ化: LLM が訓練セットから特定の機密データを不注意に学習して保持すると、この情報がレスポンスで再現される可能性があります。
3. LLM の誤った解釈、データスクラビング方法の欠如やエラーによる機密情報の意図しない開示: LLM が入力データを誤って解釈したり、効果的なデータサニタイゼーションメカニズムを書いている場合に発生し、機密情報を意図せず開示することにつながります。

### 予防および緩和戦略

1. 適切なデータサニタイゼーションとスクラビング技法を統合する: ユーザーデータが訓練データモデルに入ることを防ぎます。by implementing effective data sanitization and scrubbing methods.
2. 堅牢な入力バリデーションとサニタイゼーション手法を実装する: 潜在的な悪意のある入力を特定して除外し、モデルが汚染されることを防ぎます。
3. Fine-Tuning with Sensitive Data:
   - Apply the Rule of Least Privilege: Do not train the model on information accessible to the highest-privileged user if it may be displayed to lower-privileged users.
   - Limit Access to External Data Sources: Restrict access to external data sources and ensure proper data orchestration at runtime.
   - Enforce Strict Access Control: Apply rigorous access control methods to external data sources and maintain a secure supply chain.
4. Utilize Federated Learning: Train models across multiple decentralized devices or servers holding local data samples without exchanging them, thus reducing the risk of sensitive data exposure.
5. Integrate Differential Privacy Techniques: Ensure that individual data points cannot be reverse-engineered from the LLM outputs by incorporating differential privacy techniques.
   - User Education and Training: Educate users on the risks of inputting sensitive information into LLMs and provide training on best practices.
6. Data Minimization Principles: Adhere to data minimization principles by collecting and processing only the data that is necessary for the specific purpose of the application.
7. Tokenization for Sensitive Information Disclosure: A tokenizer can prevent sensitive information disclosure within an LLM application by sanitizing data through preprocessing (e.g., masking sensitive information) and redacting sensitive terms using pattern matching techniques.
   - Data Sanitization: Preprocessing data to mask or remove sensitive information (e.g., replacing credit card numbers with placeholders).
   - Using pattern matching techniques to detect and sanitize sensitive information before tokenization.
   - Redaction: Configuring the tokenizer to recognize and redact specific sensitive terms or phrases before processing by the model.
9. Padding: Apply padding to the token responses with random length noise to obscure the length of the token so that responses can not be inferred from the packets in aid to prevent side-channel attacks.
10. Homomorphic encryption can protect sensitive information in AI applications by enabling secure data analysis, facilitating privacy-preserving machine learning, supporting federated learning with encrypted data, and ensuring secure predictions while keeping user data confidential.
11. Continuous Red Teaming Operations: Regularly perform red teaming exercises to address evolving threat vectors such as Prompt Injection Attacks (LLM01) and Data Poisoning (LLM03).
12. Dynamic Monitoring and Anomaly Detection: Implement real-time monitoring and anomaly detection systems to identify and mitigate potential data leaks as they occur.
13. User Consent and Transparency:
   - Explicit Consent Mechanisms: Ensure that users explicitly consent to data usage policies.
   - Transparent Data Practices: Maintain transparency in data handling practices, including clear communication about data retention, usage, and deletion policies.
14. Limit Overrides and Conceal System Preamble to Prevent Exploitation
       - Restrict Model Preamble Overrides and Conceal System Preamble: Prevent the possibility of malicious actors exploiting the LLM by limiting the ability to override the model's preamble capabilities and ensuring that the system preamble is  not revealed. This involves implementing strict access controls and safeguards to prevent unauthorized changes or disclosures of the model's initial setup instructions. By doing so, you reduce the risk of adversaries gaining insights into the model’s structure and behavior, which they could use during the reconnaissance and weaponization phases of an attack. This strategy ensures the integrity of the LLM's foundational parameters and minimizes potential attack vectors.
15. Refer to the [OWASP API8:2023 Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/) when error messages are not handled properly, they can inadvertently expose sensitive information in logs or responses. This information can include stack traces, database dumps, API keys, user credentials, or other sensitive data that could be exploited by attackers.
       - Sanitize Error Messages: Ensure that error messages returned to clients are generic and do not reveal internal implementation details. Use custom error messages that provide minimal information.
       - Secure Logging Practices: Implement secure logging practices by sanitizing and redacting sensitive information from logs. Only log the necessary information for troubleshooting.
       - Configuration Management: Regularly review and update API configurations to ensure they follow security best practices. Disable verbose logging and other insecure settings by default.
       - Monitoring and Auditing: Monitor logs and audit configurations regularly to detect and respond to any security misconfigurations.

### 攻撃シナリオの例

1. Unintentional Data Exposure: 疑う余地のない正規ユーザー A は悪意のない方法で LLM アプリケーションと対話する際に、LLM を介して他の特定のユーザーデータにさらされます。For instance, while asking a general question, user A receives a response containing snippets of another user's personal information due to inadequate data sanitization.
2. Targeted Prompt Injection Attack: User A crafts a well-constructed set of prompts to bypass input filters and sanitization mechanisms, causing the LLM to reveal sensitive information (e.g., PII) about other users of the application. This attack exploits weaknesses in the LLM's input validation process.
3. Data Leak via Training Data: Personal data such as PII is inadvertently included in the model's training data due to negligence by the user or the LLM application. This can happen if the training data is not properly vetted and sanitized before being used to train the model. As a result, sensitive information may be revealed in the LLM's responses, exacerbating the impact of scenarios 1 and 2.
4. Insufficient Access Controls: In a scenario where the LLM accesses external data sources at runtime, weak access control methods may allow unauthorized users to query sensitive information through the LLM. For example, if the LLM is integrated with a corporate database without proper access restrictions, it might expose confidential business data to unauthorized users.
5. Model Overfitting and Memorization: During the training process, the LLM overfits on sensitive data points, memorizing them. This leads to unintentional disclosure when the LLM generates responses. For instance, an LLM trained on internal emails might inadvertently reproduce exact phrases or sensitive details from those emails in its responses.

### 参考情報リンク

1. [Lessons learned from ChatGPT’s Samsung leak](https://cybernews.com/security/chatgpt-samsung-leak-explained-lessons/): **Cybernews**
2. [AI data leak crisis: New tool prevents company secrets from being fed to ChatGPT](https://www.foxbusiness.com/politics/ai-data-leak-crisis-prevent-company-secrets-chatgpt): **Fox Business**
3. [ChatGPT Spit Out Sensitive Data When Told to Repeat ‘Poem’ Forever](https://www.wired.com/story/chatgpt-poem-forever-security-roundup/) **Wired**
4. [Nvidia’s AI software tricked into leaking data](https://www.ft.com/content/5aceb7a6-9d5a-4f1f-af3d-1ef0129b0934) **Financial Times**
5. [Building a serverless tokenization solution to mask sensitive data](https://aws.amazon.com/blogs/compute/building-a-serverless-tokenization-solution-to-mask-sensitive-data/#:~:text=Tokenization%20replaces%20the%20sensitive%20data,while%20helping%20with%20data%20protection.) **AWS**
6. [Hackers can read private AI-assistant chats even though they’re encrypted](https://arstechnica.com/security/2024/03/hackers-can-read-private-ai-assistant-chats-even-though-theyre-encrypted/) **ArsTechnica**
7. [Mitigating a token-length side-channel attack in our AI products](https://blog.cloudflare.com/ai-side-channel-attack-mitigated#:~:text=The%20researchers%20suggested%20a%20few,be%20inferred%20from%20the%20packets.)
8. [How Federated Learning Protects Privacy](https://pair.withgoogle.com/explorables/federated-learning/)
9. [Using Differential Privacy to Build Secure Models: Tools, Methods, Best Practices](https://neptune.ai/blog/using-differential-privacy-to-build-secure-models-tools-methods-best-practices) **Neptune Blog**
10. [Maximizing Data Privacy in Fine-Tuning LLMs](https://pvml.com/maximizing-data-privacy-in-fine-tuning-llms/#:~:text=of%20customer%20trust.-,Organizations%20that%20fail%20to%20protect%20sensitive%20data%20during%20the%20fine,to%20concerns%20about%20data%20privacy.)
11. [What is Data Minimization? Main Principles & Techniques](https://www.piiano.com/blog/data-minimization#:~:text=Data%20minimization%20plays%20a%20big,making%20your%20data%20even%20safer.)
12. [Solving LLM Privacy with FHE](https://medium.com/@ingonyama/solving-llm-privacy-with-fhe-3486de6ee228)
13. [OWASP API8:2023 Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/) **OWASP API Security**
