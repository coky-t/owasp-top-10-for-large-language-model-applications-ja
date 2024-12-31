## LLM06:2025 過剰なエージェンシー (Excessive Agency)

### 説明

LLM ベースのシステムは開発者によってある程度のエージェンシーを付与されることがよくあります。拡張機能 (ベンダーによってツール、スキル、プラグインと呼ばれることもあります) を介して機能を呼び出したり、他のシステムとインタフェースして、プロンプトへのレスポンスとしてアクションを実行できます。どの拡張機能を呼び出すかの決定は、LLM 'agent' に委譲して、入力プロンプトや LLM 出力に基づいて動的に決定することもできます。エージェントベースのシステムは一般的に LLM を繰り返し呼び出し、以前の呼び出しからの出力を使用して後続の呼び出しの基盤として指示します。

過剰なエージェンシーは、LLM が誤動作する原因となっているものに関わらず、LLM からの予期しない出力、曖昧な出力、操作された出力へのレスポンスとして有害なアクションを実行できる脆弱性です。一般的なトリガーは以下のとおりです。
* 不十分な設計での無害なプロンプト、または単にパフォーマンスが悪いモデルによって引き起こされる幻覚/幻想。
* 悪意のあるユーザーからの直接/間接プロンプトインジェクション、悪意のある/侵害された拡張機能の早期呼び出し、(マルチエージェント/共同システムにおける) 悪意のある/侵害されたピアエージェント。

過剰なエージェンシーの根本原因は一般的に以下のひとつまたは複数です。
* 過剰な機能性
* 過剰な権限
* 過剰な自律性

過剰なエージェンシーは、機密性、完全性、可用性の各領域にわたって広範にわたる影響をもたらす可能性があり、LLM ベースのアプリがどのシステムとやり取りできるかに依存します。

注: 過剰なエージェンシーは、LLM 出力の不十分な精査を懸念する、不適切な出力処理とは異なります。

### リスクの一般的な例

#### 1. Excessive Functionality
  An LLM agent has access to extensions which include functions that are not needed for the intended operation of the system. For example, a developer needs to grant an LLM agent the ability to read documents from a repository, but the 3rd-party extension they choose to use also includes the ability to modify and delete documents.
#### 2. Excessive Functionality
  An extension may have been trialled during a development phase and dropped in favor of a better alternative, but the original plugin remains available to the LLM agent.
#### 3. Excessive Functionality
  An LLM plugin with open-ended functionality fails to properly filter the input instructions for commands outside what's necessary for the intended operation of the application. E.g., an extension to run one specific shell command fails to properly prevent other shell commands from being executed.
#### 4. Excessive Permissions
  An LLM extension has permissions on downstream systems that are not needed for the intended operation of the application. E.g., an extension intended to read data connects to a database server using an identity that not only has SELECT permissions, but also UPDATE, INSERT and DELETE permissions.
#### 5. Excessive Permissions
  An LLM extension that is designed to perform operations in the context of an individual user accesses downstream systems with a generic high-privileged identity. E.g., an extension to read the current user's document store connects to the document repository with a privileged account that has access to files belonging to all users.
#### 6. Excessive Autonomy
  An LLM-based application or extension fails to independently verify and approve high-impact actions. E.g., an extension that allows a user's documents to be deleted performs deletions without any confirmation from the user.

### 予防および緩和戦略

The following actions can prevent Excessive Agency:

#### 1. Minimize extensions
  Limit the extensions that LLM agents are allowed to call to only the minimum necessary. For example, if an LLM-based system does not require the ability to fetch the contents of a URL then such an extension should not be offered to the LLM agent.
#### 2. Minimize extension functionality
  Limit the functions that are implemented in LLM extensions to the minimum necessary. For example, an extension that accesses a user's mailbox to summarise emails may only require the ability to read emails, so the extension should not contain other functionality such as deleting or sending messages.
