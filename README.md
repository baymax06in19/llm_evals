# LLM Model Evaluation

## Level 1 - Open Benchmark Usage

- Use public benchmarks selectively, prioritizing those that match our needs  
- Since models are often exposed to these benchmarks during training; their performance may not reflect true generalization, but we may use these for initial filtering.

Models on public leaderboards like Hugging Face can be directly compared by adjusting evaluation settings.  
For quantized versions, test results may be available in associated repositories or result tables.  
If high-priority benchmark results aren't available or seem unreliable, or if further adjustments are needed to best fit our use case, local evaluations can be done using the available test data and evaluation methods to get results.  

For example, since instruction-following is an important capability, using evaluation through benchmarks that focus on it could help ensure the model's performance remains within an acceptable performance range.  

**EX:**  
IFEval Benchmark (Instruction-Following Evaluation)  
Capability of Large Language Models (LLMs) is to follow natural language instructions.  
A set of verifiable instructions (25 types - constructed around 500 prompts)  

**ex:**  
- Write in more than 400 words  
- Your entire output should be in JSON format  
- Write a list of the top 10 facts about the UK without using commas.  

Implement a relatively robust verification approach by considering commonly seen variations.  

**Evaluation**  
- `is_followed(resp, inst);` programmatic – rule-based logic.  
- `is_followedloose(resp, inst) = Any is_followed transformt(resp), inst fort = 1, 2, …` also Programmatic but transform the response to avoid false negatives.  

**Results:**  

For further improvement based on the existing test data from a selected benchmark set, we can develop a specialized set of tests for our local LLM models, tailored to our specific system requirements.

---

## Level 2 - Internal Tests

- Some domain-specific simple tests can be done on selected models to check whether the model actually performs well.  
- Benchmark values may not be sufficient without manual validation.  
- This should be a general but impactful set of tests.  
- It can be a small number of cases that we could create using another LLM or human, which would act as a base QA for any model we choose.  
- Evaluations can be done through an LLM or using a predefined set of answers.

**Example:**  

| Test | Evaluation method |
|------|-----------------|
| Generate C++ Code for given task | Reliable LLM*/ programmatic / human |
| Extract some hidden ideas bound up with the full functional context window. | programmatic/human |

- All selected tasks have to be done on incremental hard versions.  
- Assign computationally intensive tasks to assess performance under load  
  **EX:** Text to SQL converter  
- Measure understandability and evaluate how well instructions are followed.  
- Evaluate through generated results.

---

## Level 3 - Application-Specified

### Level 3.1 - Tests Through a Reliable LLM

- Example is about the HR agent  
- Determine typical scenarios that significantly affect agent behavior.  
- Models should never hallucinate when provided with the correct prompt.  
- Including information from the model's own memory into the context will negatively impact the score and should be avoided.

**Test cases to cover up those**

**Method - 1**  
- A set of 10 CVs can be used for the evaluation based on given requirements.  
- The evaluation results and the prompt used to evaluate the local LLM can be submitted to a reliable LLM.  
- This reliable LLM would have predefined guidelines and rules to identify issues such as hallucinations.  
- This reliable LLM* can then identify key issues with the results, such as hallucinations.

**Method - 2**  
- Compare reasons from several LLMs for the same scenario to evaluate internal thinking criteria.  
- Consider missed and gained points during scoring when evaluating an LLM.  
- Involve humans in the scoring process for better evaluation.

**Method - 3**  
- Prepare typical interview questions to interview LLMs.  
- Consider key areas for evaluating requirement fulfillment and necessary information for candidate selection.  
- Check for biases in the LLM's output.  
- Send generated results and queries to a Reliable LLM for performance feedback, simulating an interview scenario.

---

### Level 3.2 - OWUI Arena Mode Evaluations

- To identify the most reliable model, we can use human-in-the-loop evaluation within OWUI.  
- Two unknown models generate outputs for the same set of CVs.  
- Domain-specific human evaluators then review the outputs and select the ones that best align with the given requirements.  
- Since the evaluators are qualified in the relevant field, their judgments reflect true output quality.  

- This evaluation process can be directly used within the OpenWebUI application by allowing users to give thumbs up or down for each model’s output.  
- These interactions are used to compute Elo scores for the models, helping determine which performs better over time.  
- This approach can be applied during the early implementation phase of an agent system to identify the most suitable model based on actual user feedback and domain-specific evaluation.

---

