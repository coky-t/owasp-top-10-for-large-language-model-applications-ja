## プロンプトインジェクション (Prompt Injection)

**説明:**

プロンプトインジェクション脆弱性は攻撃者が細工した入力を通じて大規模言語モデル (LLM) を操作し、LLM が攻撃者の意図を知らずに実行する場合に発生します。これはシステムプロンプトを「脱獄」することによって直接的に行われることもあれば、操作された外部入力によって間接的に行われることもあり、データ流出、ソーシャルエンジニアリング、その他の問題につながる可能性があります。

* **直接プロンプトインジェクション** は「脱獄」としても知られ、悪意のあるユーザーが基礎となる *システム* プロンプトを上書きしたり漏洩した場合に発生します。これにより攻撃者は LLM を通じてアクセスできる安全でない関数やデータストアとやり取りすることでバックエンドシステムを悪用できる可能性があります。
* **間接プロンプトインジェクション** はウェブサイトやファイルなど、攻撃者が制御できる外部ソースから入力を受け入れる場合に発生します。攻撃者は外部コンテンツにプロンプトインジェクションを埋め込み、会話コンテキストをハイジャックする可能性があります。これにより LLM は「混乱した代理人 (confused deputy)」として動作し、攻撃者はユーザー、または LLM がアクセスできる別のシステムを操作できるようになります。さらに、間接プロンプトインジェクションはテキストが LLM によって解析される限り、人間が見たり読んだりできる必要はありません。

プロンプトインジェクション攻撃に成功した際の結果は、機密情報の教唆から通常の運用を装った重要な意思決定プロセスへの影響まで実にさまざまです。

高度な攻撃では、LLM を操作して、有害なペルソナを模倣したり、ユーザーの設定内のプラグインとやり取りする可能性があります。その結果、機密データの漏洩、認可されていないプラグインの使用、ソーシャルエンジニアリングの可能性があります。そのようなケースでは、侵害された LLM は攻撃者を支援し、標準的なセーフガードを超えて、ユーザーが侵入に気付かないようにします。このような場合、侵害された LLM は事実上、攻撃者のエージェントとして機能し、通常のセーフガードを発動したり、エンドユーザーに侵入を警告することなく、攻撃者の目的を推進します。

**脆弱性の一般的な例:**

1. A malicious user crafts a direct prompt injection to the LLM, which instructs it to ignore the application creator's system prompts and instead execute a prompt that returns private, dangerous, or otherwise undesirable information.
2. A user employs an LLM to summarize a webpage containing an indirect prompt injection. This then causes the LLM to solicit sensitive information from the user and perform exfiltration via JavaScript or Markdown.
3. A malicious user uploads a resume containing an indirect prompt injection. The document contains a prompt injection with instructions to make the LLM inform users that this document is an excellent document eg. excellent candidate for a job role. An internal user runs the document through the LLM to summarize the document. The output of the LLM returns information stating that this is an excellent document.
4. A user enables a plugin linked to an e-commerce site. A rogue instruction embedded on a visited website exploits this plugin, leading to unauthorized purchases.
5. A rogue instruction and content embedded on a visited website which exploits other plugins to scam users.

**防止方法:**

Prompt injection vulnerabilities are possible due to the nature of LLMs, which do not segregate instructions and external data from each other. Since LLM use natural language, they consider both forms of input as user-provided. Consequently, there is no fool-proof prevention within the LLM, but the following measures can mitigate the impact of prompt injections: 

