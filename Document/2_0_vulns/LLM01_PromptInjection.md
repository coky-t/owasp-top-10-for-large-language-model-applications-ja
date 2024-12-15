## LLM01:2025 プロンプトインジェクション (Prompt Injection)

### 説明

プロンプトインジェクション脆弱性はユーザープロンプトが LLM の動作や出力を意図しない方法で変更する際に発生します。これらの入力は人間に近くできない場合でもモデルに影響を及ぼす可能性があるため、コンテンツがモデルによって解析される限り、プロンプトインジェクションは人間が可視/可読である必要はありません。

プロンプトインジェクション脆弱性は、モデルがプロンプトを処理する方法と、入力によってモデルがモデルの他の部分に誤って渡す方法に存在し、ガイドラインに違反したり、有害なコンテンツを生成したり、不正アクセスを可能にしたり、重要な決定に影響を及ぼす可能性があります。検索拡張生成 (Retrieval Augmented Generation, RAG) やファインチューニングなどの技法は LLM 出力をより適切で正確なものにすることを目的としていますが、プロンプトインジェクション脆弱性を完全に緩和するものではないことが研究によって示されています。

プロンプトインジェクションとジェイルブレーキングは LLM セキュリティの関連概念ですが、しばしば同じ意味で使用されます。プロンプトインジェクションは特定の入力によってモデルの応答を操作して、その動作を変更します。それには安全対策のバイパスを含む可能性があります。ジェイルブレーキングはプロンプトインジェクションの一形態であり、攻撃者がモデルの安全プロトコルを完全に無視させる入力を提供します。開発者はシステムプロンプトと入力処理にセーフガードを組み込み、プロンプトインジェクション攻撃の軽減しますが、ジェイルブレーキングを効果的に防止するには、モデルのトレーニングと安全メカニズムを継続的に更新する必要があります。

### プロンプトインジェクション脆弱性の種類

#### 直接プロンプトインジェクション
  直接プロンプトインジェクションはユーザーのプロンプト入力が意図しない方法や予期しない方法でモデルの動作を直接変更する際に発生します。入力には意図的な (つまり、悪意のある行為者がモデルを悪用するためにプロンプトを作成する) ものと意図的でない (つまり、ユーザーが予期しない動作を引き起こす入力を誤って提供する) ものがあります。

#### 間接プロンプトインジェクション
  間接プロンプトインジェクションは LLM がウェブサイトやファイルなどの外部ソースからの入力を受け入れる際に発生します。そのコンテンツは、モデルによって解釈される際に、意図しない方法や予期しない方法でモデルの動作を変更する外部コンテンツデータを有する可能性があります。直接インジェクションと同様に、間接インジェクションにも意図的なものと意図的でないものがあります。

プロンプトインジェクション攻撃が成功した場合の影響の重大性と性質は大きく異なる可能性があり、モデルが動作するビジネスコンテキストと、モデルが設計されたエージェンシーの両方に大きく依存します。しかし、一般的にプロンプトインジェクションは意図しない結果につながる可能性があり、以下を含みますがそれに限定されません。

- 機密情報の開示
- AI システムインフラストラクチャやシステムプロンプトに関する機密情報の漏洩
- 誤った出力や偏った出力につながるコンテンツ操作
- LLM で利用可能な機能への不正アクセスの提供
- 接続されたシステムでの任意のコマンドの実行
- 重要な意思決定プロセスの操作

複数のデータタイプを同時に処理するマルチモーダル AI の台頭は、プロンプトインジェクション特有のリスクをもたらします。悪意のある行為者は、無害なテキストに付随する画像に指示を隠すなど、モダリティ間のやり取りを悪用する可能性があります。これらのシステムの複雑さは攻撃対象領域を拡大します。マルチモーダルモデルは、現在の技術では検出や軽減が難しい、新しいクロスモーダル攻撃の影響を受けやすくなる可能性もあります。堅牢なマルチモーダル固有の防御はさらなる研究開発の重要な分野です。

### 予防および緩和戦略

Prompt injection vulnerabilities are possible due to the nature of generative AI. Given the stochastic influence at the heart of the way models work, it is unclear if there are fool-proof methods of prevention for prompt injection. However, the following measures can mitigate the impact of prompt injections:

#### 1. Constrain model behavior
  Provide specific instructions about the model's role, capabilities, and limitations within the system prompt. Enforce strict context adherence, limit responses to specific tasks or topics, and instruct the model to ignore attempts to modify core instructions.
#### 2. Define and validate expected output formats
  Specify clear output formats, request detailed reasoning and source citations, and use deterministic code to validate adherence to these formats.
#### 3. Implement input and output filtering
  Define sensitive categories and construct rules for identifying and handling such content. Apply semantic filters and use string-checking to scan for non-allowed content. Evaluate responses using the RAG Triad: Assess context relevance, groundedness, and question/answer relevance to identify potentially malicious outputs.
