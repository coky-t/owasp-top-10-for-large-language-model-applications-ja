## LLM01: プロンプトインジェクション (Prompt Injection)

### 説明

プロンプトインジェクション脆弱性は攻撃者が細工した入力を通じて大規模言語モデル (LLM) を操作し、LLM が攻撃者の意図を知らずに実行する場合に発生します。これはシステムプロンプトを「脱獄」することによって直接的に行われることもあれば、操作された外部入力によって間接的に行われることもあり、データ流出、ソーシャルエンジニアリング、その他の問題につながる可能性があります。

* **直接プロンプトインジェクション** は「脱獄」としても知られ、悪意のあるユーザーが基礎となる *システム* プロンプトを上書きしたり漏洩した場合に発生します。これにより攻撃者は LLM を通じてアクセスできる安全でない関数やデータストアとやり取りすることでバックエンドシステムを悪用できる可能性があります。
* **間接プロンプトインジェクション** はウェブサイトやファイルなど、攻撃者が制御できる外部ソースから入力を受け入れる場合に発生します。攻撃者は外部コンテンツにプロンプトインジェクションを埋め込み、会話コンテキストをハイジャックする可能性があります。これにより LLM 出力ステアリングの安定しなくなり、攻撃者はユーザー、または LLM がアクセスできる別のシステムを操作できるようになります。さらに、間接プロンプトインジェクションはテキストが LLM によって解析される限り、人間が見たり読んだりできる必要はありません。

プロンプトインジェクション攻撃に成功した際の結果は、機密情報の教唆から通常の運用を装った重要な意思決定プロセスへの影響まで実にさまざまです。

高度な攻撃では、LLM を操作して、有害なペルソナを模倣したり、ユーザーの設定内のプラグインとやり取りする可能性があります。その結果、機密データの漏洩、認可されていないプラグインの使用、ソーシャルエンジニアリングの可能性があります。そのようなケースでは、侵害された LLM は攻撃者を支援し、標準的なセーフガードを超えて、ユーザーが侵入に気付かないようにします。このような場合、侵害された LLM は事実上、攻撃者のエージェントとして機能し、通常のセーフガードを発動したり、エンドユーザーに侵入を警告することなく、攻撃者の目的を推進します。

### 脆弱性の一般的な例

1. 悪意のあるユーザーは LLM に直接プロンプトインジェクションを作成し、アプリケーション作成者のシステムプロンプトを無視し、代わりに個人情報、危険情報、その他の望ましくない情報を返すプロンプトを実行するように LLM に指示します。
2. ユーザーは LLM を使用して、間接プロンプトインジェクションを含めてウェブページを要約します。これにより LLM はユーザーから機密情報を要求し、JavaScript やマークダウンを介して抽出を実行します。
3. 悪意のあるユーザーは間接プロンプトインジェクションを含めて履歴書をアップロードします。このドキュメントには、職務に適した優秀な候補者など、このドキュメントが優れていることを LLM がユーザーに知らせる指示でのプロンプトインジェクションを含んでいます。内部ユーザーは LLM を通じてドキュメントを実行し、ドキュメントを要約します。LLM の出力にはこれが優れたドキュメントであることを示す情報を返します。
4. ユーザーは e コマースサイトにリンクしたプラグインを有効にします。訪問したウェブサイトに埋め込まれた不正な命令がこのプラグインを悪用し、認可されていない購入につながります。
5. 不正な命令やコンテンツが訪問したウェブサイトに埋め込まれ、他のプラグインを悪用してユーザーを詐欺します。

### 予防および緩和戦略

プロンプトインジェクションの脆弱性は、命令と外部データを互いに分離しない LLM の性質に起因しています。LLM は自然言語を使用するため、両方の形式の入力をユーザーが提供したものとみなします。そのため、LLM の内部で完全に防ぐことはできませんが、以下の対策によりプロンプトインジェクションの影響を軽減できます。

