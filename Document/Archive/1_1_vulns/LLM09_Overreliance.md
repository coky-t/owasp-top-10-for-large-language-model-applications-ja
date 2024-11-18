## LLM09: 過度の依存 (Overreliance)

### 説明

過度の依存は LLM が誤った情報を生成し、それを権威のある方法で提供する場合に発生する可能性があります。LLM は創造的で有益なコンテンツを生成できる一方で、事実と異なる、不適切、あるいは安全でないコンテンツを生成することもあります。これは幻覚 (hallucination) または作話 (confabulation) とよばれます。人々やシステムが監視や確認なしにこの情報を信頼すると、セキュリティ侵害、誤った情報、誤った伝達、法的問題、風評被害をもたらす可能性があります。

LLM が生成したソースコードは気づかれないうちにセキュリティ上の脆弱性を引き起こす可能性があります。これはアプリケーションの運用上の安全性とセキュリティに重大なリスクをもたらします。これらのリスクは以下のような厳格なレビュープロセスの重要性を示しています。

* 監視
* 継続的なバリデーションメカニズム
* リスクに関する免責事項

### 脆弱性の一般的な例

1. LLM はレスポンスとして不正確な情報を提供しながら、それが非常に権威のあることを示唆するような言い方をします。システム全体がこれを処理するための適切なチェックとバランスを欠いて設定されており、その情報は危害をもたらす形でユーザーを欺きます。
2. LLM は安全でないコードや欠陥のあるコードを提案し、適切な監視や検証なしでソフトウェアシステムに組み込んだ際に脆弱性を引き起こします。

### 予防および緩和戦略

1. LLM 出力を定期的に監視してレビューします。自己一貫性や投票技法を使用して、一貫性のないテキストを除外します。単一のプロンプトに対して複数のモデルのレスポンスを比較することで、出力の品質と一貫性をより適切に判断できます。
2. LLM 出力を信頼できる外部情報源とクロスチェックします。このようにバリデーションのレイヤを追加することで、モデルによって提供される情報が正確で信頼できることを確保できます。
3. ファインチューニングや embedding でモデルを強化して、出力品質を向上します。一般的な事前訓練モデルは特定のドメインで調整されたモデルと比較して不正確な情報を生成する可能性が高くなります。この目的のために、プロンプトエンジニアリング、パラメータ効率チューニング (PET)、フルモデルチューニング、思考連鎖プロンプトなどの技法を採用できます。
4. 生成された出力を既知の事実やデータと相互検証できる自動バリデーションメカニズムを実装します。これはセキュリティの追加レイヤを提供し、幻覚 (hallucination) に関連するリスクを軽減できます。
5. 複雑なタスクを管理可能なサブタスクに分割し、さまざまなエージェントに割り当てます。これは複雑さを管理するのに役立つだけでなく、各エージェントをより小さなタスクに責任を負うことができるため、幻覚 (hallucination) の可能性も減少します。
6. LLM の使用に関するリスクと制限を明確に伝えます。これには情報の不正確さの可能性や他のリスクを含みます。効果的なリスク伝達により、ユーザーは潜在的な問題に備え、情報に基づいた意思決定を行うことができます。
7. LLM の責任ある安全な使用を促す API とユーザーインタフェースを構築します。これにはコンテンツフィルタ、潜在的な不正確さに関するユーザーへの警告、AI が生成したコンテンツの明確なラベル付けなどの手段があります。
8. 開発環境で LLM を使用する場合、セキュアコーディングプラクティスとガイドラインを確立し、潜在的な脆弱性の統合を防ぎます。

### 攻撃シナリオの例

1. ある報道機関は LLMを多用してニュース記事を生成します。悪意のあるユーザーはこの過度の依存を悪用して、LLM に誤解を招く情報を与え、偽情報の拡散を引き起こします。
2. AI は意図せずコンテンツを盗用し、著作権の問題や組織の信頼低下につながります。
3. あるソフトウェア開発チームは LLM システムを利用してコーディングプロセスを迅速化します。AI の提案に過度に依存すると、安全でないデフォルト設定やセキュアコーディングプラクティスと矛盾する推奨事項により、アプリケーションにセキュリティ脆弱性をもたらします。
4. あるソフトウェア開発会社は LLM を使用して開発者を支援します。LLM は存在しないコードライブラリやパッケージを提案し、開発者は AI を信頼して、知らないうちに悪意のあるパッケージを会社のソフトウェアに組み込んでしまいます。これは、特にサードパーティコードやライブラリが関与する場合、LLM の提案をクロスチェックすることの重要性を浮き彫りにしています。

### 参考情報リンク

1. [Understanding LLM Hallucinations](https://towardsdatascience.com/llm-hallucinations-ec831dcd7786): **Towards Data Science**
2. [How Should Companies Communicate the Risks of Large Language Models to Users?](https://techpolicy.press/how-should-companies-communicate-the-risks-of-large-language-models-to-users/): **Techpolicy**
3. [A news site used AI to write articles. It was a journalistic disaster](https://www.washingtonpost.com/media/2023/01/17/cnet-ai-articles-journalism-corrections/): **Washington Post**
4. [AI Hallucinations: Package Risk](https://vulcan.io/blog/ai-hallucinations-package-risk): **Vulcan.io**
5. [How to Reduce the Hallucinations from Large Language Models](https://thenewstack.io/how-to-reduce-the-hallucinations-from-large-language-models/): **The New Stack**
6. [Practical Steps to Reduce Hallucination](https://newsletter.victordibia.com/p/practical-steps-to-reduce-hallucination): **Victor Debia**
