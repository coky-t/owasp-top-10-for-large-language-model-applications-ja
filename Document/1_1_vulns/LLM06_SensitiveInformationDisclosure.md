## LLM06: 機密情報の開示 (Sensitive Information Disclosure)

### 説明

LLM アプリケーションはその出力を通じて機密情報、プロプライエタリアルゴリズム、その他の機密情報を明らかにする可能性があります。これにより機密データや知的財産への認可されていないアクセス、プライバシー侵害、その他のセキュリティ侵害を引き起こす可能性があります。LLM アプリケーションの利用者は LLM と安全にやり取りする方法を認識し、意図せずに機密データを入力すると、後に LLM によって他の場所に出力される可能性があるというリスクを認識することが重要です。

このリスクを軽減するには、LLM アプリケーションは適切なデータサニタイゼーションを実行して、ユーザーデータが訓練モデルデータに混入するのを防ぐ必要があります。また、LLM アプリケーションオーナーは適切な利用規約ポリシーを用意し、自分のデータがどのように処理されるか、また、訓練モデルにデータが含まれることをオプトアウトできることを利用者が認識できるようにする必要があります。

利用者と LLM アプリケーションのやり取りは双方向の信頼境界を形成し、私たちは クライアント → LLM 入力 や LLM → クライアント出力 を本質的に信頼できません。この脆弱性は脅威モデリング演習、インフラストラクチャの保護、適切なサンドボックス化など、特定の前提条件がスコープ外であることを仮定していることに注意することが重要です。LLM が返すデータの種類に関する制限をシステムプロンプト内に追加することで、機密情報の漏洩をある程度緩和できますが、LLM の予測不可能な性質により、そのような制限が常に守られるとは限らず、プロンプトインジェクションや他の攻撃によって回避される可能性があります。

### 脆弱性の一般的な例

1. LLM のレスポンスにおいて、機密情報のフィルタリングが不完全または不適切。
2. LLM のトレーニングプロセスにおいて、機密データの過学習やメモ化。
3. LLM の誤った解釈、データスクラビング方法の欠如やエラーによる機密情報の意図しない開示。

### 予防および緩和戦略

1. 適切なデータサニタイゼーションとスクラビング技法を統合して、ユーザーデータが訓練データモデルに入ることを防ぎます。
2. 堅牢な入力バリデーションとサニタイゼーション手法を実装し、潜在的な悪意のある入力を特定して除外し、モデルが汚染されることを防ぎます。
3. モデルをデータで強化する場合、およびモデルを [ファインチューニング](https://github.com/OWASP/www-project-top-10-for-large-language-model-applications/wiki/Definitions) する場合 (つまり、データがデプロイメント前またはデプロイメント中にモデルに投入される場合)
   1. ファインチューニングデータ内の機密性が高いとみなされるものがユーザーに公開される可能性があります。したがって、最小権限のルールを適用し、最高権限のユーザーがアクセスでき、低い権限のユーザーに表示される可能性がある情報でモデルを訓練してはいけません。
   2. 外部データソースへのアクセス (実行時のデータのオーケストレーション) は制限されるべきです。
   3. 外部データソースに厳格なアクセスコントロール方法と、安全なサプライチェーンを維持するための厳密なアプローチを適用します。

### 攻撃シナリオの例

1. 疑う余地のない正規ユーザー A は悪意のない方法で LLM アプリケーションと対話する際に、LLM を介して他の特定のユーザーデータにさらされます。
2. ユーザー A は巧妙に作成された一連のプロンプトをターゲットにして、LLM からの入力フィルタとサニタイゼーションをバイパスし、アプリケーションの他のユーザーに関する機密情報 (PII) を漏洩させます。
3. ユーザー自身または LLM アプリケーションの不注意により、PII などの個人データが訓練データを介してモデルに漏洩します。この場合、上記のシナリオ 1 または 2 のリスクと確率が高まる可能性があります。

### 参考情報リンク

1. [AI data leak crisis: New tool prevents company secrets from being fed to ChatGPT](https://www.foxbusiness.com/politics/ai-data-leak-crisis-prevent-company-secrets-chatgpt): **Fox Business**
2. [Lessons learned from ChatGPT’s Samsung leak](https://cybernews.com/security/chatgpt-samsung-leak-explained-lessons/): **Cybernews**
3. [Cohere - Terms Of Use](https://cohere.com/terms-of-use) **Cohere**
4. [A threat modeling example](https://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
5. [OWASP AI Security and Privacy Guide](https://owasp.org/www-project-ai-security-and-privacy-guide/): **OWASP AI Security & Privacy Guide**
6. [Ensuring the Security of Large Language Models](https://www.experts-exchange.com/articles/38220/Ensuring-the-Security-of-Large-Language-Models-Strategies-and-Best-Practices.html): **Experts Exchange**