### Level 3.3 - Utilizing a Predefined Dataset

- If we can use real-world application data and results:  
  - A collection of CVs is submitted for a job opening.  
  - The hiring process produces a shortlist of CVs (ground truth).  
  - The model then generates its own shortlist of CVs.  

- We compare how well the model's shortlisted candidates align with the actual ones.  
- If the model correctly identifies at least 80% of the shortlisted candidates, its performance can be considered highly reliable.  
- For the mismatched cases, analysis can reveal whether the model failed or, interestingly, whether the model selected better candidates than the human process — showing cases where the model may outperform human evaluation.

---

## Benchmarks

- **HellaSwag – Evaluate common sense**  
  Focus: Common sense reasoning  
  Tests: Sentence completion requiring real-world knowledge and plausibility judgment  
  [PDF](https://arxiv.org/pdf/1905.07830)  

- **TruthfulQA – Evaluate truthfulness**  
  Focus: Factual correctness  
  Tests: Whether the model gives factually accurate and honest answers  
  [PDF](https://arxiv.org/pdf/2109.07958)  

- **ARC (AI2 Reasoning Challenge) – Evaluate reasoning skills**  
  Focus: Logical and scientific reasoning  
  Tests: Grade-school level science multiple-choice questions  
  [PDF](https://arxiv.org/pdf/1803.05457)  

- **DROP (Discrete Reading Comprehension) – English reading comprehension**  
  Focus: Understanding and reasoning over paragraphs  
  Tests: Extractive QA requiring logical inferences from text  
  [PDF](https://arxiv.org/pdf/1903.00161)  

- **MMMU (Massive MultiModal Understanding) – Multimodal capabilities**  
  Focus: Vision and language integration  
  Tests: Multimodal reasoning across scientific and real-world topics  
  [PDF](https://arxiv.org/pdf/2311.16502)  

- **MMLU (Massive Multitask Language Understanding) – Evaluate multitask performance**  
  Focus: General academic and professional knowledge  
  Tests: 57+ subjects including STEM, law, medicine, humanities  
  [PDF](https://arxiv.org/pdf/2009.03300)  

- **IFEval (Instruction Following Evaluation) – Format alignment**  
  Focus: Instruction-following accuracy  
  Tests: Model's ability to follow explicit formatting or layout instructions  
  [PDF](https://arxiv.org/pdf/2311.07911)  

- **Big Bench Hard (BBH) – Evaluate reasoning and world knowledge**  
  Focus: Language understanding, mathematical reasoning, and common sense  
  Tests: Diverse set of challenging tasks for LLMs across domains  

- **MATH (Mathematics Aptitude Test of Heuristics) – Mathematical reasoning**  
  Focus: High school level competition problems  
  Tests: Complex algebra, geometry, advanced calculus, and proofs  

- **GPQA (Graduate-Level Google-Proof Q&A) – Expert-level science**  
  Focus: PhD-level scientific knowledge  
  Tests: Multiple-choice questions in chemistry, biology, and physics  

- **MuSR (Multistep Soft Reasoning) – Long-context reasoning**  
  Focus: Reasoning and understanding over extended texts  
  Tests: Language understanding, multi-step reasoning, and long-context analysis  

- **MMLU-Pro (Professional Massive Multitask Language Understanding) – Professional-level multitasking**  
  Focus: Domain-specific expertise  
  Tests: Expert-reviewed multiple-choice questions in fields like: Medicine and healthcare, Law and ethics, Engineering, Mathematics  
  [PDF](https://www.researchgate.net/publication/381152925_MMLU-Pro_A_More_Robust_and_Challenging_Multi-Task_Language_Understanding_Benchmark)  

**Quick summary of IFEval benchmark paper**  
- IFEval (Instruction Following Evaluation) – Format alignment  
- Capability of Large Language Models (LLMs) is to follow natural language instructions  
- Set of verifiable instructions (25 types - constructed around 500 prompts)  
- Write in more than 400 words  
- Your entire output should be in JSON output  

**Following shows the type of verifications used within this benchmark:**  
- Implement a relatively robust verification approach by considering commonly seen variations.  
- **Evaluation**  
  - `is_followed(resp, inst)` -> programmatic – rule-based logic.  
  - `is_followedloose(resp, inst) = Any is_followed transformt(resp), inst fort = 1, 2, …` also Programmatic but transform the response to avoid false negatives.  

**Example results:**
