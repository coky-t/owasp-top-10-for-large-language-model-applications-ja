## モデル窃取 (Model Theft)

**説明:**

このエントリでは悪意のある攻撃者や APT による LLM モデルへの認可されていないアクセスや抽出について言及します。これはプロプライエタリ LLM モデル (貴重な知的財産である) が侵害されたり、物理的に盗まれたり、複製されたり、機能的に等価なものを作成するために重みとパラメータが抽出される場合に発生します。LLM モデル窃取の影響には、経済的およびブランド的評判の損失、競争上の優勢性の低下、モデルの認可されていない使用、モデル内に含まれる機密情報の認可されていないアクセスなどの可能性があります。

言語モデルがまずます強力になり普及するにつれて、LLM の窃取はセキュリティ上の重大な懸念事項となっています。組織や研究者は堅牢なセキュリティ対策を優先して、LLM モデルを保護し、知的財産の機密性と完全性を確保する必要があります。アクセス制御、暗号化、継続的監視を含む包括的なセキュリティフレームワークを採用することは、LLM モデル窃取に関連するリスクを軽減し、LLM に依存する個人と組織の両方の利益を守るうえで極めて重要です。

**脆弱性の一般的な例:**

1. An attacker exploits a vulnerability in a company's infrastructure to gain unauthorized access to their LLM model repository via misconfiguration in their network or application security settings.
2. An insider threat scenario where a disgruntled employee leaks model or related artifacts.
3. An attacker queries the model API using carefully crafted inputs and prompt injection techniques to collect a sufficient number of outputs to create a shadow model.
4. A malicious attacker is able to bypass input filtering techniques of the LLM to perform a side-channel attack and ultimately harvest model weights and architecture information to a remote controlled resource.
5. The attack vector for model extraction involves querying the LLM with a large number of prompts on a particular topic. The outputs from the LLM can then be used to fine-tune another model. However, there are a few things to note about this attack:
   - The attacker must generate a large number of targeted prompts. If the prompts are not specific enough, the outputs from the LLM will be useless.
   - The outputs from LLMs can sometimes contain hallucinated answers meaning the attacker may not be able to extract the entire model as some of the outputs can be nonsensical.
     - It is not possible to replicate an LLM 100% through model extraction. However, the attacker will be able to replicate a partial model.
6. The attack vector for **_functional model replication_** involves using the target model via prompts to generate synthetic training data (an approach called "self-instruct") to then use it and fine-tune another foundational model to produce a functional equivalent. This bypasses the limitations of traditional query-based extraction used in Example 5 and has been successfully used in research of using an LLM to train another LLM. Although in the context of this research, model replication is not an attack. The approach could be used by an attacker to replicate a proprietary model with a public API.

Use of a stolen model, as a shadow model, can be used to stage adversarial attacks including unauthorized access to sensitive information contained within the model or experiment undetected with adversarial inputs to further stage advanced prompt injections.

**防止方法:**

1. Implement strong access controls (E.G., RBAC and rule of least privilege) and strong authentication mechanisms to limit unauthorized access to LLM model repositories and training environments.
   1. This is particularly true for the first three common examples, which could cause this vulnerability due to insider threats, misconfiguration, and/or weak security controls about the infrastructure that houses LLM models, weights and architecture in which a malicious actor could infiltrate from insider or outside the environment.
   2. Supplier management tracking, verification and dependency vulnerabilities are important focus topics to prevent exploits of supply-chain attacks.
2. Restrict the LLM's access to network resources, internal services, and APIs.
   1. This is particularly true for all common examples as it covers insider risk and threats, but also ultimately controls what the LLM application "*has access to*" and thus could be a mechanism or prevention step to prevent side-channel attacks.
3. Regularly monitor and audit access logs and activities related to LLM model repositories to detect and respond to any suspicious or unauthorized behavior promptly.
4. Automate MLOps deployment with governance and tracking and approval workflows to tighten access and deployment controls within the infrastructure.
5. Implement controls and mitigation strategies to mitigate and|or reduce risk of prompt injection techniques causing side-channel attacks.
6. Rate Limiting of API calls where applicable and|or filters to reduce risk of data exfiltration from the LLM applications, or implement techniques to detect (E.G., DLP) extraction activity from other monitoring systems.
7. Implement adversarial robustness training to help detect extraction queries and tighten physical security measures.
8. Implement a watermarking framework into the embedding and detection stages of an LLMs lifecyle.

**攻撃シナリオの例:**

1. An attacker exploits a vulnerability in a company's infrastructure to gain unauthorized access to their LLM model repository. The attacker proceeds to exfiltrate valuable LLM models and uses them to launch a competing language processing service or extract sensitive information, causing significant financial harm to the original company.
2. A disgruntled employee leaks model or related artifacts. The public exposure of this scenario increases knowledge to attackers for gray box adversarial attacks or alternatively directly steal the available property.
3. An attacker queries the API with carefully selected inputs and collects sufficient number of outputs to create a shadow model.
4. A security control failure is present within the supply-chain and leads to data leaks of proprietary model information.
5. A malicious attacker bypasses input filtering techniques and preambles of the LLM to perform a side-channel attack and retrieve model information to a remote controlled resource under their control.

**参考情報リンク:**

1. [Meta’s powerful AI language model has leaked online](https://www.theverge.com/2023/3/8/23629362/meta-ai-language-model-llama-leak-online-misuse) A news article highlighting a real-world incident where an AI language model leaked online, emphasizing the importance of protecting LLM models from unauthorized access and misuse.
2. [Runaway LLaMA | How Meta's LLaMA NLP model leaked](https://www.deeplearning.ai/the-batch/how-metas-llama-nlp-model-leaked/) The same example as the prior reference, but detailing exploitation steps from a detailed-level of how the proprietary model was leaked. Physical model theft is a key concern and the Meta "LaMa leak" indicates the challenges of applying access control in collaborative research.
3. [I Know What You See:](https://arxiv.org/pdf/1803.05847.pdf) Power Side-Channel Attack on Convolutional Neural Network Accelerators: Example of Side-channel attacks to extract model information.
4. [D-DAE: Defense-Penetrating Model Extraction Attacks:](https://www.computer.org/csdl/proceedings-article/sp/2023/933600a432/1He7YbsiH4c) Adversarial approaches to defeat current extraction techniques.
5. [A Comprehensive Defense Framework Against Model Extraction Attacks](https://ieeexplore.ieee.org/document/10080996) Techniques to defend against Extraction Attacks focusing on adversarial training and measures to achieve robustness against adaptive adversarial techniques.
6. [Alpaca: A Strong, Replicable Instruction-Following Model](https://crfm.stanford.edu/2023/03/13/alpaca.html) Use of self-instruct to create an equivalent functional model to OpenAI’s ext-davinci-003 model on top of LLaMa.
7. [How Watermarking Can Help Mitigate The Potential Risks Of LLMs?](https://www.kdnuggets.com/2023/03/watermarking-help-mitigate-potential-risks-llms.html) Adding embedding signals into generated text can help mitigate potential risks of plagiarism, misinformation, and abuse in large language models.
