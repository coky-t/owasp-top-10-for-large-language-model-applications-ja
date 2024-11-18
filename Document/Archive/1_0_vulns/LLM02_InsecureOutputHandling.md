## 安全でない出力処理 (Insecure Output Handling)

**説明:**

安全でない出力処理はダウンストリームコンポーネントが大規模言語モデル (LLM) 出力を適切な精査なしに盲目的に受け入れる場合 (LLM 出力をバックエンド機能、特権機能、クライアント側機能に直接渡すなど) に発生する脆弱性です。LLM が生成するコンテンツはプロンプト入力によって制御できるため、この動作はユーザーに追加機能への間接的なアクセスを提供するのと同様です。

安全でない出力処理の悪用に成功すると、ウェブブラウザでの XSS や CSRF だけでなく、バックエンドシステムでの SSRF、権限昇格、リモートコード実行につながる可能性があります。
以下の条件によりこの脆弱性の影響が増大する可能性があります。
* アプリケーションはエンドユーザーが意図する権限を超えたものを LLM に付与し、権限昇格やリモートコード実行を可能にしています。
* アプリケーションは外部プロンプトインジェクション攻撃に対して脆弱であり、攻撃者がターゲットユーザーの環境への特権アクセスを取得できる可能性があります。

**脆弱性の一般的な例:**

1. LLM 出力はシステムシェルまたは `exec` や `eval` などの同様の関数に直接入力され、リモートコード実行につながります。
2. JavaScript やマークダウンは LLM によって生成され、ユーザーに返されます。コードはブラウザによって解釈され、XSS につながります。

**防止方法:**

1. モデルを他のユーザーとして扱い、モデルからバックエンド機能へのレスポンスに適切な入力バリデーションを適用します。OWASP ASVS (Application Security Verification Standard) ガイドラインに従い、効果的な入力バリデーションとサニタイゼーションを確保します。
2. モデル出力をエンコードしてユーザーに返し、JavaScript やマークダウンによる望ましくないコード実行を軽減します。OWASP ASVS は出力エンコーディングに関する詳細なガイダンスを提供します。


**攻撃シナリオの例:**

1. アプリケーションは LLM プラグインを使用して、チャットボット機能のレスポンスを生成します。しかし、アプリケーションは適切なバリデーションを行わずに LLM で生成されたレスポンスをシステムコマンドの実行を担当する内部関数に直接渡します。これにより攻撃者は LLM 出力を操作して、基盤となるシステム上で任意のコマンドを実行し、認可されていないアクセスや意図しないシステム改変を引き起こします。

2. ユーザーは LLM を搭載したウェブサイト要約ツールを利用して、記事の簡潔な要約を生成します。このウェブサイトにはウェブサイトやユーザーの会話から機密コンテンツをキャプチャするよう LLM に指示するプロンプトインジェクションを含みます。そこから LLM は機密データをエンコードし、攻撃者が制御するサーバーに送信できます。

3. LLM ではユーザーはチャットのような機能を通じてバックエンドデータベースに対する SQL クエリを作成できます。ユーザーはすべてのデータベーステーブルを削除するクエリをリクエストします。LLM から作成されたクエリが精査されない場合、すべてのデータベーステーブルが削除されてしまいます。

4. 悪意のあるユーザーはサニタイゼーション制御なしで JavaScript ペイロードをユーザーに返すように LLM に指示します。これはプロンプトの共有、プロンプト注入ウェブサイト、URL パラメータからのプロンプトを受け入れるチャットボットのいずれかを介して発生する可能性があります。その後 LLM はサニタイズされていない XSS ペイロードをユーザーに返します。追加のフィルターがなく、LLM 自体で除外されるものの外側で、JavaScript がユーザーブラウザ内で実行します。

**参考情報リンク:**
1. [Arbitrary Code Execution](https://security.snyk.io/vuln/SNYK-PYTHON-LANGCHAIN-5411357): `LLMMathChain` での安全でないメソッド `exec` および `eval` の使用による任意のコード実行に関する脆弱性レポート。
2. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): 最初の悪用可能な LLM ベースの Cross Plugin Request Forgery がどのように発見されたかおよび適用された修正についての説明。
3. [New prompt injection attack on ChatGPT web version. Markdown images can steal your chat data.](https://systemweakness.com/new-prompt-injection-attack-on-chatgpt-web-version-ef717492c5c2?gi=8daec85e2116): 単一ピクセルの画像がユーザーの機密性の高いチャットデータを窃取し、悪意のあるサードパーティに送信する可能性がある脆弱性の説明。
4. [Don’t blindly trust LLM responses. Threats to chatbots](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/): LLM レスポンスの信頼性の低さ、チャットボット、リスクを軽減する方法に焦点を当てた投稿。
5. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/): 汎用 LLM ベースのアプリケーションの高レベルの脅威モデルとその脅威モデルの分析を示します。
6. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding): OWASP の Application Security Verification Standard の章。