#### 3. Avoid open-ended extensions
  Avoid the use of open-ended extensions where possible (e.g., run a shell command, fetch a URL, etc.) and use extensions with more granular functionality. For example, an LLM-based app may need to write some output to a file. If this were implemented using an extension to run a shell function then the scope for undesirable actions is very large (any other shell command could be executed). A more secure alternative would be to build a specific file-writing extension that only implements that specific functionality.
#### 4. Minimize extension permissions
  Limit the permissions that LLM extensions are granted to other systems to the minimum necessary in order to limit the scope of undesirable actions. For example, an LLM agent that uses a product database in order to make purchase recommendations to a customer might only need read access to a 'products' table; it should not have access to other tables, nor the ability to insert, update or delete records. This should be enforced by applying appropriate database permissions for the identity that the LLM extension uses to connect to the database.
#### 5. Execute extensions in user's context
  Track user authorization and security scope to ensure actions taken on behalf of a user are executed on downstream systems in the context of that specific user, and with the minimum privileges necessary. For example, an LLM extension that reads a user's code repo should require the user to authenticate via OAuth and with the minimum scope required.
#### 6. Require user approval
  Utilise human-in-the-loop control to require a human to approve high-impact actions before they are taken. This may be implemented in a downstream system (outside the scope of the LLM application) or within the LLM extension itself. For example, an LLM-based app that creates and posts social media content on behalf of a user should include a user approval routine within the extension that implements the 'post' operation.
#### 7. Complete mediation
  Implement authorization in downstream systems rather than relying on an LLM to decide if an action is allowed or not. Enforce the complete mediation principle so that all requests made to downstream systems via extensions are validated against security policies.
#### 8. Sanitise LLM inputs and outputs
  Follow secure coding best practice, such as applying OWASP’s recommendations in ASVS (Application Security Verification Standard), with a particularly strong focus on input sanitisation. Use Static Application Security Testing (SAST) and Dynamic and Interactive application testing (DAST, IAST) in development pipelines.

The following options will not prevent Excessive Agency, but can limit the level of damage caused:

- Log and monitor the activity of LLM extensions and downstream systems to identify where undesirable actions are taking place, and respond accordingly.
- Implement rate-limiting to reduce the number of undesirable actions that can take place within a given time period, increasing the opportunity to discover undesirable actions through monitoring before significant damage can occur.

### 攻撃シナリオの例

An LLM-based personal assistant app is granted access to an individual’s mailbox via an extension in order to summarise the content of incoming emails. To achieve this functionality, the extension requires the ability to read messages, however the plugin that the system developer has chosen to use also contains functions for sending messages. Additionally, the app is vulnerable to an indirect prompt injection attack, whereby a maliciously-crafted incoming email tricks the LLM into commanding the agent to scan the user's inbox for senitive information and forward it to the attacker's email address. This could be avoided by:
* eliminating excessive functionality by using an extension that only implements mail-reading capabilities,
* eliminating excessive permissions by authenticating to the user's email service via an OAuth session with a read-only scope, and/or
* eliminating excessive autonomy by requiring the user to manually review and hit 'send' on every mail drafted by the LLM extension.

Alternatively, the damage caused could be reduced by implementing rate limiting on the mail-sending interface.

### 参考情報リンク

1. [Slack AI data exfil from private channels](https://promptarmor.substack.com/p/slack-ai-data-exfiltration-from-private): **PromptArmor**
2. [Rogue Agents: Stop AI From Misusing Your APIs](https://www.twilio.com/en-us/blog/rogue-ai-agents-secure-your-apis): **Twilio**
3. [Embrace the Red: Confused Deputy Problem](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
4. [NeMo-Guardrails: Interface guidelines](https://github.com/NVIDIA/NeMo-Guardrails/blob/main/docs/security/guidelines.md): **NVIDIA Github**
6. [Simon Willison: Dual LLM Pattern](https://simonwillison.net/2023/Apr/25/dual-llm-pattern/): **Simon Willison**
