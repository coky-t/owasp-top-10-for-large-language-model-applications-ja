### LLM10:2023 - 訓練データポイズニング (Training Data Poisoning)

**説明:**
訓練データポイズニングは攻撃者が LLM の訓練データや微調整手順を操作して、モデルのセキュリティ、有効性、倫理的動作を損なう可能性のある脆弱性、バックドア、バイアスを導入した際に発生します。

**一般的な訓練データポイズニングの問題:**
- 悪意を持って操作された訓練データを通じて LLM にバックドアや脆弱性を導入します。
- LLM にバイアスを注入し、偏ったレスポンスや不適切なレスポンスを生成します。
- 微調整プロセスを悪用して、LLM のセキュリティや有効性を侵害します。

**防止方法:**
- 信頼できるソースから訓練データを取得し、その品質を検証することで、訓練データの完全性を確保します。
- 堅牢なデータサニタイズと前処理の技法を実装して、訓練データから潜在的な脆弱性やバイアスを除去します。
- 定期的に LLM の訓練データと微調整手順をレビューおよび監査して、潜在的な問題や悪意のある操作を検出します。
- モニタリングとアラートのメカニズムを利用して、訓練データポイズニングを示す可能性のある LLM の異常な動作やパフォーマンスの問題を検出します。

**攻撃シナリオの例:**
_シナリオ #1:_ 攻撃者は訓練データパイプラインに侵入し、悪意のあるデータを注入して、LLM が有害なレスポンスや不適切なレスポンスを生成します。

_シナリオ #2:_ 悪意のある内部関係者が微調節プロセスを侵害し、LLM に後のステージで悪用できる脆弱性やバックドアを導入します。

訓練データの完全性を確保し、堅牢なデータサニタイズ技法を実装し、定期的に LLM のトレーニングと微調整のプロセスを監査することにより、開発者は訓練データポイズニングのリスクを最小限に抑え、LLM を潜在的な脆弱性から保護します。
