## LLM02: 安全でない出力処理 (Insecure Output Handling)


### 説明

安全でない出力処理は、大規模言語モデルによって生成された出力をダウンストリームのコンポーネントやシステムに渡す前に、特にその出力を十分にバリデーション、サニタイゼーション、ハンドリングしないことを指します。LLM が生成するコンテンツはプロンプト入力によって制御できるため、この動作はユーザーに追加機能への間接的なアクセスを提供するのと同様です。

安全でない出力処理は LLM で生成された出力をダウンストリームに渡す前に処理するという点で過度の依存とは異なります。過度の依存では LLM 出力の正確さと適切さへの過剰な依存に関するより広範な懸念に焦点を当てています。

安全でない出力処理の悪用に成功すると、ウェブブラウザでの XSS や CSRF だけでなく、バックエンドシステムでの SSRF、権限昇格、リモートコード実行につながる可能性があります。

以下の条件によりこの脆弱性の影響が増大する可能性があります。
* アプリケーションはエンドユーザーが意図する権限を超えたものを LLM に付与し、権限昇格やリモートコード実行を可能にしています。
* アプリケーションは間接プロンプトインジェクション攻撃に対して脆弱であり、攻撃者がターゲットユーザーの環境への特権アクセスを取得できる可能性があります。
* サードパーティプラグインは入力を適切に検証していません。

### 脆弱性の一般的な例

1. LLM 出力はシステムシェルまたは exec や eval などの同様の関数に直接入力され、リモートコード実行につながります。
2. JavaScript やマークダウンは LLM によって生成され、ユーザーに返されます。コードはブラウザによって解釈され、XSS につながります。

### 予防および緩和戦略

1. モデルを他のユーザーとして扱い、ゼロトラストアプローチを採用して、モデルからバックエンド機能へのレスポンスに適切な入力バリデーションを適用します。
2. OWASP ASVS (Application Security Verification Standard) ガイドラインに従い、効果的な入力バリデーションとサニタイゼーションを確保します。
3. モデル出力をエンコードしてユーザーに返し、JavaScript やマークダウンによる望ましくないコード実行を軽減します。OWASP ASVS は出力エンコーディングに関する詳細なガイダンスを提供します。

### 攻撃シナリオの例

1. アプリケーションは LLM プラグインを使用して、チャットボット機能のレスポンスを生成します。このプラグインは別の特権 LLM にアクセスできる多くの管理機能を提供します。汎用 LLM は適切な出力バリデーションを行わずにそのレスポンスをプラグインに直接渡し、プラグインはメンテナンスのためにシャットダウンしてしまいます。
2. ユーザーは LLM を搭載したウェブサイト要約ツールを利用して、記事の簡潔な要約を生成します。このウェブサイトにはウェブサイトやユーザーの会話から機密コンテンツをキャプチャするよう LLM に指示するプロンプトインジェクションを含みます。そこから LLM は機密データをエンコードし、出力バリデーションやフィルタリングを行わずに、攻撃者が制御するサーバーに送信できます。
3. LLM ではユーザーはチャットのような機能を通じてバックエンドデータベースに対する SQL クエリを作成できます。ユーザーはすべてのデータベーステーブルを削除するクエリをリクエストします。LLM から作成されたクエリが精査されない場合、すべてのデータベーステーブルが削除されてしまいます。
4. あるウェブアプリは LLM を使用して、出力バリデーションを行わずにユーザーテキストプロンプトからコンテンツを生成します。攻撃者は細工したプロンプトを送信すると、LLM がサニタイズされていない JavaScript ペイロードを返し、被害者のブラウザで表示される際に XSS を引き起こす可能性があります。プロンプトのバリデーションが不十分なため、この攻撃が可能になりました。

### 参考情報リンク

1. [Arbitrary Code Execution](https://security.snyk.io/vuln/SNYK-PYTHON-LANGCHAIN-5411357): **Snyk Security Blog**
2. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
3. [New prompt injection attack on ChatGPT web version. Markdown images can steal your chat data.](https://systemweakness.com/new-prompt-injection-attack-on-chatgpt-web-version-ef717492c5c2?gi=8daec85e2116): **System Weakness**
4. [Don’t blindly trust LLM responses. Threats to chatbots](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/): **Embrace The Red**
5. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
6. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding): **OWASP AASVS**
