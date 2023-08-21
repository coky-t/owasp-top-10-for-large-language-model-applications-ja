## 過度の依存 (Overreliance)

**説明:**

過度の依存はシステムや人々が十分な監視なしに意思決定やコンテンツ生成を LLM に依存している場合に発生します。LLM は創造的で有益なコンテンツを生成できる一方で、事実と異なる、不適切、あるいは安全でないコンテンツを生成することもあります。これは幻覚 (hallucination) または作話 (confabulation) とよばれ、誤った情報、誤った伝達、法的問題、風評被害をもたらす可能性があります。

LLM が生成したソースコードは気づかれないうちにセキュリティ上の脆弱性を引き起こす可能性があります。これはアプリケーションの運用上の安全性とセキュリティに重大なリスクをもたらします。これらのリスクは以下のような厳格なレビュープロセスの重要性を示しています。

- 監視
- 継続的なバリデーションメカニズム
- リスクに関する免責事項

**脆弱性の一般的な例:**

1. LLM provides inaccurate information as a response, causing misinformation. 
2. LLM produces logically incoherent or nonsensical text that, while grammatically correct, doesn't make sense.
3. LLM melds information from varied sources, creating misleading content.
4. LLM suggests insecure or faulty code, leading to vulnerabilities when incorporated into a software system.
5. Failure of provider to appropriately communicate the inherent risks to end users, leading to potential harmful consequences.

**防止方法:**

1. Regularly monitor and review the LLM outputs. Use self-consistency or voting techniques to filter out inconsistent text. Comparing multiple model responses for a single prompt can better judge the quality and consistency of output.
2. Cross-check the LLM output with trusted external sources. This additional layer of validation can help ensure the information provided by the model is accurate and reliable.
3. Enhance the model with fine-tuning or embeddings to improve output quality. Generic pre-trained models are more likely to produce inaccurate information compared to tuned models in a partiular domain.  Techniques such as prompt engineering, parameter efficient tuning (PET), full model tuning, and chain of thought prompting can be employed for this purpose.
4. Implement automatic validation mechanisms that can cross-verify the generated output against known facts or data. This can provide an additional layer of security and mitigate the risks associated with hallucinations.
5. Break down complex tasks into manageable subtasks and assign them to different agents. This not only helps in managing complexity, but it also reduces the chances of hallucinations as each agent can be held accountable for a smaller task.
6. Clearly communicate the risks and limitations associated with using LLMs. This includes potential for information inaccuracies, and other risks. Effective risk communication can prepare users for potential issues and help them make informed decisions.
7. Build APIs and user interfaces that encourage responsible and safe use of LLMs. This can involve measures such as content filters, user warnings about potential inaccuracies, and clear labeling of AI-generated content.
8. When using LLMs in development environments, establish secure coding practices and guidelines to prevent the integration of possible vulnerabilities.

**攻撃シナリオの例:**

1. A news organization heavily uses an AI model to generate news articles. A malicious actor exploits this over-reliance, feeding the AI misleading information, causing the spread of disinformation. The AI unintentionally plagiarizes content, leading to copyright issues and decreased trust in the organization.
2. A software development team utilizes an AI system like Codex to expedite the coding process. Over-reliance on the AI's suggestions introduces security vulnerabilities into the application due to insecure default settings or recommendations inconsistent with secure coding practices.
3. A software development firm uses an LLM to assist developers. The LLM suggests a non-existent code library or package, and a developer, trusting the AI, unknowingly integrates a malicious package into the firm's software. This highlights the importance of cross-checking AI suggestions, especially when involving third-party code or libraries.

**参考情報リンク:**

1. [Understanding LLM Hallucinations](https://towardsdatascience.com/llm-hallucinations-ec831dcd7786)
2. [How Should Companies Communicate the Risks of Large Language Models to Users?](https://techpolicy.press/how-should-companies-communicate-the-risks-of-large-language-models-to-users/)
3. [A news site used AI to write articles. It was a journalistic disaster](https://www.washingtonpost.com/media/2023/01/17/cnet-ai-articles-journalism-corrections/)
4. [AI Hallucinations: Package Risk](https://vulcan.io/blog/ai-hallucinations-package-risk)
5. [How to Reduce the Hallucinations from Large Language Models](https://thenewstack.io/how-to-reduce-the-hallucinations-from-large-language-models/)
6. [Practical Steps to Reduce Hallucination](https://newsletter.victordibia.com/p/practical-steps-to-reduce-hallucination)