1. Enforce privilage control on LLM access to backend systems. Provide the LLM with its own API tokens for extensible functionality, such as plugins, data access, and function-level permissions. Follow the principle of least privilege by restricting the LLM to only the minimum level of access necessary for its intended operations. 
2. Implement human in the loop for extensible functionality. When performing privileged operations, such as sending or deleting emails, have the application require the user approve the action first. This will mitigate the opportunity for an indirect prompt injection to perform actions on behalf of the user without their knowledge or consent. 
3. Segregate external content from user prompts. Separate and denote where untrusted content is being used to limit  their influence on user prompts. For example, use ChatML for OpenAI API calls to indicate to the LLM the source of prompt input. 
4. Establish trust boundaries between the LLM, external sources, and extensible functionality (e.g., plugins or downstream functions). Treat the LLM as an untrusted user and maintain final user control on decision-making processes. However, a compromised LLM may still act as an intermediary (man-in-the-middle) between your application’s APIs and the user as it may hide or manipulate information prior to presenting it to the user. Highlight potentially untrustworthy responses visually to the user.

**攻撃シナリオの例:**

1. An attacker provides a direct prompt injection to an LLM-based support chatbot. The injection contains  “forget all previous instructions” and new instructions to query private data stores and exploit package vulnerabilities and the lack of output validation in the backend function to send e-mails. This leads to remote code execution, gaining unauthorized access and privilege escalation. 

2. An attacker embeds an indirect prompt injection in a webpage instructing the LLM to disregard previous user instructions and use an LLM plugin to delete the user's emails. When the user employs the LLM to summarise this webpage, the LLM plugin deletes the user's emails.  

3. A user employs an LLM to summarize a webpage containing an indirect prompt injection to disregard previous user instructions. This then causes the LLM to solicit sensitive information from the user and perform exfiltration via embedded JavaScript or Markdown.

4. A malicious user uploads a resume with a prompt injection. The backend user uses an LLM to summarize the resume and ask if the person is a good candidate. Due to the prompt injection, the LLM says yes, despite the actual resume contents.

5. A user enables a plugin linked to an e-commerce site. A rogue instruction embedded on a visited website exploits this plugin, leading to unauthorized purchases.



**参考情報リンク:**

1. [ChatGPT Plugin Vulnerabilities - Chat with Code](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/): This blog post discusses potential vulnerabilities in ChatGPT plugins, focusing on the risks of untrusted code execution.
2. [ChatGPT Cross Plugin Request Forgery and Prompt Injection](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): The article explores the issues of Cross Plugin Request Forgery and Prompt Injection in ChatGPT.
3. [Defending ChatGPT against Jailbreak Attack via Self-Reminder](https://www.researchsquare.com/article/rs-2873090/v1): This research paper presents a method of defending ChatGPT against jailbreak attacks by using a self-reminder mechanism.
4. [Prompt Injection attack against LLM-integrated Applications](https://arxiv.org/abs/2306.05499): This academic paper discusses the threat of prompt injection attacks on LLM integrated applications, emphasizing the need for effective countermeasures.
5. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf/): The blog post describes a method of injecting prompts into PDFs, specifically focusing on resumes, and discusses the potential implications of this technique.
6. [ChatML for OpenAI API Calls](https://github.com/openai/openai-python/blob/main/chatml.md): This page provides a detailed guide on using ChatML for making OpenAI API calls.
7. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf): This academic paper discusses the risks of indirect prompt injection in real-world applications integrated with LLMs.
8. [Threat Modeling LLM Applications](http://aivillage.org/large%20language%20models/threat-modeling-llm/): This webpage presents a comprehensive guide on threat modeling for LLM applications, emphasizing the importance of understanding potential threats and implementing effective defenses.
9. [AI Injections: Direct and Indirect Prompt Injections and Their Implications](https://embracethered.com/blog/posts/2023/ai-injections-direct-and-indirect-prompt-injection-basics/): This blog post provides an in-depth look at both direct and indirect prompt injections in AI, discussing their implications and potential risks.
10. [Reducing The Impact of Prompt Injection Attacks Through Design](https://research.kudelskisecurity.com/2023/05/25/reducing-the-impact-of-prompt-injection-attacks-through-design/): This research article discusses strategies for reducing the impact of prompt injection attacks through thoughtful design.
11. [Universal and Transferable Attacks on Aligned Language Models](https://llm-attacks.org/): This research presents automatic methods of constructing prompt injections against open source LLMs.
