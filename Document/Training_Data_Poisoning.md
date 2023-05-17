### LLM10:2023 - 訓練データポイズニング (Training Data Poisoning)

**説明:**  
Training data poisoning occurs when an attacker manipulates the training data or fine-tuning procedures of an LLM to introduce vulnerabilities, backdoors, or biases that could compromise the model's security, effectiveness, or ethical behavior.

**一般的な訓練データポイズニングの問題:**
- Introducing backdoors or vulnerabilities into the LLM through maliciously manipulated training data.
- Injecting biases into the LLM, causing it to produce biased or inappropriate responses.
- Exploiting the fine-tuning process to compromise the LLM's security or effectiveness.

**防止方法:**
- Ensure the integrity of the training data by obtaining it from trusted sources and validating its quality.
- Implement robust data sanitization and preprocessing techniques to remove potential vulnerabilities or biases from the training data.
- Regularly review and audit the LLM's training data and fine-tuning procedures to detect potential issues or malicious manipulations.
- Utilize monitoring and alerting mechanisms to detect unusual behavior or performance issues in the LLM, potentially indicating training data poisoning.

**攻撃シナリオの例:**
_シナリオ #1:_ An attacker infiltrates the training data pipeline and injects malicious data, causing the LLM to produce harmful or inappropriate responses.

_シナリオ #2:_ A malicious insider compromises the fine-tuning process, introducing vulnerabilities or backdoors into the LLM that can be exploited at a later stage.

By ensuring the integrity of the training data, implementing robust data sanitization techniques, and regularly auditing the LLM's training and fine-tuning processes, developers can minimize the risk of training data poisoning and protect their LLMs from potential vulnerabilities.
