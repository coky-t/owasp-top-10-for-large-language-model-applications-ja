## 過剰なエージェンシー (Excessive Agency)

**説明:**

LLM ベースのシステムは開発者によってある程度のエージェンシー、他のシステムとのインタフェースやプロンプトに応答してアクションを実行する機能、が与えられることがよくあります。どの機能を呼び出すかの判断は、入力プロンプトまたは LLM 出力に基づいて動的に決定する LLM 「エージェント」に委譲されることもあります。

過剰なエージェンシーは LLM からの予期しない/あいまいな出力に応答して有害なアクションを実行できる脆弱性です (LLM が誤動作する原因が何であるかに関係なく、ハルシネーション/作話、直接的/間接的プロンプトインジェクション、悪意のあるプラグイン、不十分な設計の無害なプロンプト、単にパフォーマンスが悪いモデルなど) 。過剰なエージェンシーの根本原因は一般的に過剰な機能、過剰な権限、過剰な自律性のうちの一つ以上です。

過剰なエージェンシーは機密性、完全性、可用性の領域にわたる広範囲の影響につながる可能性があり、LLM ベースのアプリがどのシステムとやり取りできるかに依存します。

**脆弱性の一般的な例:**

1. Excessive Functionality: An LLM agent has access to plugins which include functions that are not needed for the intended operation of the system. For example, a developer needs to grant an LLM agent the ability to read documents from a repository, but the 3rd-party plugin they choose to use also includes the ability to modify and delete documents. Alternatively, a plugin may have been trialled during a development phase and dropped in favour of a better alternative, but the original plugin remains available to the LLM agent.
2. Excessive Functionality: An LLM plugin with open-ended functionality fails to properly filter the input instructions for commands outside what's necessary for the intended operation of the application. E.g., a plugin to run one specific shell command fails to properly prevent other shell commands from being executed.
3. Excessive Permissions: An LLM plugin has permissions on other systems that are not needed for the intended operation of the application. E.g., a plugin intended to read data connects to a database server using an identity that not only has SELECT permissions, but also UPDATE, INSERT and DELETE permissions.
4. Excessive Permissions: An LLM plugin that is designed to perform operations on behalf of a user accesses downstream systems with a generic high-privileged identity. E.g., a plugin to read the current user's document store connects to the document repository with a privileged account that has access to all users' files.
5. Excessive Autonomy: An LLM-based application or plugin fails to independently verify and approve high-impact actions. E.g., a plugin that allows a user's documents to be deleted performs deletions without any confirmation from the user. 

**防止方法:**

The following actions can prevent Excessive Agency:

1. Limit the plugins/tools that LLM agents are allowed to call to only the minimum functions necessary. For example, if an LLM-based system does not require the ability to fetch the contents of a URL then such a plugin should not be offered to the LLM agent.
2. Limit the functions that are implemented in LLM plugins/tools to the minimum necessary. For example, a plugin that accesses a user's mailbox to summarise emails may only require the ability to read emails, so the plugin should not contain other functionality such as deleting or sending messages.
3. Avoid open-ended functions where possible (e.g., run a shell command, fetch a URL, etc) and use plugins/tools with more granular functionality. For example, an LLM-based app may need to write some output to a file. If this were implemented using a plugin to run a shell function then the scope for undesirable actions is very large (any other shell command could be executed). A more secure alternative would be to build a file-writing plugin that could only support that specific functionality.
4. Limit the permissions that LLM plugins/tools are granted to other systems the minimum necessary in order to limit the scope of undesirable actions. For example, an LLM agent that uses a product database in order to make purchase recommendations to a customer might only need read access to a 'products' table; it should not have access to other tables, nor the ability to insert, update or delete records. This should be enforced by applying appropriate database permissions for the identity that the LLM plugin uses to connect to the database.
5. Track user authorization and security scope to ensure actions taken on behalf of a user are executed on downstream systems in the context of that specific user, and with the minimum privileges necessary. For example, an LLM plugin that reads a user's code repo should require the user to authenticate via OAuth and with the minimum scope required.
6. Utilise human-in-the-loop control to require a human to approve all actions before they are taken. This may be implemented in a downstream system (outside the scope of the LLM application) or within the LLM plugin/tool itself. For example, an LLM-based app that creates and posts social media content on behalf of a user should include a user approval routine within the plugin/tool/API that implements the 'post' operation.
7. Implement authorization in downstream systems rather than relying on an LLM to decide if an action is allowed or not. When implementing tools/plugins enforce the complete mediation principle so that all requests made to downstream systems via the plugins/tools are validated against security policies.

The following options will not prevent Excessive Agency, but can limit the level of damage caused:

1. Log and monitor the activity of LLM plugins/tools and downstream systems to identify where undesirable actions are taking place, and respond accordingly.
2. Implement rate-limiting to reduce the number of undesirable actions that can take place within a given time period, increasing the opportunity to discover undesirable actions through monitoring before significant damage can occur.

**攻撃シナリオの例:**

An LLM-based personal assistant app is granted access to an individual’s mailbox via a plugin in order to summarise the content of incoming emails. To achieve this functionality, the email plugin requires the ability to read messages, however the plugin that the system developer has chosen to use also contains functions for sending messages. The LLM is vulnerable to an indirect prompt injection attack, whereby a maliciously-crafted incoming email tricks the LLM into commanding the email plugin to call the 'send message' function to send spam from the user's mailbox. This could be avoided by:
(a) eliminating excessive functionality by using a plugin that only offered mail-reading capabilities,
(b) eliminating excessive permissions by authenticating to the user's email service via an OAuth session with a read-only scope, and/or
(c) eliminating excessive autonomy by requiring the user to manually review and hit 'send' on every mail drafted by the LLM plugin.
Alternatively, the damage caused could be reduced by implementing rate limiting on the mail-sending interface.

**参考情報リンク:**

1. [Embrace the Red: Confused Deputy Problem](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): In this blog post, wunderwuzzi describes a PoC exploit of triggering a variety of undesirable actions (in this case through prompt injection).
2. [NeMo-Guardrails: Interface guidelines](https://github.com/NVIDIA/NeMo-Guardrails/blob/main/docs/security/guidelines.md): This document from the NeMo-Guardrails project sets out guidelines and principles for providing LLMs access to external data and compute resources in a safe and secure way.
3. [LangChain: Human-approval for tools](https://python.langchain.com/docs/modules/agents/tools/how_to/human_approval): An example of adding a human-in-the-loop verification step into a LangChain tool.
4. [Simon Willison: Dual LLM Pattern](https://simonwillison.net/2023/Apr/25/dual-llm-pattern/): In this blog post, Simon describes a novel approach to limiting what actions an LLM can perform by utilising a Dual-LLM approach.
