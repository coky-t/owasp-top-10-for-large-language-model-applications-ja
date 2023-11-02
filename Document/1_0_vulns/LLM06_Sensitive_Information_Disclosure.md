## 機密情報の開示 (Sensitive Information Disclosure)

**説明:**

LLM アプリケーションはその出力を通じて機密情報、プロプライエタリアルゴリズム、その他の機密情報を明らかにする可能性があります。これにより機密データや知的財産への認可されていないアクセス、プライバシー侵害、その他のセキュリティ侵害を引き起こす可能性があります。LLM アプリケーションの利用者は LLM と安全にやり取りする方法を認識し、意図せずに機密データを入力すると LLM によって他の場所に出力される可能性があるというリスクを認識することが重要です。

このリスクを軽減するには、LLM アプリケーションは適切なデータサニタイゼーションを実行して、ユーザーデータが訓練モデルデータに混入するのを防ぐ必要があります。また、LLM アプリケーションオーナーは適切な利用規約ポリシーを用意し、自分のデータがどのように処理されるか、また、訓練モデルにデータが含まれることをオプトアウトできることを利用者が認識できるようにする必要があります。

利用者と LLM アプリケーションのやり取りは双方向の信頼境界を形成し、私たちは クライアント → LLM 入力 や LLM → クライアント出力 を本質的に信頼できません。この脆弱性は脅威モデリング演習、インフラストラクチャの保護、適切なサンドボックス化など、特定の前提条件がスコープ外であることを仮定していることに注意することが重要です。LLM が返すデータの種類に関する制限をシステムプロンプト内に追加することで、機密情報の漏洩をある程度緩和できますが、LLM の予測不可能な性質により、そのような制限が常に守られるとは限らず、プロンプトインジェクションや他の攻撃によって回避される可能性があります。

**脆弱性の一般的な例:**

1. Incomplete or improper filtering of sensitive information in the LLM’s responses.
2. Overfitting or memorization of sensitive data in the LLM’s training process.
3. Unintended disclosure of confidential information due to LLM misinterpretation, lack of data scrubbing methods or errors.

**防止方法:**

1. Integrate adequate data sanitization and scrubbing techniques to prevent user data from entering the training model data.
2. Implement robust input validation and sanitization methods to identify and filter out potential malicious inputs to prevent the model from being poisoned.
3. When enriching the model with data and if [fine-tuning](https://github.com/OWASP/www-project-top-10-for-large-language-model-applications/wiki/Definitions) a model: (I.E, data fed into the model before or during deployment)
   1. Anything that is deemed sensitive in the fine-tuning data has the potential to be revealed to a user. Therefore, apply the rule of least privilege and do not train the model on information that the highest-privileged user can access which may be displayed to a lower-privileged user.
   2. Access to external data sources (orchestration of data at runtime) should be limited.
   3. Apply strict access control methods to external data sources and a rigorous approach to maintaining a secure supply chain.

**攻撃シナリオの例:**

1. Unsuspecting legitimate user A is exposed to certain other user data via the LLM when interacting with the LLM application in a non-malicious manner.

2. User A targets a well crafted set of prompts to bypass input filters and sanitization from the LLM to cause it to reveal sensitive information (PII) about other users of the application.

3. Personal data such as PII is leaked into the model via training data due to either negligence from the user themselves, or the LLM application. This case could increase risk and probability of scenario 1 or 2 above.

**参考情報リンク:**

1. [AI data leak crisis: New tool prevents company secrets from being fed to ChatGPT](https://www.foxbusiness.com/politics/ai-data-leak-crisis-prevent-company-secrets-chatgpt) A blog explaining the risks associated to users unintentionally leaking sensitive data into LLM's and the consequences of when this information is fed into training data.
2. [Lessons learned from ChatGPT’s Samsung leak](https://cybernews.com/security/chatgpt-samsung-leak-explained-lessons/) A write up on an incident caused due to an employee unintentionally leaking source code into an LLM's training data due to misuse and unawareness, leading to this being leaked across other use-case interactions with the LLM.
3. [Cohere - Terms Of Use](https://cohere.com/terms-of-use) An example terms of use notice made available to users of an LLM to identify how data is processed.
4. [A threat modeling example](https://aivillage.org/large%20language%20models/threat-modeling-llm/) for LLM applications to assess the understanding of a systems’ goals from a business objective, mapping out the components responsible for them and recursively identifying system and performance criteria down to the essence of the implementation. Thus, the exercise aims to pre-anticipate and assess vulnerabilities to remediate and|or reduce risk.
5. [OWASP AI Security and Privacy Guide](https://owasp.org/www-project-ai-security-and-privacy-guide/) which is separate from this project.
6. [Ensuring the Security of Large Language Models](https://www.experts-exchange.com/articles/38220/Ensuring-the-Security-of-Large-Language-Models-Strategies-and-Best-Practices.html): Strategies and best practices, including Homomorphic Encryption.
