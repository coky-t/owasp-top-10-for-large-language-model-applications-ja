## 訓練データポイズニング (Training Data Poisoning)

**説明:**

あらゆる機械学習アプローチの出発点は訓練データ、つまり単なる "raw text" です。高い能力 (言語や世界の知識など) を得るには、このテキストは幅広いドメイン、ジャンル、言語にまたがる必要があります。大規模言語モデルはディープニューラルネットワークを使用して、訓練データから学習したパターンに基づいて出力を生成します。

訓練データポイズニングはモデルのセキュリティ、有効性、倫理的行動を損なう可能性のある脆弱性、バックドア、バイアスを導入するためにデータやファインチューニングプロセスを操作することを指します。汚染された情報はユーザーに公開されたり、パフォーマンス低下、ダウンストリームソフトウェア悪用、風評被害などの他のリスクを引き起こす可能性があります。たとえユーザーが問題のある AI 出力に不信感を抱いたとしても、モデル機能の低下やブランドの評判を損なう可能性などのリスクが残ります。

データポイズニングは完全性攻撃とみなされ、訓練データの改竄が正しい予測を出力するモデルの能力に影響を与えます。当然ながら、外部データソースはモデル作成者がデータを管理できず、コンテンツにバイアス、改竄された情報、不適切なコンテンツが含まれていないという高いレベルの信頼性がないため、リスクがより高くなります。

**脆弱性の一般的な例:**

1. A malicious actor, or a competitor brand intentionally creates inaccurate or malicious documents which are targeted at a model’s training data.
   - The victim model trains using falsified information which is reflected in outputs of generative AI prompts to it's consumers.
2. A model is trained using data which has not been verified by its source, origin or content.
3. The model itself when situated within infrastructure has unrestricted access or inadequate sandboxing to gather datasets to be used as training data which has negative influence on outputs of generative AI prompts as well as loss of control from a management perspective.

*Whether a developer, client or general consumer of the LLM, it is important to understand the implications of how this vulnerability could reflect risks within your LLM application when interacting with a non-proprietary LLM.*

**防止方法:**

1. Verify the supply chain of the training data, especially when sourced externally as well as maintaining attestations, similar to the "SBOM" (Software Bill of Materials) methodology.
2. Verify the correct legitimacy of targeted data sources and data contained obtained during both the training and fine-tuning stages.
3. Verify your use-case for the LLM and the application it will integrate to. Craft different models via separate training data or fine-tuning for different use-cases to create a more granular and accurate generative AI output as per it's defined use-case.
4. Ensure sufficient sandboxing is present to prevent the model from scraping unintended data sources which could hinder the machine learning output.
5. Use strict vetting or input filters for specific training data or categories of data sources to control volume of falsified data. Data sanitization, with techniques such as statistical outlier detection and anomaly detection methods to detect and remove adversarial data from potentially being fed into the fine-tuning process.
6. Adversarial robustness techniques such as federated learning and constraints to minimize the effect of outliers or adversarial training to be vigorous against worst-case perturbations of the training data.
   1. An "MLSecOps" approach could be to include adversarial robustness to the training lifecycle with the auto poisoning technique.
   2. An example repository of this would be [Autopoison](https://github.com/azshue/AutoPoison) testing, including both attacks such as Content Injection Attacks (“how to inject your brand into the LLM responses”) and Refusal Attacks (“always making the model refuse to respond”) that can be accomplished with this approach.
7. Testing and Detection, by measuring the loss during the training stage and analyzing trained models to detect signs of a poisoning attack by analyzing model behavior on specific test inputs.
   1. Monitoring and alerting on number of skewed responses exceeding a threshold.
   2. Use of a human loop to review responses and auditing.
   3. Implement dedicated LLM's to benchmark against undesired consequences and train other LLM's using [reinforcement learning techniques](https://wandb.ai/ayush-thakur/Intro-RLAIF/reports/An-Introduction-to-Training-LLMs-Using-Reinforcement-Learning-From-Human-Feedback-RLHF---VmlldzozMzYyNjcy).
   4. Perform LLM-based [red team exercises](https://www.anthropic.com/index/red-teaming-language-models-to-reduce-harms-methods-scaling-behaviors-and-lessons-learned) or [LLM vulnerability scanning](https://github.com/leondz/garak) into the testing phases of the LLM's lifecycle.

**攻撃シナリオの例:**

1. The LLM generative AI prompt output can mislead users of the application which can lead to biased opinions, followings or even worse, hate crimes etc.
2. If the training data is not correctly filtered and|or sanitized, a malicious user of the application may try to influence and inject toxic data into the model for it to adapt to the biased and false data.
3. A malicious actor or competitor intentionally creates inaccurate or malicious documents which are targeted at a model’s training data in which is training the model at the same time based on inputs. The victim model trains using this falsified information which is reflected in outputs of generative AI prompts to it's consumers.
4. The vulnerability [Prompt Injection](https://github.com/OWASP/www-project-top-10-for-large-language-model-applications/blob/main/1_0_vulns/PromptInjection.md) could be an attack vector to this vulnerability if insufficient sanitization and filtering is performed when clients of the LLM application input is used to train the model. I.E, if malicious or falsified data is input to the model from a client as part of a prompt injection technique, this could inherently be portrayed into the model data.

**参考情報リンク:**

1. [Stanford Research Paper:CS324](https://stanford-cs324.github.io/winter2022/lectures/data/) Talks about the data behind large language models.
2. [How data poisoning attacks corrupt machine learning models](https://www.csoonline.com/article/3613932/how-data-poisoning-attacks-corrupt-machine-learning-models.html) A well explained high-level overview explaining data poisoning attacks and why you should care.
3. [MITRE ATLAS (framework) Tay Poisoning](https://atlas.mitre.org/studies/AML.CS0009/) The MITRE ATLAS framework published a case study on the Microsoft "Tay" chatbot project which portrays how non-proficient red team skills and efforts from individuals able to access the model were able to train it with poisoned data which ultimately led to other vulnerabilities and concerns.
4. [PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news](https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/) An article detailing how one can surgically modify an open-source model, and upload it to a provider to make it spread misinformation while being undetected by standard benchmarks.
5. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf/) Evidence of how a prompt injection ([LLM01:2023 - Prompt Injections](https://owasp.org/www-project-top-10-for-large-language-model-applications/descriptions/Prompt_Injection.html)) technique could also lead to Training Data Poisoning during scraping.
6. [Backdoor Attacks on Language Models](https://towardsdatascience.com/backdoor-attacks-on-language-models-can-we-trust-our-models-weights-73108f9dcb1f). Can We Trust Our Model’s Weights? A primer in data poisoning and backdoor on Language Models.
7. [Poisoning Language Models During Instruction](https://arxiv.org/abs/2305.00944) Tuning Research paper demonstrating Data Poisoning in the model fine tuning process.
8. [FedMLSecurity:arXiv:2306.04959](https://arxiv.org/abs/2306.04959) A Benchmark for attacks and defenses in federated learning and LLMs: Research demonstrating the effectiveness of Federated Learning against data poisoning attacks on LLMs.
9. [The poisoning of ChatGPT](https://softwarecrisis.dev/letters/the-poisoning-of-chatgpt/) A controversial but informative article on the risks of Data Poisoning in LLMs.
