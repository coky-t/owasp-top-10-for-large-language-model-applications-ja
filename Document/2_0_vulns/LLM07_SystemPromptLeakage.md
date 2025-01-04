## LLM07:2025 システムプロンプトの漏洩 (System Prompt Leakage)

### 説明

LLM におけるシステムプロンプトの漏洩の脆弱性とは、モデルの動作を操縦するために使用されるシステムプロンプトや指示に、発見されることを意図していない機密情報も含む可能性があるリスクを指します。システムプロンプトは、アプリケーションの要件に基づいてモデルの出力をガイドするように設計されていますが、不注意にシークレットを含む可能性があります。発見された場合、この情報は他の攻撃を容易にするために使用される可能性があります。

システムプロンプトはシークレットとみなされるべきではなく、セキュリティコントロールとして使用されるべきでもないことを理解することが重要です。したがって、クレデンシャル、接続文字列などの機密データはシステムプロンプト言語内に含めるべきではありません。

同様に、システムプロンプトがさまざまなロールや権限を記述する情報や、接続文字列やパスワードなどの機密データを含む場合、そのような情報の開示は役に立つかもしれませんが、根本的なセキュリティリスクはこれらが開示されたことではなく、アプリケーションが LLM にそれらを委譲することで強力なセッション管理と認可チェックをバイパスし、機密データがあるべきではない場所に保存されてしまうことにあります。

つまり、システムプロンプト自体の開示は実際のリスクをもたらすのではなく、セキュリティリスクは、機密情報の開示、システムガードレールのバイパス、権限の不適切な分離など、その基盤となる要素にあります。たとえ正確な文言が開示されていなくても、システムとやり取りする攻撃者は、アプリケーションを使用し、モデルに発話を送信し、結果を観察する過程で、システムプロンプト言語に存在するガードレールとフォーマットの制限の多くをほぼ確実に判断できます。

### リスクの一般的な例

#### 1. Exposure of Sensitive Functionality
  The system prompt of the application may reveal sensitive information or functionality that is intended to be kept confidential, such as sensitive system architecture, API keys, database credentials, or user tokens.  These can be extracted or used by attackers to gain unauthorized access into the application. For example, a system prompt that contains the type of database used for a tool could allow the attacker to target it for SQL injection attacks.
#### 2. Exposure of Internal Rules
  The system prompt of the application reveals information on internal decision-making processes that should be kept confidential. This information allows attackers to gain insights into how the application works which could allow attackers to exploit weaknesses or bypass controls in the application. For example - There is a banking application that has a chatbot and its system prompt may reveal information like 
    >"The Transaction limit is set to $5000 per day for a user. The Total Loan Amount for a user is $10,000".
  This information allows the attackers to bypass the security controls in the application like doing transactions more than the set limit or bypassing the total loan amount.
#### 3. Revealing of Filtering Criteria
  A system prompt might ask the model to filter or reject sensitive content. For example, a model might have a system prompt like,
    >“If a user requests information about another user, always respond with ‘Sorry, I cannot assist with that request’”.
#### 4. Disclosure of Permissions and User Roles
  The system prompt could reveal the internal role structures or permission levels of the application. For instance, a system prompt might reveal,
    >“Admin user role grants full access to modify user records.”
  If the attackers learn about these role-based permissions, they could look for a privilege escalation attack.

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
