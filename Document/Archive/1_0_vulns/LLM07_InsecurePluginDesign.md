## 安全でないプラグイン設計 (Insecure Plugin Design)

**説明**

LLM プラグインは、有効にすると、ユーザーインタラクション時にモデルによって自動的に呼び出される拡張機能です。プラグインはモデルによって駆動され、アプリケーションによる実行制御はありません。さらに、コンテキストサイズの制限に対処するために、プラグインはバリデーションや型チェックを行わずにモデルからフリーテキスト入力を可能性があります。これにより悪意のある攻撃者がプラグインに悪意のあるリクエストを作成できるようになり、リモートコード実行に至るまで、さまざまな望ましくない動作を引き起こす可能性があります。

悪意のある入力の危害はアクセス制御が不十分であったり、プラグイン間の認可を追跡できないことが原因であることがよくあります。アクセス制御が不適切であると、プラグイン型のプラグインを盲目的に信頼し、エンドユーザーが入力を提供したとみなす可能性があります。このような不適切なアクセス制御により悪意のある入力がデータ流出、リモートコード実行、権限昇格などの有害な結果をもたらす可能性があります。

この項目では LLM サプライチェーン脆弱性でカバーされるサードパーティプラグインの使用ではなく、LLM プラグインの作成に焦点を当てています。

**脆弱性の一般的な例:**

1. プラグインは個別の入力パラメータではなく単一のテキストフィールドですべてのパラメータを受け入れます。
2. プラグインはパラメータの代わりにコンフィギュレーション文字列を受け入れ、コンフィギュレーション設定全体をオーバーライドできます。
3. プラグインはパラメータの代わりに生の SQL やプログラミングステートメントを受け入れます。
4. 認証は特定のプラグインに対する明示的な認可なしで実施されます。
5. プラグインはすべての LLM コンテンツを完全にユーザーによって作成されたものとして扱い、追加の認可を必要とせずにリクエストされたアクションを実行します。

**防止方法:**

1. プラグインは可能な限り厳密にパラメータ化された入力を強制し、入力の型と範囲のチェックを含める必要があります。これが可能でない場合には、型付き呼び出しの第二のレイヤーを導入し、リクエストを解析してバリデーションとサニタイゼーションを適用する必要があります。アプリケーションのセマンティクスのために自由形式の入力を受け入れなければならない場合には、入力を注意深く検査して、有害な可能性があるメソッドが呼び出されていないことを確保します。
2. プラグイン開発者は ASVS (Application Security Verification Standard) での OWASP の推奨事項を適用して、効果的な入力バリデーションとサニタイゼーションを確保する必要があります。
3. プラグインを徹底的に検査及びテストして、適切なバリデーションを確保します。開発パイプラインでは静的アプリケーションセキュリティテスト (SAST) スキャンと動的および対話的アプリケーションテスト (DAST, IAST) を使用します。
4. プラグインは OWASP ASVS アクセス制御ガイドラインに従い、安全でない入力パラメータの悪用による影響を最小限にするように設計する必要があります。これには最小権限アクセス制御が含まれ、必要な機能を実行しますが、可能な限り少ない機能だけ公開します。
5. プラグインは OAuth2 などの適切な認証 ID を使用して、効果的な認可とアクセス制御を適用する必要があります。さらに、API キーはデフォルトの対話型ユーザーではなくプラグインのルートを反映するカスタム認可決定のコンテキストを提供するために使用する必要があります。
6. 機密性の高いプラグインによって実行されるすべてのアクションについて、手動によるユーザー認可と確認を要求します。
7. プラグインは一般的に REST API ですので、開発者は OWASP Top 10 API セキュリティリスク 2023 にある推奨事項を適用し、一般的な脆弱性を最小限に抑える必要があります。

**攻撃シナリオの例:**

1. プラグインはベース URL を受け入れ、LLM にその URL とクエリを組み合わせて、ユーザーリクエストの処理に含まれる天気予報を取得するように指示します。悪意のあるユーザーは URL が自分の制御するドメインを指すようにリクエストを作成し、そのドメインを介して LLM システムに自分のコンテンツを注入できます。

2. プラグインは検証していない単一フィールドへの自由形式入力を受け入れます。攻撃者は注意深く作成されたペイロードを提供して、エラーメッセージから偵察を実行します。次に、既知のサードパーティ脆弱性を悪用してコードを実行し、データ流出や権限昇格を実行します。

3. vector ストアから embedding を取得するために使用されるプラグインを使用して、バリデーションなしでコンフィギュレーションパラメータを接続文字列として受け入れます。これにより攻撃者は名前やホストパラメータを変更することで、他の vector ストアを実験およびアクセスして、アクセスすべきでない embedding を流出できます。

4. プラグインは SQL WHERE 句を高度なフィルタとして、フィルタリング SQL に追加します。これにより攻撃者は SQL 攻撃を実行できます。

5. 攻撃者は間接プロンプトインジェクションを使用して、入力バリデーションがなく、アクセス制御が弱い、安全でないコード管理プラグインを悪用し、リポジトリの所有権を譲渡して、ユーザーをリポジトリからロックアウトします。

**参考情報リンク:**

1. [OpenAI ChatGPT Plugins](https://platform.openai.com/docs/plugins/introduction): ChatGPT 開発者のガイド
2. [OpenAI ChatGPT Plugins - Plugin Flow](https://platform.openai.com/docs/plugins/introduction/plugin-flow): プラグインの実行フローの説明と、すべての POST オペレーションに対するユーザー確認の要件
3. [OpenAI ChatGPT Plugins - Authentication](https://platform.openai.com/docs/plugins/authentication/service-level): サービスおよびユーザーレベルの認証 (API トークンと OAuth2 を含む) と非認証モードの説明。
4. [OpenAI Semantic Search Plugin Sample](https://github.com/openai/chatgpt-retrieval-plugin): embedding と vector データベースを使用したセマンティック検索のためのフル機能のソリューション。また、すべての認証方法も説明します。
5. [Plugin Vulnerabilities: Visit a Website and Have Your Source Code Stolen](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/): 悪意のあるプラグインを使用して GitHub リポジトリ上で認可されていないアクションを実行することについての簡単な説明。
6. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) 不適切なクロスプラグイン認可によって引き起こされるクロスプラグインリクエストフォージェリ攻撃のウォークスルー。
7. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./)
8. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding)
9. [OWASP ASVS 4.1 General Access Control Design](https://owasp-aasvs4.readthedocs.io/en/latest/V4.1.html#general-access-control-design)
10. [OWASP Top 10 API Security Risks – 2023](https://owasp.org/API-Security/editions/2023/en/0x11-t10/)
