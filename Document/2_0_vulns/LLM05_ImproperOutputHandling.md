## LLM05:2025 不適切な出力処理 (Improper Output Handling)

### 説明

不適切な出力処理は、特に、大規模言語モデルによって生成された出力がダウンストリームの他のコンポーネントやシステムに渡される前に、バリデーション、サニタイゼーション、ハンドリングが不十分であることを意味します。LLM が生成するコンテンツはプロンプト入力によって制御できるため、この動作はユーザーに追加機能への間接的なアクセスを提供しているのと同じようなものです。
不適切な出力処理は、LLM が生成する出力をダウンストリームに渡される前に処理するという点で過度の依存 (Overreliance) とは異なりますが、過度の依存 (Overreliance) は LLM 出力の正確性と適切性への過度の依存 (overdependence) に関するより広範な懸念に焦点を当てています。
不適切な出力処理の脆弱性の悪用に成功すると、ウェブブラウザで XSS や CSRF が発生するだけでなく、バックエンドシステムで SSRF、権限昇格、リモートコード実行が発生する可能性があります。
以下のような状況でこの脆弱性の影響が増大する可能性があります。
- アプリケーションがエンドユーザーの意図する以上の権限を LLM に付与し、権限昇格やリモートコード実行を可能にする。
- アプリケーションが間接プロンプトインジェクション攻撃に対して脆弱であり、攻撃者がターゲットユーザーの環境への特権的なアクセスを取得できる可能性がある。
- サードパーティの拡張機能では入力を適切に検証していない。
- さまざまなコンテキスト (HTML, JavaScript, SQL など) に対する適切な出力エンコーディングの欠如
- LLM 出力の監視とログ記録が不十分
- LLM 使用時のレート制限や異常検出の欠如

### 脆弱性の一般的な例

1. LLM 出力がシステムシェルまたは、exec や eval などの同様の関数に直接入力され、リモートコード実行につながります。
2. JavaScript や Markdown が LLM によって生成され、ユーザーに返されます。それからそのコードはブラウザによって解釈され、XSS につながります。
3. LLM が生成する SQL クエリが適切なパラメータ化なしで実行されるため、SQL インジェクションにつながります。
4. LLM 出力が適切なサニタイゼーションなしでファイルパスを構築するために使用され、パストラバーサル脆弱性につながる可能性があります。
5. LLM が生成するコンテンツが適切なエスケープなしでメールテンプレートに使用されるため、フィッシング攻撃につながる可能性があります。

### 予防および緩和戦略

1. モデルを他のユーザーと同様に扱い、ゼロトラストアプローチを採用し、モデルからバックエンド機能へのレスポンスに対して適切な入力バリデーションを適用します。
2. OWASP ASVS (Application Security Verification Standard) ガイドラインに従い、効果的な入力バリデーションとサニタイゼーションを確実に実行します。
3. モデル出力をエンコードしてユーザーに戻し、JavaScript や Markdown による望ましくないコード実行を緩和します。OWASP ASVS は出力エンコーディングに関する詳細なガイダンスを提供しています。
4. LLM 出力が使用される場所 (ウェブコンテンツの HTML エンコーディング、データベースクエリの SQL エスケープなど) に基づいて、コンテキストを考慮した出力エンコーディングを実装します。
5. LLM 出力に関連するすべてのデータベース操作に、パラメータ化されたクエリまたはプリペアドステートメントを使用します。
6. 厳格な Content Security Policies (CSP) を採用して、LLM が生成するコンテンツから XSS 攻撃のリスクを緩和します。
7. 堅牢なログ記録および監視システムを実装して、悪用の試みを示す可能性のある LLM 出力の異常なパターンを検出します。

### 攻撃シナリオの例

#### シナリオ #1
  アプリケーションは LLM 拡張機能を利用して、チャットボット機能のレスポンスを生成します。この拡張機能は別の特権 LLM にアクセスできる管理機能もいくつか提供します。汎用 LLM は、適切な出力バリデーションなしで、そのレスポンスを直接渡すため、この拡張機能はメンテナンスのためのシャッドダウンを引き起こします。
#### シナリオ #2
  ユーザーは LLM を搭載したウェブサイト要約ツールを利用して、記事の簡潔な要約を生成します。ウェブサイトには LLM にウェブサイトまたはユーザーの会話から機密コンテンツをキャプチャするよう指示するプロンプトインジェクションを含んでいます。そこから LLM は機密データをエンコードし、出力バリデーションやフィルタリングなしで、攻撃者が制御するサーバーに送信できます。
#### シナリオ #3
  LLM はユーザーがチャットのような機能を通じてバックエンドデータベースに対する SQL クエリを作成できます。ユーザーはすべてのデータベーステーブルを削除するクエリをリクエストします。LLM から作成されたクエリが精査されなければ、すべてのデータベーステーブルが削除されます。
#### シナリオ #4
  ウェブアプリは LLM を使用して、出力サニタイゼーションなしでユーザーテキストプロンプトからコンテンツを生成します。攻撃者は細工したプロンプトを送信して、LLM がサニタイズされていない JavaScript ペイロードを返すようにし、被害者のブラウザで処理された際に XSS を引き起こす可能性があります。プロンプトのバリデーションが不十分なため、この攻撃が可能になりました。
#### シナリオ # 5
  LLM を使用して、マーケティングキャンペーン用の動的な電子メールテンプレートを生成します。攻撃者は LLM を操作して、電子メールコンテンツの中に悪意のある JavaScript を含めます。アプリケーションが LLM 出力を適切にサニタイズしない場合、これは脆弱な電子メールクライアントで電子メールを閲覧する受信者に対する XSS 攻撃につながる可能性があります。
#### シナリオ #6
  LLM は、ソフトウェア会社で自然言語入力からコードを生成するために使用され、開発タスクの効率化を目指しています。このアプローチは効率的ですが、機密情報の開示、安全でないデータ処理手法の作成、SQL インジェクションなどの脆弱性の導入といったリスクがあります。また、AI は存在しないソフトウェアパッケージを幻覚する可能性があり、開発者はマルウェアに感染したリソースのダウンロードにつながる可能性があります。セキュリティ侵害、不正アクセス、システム侵害を防ぐには、提案されたパッケージの徹底したコードレビューと検証が極めて重要です。

### 参考情報リンク

1. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)
2. [Arbitrary Code Execution](https://security.snyk.io/vuln/SNYK-PYTHON-LANGCHAIN-5411357): **Snyk Security Blog**
3. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
4. [New prompt injection attack on ChatGPT web version. Markdown images can steal your chat data.](https://systemweakness.com/new-prompt-injection-attack-on-chatgpt-web-version-ef717492c5c2?gi=8daec85e2116): **System Weakness**
5. [Don’t blindly trust LLM responses. Threats to chatbots](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/): **Embrace The Red**
6. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
7. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding): **OWASP AASVS**
8. [AI hallucinates software packages and devs download them – even if potentially poisoned with malware](https://www.theregister.com/2024/03/28/ai_bots_hallucinate_software_packages/) **Theregiste**
