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

#### 1. 過剰な機能性
  LLM エージェントはシステムの意図した動作には必要のない機能を含む拡張機能にアクセスできます。たとえば、開発者は LLM エージェントにリポジトリからドキュメントを読み取る機能を付与する必要がありますが、使用するために選択したサードパーティ拡張機能にはドキュメントを変更および削除する機能も含みます。
#### 2. 過剰な機能性
  拡張機能は開発段階で使用され、より優れた代替手段のために取りやめとなりましたが、元のプラグインは LLM エージェントで利用可能なままとなっています。
#### 3. 過剰な機能性
  オープンエンドな機能性を有する LLM プラグインは、アプリケーションの意図した動作に必要となるもの以外の入力指示を適切にフィルタできません。たとえば、特定のシェルコマンドを実行する拡張機能は、他のシェルコマンドの実行を適切に防止できません。
#### 4. 過剰な権限
  LLM 拡張機能はアプリケーションの意図した動作に必要のないダウンストリームシステムに対する権限を持ちます。たとえば、データの読み取りを意図した拡張機能は、SELECT 権限だけでなく UPDATE、INSERT、DELETE 権限も有する ID を使用してデータベースサーバーに接続します。
#### 5. 過剰な権限
  個々のユーザーのコンテキストで操作を実行するように設計された LLM 拡張機能は、一般的な高権限 ID でダウンストリームシステムにアクセスします。たとえば、カレントユーザーのドキュメントストアを読み取る拡張機能は、すべてのユーザーのファイルにアクセスできる特権アカウントでドキュメントリポジトリに接続します。
#### 6. 過剰な自律性
  LLM ベースのアプリケーションや拡張機能は、影響の大きいアクションを独立して検証および承認できません。たとえば、ユーザーのドキュメントを削除できるようにする拡張機能は、ユーザーからの確認なしに削除を実行します。

### 予防および緩和戦略

以下のアクションで過剰なエージェンシーを防止できます。

#### 1. 拡張機能を最小限に抑える
  LLM エージェントが呼び出すことができる拡張機能を必要最小限のものに制限します。たとえば、LLM ベースのシステムが URL のコンテンツを取得する機能を必要としない場合、そのような拡張機能は LLM エージェントに提供すべきではありません。
#### 2. 拡張機能の機能性を最小限に抑える
  LLM 拡張機能に実装される機能は必要最小限に制限します。たとえば、ユーザーのメールボックスにアクセスして電子メールを要約する拡張機能では、電子メールの読み取り機能のみが必要になるため、拡張機能にはメッセージの削除や送信などの他の機能を含めるべきではありません。
#### 3. オープンエンドな拡張機能を避ける
  可能な限りオープンエンドな拡張機能 (シェルコマンドの実行、URL の取得など) の使用を避け、より細かい機能性の拡張機能を使用します。たとえば、LLM ベースのアプリでは出力をファイルに書き込み必要があるかもしれません。これをシェル関数を実行する拡張機能を使用して実装した場合、望ましくないアクションの範囲が非常に大きくなります (他のシェルコマンドが実行できるかもしれません)。より安全な代替手段としては、その特定の機能のみを実装した、特定のファイル書き込み拡張機能を構築することです。
#### 4. 拡張機能の権限を最小限に抑える
  LLM 拡張機能が他のシステムに付与する権限を必要最小限に制限して、望ましくないアクションの範囲を制限します。たとえば、顧客に購入をお勧めするために商品データベースを使用する LLM エージェントは 'products' テーブルへの読み取りアクセスのみが必要かもしれません。他のテーブルにアクセスしたり、レコードを挿入、更新、削除する権限は持つべきではありません。これは、LLM 拡張機能がデータベースに接続するために使用する ID に対して、適切なデータベース権限を適用することで実施すべきです。
#### 5. ユーザーのコンテキストで拡張機能を実行する
  ユーザー認可とセキュリティスコープを追跡して、ユーザーに代わって実行されたアクションが、その特定のユーザーのコンテキストで、かつ必要最小限の権限で、ダウンストリームシステム上で実行されるようにします。たとえば、ユーザーのコードリポジトリを読み取る LLM 拡張機能はユーザーを OAuth 経由かつ必要最小限のスコープで認証するように要求すべきです。
#### 6. ユーザーの承認を必要とする
  人間参加型 (human-in-the-loop) 制御を利用して、影響の大きいアクションを実行する前に人間が承認することを求めます。これはダウンストリームシステム内 (LLM アプリケーションの範囲外) または LLM 拡張機能自体に実装できます。たとえば、ユーザーに代わってソーシャルメディアコンテンツを作成して投稿する LLM ベースのアプリは、'post' 操作を実装する拡張機能内にユーザー承認ルーチンを含めるべきです。
#### 7. 完全な仲介
  アクションが許可されるかどうかを決定するために LLM に依存するのではなく、ダウンストリームシステムに認可を実装します。完全な仲介の原則を適用して、拡張機能を介してダウンストリームシステムに対して行われるすべてのリクエストがセキュリティポリシーに照らして検証されるようにします。
#### 8. LLM の入出力をサニタイズする
  OWASP の ASVS (Application Security Verification Standard) の勧告を適用するなど、セキュアコーディングのベストプラクティスに従います。特に入力のサニタイゼーションに強く重点を置きます。開発パイプラインにおいて静的アプリケーションセキュリティテスト (SAST) と動的およびインタラクティブアプリケーション (DAST, IAST) を使用します。

以下のオプションは過剰なエージェンシーを防ぐものではありませんが、発生する損害のレベルを制限することができます。

- LLM 拡張機能とダウンストリームシステムのアクティビティをログ記録して監視し、望ましくないアクションが行われている場所を特定し、それに応じて対応します。
- レート制限を実装して、一定期間内に起こりうる望ましくないアクションの回数を減らし、重大な損害が発生する前に監視を通じて望ましくないアクションを発見する機会を増やします。

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
