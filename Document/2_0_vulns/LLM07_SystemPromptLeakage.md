## LLM07:2025 システムプロンプトの漏洩 (System Prompt Leakage)

### 説明

LLM におけるシステムプロンプトの漏洩の脆弱性とは、モデルの動作を操縦するために使用されるシステムプロンプトや指示に、発見されることを意図していない機密情報も含む可能性があるリスクを指します。システムプロンプトは、アプリケーションの要件に基づいてモデルの出力をガイドするように設計されていますが、不注意にシークレットを含む可能性があります。発見された場合、この情報は他の攻撃を容易にするために使用される可能性があります。

システムプロンプトはシークレットとみなされるべきではなく、セキュリティコントロールとして使用されるべきでもないことを理解することが重要です。したがって、クレデンシャル、接続文字列などの機密データはシステムプロンプト言語内に含めるべきではありません。

同様に、システムプロンプトがさまざまなロールや権限を記述する情報や、接続文字列やパスワードなどの機密データを含む場合、そのような情報の開示は役に立つかもしれませんが、根本的なセキュリティリスクはこれらが開示されたことではなく、アプリケーションが LLM にそれらを委譲することで強力なセッション管理と認可チェックをバイパスし、機密データがあるべきではない場所に保存されてしまうことにあります。

つまり、システムプロンプト自体の開示は実際のリスクをもたらすのではなく、セキュリティリスクは、機密情報の開示、システムガードレールのバイパス、権限の不適切な分離など、その基盤となる要素にあります。たとえ正確な文言が開示されていなくても、システムとやり取りする攻撃者は、アプリケーションを使用し、モデルに発話を送信し、結果を観察する過程で、システムプロンプト言語に存在するガードレールとフォーマットの制限の多くをほぼ確実に判断できます。

### リスクの一般的な例

#### 1. 機密機能の露出
  アプリケーションのシステムプロンプトは、機密性の高いシステムアーキテクチャ、API キー、データベースクレデンシャル、ユーザートークンなど、機密情報や機能を明らかにするかもしれません。これらは、攻撃者がアプリケーションに不正アクセスを行うために抽出されたり使用される可能性があります。たとえば、ツールに使用されるデータベースのタイプを含むシステムプロンプトは、攻撃者がそれを SQL インジェクション攻撃のターゲットにする可能性があります。
#### 2. 内部ルールの露出
  アプリケーションのシステムプロンプトは、機密にしておくべき内部意思決定プロセスの情報を明らかにします。この情報により、攻撃者がアプリケーションの動作方法の洞察を得て、攻撃者がアプリケーションの弱点を悪用したりコントロールをバイパスできます。たとえば、チャットボットを備えた銀行アプリケーションがあり、そのシステムプロンプトから以下のような情報を明らかにすることがあります。
    > 「ユーザーの取引限度額は一日あたり 5000 ドルに設定されています。ユーザーの総融資額は 10000 ドルです。」
  この情報により、攻撃者が設定された限度額を取引を実行したり、総融資額をバイパスするなど、アプリケーションのセキュリティコントロールをバイパスできます。
#### 3. フィルタリング基準の露呈
  システムプロンプトは機密コンテンツをフィルタしたり拒否することをモデルに要求するかもしれません。たとえば、モデルには以下のようなシステムプロンプトがあるかもしれません。
    > 「ユーザーが別のユーザーに関する情報を供給した場合、常に『申し訳ありませんが、そのリクエストには対応できません』と応答します。」
#### 4. 権限とユーザーロールの開示
  システムプロンプトはアプリケーションの内部ロール構造や権限レベルを明らかにする可能性があります。たとえば、システムプロンプトは以下のことを明らかにするかもしれません。
    > 「管理ユーザーロールはユーザーレコードを変更するためのフルアクセスを許可します。」
  攻撃者がこれらのロールベースの権限について知ると、権限昇格攻撃を招く可能性があります。

### 予防および緩和戦略

#### 1. Separate Sensitive Data from System Prompts
  Avoid embedding any sensitive information (e.g. API keys, auth keys, database names, user roles, permission structure of the application) directly in the system prompts. Instead, externalize such information to the systems that the model does not directly access.
#### 2. Avoid Reliance on System Prompts for Strict Behavior Control
  Since LLMs are susceptible to other attacks like prompt injections which can alter the system prompt, it is recommended to avoid using system prompts to control the model behavior where possible.  Instead, rely on systems outside of the LLM to ensure this behavior.  For example, detecting and preventing harmful content should be done in external systems.
#### 3. Implement Guardrails
  Implement a system of guardrails outside of the LLM itself.  While training particular behavior into a model can be effective, such as training it not to reveal its system prompt, it is not a guarantee that the model will always adhere to this.  An independent system that can inspect the output to determine if the model is in compliance with expectations is preferable to system prompt instructions.
#### 4. Ensure that security controls are enforced independently from the LLM
  Critical controls such as privilege separation, authorization bounds checks, and similar must not be delegated to the LLM, either through the system prompt or otherwise. These controls need to occur in a deterministic, auditable manner, and LLMs are not (currently) conducive to this. In cases where an agent is performing tasks, if those tasks require different levels of access, then multiple agents should be used, each configured with the least privileges needed to perform the desired tasks.

### 攻撃シナリオの例

#### Scenario #1
   An LLM has a system prompt that contains a set of credentials used for a tool that it has been given access to.  The system prompt is leaked to an attacker, who then is able to use these credentials for other purposes.
#### Scenario #2
  An LLM has a system prompt prohibiting the generation of offensive content, external links, and code execution. An attacker extracts this system prompt and then uses a prompt injection attack to bypass these instructions, facilitating a remote code execution attack.

### 参考情報リンク

1. [SYSTEM PROMPT LEAK](https://x.com/elder_plinius/status/1801393358964994062): Pliny the prompter
2. [Prompt Leak](https://www.prompt.security/vulnerabilities/prompt-leak): Prompt Security
3. [chatgpt_system_prompt](https://github.com/LouisShark/chatgpt_system_prompt): LouisShark
4. [leaked-system-prompts](https://github.com/jujumilk3/leaked-system-prompts): Jujumilk3
5. [OpenAI Advanced Voice Mode System Prompt](https://x.com/Green_terminals/status/1839141326329360579): Green_Terminals

### 関連するフレームワークと分類

Refer to this section for comprehensive information, scenarios strategies relating to infrastructure deployment, applied environment controls and other best practices.

- [AML.T0051.000 - LLM Prompt Injection: Direct (Meta Prompt Extraction)](https://atlas.mitre.org/techniques/AML.T0051.000) **MITRE ATLAS**
