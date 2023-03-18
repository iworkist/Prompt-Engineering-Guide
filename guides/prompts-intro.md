# 프롬프트 소개

프롬프트 엔지니어링은 다양한 애플리케이션과 연구 주제에 언어 모델(LM)을 효율적으로 사용할 수 있도록 프롬프트를 개발하고 최적화하는 비교적 새로운 분야입니다. 프롬프트 엔지니어링 기술은 대규모 언어 모델(LLM)의 기능과 한계를 더 잘 이해하는 데 도움이 됩니다. 연구자들은 프롬프트 엔지니어링을 사용하여 질문 답변 및 산술 추론과 같은 일반적이고 복잡한 다양한 작업에서 LLM의 역량을 향상시킵니다. 개발자는 프롬프트 엔지니어링을 사용하여 LLM 및 기타 도구와 인터페이스하는 강력하고 효과적인 프롬프트 기술을 설계합니다.

이 가이드에서는 표준 프롬프트의 기본 사항을 다루며 프롬프트를 사용하여 대규모 언어 모델(LLM)과 상호 작용하고 지시하는 방법에 대한 대략적인 아이디어를 제공합니다.

모든 예제는 달리 명시되지 않는 한 `텍스트-davinci-003`(OpenAI의 플레이그라운드 사용)으로 테스트되었습니다. 기본 구성(예: `temperature=0.7` 및 `top-p=1`)을 사용합니다.

