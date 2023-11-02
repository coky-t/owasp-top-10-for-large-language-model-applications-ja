## モデルサービス拒否 (Model Denial of Service)

**説明:**

攻撃者は非常に多くのリソースを消費する方法で LLM とやり取りし、その結果、攻撃者や他のユーザーのサービス品質が低下し、高額なリソースコストが発生する可能性があります。さらに、新たなセキュリティ上な重大な懸念は攻撃者が LLM のコンテキストウィンドウを妨害または操作する可能性があることです。この問題はさまざまなアプリケーションでの LLM の使用の増加、集中的なリソース使用、ユーザー入力の予測不可能性、およびこの脆弱性に関する開発者の一般的な意識の低さのため、よりクリティカルになってきています。LLM では、コンテキストウィンドウはモデルが管理できるテキストの最大長を表し、入力と出力の両方をカバーします。これはモデルが理解できる言語パターンの複雑さと任意の時点で処理できるテキストのサイズを決定する LLM の重要な特定です。コンテキストウィンドウのサイズはモデルのアーキテクチャによって定義され、モデル間で異なる可能性があります。

**脆弱性の一般的な例:**

1. Posing queries that lead to recurring resource usage through high-volume generation of tasks in a queue, e.g. with LangChain or AutoGPT.
2. Sending queries that are unusually resource-consuming, perhaps because they use unusual orthography or sequences.
3. Continuous input overflow: An attacker sends a stream of input to the LLM that exceeds its context window, causing the model to consume excessive computational resources.
4. Repetitive long inputs: The attacker repeatedly sends long inputs to the LLM, each exceeding the context window.
5. Recursive context expansion: The attacker constructs input that triggers recursive context expansion, forcing the LLM to repeatedly expand and process the context window.
6. Variable-length input flood: The attacker floods the LLM with a large volume of variable-length inputs, where each input is carefully crafted to just reach the limit of the context window. This technique aims to exploit any inefficiencies in processing variable-length inputs, straining the LLM and potentially causing it to become unresponsive.

**防止方法:**

1. Implement input validation and sanitization to ensure user input adheres to defined limits and filters out any malicious content.
2. Cap resource use per request or step, so that requests involving complex parts execute more slowly.
3. Enforce API rate limits to restrict the number of requests an individual user or IP address can make within a specific timeframe.
4. Limit the number of queued actions and the number of total actions in a system reacting to LLM responses.
5. Continuously monitor the resource utilization of the LLM to identify abnormal spikes or patterns that may indicate a DoS attack.
6. Set strict input limits based on the LLM's context window to prevent overload and resource exhaustion.
7. Promote awareness among developers about potential DoS vulnerabilities in LLMs and provide guidelines for secure LLM implementation.


**攻撃シナリオの例:**

1. An attacker repeatedly sends multiple requests to a hosted model that are difficult and costly for it to process, leading to worse service for other users and increased resource bills for the host.
2. A piece of text on a webpage is encountered while an LLM-driven tool is collecting information to respond to a benign query. This leads to the tool making many more web page requests, resulting in large amounts of resource consumption.
3. An attacker continuously bombards the LLM with input that exceeds its context window. The attacker may use automated scripts or tools to send a high volume of input, overwhelming the LLM's processing capabilities. As a result, the LLM consumes excessive computational resources, leading to a significant slowdown or complete unresponsiveness of the system.
4. An attacker sends a series of sequential inputs to the LLM, with each input designed to be just below the context window's limit. By repeatedly submitting these inputs, the attacker aims to exhaust the available context window capacity. As the LLM struggles to process each input within its context window, system resources become strained, potentially resulting in degraded performance or a complete denial of service.
5. An attacker leverages the LLM's recursive mechanisms to trigger context expansion repeatedly. By crafting input that exploits the recursive behavior of the LLM, the attacker forces the model to repeatedly expand and process the context window, consuming significant computational resources. This attack strains the system and may lead to a DoS condition, making the LLM unresponsive or causing it to crash.
6. An attacker floods the LLM with a large volume of variable-length inputs, carefully crafted to approach or reach the context window's limit. By overwhelming the LLM with inputs of varying lengths, the attacker aims to exploit any inefficiencies in processing variable-length inputs. This flood of inputs puts excessive load on the LLM's resources, potentially causing performance degradation and hindering the system's ability to respond to legitimate requests.



**参考情報リンク:**

1. [LangChain max_iterations](https://twitter.com/hwchase17/status/1608467493877579777): demo + fix of this vulnerability in LangChain.
2. [Sponge Examples: Energy-Latency Attacks on Neural Networks](https://arxiv.org/abs/2006.03463): POC
3. [OWASP DOS Attack](https://owasp.org/www-community/attacks/Denial_of_Service)
4. [Learning From Machines: Know Thy Context](https://lukebechtel.com/blog/lfm-know-thy-context)