#### 4. Enforce privilege control and least privilege access
  Provide the application with its own API tokens for extensible functionality, and handle these functions in code rather than providing them to the model. Restrict the model's access privileges to the minimum necessary for its intended operations.
#### 5. Require human approval for high-risk actions
  Implement human-in-the-loop controls for privileged operations to prevent unauthorized actions.
#### 6. Segregate and identify external content
  Separate and clearly denote untrusted content to limit its influence on user prompts.
#### 7. Conduct adversarial testing and attack simulations
  Perform regular penetration testing and breach simulations, treating the model as an untrusted user to test the effectiveness of trust boundaries and access controls.

### 攻撃シナリオの例

#### Scenario #1: Direct Injection
  An attacker injects a prompt into a customer support chatbot, instructing it to ignore previous guidelines, query private data stores, and send emails, leading to unauthorized access and privilege escalation.
#### Scenario #2: Indirect Injection
  A user employs an LLM to summarize a webpage containing hidden instructions that cause the LLM to insert an image linking to a URL, leading to exfiltration of the the private conversation.
#### Scenario #3: Unintentional Injection
  A company includes an instruction in a job description to identify AI-generated applications. An applicant, unaware of this instruction, uses an LLM to optimize their resume, inadvertently triggering the AI detection.
#### Scenario #4: Intentional Model Influence
  An attacker modifies a document in a repository used by a Retrieval-Augmented Generation (RAG) application. When a user's query returns the modified content, the malicious instructions alter the LLM's output, generating misleading results.
#### Scenario #5: Code Injection
  An attacker exploits a vulnerability (CVE-2024-5184) in an LLM-powered email assistant to inject malicious prompts, allowing access to sensitive information and manipulation of email content.
#### Scenario #6: Payload Splitting
  An attacker uploads a resume with split malicious prompts. When an LLM is used to evaluate the candidate, the combined prompts manipulate the model's response, resulting in a positive recommendation despite the actual resume contents.
#### Scenario #7: Multimodal Injection
  An attacker embeds a malicious prompt within an image that accompanies benign text. When a multimodal AI processes the image and text concurrently, the hidden prompt alters the model's behavior, potentially leading to unauthorized actions or disclosure of sensitive information.
#### Scenario #8: Adversarial Suffix
  An attacker appends a seemingly meaningless string of characters to a prompt, which influences the LLM's output in a malicious way, bypassing safety measures.
#### Scenario #9: Multilingual/Obfuscated Attack
  An attacker uses multiple languages or encodes malicious instructions (e.g., using Base64 or emojis) to evade filters and manipulate the LLM's behavior.

### 参考情報リンク

1. [ChatGPT Plugin Vulnerabilities - Chat with Code](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/) **Embrace the Red**
2. [ChatGPT Cross Plugin Request Forgery and Prompt Injection](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) **Embrace the Red**
3. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Arxiv**
4. [Defending ChatGPT against Jailbreak Attack via Self-Reminder](https://www.researchsquare.com/article/rs-2873090/v1) **Research Square**
5. [Prompt Injection attack against LLM-integrated Applications](https://arxiv.org/abs/2306.05499) **Cornell University**
6. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf) **Kai Greshake**
8. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Cornell University**
9. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/) **AI Village**
10. [Reducing The Impact of Prompt Injection Attacks Through Design](https://research.kudelskisecurity.com/2023/05/25/reducing-the-impact-of-prompt-injection-attacks-through-design/) **Kudelski Security**
11. [Adversarial Machine Learning: A Taxonomy and Terminology of Attacks and Mitigations (nist.gov)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2023.pdf)
12. [2407.07403 A Survey of Attacks on Large Vision-Language Models: Resources, Advances, and Future Trends (arxiv.org)](https://arxiv.org/abs/2407.07403)
13. [Exploiting Programmatic Behavior of LLMs: Dual-Use Through Standard Security Attacks](https://ieeexplore.ieee.org/document/10579515)
14. [Universal and Transferable Adversarial Attacks on Aligned Language Models (arxiv.org)](https://arxiv.org/abs/2307.15043)
15. [From ChatGPT to ThreatGPT: Impact of Generative AI in Cybersecurity and Privacy (arxiv.org)](https://arxiv.org/abs/2307.00691)

### 関連するフレームワークと分類

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [AML.T0051.000 - LLM Prompt Injection: Direct](https://atlas.mitre.org/techniques/AML.T0051.000) **MITRE ATLAS**
- [AML.T0051.001 - LLM Prompt Injection: Indirect](https://atlas.mitre.org/techniques/AML.T0051.001) **MITRE ATLAS**
- [AML.T0054 - LLM Jailbreak Injection: Direct](https://atlas.mitre.org/techniques/AML.T0054) **MITRE ATLAS**