1. バックエンドシステムへの LLM アクセスに権限制御を適用します。プラグイン、データアクセス、機能レベルの権限など拡張可能な機能のために、LLM に独自の API トークンを提供します。最小権限の原則に従って、LLM をその意図された操作に必要な最小限のアクセスレベルのみに制限します。
2. 拡張機能のためにループ内に人間を追加します。電子メールの送信や削除のような特権的な操作を実行する際、最初にユーザーがアクションを承認することをアプリケーションに要求します。これにより間接プロンプトインジェクションがユーザーの知らないうちに、またはユーザーの同意なしに、ユーザーの代わりに認可されていないアクションを実行する機会を軽減します。
3. 外部コンテンツをユーザープロンプトから分離します。信頼できないコンテンツが使用されている場所を分離して示すことで、ユーザープロンプトへの影響を制限します。たとえば、OpenAI API 呼び出しに ChatML を使用して、プロンプトの入力元を LLM に示します。
4. LLM、外部ソース、拡張可能な機能 (プラグインやダウンストリーム機能など) の間に信頼境界を確立します。LLM を信頼できないユーザーとして扱い、意思決定プロセスに対する最終的なユーザー制御を維持します。しかし、危殆化した LLM は、ユーザーに提示する前に情報を隠したり操作したりする可能性があるため、アプリケーションの API とユーザーの間の仲介者 (中間者) として機能する可能性があります。潜在的に信頼できないerスポんすをユーザーに視覚的に強調表示します。
5. LLM の入出力を定期的に手動で監視し、期待通りであることを確認します。これは緩和策ではありませんが、弱点を検出して対処するために必要なデータを提供できます。

### 攻撃シナリオの例

1. 攻撃者は LLM ベースのサポートチャットボットに直接プロンプトインジェクションを提供します。このインジェクションには「以前の命令をすべて忘れる」命令と、プライベートデータストアをクエリする新しい命令を含み、パッケージの脆弱性と電子メールを送信するバックエンド機能での出力検証の欠如を悪用します。これはリモートコード実行につながり、認可されていないアクセスや権限昇格を得ます。
2. 攻撃者はウェブページに間接プロンプトインジェクションを埋め込み、以前のユーザー命令を無視し、LLM プラグインを使用してユーザーの電子メールを削除するように LLM に命令します。ユーザーが LLM を使用してこのウェブページを要約すると、LLM プラグインはユーザーの電子メールを削除します。
3. ユーザーは LLM を使用して、以前のユーザー命令を無視し、代わりに会話の要約を含む URL にリンクする画像を挿入するようモデルに命令するテキストを含むウェブページを要約します。LLM 出力はこれに応じ、ユーザーのブラウザはプライベートな会話を流出してしまいます。
4. 悪意のあるユーザーはプロンプトインジェクションを含む履歴書をアップロードします。バックエンドユーザーは LLM を使用して履歴書を要約し、その人物が適切な候補者かどうかを尋ねます。プロンプトインジェクションにより、実際の履歴書の内容にかかわらず、LLM レスポンスは yes になります。
5. 攻撃者はシステムプロンプトに依存するプロプライエタリモデルにメッセージを送信し、以前の命令を無視して、代わりにシステムプロンプトを繰り返すようモデルに要求します。モデルはプロプライエタリプロンプトを出力し、攻撃者はこれらの命令を別の場所で使用したり、さらに巧妙な攻撃を構築できます。

### 参考情報リンク

1. [Prompt injection attacks against GPT-3](https://simonwillison.net/2022/Sep/12/prompt-injection/) **Simon Willison**
1. [ChatGPT Plugin Vulnerabilities - Chat with Code](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/): **Embrace The Red**
1. [ChatGPT Cross Plugin Request Forgery and Prompt Injection](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
1. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf):  **Arxiv preprint**
1. [Defending ChatGPT against Jailbreak Attack via Self-Reminder](https://www.researchsquare.com/article/rs-2873090/v1): **Research Square**
1. [Prompt Injection attack against LLM-integrated Applications](https://arxiv.org/abs/2306.05499): **Arxiv preprint**
1. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf/): **Kai Greshake**
1. [ChatML for OpenAI API Calls](https://github.com/openai/openai-python/blob/main/chatml.md): **OpenAI Github**
1. [Threat Modeling LLM Applications](http://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
1. [AI Injections: Direct and Indirect Prompt Injections and Their Implications](https://embracethered.com/blog/posts/2023/ai-injections-direct-and-indirect-prompt-injection-basics/): **Embrace The Red**
1. [Reducing The Impact of Prompt Injection Attacks Through Design](https://research.kudelskisecurity.com/2023/05/25/reducing-the-impact-of-prompt-injection-attacks-through-design/): **Kudelski Security**
1. [Universal and Transferable Attacks on Aligned Language Models](https://llm-attacks.org/): **LLM-Attacks.org**
1. [Indirect prompt injection](https://kai-greshake.de/posts/llm-malware/): **Kai Greshake**
1. [Declassifying the Responsible Disclosure of the Prompt Injection Attack Vulnerability of GPT-3](https://www.preamble.com/prompt-injection-a-critical-vulnerability-in-the-gpt-3-transformer-and-how-we-can-begin-to-solve-it): **Preamble; earliest disclosure of Prompt Injection**