Topic:
- [Basic Prompts](#basic-prompts)
- [A Word on LLM Settings](#a-word-on-llm-settings)
- [Standard Prompts](#standard-prompts)
- [Prompt Elements](#elements-of-a-prompt)
- [General Tips for Designing Prompts](#general-tips-for-designing-prompts)

---

## 기본 프롬프트

프롬프트를 통해 이미 많은 것을 얻을 수 있지만 결과의 품질은 얼마나 많은 정보를 제공하느냐에 따라 달라집니다. 프롬프트에는 모델에 전달할 지시 사항이나 질문과 같은 정보를 포함할 수 있으며 입력 또는 예제와 같은 기타 세부 정보도 포함할 수 있습니다.

다음은 간단한 프롬프트의 기본 예입니다:

*Prompt*
```
The sky is
```

*Output:*
```
blue

The sky is blue on a clear day. On a cloudy day, the sky may be gray or white.
```

보시다시피 언어 모델은 "하늘은(The sky is)"이라는 문맥에 맞는 문자열의 연속을 출력합니다. 출력은 예상치 못한 것이거나 수행하려는 작업과 거리가 멀 수도 있습니다.

이 기본 예는 구체적으로 무엇을 달성하고자 하는지에 대한 더 많은 컨텍스트나 지침을 제공해야 할 필요성을 강조합니다.

조금 더 개선해 봅시다:

*Prompt:*
```
Complete the sentence: 

The sky is
```

*Output:*

```
 so  beautiful today.
```

이제 좀 나아졌나요? 모델에 문장을 완성하라고 지시했더니 모델에 지시한 대로("문장을 완성하라:", "Complete the sentence:") 결과가 훨씬 더 좋아 보입니다. 모델에 작업을 수행하도록 지시하는 최적의 프롬프트를 설계하는 이러한 접근 방식을 프롬프트 엔지니어링이라고 합니다.

위의 예는 오늘날 LLM으로 가능한 것을 보여주는 기본적인 예시입니다. 오늘날의 LLM은 텍스트 요약부터 수학적 추론, 코드 생성에 이르기까지 모든 종류의 고급 작업을 수행할 수 있습니다.

---
## LLM 설정에 대해 한마디

프롬프트로 작업할 때는 API를 통해 또는 직접 LLM과 상호 작용하게 됩니다. 몇 가지 매개변수를 구성하여 프롬프트에 대해 다양한 결과를 얻을 수 있습니다.

**온도** - 간단히 말해, 온도가 낮을수록 항상 가장 높은 확률의 다음 토큰이 선택된다는 의미에서 결과가 더 결정적입니다. 온도를 높이면 무작위성이 높아져 더 다양하고 창의적인 결과가 나올 수 있습니다. 기본적으로 다른 가능한 토큰의 가중치를 높이는 것입니다. 애플리케이션 측면에서, 사실에 기반한 QA와 같은 작업에는 온도를 낮게 설정하여 보다 사실적이고 간결한 응답을 유도할 수 있습니다. 시 창작이나 기타 창의적인 작업의 경우 온도를 높이는 것이 유리할 수 있습니다.

**Top_p** - 마찬가지로 핵 샘플링이라고 하는 온도 샘플링 기법인 top_p를 사용하면 모델이 응답을 생성할 때 얼마나 결정론적인지 제어할 수 있습니다. 정확하고 사실적인 답변을 찾고 있다면 이 값을 낮게 유지하세요. 보다 다양한 응답을 원한다면 더 높은 값으로 높입니다.

일반적으로 두 가지 모두 변경하지 말고 한 가지만 변경하는 것이 좋습니다.

몇 가지 기본 예제를 시작하기 전에 사용 중인 LLM 버전에 따라 결과가 달라질 수 있다는 점을 염두에 두세요.

---
## 표준 프롬프트

위에서 매우 간단한 프롬프트를 시도해 보았습니다. 표준 프롬프트의 형식은 다음과 같습니다:

```
<Question>?
```
 
이 데이터는 다음과 같이 많은 QA 데이터 세트에서 표준으로 사용되는 QA 형식으로 포맷할 수 있습니다:

```
Q: <Question>?
A: 
```

위의 표준 형식이 주어질때, 널리 사용되고 있는 효과적인 프롬프트 기법은 퓨샷(few-shot) 프롬프트라고 합니다. 퓨샷 프롬프트의 형식은 다음과 같습니다:


```
<Question>?
<Answer>

<Question>?
<Answer>

<Question>?
<Answer>

<Question>?

```

QA 형식 버전은 다음과 같을 것이라는 것을 이미 짐작할 수 있습니다:


```
Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A:
```

반드시 QA 형식을 사용해야 하는 것은 아닙니다. 형식은 당면한 작업에 따라 다릅니다. 예를 들어 간단한 분류 작업을 수행하고 다음과 같이 작업을 보여주는 예시를 제공할 수 있습니다:

*Prompt:*
```
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
```

*Output:*
```
Negative
```

몇 개의 예제만 주어져도 언어 모델이 작업을 학습할 수 있는 기능인 상황에 맞는 학습이 가능합니다. 향후 가이드에서 이 기능에 대해 더 자세히 살펴볼 예정입니다.

---
## Elements of a Prompt

As we cover more and more examples and applications that are possible with prompt engineering, you will notice that there are certain elements that make up a prompt. 

A prompt can contain any of the following components:

**Instruction** - a specific task or instruction you want the model to perform

**Context** - can involve external information or additional context that can steer the model to better responses

**Input Data** - is the input or question that we are interested to find a response for

**Output Indicator** - indicates the type or format of output.

Not all the components are required for a prompt and the format depends on the task at hand. We will touch on more concrete examples in upcoming guides.

---
## General Tips for Designing Prompts

Here are some tips to keep in mind while you are designing your prompts:


### Start Simple
As you get started with designing prompts, you should keep in mind that it is really an iterative process that requires lot of experimentation to get optimal results. Using a simple playground like OpenAI's or Cohere's is a good starting point. 

You can start with simple prompts and keep adding more elements and context as you aim for better results. Versioning your prompt along the way is vital for this reason. As we read the guide you will see many examples where specificity, simplicity, and conciseness will often give you better results.

When you have big task that involves many different subtasks, you can try to break down the task into simpler subtasks and keep building up as you get better results. This avoids adding too much complexity to the prompt design process at the beginning.

### The Instruction
You can design effective prompts for various simple tasks by using commands to instruct the model what you want to achieve such as "Write", "Classify", "Summarize", "Translate", "Order", etc.

Keep in mind that you also need to experiment a lot so see what works best. Trying different instructions with different keywords, context, and data and see what works best for your particular use case and task. Usually, the more specific and relevant the context is to the task you are trying to perform, the better. We will touch on the importance of sampling and adding more context in the upcoming guides.

Others recommend that instructions are placed at the beginning of the prompt. It's also recommended that some clear separator like "###" is used to separate the instruction and context. 

For instance:

*Prompt:*
```
### Instruction ###
Translate the text below to Spanish:

Text: "hello!"
```

*Output:*
```
¡Hola!
```

### Specificity
Be very specific about the instruction and task you want the model to perform. The more descriptive and detailed the prompt is, the better the results. This is particularly important when you have a desired outcome or style of generation you are seeking. There aren't specific tokens or keywords that lead to better results. It's more important to have a good format and descriptive prompt. In fact, providing examples in the prompt is very effective to get desired output in specific formats. 

When designing prompts you should also keep in mind the length of the prompt as there are limitations regarding how long this can be. Thinking about how specific and detailed you should be is something to consider. Too many unnecessary details is not necessarily a good approach. The details should be relevant and contribute to the task at hand. This is something you will need to experiment with a lot. We encourage a lot of experimentation and iteration to optimize prompts for your applications.

As an example, let's try a simple prompt to extract specific information from a piece of text.

*Prompt:*
```
Extract the name of places in the following text. 

Desired format:
Place: <comma_separated_list_of_company_names>

Input: "Although these developments are encouraging to researchers, much is still a mystery. “We often have a black box between the brain and the effect we see in the periphery,” says Henrique Veiga-Fernandes, a neuroimmunologist at the Champalimaud Centre for the Unknown in Lisbon. “If we want to use it in the therapeutic context, we actually need to understand the mechanism.""
```

*Output:*
```
Place: Champalimaud Centre for the Unknown, Lisbon
```

Input text is obtained from [this Nature article](https://www.nature.com/articles/d41586-023-00509-z).

### Avoid Impreciseness

Given the tips above about being detailed and improving format, it's easy to fall into the trap of wanting to be too clever about prompts and potentially creating imprecise descriptions. It's often better to be specific and direct. The analogy here is very similar to effective communication -- the more direct, the more effective the message gets across.

For example, you might be interested in learning the concept of prompt engineering. You might try something like:

```
Explain the concept prompt engineering. Keep the explanation short, only a few sentences, and don't be too descriptive.
```

It's not clear from the prompt above how many sentences to use and what style. You might still somewhat get good responses with the above prompts but the better prompt would be one that is very specific, concise, and to the point. Something like:

```
Use 2-3 sentences to explain the concept of prompt engineering to a high school student.
```

### To do or not to do?
Another common tip when designing prompts is to avoid saying what not to do but say what to do instead. This encourages more specificity and focus on the details that lead to good responses from the model.

Here is an example of movie recommendation chatbot failing at exactly what I don't want it to do because of how I wrote the instruction -- focusing on what not to do. 

*Prompt:*
```
The following is an agent that recommends movies to a customer. DO NOT ASK FOR INTERESTS. DO NOT ASK FOR PERSONAL INFORMATION.

Customer: Please recommend a movie based on my interests.
Agent: 
```

*Output:*
```
Sure, I can recommend a movie based on your interests. What kind of movie would you like to watch? Do you prefer action, comedy, romance, or something else?
```

Here is a better prompt:

*Prompt:*
```
The following is an agent that recommends movies to a customer. The agent is responsible to recommend a movie from the top global trending movies. It should refrain from asking users for their preferences and avoid asking for personal information. If the agent doesn't have a movie to recommend, it should respond "Sorry, couldn't find a movie to recommend today.".

Customer: Please recommend a movie based on my interests.
Agent:
```

*Output:*
```
Sorry, I don't have any information about your interests. However, here's a list of the top global trending movies right now: [list of movies]. I hope you find something you like!
```

Some of the examples above were adopted from the ["Best practices for prompt engineering with OpenAI API" article.](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)


---
[Next Section (Basic Prompting)](./prompts-basic-usage.md)
