## LLM02: 安全でない出力処理 (Insecure Output Handling)

### 説明
安全でない出力処理は、大規模言語モデルによって生成された出力をダウンストリームのコンポーネントやシステムに渡す前に、特にその出力を十分にバリデーション、サニタイゼーション、ハンドリングしないことを指します。LLM が生成するコンテンツはプロンプト入力によって制御できるため、この動作はユーザーに追加機能への間接的なアクセスを提供するのと同様です。
安全でない出力処理は LLM で生成された出力をダウンストリームに渡す前に処理するという点で過度の依存とは異なります。過度の依存では LLM 出力の正確さと適切さへの過剰な依存に関するより広範な懸念に焦点を当てています。
安全でない出力処理の悪用に成功すると、ウェブブラウザでの XSS や CSRF だけでなく、バックエンドシステムでの SSRF、権限昇格、リモートコード実行につながる可能性があります。
以下の条件によりこの脆弱性の影響が増大する可能性があります。
- アプリケーションはエンドユーザーが意図する権限を超えたものを LLM に付与し、権限昇格やリモートコード実行を可能にしています。
- アプリケーションは間接プロンプトインジェクション攻撃に対して脆弱であり、攻撃者がターゲットユーザーの環境への特権アクセスを取得できる可能性があります。
- サードパーティエクステンションは入力を適切に検証していません。
- さまざまなコンテキスト (HTML, JavaScript, SQL など) に対する適切な出力エンコーディングの欠如
- LLM 出力の不十分な監視とログ記録
- LLM 使用時のレート制限や異常検出の欠如
### 脆弱性の一般的な例
- LLM 出力はシステムシェルまたは exec や eval などの同様の関数に直接入力され、リモートコード実行につながります。
- JavaScript やマークダウンは LLM によって生成され、ユーザーに返されます。コードはブラウザによって解釈され、XSS につながります。
- LLM が生成した SQL クエリを適切なパラメータ化なしで実行すると、SQL インジェクションにつながります。
- LLM 出力を使用して、適切なサニタイゼーションなしでファイルパスを構築すると、パストラバーサル脆弱性を引き起こす可能性があります。
- LLM が生成したコンテンツを適切なエスケープなしで電子メールテンプレートに使用すると、フィッシング攻撃につながる可能性があります。

### 予防および緩和戦略
- モデルを他のユーザーとして扱い、ゼロトラストアプローチを採用して、モデルからバックエンド機能へのレスポンスに適切な入力バリデーションを適用します。
- OWASP ASVS (Application Security Verification Standard) ガイドラインに従い、効果的な入力バリデーションとサニタイゼーションを確保します。
- モデル出力をエンコードしてユーザーに返し、JavaScript やマークダウンによる望ましくないコード実行を軽減します。OWASP ASVS は出力エンコーディングに関する詳細なガイダンスを提供します。
- LLM 出力が使用される場所に基づいて、コンテキストを意識した出力エンコーディングを実装します (ウェブコンテンツに対する HTML エンコーディング、データベースクエリに対する SQL エスケープなど)。
- LLM 出力を含むすべてのデータベース操作に対してパラメータ化クエリまたはプリペアドステートメントを使用します。
- 厳格なコンテンツセキュリティポリシー (CSP) を採用して、LLM が生成したコンテンツからの XSS 攻撃のリスクを軽減します。
- 堅牢なログ記録と監視システムを導入して、悪用の試みを示す可能性がある LLM 出力の異常なパターンを検出します。

### 攻撃シナリオの例
1. アプリケーションは LLM エクステンションを使用して、チャットボット機能のレスポンスを生成します。このエクステンションは別の特権 LLM にアクセスできる多くの管理機能を提供します。汎用 LLM は適切な出力バリデーションを行わずにそのレスポンスをエクステンションに直接渡し、エクステンションはメンテナンスのためにシャットダウンしてしまいます。
2. ユーザーは LLM を搭載したウェブサイト要約ツールを利用して、記事の簡潔な要約を生成します。このウェブサイトにはウェブサイトやユーザーの会話から機密コンテンツをキャプチャするよう LLM に指示するプロンプトインジェクションを含みます。そこから LLM は機密データをエンコードし、出力バリデーションやフィルタリングを行わずに、攻撃者が制御するサーバーに送信できます。
3. LLM ではユーザーはチャットのような機能を通じてバックエンドデータベースに対する SQL クエリを作成できます。ユーザーはすべてのデータベーステーブルを削除するクエリをリクエストします。LLM から作成されたクエリが精査されない場合、すべてのデータベーステーブルが削除されてしまいます。
4. あるウェブアプリは LLM を使用して、出力バリデーションを行わずにユーザーテキストプロンプトからコンテンツを生成します。攻撃者は細工したプロンプトを送信すると、LLM がサニタイズされていない JavaScript ペイロードを返し、被害者のブラウザで表示される際に XSS を引き起こす可能性があります。プロンプトのバリデーションが不十分なため、この攻撃が可能になりました。
5. LLM を使用して、マーケティングキャンペーン用の動的な電子メールテンプレートを生成します。攻撃者は LLM を操作して、電子メールコンテンツ内に悪意のある JavaScript を含めます。アプリケーションが LLM 出力を適切にサニタイズしない場合、脆弱な電子メールクライアントで電子メールを閲覧する受信者に対する XSS 攻撃につながる可能性があります。
6: LLM を使用して、ソフトウェア企業で自然言語入力からコードを生成し、開発タスクの効率化を目指します。効率的ではありますが、このアプローチは機密情報を暴露したり、安全でないデータ処理方法を作成したり、SQL インジェクションなどの脆弱性を導入するリスクがあります。また AI は存在しないソフトウェアパッケージを幻覚する可能性があり、開発者がマルウェアに感染したリソースをダウンロードする可能性があります。セキュリティ侵害、認可されていないアクセス、システム侵害を防ぐには、提案されたパッケージの徹底的なコードレビューと検証が不可欠です。

### 参考情報リンク

1. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)
2. [Arbitrary Code Execution](https://security.snyk.io/vuln/SNYK-PYTHON-LANGCHAIN-5411357): **Snyk Security Blog**
3. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
4. [New prompt injection attack on ChatGPT web version. Markdown images can steal your chat data.](https://systemweakness.com/new-prompt-injection-attack-on-chatgpt-web-version-ef717492c5c2?gi=8daec85e2116): **System Weakness**
5. [Don’t blindly trust LLM responses. Threats to chatbots](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/): **Embrace The Red**
6. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
7. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding): **OWASP AASVS**
8. [AI hallucinates software packages and devs download them – even if potentially poisoned with malware](https://www.theregister.com/2024/03/28/ai_bots_hallucinate_software_packages/) **Theregiste**
