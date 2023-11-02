## 安全でないプラグイン設計 (Insecure Plugin Design)

**説明**

LLM プラグインは、有効にすると、ユーザーインタラクション時にモデルによって自動的に呼び出される拡張機能です。プラグインはモデルによって駆動され、アプリケーションによる実行制御はありません。さらに、コンテキストサイズの制限に対処するために、プラグインはバリデーションや型チェックを行わずにモデルからフリーテキスト入力を可能性があります。これにより悪意のある攻撃者がプラグインに悪意のあるリクエストを作成できるようになり、リモートコード実行に至るまで、さまざまな望ましくない動作を引き起こす可能性があります。

悪意のある入力の危害はアクセス制御が不十分であったり、プラグイン間の認可を追跡できないことが原因であることがよくあります。アクセス制御が不適切であると、プラグイン型のプラグインを盲目的に信頼し、エンドユーザーが入力を提供したとみなす可能性があります。このような不適切なアクセス制御により悪意のある入力がデータ流出、リモートコード実行、権限昇格などの有害な結果をもたらす可能性があります。

この項目では LLM サプライチェーン脆弱性でカバーされるサードパーティプラグインの使用ではなく、LLM プラグインの作成に焦点を当てています。

**脆弱性の一般的な例:**

1. A plugin accepts all parameters in a single text field instead of distinct input parameters.
2. A plugin accepts configuration strings, instead of parameters, that can override entire configuration settings.
3. A plugin accepts raw SQL or programming statements instead of parameters.
4. Authentication is performed without explicit authorization to a particular plugin.
5. A plugin treats all LLM content as being created entirely by the user and performs any requested actions without requiring additional authorization.

**防止方法:**

1. Plugins should enforce strict parameterized input wherever possible and include type and range checks on inputs. When this is not possible, a second layer of typed calls should be introduced, parsing requests and applying validation and sanitisation. When freeform input must be accepted because of application semantics, it should be carefully inspected to ensure that no potentially harmful methods are being called.
2. Plugin developers should apply OWASP’s recommendations in ASVS  (Application Security Verification Standard) to ensure effective input validation and sanitisation.
3. Plugins should be inspected and tested thoroughly to ensure adequate validation. Use Static Application Security Testing (SAST) scans as well as Dynamic and Interactive application testing (DAST, IAST) in development pipelines. 
4. Plugins should be designed to minimise the impact of any insecure input parameter exploitation following the OWASP ASVS Access Control Guidelines. This includes least-privilege access control, exposing as little functionality as possible while still performing its desired function.
5. Plugins should use appropriate authentication identities, such as OAuth2, to apply effective authorization and access control. Additionally, API Keys should be used to provide context for custom authorisation decisions which reflect the plugin route rather than the default interactive user.
6. Require manual user authorisation and confirmation of any action taken by sensitive plugins.
7. Plugins are, typically, REST APIs, so developers should apply the recommendations found in OWASP Top 10 API Security Risks – 2023 to minimise generic vulnerabilities

**攻撃シナリオの例:**

1. A plugin accepts a base URL and instructs the LLM to combine the URL with a query to obtain weather forecasts which are included in handling the user request. A malicious user can craft a request such that the URL points to a domain they control, which allows them to inject their own content into the LLM system via their domain.

2. A plugin accepts a free-form input into a single field that it does not validate. An attacker  supplies carefully crafted payloads to perform reconnaissance from error messages. It then exploits known third-party vulnerabilities to execute code and perform data exfiltration or privilege escalation.

3. A plugin used to retrieve embeddings from a vector store accepts configuration parameters as a connection string without any validation. This allows an attacker to experiment and access other vector stores by changing names or host parameters and exfiltrate embeddings they should not have access to. 

4. A plugin accepts SQL WHERE clauses as advanced filters, which are then appended to the filtering SQL. This allows an attacker to stage a SQL attack.

5. An attacker uses indirect prompt injection to exploit an insecure code management plugin with no input validation and weak access control to transfer repository ownership and lock out the user from their repositories.

**参考情報リンク:**

1. [OpenAI ChatGPT Plugins](https://platform.openai.com/docs/plugins/introduction): ChatGPT Developer’s Guide
2. [OpenAI ChatGPT Plugins - Plugin Flow](https://platform.openai.com/docs/plugins/introduction/plugin-flow): A description of how the  plugin execution flows, and the requirement to have a user confirmation for all POST operations
3. [OpenAI ChatGPT Plugins - Authentication](https://platform.openai.com/docs/plugins/authentication/service-level): Description of Service and  User level authentication (including API Tokens and OAuth2) and the Unauthenticated mode.
4. [OpenAI Semantic Search Plugin Sample](https://github.com/openai/chatgpt-retrieval-plugin): A fully-featured solution for Semantic Search using embeddings and vector databases; it also demonstrates all authentication methods.
5. [Plugin Vulnerabilities: Visit a Website and Have Your Source Code Stolen](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/): Brief discussion of using a malicious plugin to perform unauthorized actions on GitHub repositories.
6. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) Walkthrough of a cross-plugin request forgery attack resulting from inadequate cross-plugin authorization.
7. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./)
8. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding)
9. [OWASP ASVS 4.1 General Access Control Design](https://owasp-aasvs4.readthedocs.io/en/latest/V4.1.html#general-access-control-design)
10. [OWASP Top 10 API Security Risks – 2023](https://owasp.org/API-Security/editions/2023/en/0x11-t10/)
