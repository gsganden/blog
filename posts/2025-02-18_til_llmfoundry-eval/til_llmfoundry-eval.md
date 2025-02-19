---
title: "TIL: How LLM Foundry Evaluates Performance On Open-Ended Math Problems"
author: "Greg Gandenberger"
date: 2025-02-18
categories: [ml, llms, evals, math-verify, llm-foundry]
---

::: {.callout-note}
This is a [TIL](https://dev.to/jbranchaud/how-i-built-a-learning-machine-45k9) ("Today I Learned") post. I expect it to be useful to my future self and maybe to others, but it is meant to be a quick, informal way to capture something I learned rather than a polished presentation.
:::

# Context

I am contributing to a research project on [performance-efficient fine tuning of LLMs](posts/2025-02-14_fine-tuning-on-regression-task/fine_tuning_on_regression_tasks.html). That project involves fine tuning LLMs on math problems.

# Problem

**Evaluating LLM performance on open-ended math problems is difficult because LLMs can generate arbitrary text.** Checking an LLM's answer to an open-ended (as opposed to multiple-choice, for instance) math problem against ground truth requires extracting its answer from the generated text and then accounting for issues such as rounding (e.g. 0.33 vs. 0.333) and alternative representations (e.g. 0.33 vs. 1/3). HuggingFace recently released the [Math-Verify library](https://github.com/huggingface/math-verify) to address these issues and [showed that it makes a massive difference](https://huggingface.co/blog/math_verify_leaderboard) to LLM math leaderboards.

In the meantime, we have been using [LLM Foundry](https://github.com/mosaicml/llm-foundry) for model development. **LLM Foundry has its own procedure for evaluating LLM math performance. By default, it simply [checks whether LLM's output starts with an exact match to one of a set of provided answers](https://github.com/mosaicml/llm-foundry/blob/e03b23d9342471a2464a6500c509bd657381d32c/llmfoundry/eval/metrics/nlp.py#L198-L201)** after [some light post-processing](https://github.com/mosaicml/llm-foundry/blob/e03b23d9342471a2464a6500c509bd657381d32c/llmfoundry/eval/metrics/nlp.py#L133) to "lower text and remove punctuation, articles and extra whitespace".

**This approach does not always give the desired result.** For instance, in this case the model seems to be generating multiple-choice options rather than answering the question. The first of those options matches the ground truth answer, so this evaluation procedure counts it as correct even though the model did not commit to it as its actual answer. (All of the examples below come from [SmolLM2-135M](https://huggingface.co/HuggingFaceTB/SmolLM2-135M) on [GSM8K](https://github.com/mosaicml/llm-foundry/blob/main/scripts/eval/local_data/symbolic_problem_solving/gsm8k.jsonl). SmolLM2-135M is a base language model and is tiny by LLM standards, so it is not expected to perform well on these problems.)

::: {.callout-example}
- **Question:** When Freda cooks canned tomatoes into sauce, they lose half their volume. Each 16 ounce can of tomatoes that she uses contains three tomatoes. Freda’s last batch of tomato sauce made 32 ounces of sauce. How many tomatoes did Freda use?
- **Model Output:** "12\\nB: 16\\nC: 24\\nD: 32"
- **Ground Truth:** "12"
- **Evaluation:** Correct
:::

Another case leads to a comedy of errors: the model gets a numerically correct answer for entirely wrong reasons, and the evaluation procedure counts it as correct for the wrong reason.

::: {.callout-example}
- **Question:** Uriah's book bag is getting too heavy for him. He needs to remove 15 pounds from it. His comic books weigh 1/4 pound each and his toys weigh 1/2 pound each. If he removes 30 comic books, how many toys does he need to remove?
- **Model Output:** "15 pounds x 1/4 pounds x 1/2 pounds = 15 pounds."
- **Ground Truth:** "15"
- **Evaluation:** Correct
:::

In another case, the model gives the right answer, but the evaluation procedure counts it as incorrect because it does not put that answer at the start of its output.

::: {.callout-example}
- **Question:** Mico and Marco wanted to get to know each other. They realized that the sum of their ages is 20. What will be the sum of their ages in 10 years?
- **Model Output:** "20 + 20 = 40"
- **Ground Truth:** "40"
- **Evaluation:** Incorrect
:::

# Next Steps

We should be able to do better. LLM Foundry supports specifing a "metric" to use to evaluate the model's performance. The [default](https://github.com/mosaicml/llm-foundry/blob/e03b23d9342471a2464a6500c509bd657381d32c/llmfoundry/utils/builders.py#L587-L590) metric for "generation_task_with_answers" (which is at least how [scripts/eval/yamls/tasks_v0.3.yaml](https://github.com/mosaicml/llm-foundry/blob/e03b23d9342471a2464a6500c509bd657381d32c/scripts/eval/yamls/tasks_v0.3.yaml#L6) treats GSM8K) is [`InContextLearningGenerationExactMatchAccuracy`](https://github.com/mosaicml/llm-foundry/blob/e03b23d9342471a2464a6500c509bd657381d32c/llmfoundry/eval/metrics/nlp.py#L92). We could define a custom metric that uses `Math-Verify` to extract the LLM's answer and compare it to ground truth more accurately.