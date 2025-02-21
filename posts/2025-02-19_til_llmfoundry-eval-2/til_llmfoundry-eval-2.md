---
title: "TIL: An LLM Foundry Metric Must Have \"Accuracy\" in Its Name"
author: "Greg Gandenberger"
date: 2025-02-19
categories: [ml, llms, evals, llm-foundry]
---

::: {.callout-note}
This is a [TIL](https://dev.to/jbranchaud/how-i-built-a-learning-machine-45k9) ("Today I Learned") post. I expect it to be useful to my future self and maybe to others, but it is meant to be a quick, informal way to capture something I learned rather than a polished presentation.
:::

My [last post](/posts/2025-02-18_til_llmfoundry-eval/til_llmfoundry-eval.md) described limitations of LLM Foundry's default evaluation procedure for open-ended math problems and suggested that we could do better by creating a custom metric that uses `Math-Verify`.

I encountered a "gotcha" in the process of creating that custom metric: the metric must have "Accuracy" in its name. Otherwise LLM Foundry's evaluation script [skips it](https://github.com/mosaicml/llm-foundry/blob/08702d2a99da1548deac45e26dbf1b34438fe1d4/llmfoundry/command_utils/eval.py#L413-L414)!

I created [this GitHub issue](https://github.com/mosaicml/llm-foundry/issues/1722) to track this problem. I hope that we can eliminate this behavior, or at least make it more obvious.