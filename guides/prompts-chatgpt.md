# ChatGPT 프롬프트 엔지니어링

이 섹션에서는 팁, 응용 프로그램, 제한 사항, 논문 및 추가 읽기 자료를 포함하여 ChatGPT의 최신 프롬프트 엔지니어링 기술에 대해 설명합니다.

**이 섹션은 현재 개발 중입니다.

주제
- [ChatGPT 소개](#chatgpt-introduction)
- [대화 과제 검토하기](#reviewing-the-conversation-task)
- [ChatGPT로 대화하기](#conversations-with-chatgpt)
- [파이썬 노트북](#python-notebooks)

---
## ChatGPT 소개

ChatGPT는 대화형 인터랙션이 가능한 새로운 모델 [OpenAI로 학습](https://openai.com/blog/chatgpt)입니다. 이 모델은 대화의 맥락에서 적절한 응답을 제공하기 위해 프롬프트의 지침을 따르도록 학습되었습니다. ChatGPT는 질문에 답하고, 레시피를 제안하고, 특정 스타일로 가사를 작성하고, 코드를 생성하는 등의 작업에 도움을 줄 수 있습니다.

ChatGPT는 인간 피드백을 통한 강화 학습(RLHF)을 사용하여 학습됩니다. 이 모델은 이전 GPT 반복보다 훨씬 더 성능이 뛰어나지만(또한 유해하고 진실하지 않은 출력을 줄이도록 훈련되었지만) 여전히 한계가 있습니다. 구체적인 예를 통해 몇 가지 기능과 한계를 살펴보겠습니다. 

여기](https://chat.openai.com)에서 ChatGPT의 리서치 프리뷰를 사용할 수 있지만, 아래 예제에서는 OpenAI Playground의 '채팅' 모드를 사용하겠습니다.

---
## 대화 과제 검토하기

이전 가이드](https://github.com/dair-ai/Prompt-Engineering-Guide/blob/main/guides/prompts-basic-usage.md#conversation)에서 대화 기능과 역할 프롬프트에 대해 조금 다뤘습니다. 특정 의도, 행동 및 정체성을 가진 특정 스타일로 대화를 하도록 LLM에 지시하는 방법을 다루었습니다.

질문에 대해 보다 기술적이고 과학적인 답변을 생성할 수 있는 대화 시스템을 만들었던 이전의 기본 예제를 다시 살펴보겠습니다. 

*프롬프트:*
```
다음은 AI 연구 어시스턴트와의 대화입니다. 어시스턴트의 어조는 기술적이고 과학적입니다.
사람: 안녕하세요, 누구세요?
AI: 안녕하세요! 저는 인공지능 연구 조수입니다. 오늘은 무엇을 도와드릴까요?
인간: 블랙홀의 생성에 대해 알려주실 수 있나요?
AI:
```

위의 예에서 두 가지 중요한 구성 요소를 볼 수 있습니다:
- 챗봇이 무엇인지에 대한 **의도** 또는 설명
- 챗봇이 응답할 때 사용할 스타일이나 어조를 지시하는 **아이덴티티**.

위의 간단한 예는 `text-davinci-003`을 사용하는 텍스트 완성 API에서 잘 작동합니다. 최근에 OpenAI는 이러한 유형의 기능(채팅 완성)을 위해 특별히 구축된 `gpt-3.5-turbo`라는 더 강력하고 저렴한 모델인 ChatGPT API를 발표했습니다(https://openai.com/blog/introducing-chatgpt-and-whisper-apis). OpenAI는 채팅이 아닌 사용 사례에서도 이 모델을 최고의 모델로 추천합니다.  ChatGPT API 사용의 다른 이점은 상당한 비용 절감(90%)과 효율성입니다. 

Snap Inc. 및 Instacart와 같은 대기업은 이미 개인화된 추천부터 개방형 쇼핑 목표에 이르기까지 다양한 제품에 ChatGPT로 구동되는 대화 기능을 통합하고 있습니다.

--- Translated with www.DeepL.com/Translator (free version)

ChatGPT와의 대화 ###

멀티턴 대화 ###

먼저 위의 챗봇 어시스턴트 예시를 통해 ChatGPT의 기능을 시연하고 그 결과를 살펴보겠습니다. 'text-davinci-003'과 비교했을 때, ChatGPT를 구동하는 'gpt-3.5-turbo' 모델은 채팅 형식을 입력으로 사용합니다. 이 모델은 일련의 메시지를 입력으로 예상하고 이를 사용하여 응답을 생성합니다. 

*입력:*
```
시스템: 귀하는 AI 연구 조교입니다. 기술적이고 과학적인 어조를 사용합니다.
USER: 안녕하세요, 누구세요?
ASSISTANT: 안녕하세요! 저는 AI 연구 조교입니다. 오늘은 무엇을 도와드릴까요?
사용자: 블랙홀의 생성에 대해 알려주실 수 있나요?
```

*출력:*
```
보조자: 물론이죠! 블랙홀은 거대한 별이 연료가 다 떨어져서 자체 중력에 의해 붕괴할 때 만들어집니다. 이 붕괴는 별을 엄청나게 조밀하게 만들고, 빛조차도 빠져나갈 수 없을 정도로 강한 중력장을 만듭니다. 이 돌아올 수 없는 지점을 사건의 지평선이라고 합니다. 별이 사건의 지평선을 넘으면 블랙홀로 간주됩니다...
```

위의 예제에서는 입력과 출력을 단순화했지만 ChatGPT 채팅 완료 API는 메시지가 특정 형식이어야 한다는 점에 유의하세요. 이 예제가 OpenAI 플레이그라운드에서 '채팅 모드'를 사용하면 어떻게 보이는지 아래에 스냅샷을 추가했습니다:

![](../img/chatgpt-1.png)

이 예제에 대한 보다 공식적인 API 호출은 아래 예제와 비슷할 것입니다:

```python
import openai
openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "시스템", "콘텐츠": "귀하는 AI 연구 조교입니다. 기술적이고 과학적인 어조를 사용합니다."},
        {"role": "사용자", "콘텐츠": "안녕하세요, 누구세요?"},
        {"role": "어시스턴트", "콘텐츠": "안녕하세요! 저는 인공지능 연구 조교입니다. 오늘은 무엇을 도와드릴까요?"},
        {"role": "사용자", "콘텐츠": "블랙홀의 생성에 대해 알려주실 수 있나요?"}
    ]
)
```
향후 개발자가 ChatGPT와 상호작용하는 방식은 [채팅 마크업 언어](https://github.com/openai/openai-python/blob/main/chatml.md)(줄여서 ChatML)를 통해 이루어질 것으로 예상됩니다.

### 싱글 턴 작업

채팅 형식은 멀티턴 대화도 가능하지만 `text-davinci-003`에서 사용한 것과 유사한 싱글턴 작업도 지원합니다. 즉, 기존 GPT 모델에서 시연했던 것과 유사한 작업을 ChatGPT를 사용하여 수행할 수 있습니다. 예를 들어 ChatGPT를 사용하여 다음과 같은 질문-응답 작업을 수행해 보겠습니다:

*입력:*
```
USER: 아래 문맥에 따라 질문에 답하세요. 답변은 짧고 간결하게 작성하세요. 답변이 확실하지 않은 경우 "답변이 확실하지 않음"이라고 응답하세요.
컨텍스트: 테플리주맙의 기원은 뉴저지의 Ortho Pharmaceutical이라는 제약회사에 있습니다. 그곳에서 과학자들은 OKT3라고 불리는 초기 버전의 항체를 생성했습니다. 원래 생쥐에서 추출한 이 분자는 T세포 표면에 결합하여 세포 살상 가능성을 제한할 수 있었습니다. 1986년에는 신장 이식 후 장기 거부 반응을 예방하는 데 도움이 되는 것으로 승인되어 인간에게 사용하도록 허용된 최초의 치료용 항체가 되었습니다.
질문: OKT3는 원래 어디에서 유래했나요?
답변:
```

*출력:*
```
보조: 마우스.
```

ChatGPT를 사용하여 작업을 수행하는 방법을 더 잘 보여주기 위해 `사용자` 및 `보조자` 레이블을 추가하고 있다는 점에 유의하세요. 다음은 플레이그라운드를 사용한 예제입니다:

![](../img/chatgpt-classic.png)

좀 더 공식적으로, 이것은 API 호출입니다(요청의 메시지 구성 요소만 포함했습니다):

```python
CONTENT = """아래 문맥에 따라 질문에 답하세요. 답변은 짧고 간결하게 작성하세요. 답변이 확실하지 않은 경우 \"답변이 확실하지 않음\"으로 응답하세요.
컨텍스트: 테플리주맙의 기원은 뉴저지의 Ortho Pharmaceutical이라는 제약회사에 있습니다. 그곳에서 과학자들은 OKT3라고 불리는 초기 버전의 항체를 생성했습니다. 원래 생쥐에서 추출한 이 분자는 T세포 표면에 결합하여 세포를 죽이는 잠재력을 제한할 수 있었습니다. 1986년에는 신장 이식 후 장기 거부 반응을 예방하는 데 도움이 되는 것으로 승인되어 인간에게 사용하도록 허용된 최초의 치료용 항체가 되었습니다.
질문: OKT3는 원래 어디에서 유래했나요?
답변:
"""
응답 = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "사용자", "콘텐츠": CONTENT},
    ],
    temperature=0,
)
```

### 채팅 모델에 명령하기

공식 OpenAI 문서에 따르면, `gpt-3.5-turbo` 모델의 스냅샷도 제공될 예정입니다. 예를 들어, 3월 1일 `gpt-3.5-turbo-0301`의 스냅샷에 액세스할 수 있습니다. 이를 통해 개발자는 특정 모델 버전을 선택할 수 있습니다. 이는 또한 모델 지침을 위한 모범 사례가 버전마다 변경될 수 있음을 의미합니다. 

현재 `gpt-3.5-turbo-0301`에 대한 권장 사항은 사용 가능한 `system` 메시지가 아닌 `user` 메시지에 지침을 추가하는 것입니다. 


## 파이썬 노트북

|설명|노트북|
|--|--|
|`openai` 라이브러리를 사용해 ChatGPT API를 호출하는 방법에 대해 자세히 알아보세요.|[ChatGPT API 소개](../notebooks/pe-chatgpt-intro.ipynb)|
|`LangChain` 라이브러리를 사용하여 ChatGPT 기능을 사용하는 방법을 알아보세요. |[LangChain을 이용한 ChatGPT API](../notebooks/pe-chatgpt-langchain.ipynb)|

---
## References

- [Seeing ChatGPT Through Students' Eyes: An Analysis of TikTok Data](https://arxiv.org/abs/2303.05349) (March 2023)
- [Extracting Accurate Materials Data from Research Papers with Conversational Language Models and Prompt Engineering -- Example of ChatGPT](https://arxiv.org/abs/2303.05352) (Mar 2023)
- [ChatGPT is on the horizon: Could a large language model be all we need for Intelligent Transportation?](https://arxiv.org/abs/2303.05382) (Mar 2023)
- [Making a Computational Attorney](https://arxiv.org/abs/2303.05383) (Mar 2023)
- [Does Synthetic Data Generation of LLMs Help Clinical Text Mining?](https://arxiv.org/abs/2303.04360) (Mar 2023)
- [MenuCraft: Interactive Menu System Design with Large Language Models](https://arxiv.org/abs/2303.04496) (Mar 2023)
- [A Comprehensive Survey of AI-Generated Content (AIGC): A History of Generative AI from GAN to ChatGPT](https://arxiv.org/abs/2303.04226) (Mar 2023)
- [Exploring the Feasibility of ChatGPT for Event Extraction](https://arxiv.org/abs/2303.03836)
- [ChatGPT: Beginning of an End of Manual Annotation? Use Case of Automatic Genre Identification](https://arxiv.org/abs/2303.03953) (Mar 2023)
- [Is ChatGPT a Good NLG Evaluator? A Preliminary Study](https://arxiv.org/abs/2303.04048) (Mar 2023)
- [Will Affective Computing Emerge from Foundation Models and General AI? A First Evaluation on ChatGPT](https://arxiv.org/abs/2303.03186) (Mar 2023)
- [UZH_CLyp at SemEval-2023 Task 9: Head-First Fine-Tuning and ChatGPT Data Generation for Cross-Lingual Learning in Tweet Intimacy Prediction](https://arxiv.org/abs/2303.01194) (Mar 2023)
- [How to format inputs to ChatGPT models](https://github.com/openai/openai-cookbook/blob/main/examples/How_to_format_inputs_to_ChatGPT_models.ipynb) (Mar 2023)
- [Can ChatGPT Assess Human Personalities? A General Evaluation Framework](https://arxiv.org/abs/2303.01248) (Mar 2023)
- [Cross-Lingual Summarization via ChatGPT](https://arxiv.org/abs/2302.14229) (Feb 2023)
- [ChatAug: Leveraging ChatGPT for Text Data Augmentation](https://arxiv.org/abs/2302.13007) (Feb 2023)
- [Dr ChatGPT, tell me what I want to hear: How prompt knowledge impacts health answer correctness](https://arxiv.org/abs/2302.13793) (Feb 2023)
- [An Independent Evaluation of ChatGPT on Mathematical Word Problems (MWP)](https://arxiv.org/abs/2302.13814) (Feb 2023)
- [ChatGPT: A Meta-Analysis after 2.5 Months](https://arxiv.org/abs/2302.13795) (Feb 2023)
- [Let's have a chat! A Conversation with ChatGPT: Technology, Applications, and Limitations](https://arxiv.org/abs/2302.13817) (Feb 2023)
- [Check Your Facts and Try Again: Improving Large Language Models with External Knowledge and Automated Feedback](https://arxiv.org/abs/2302.12813) (Feb 2023)
- [On the Robustness of ChatGPT: An Adversarial and Out-of-distribution Perspective](https://arxiv.org/abs/2302.12095) (Feb 2023)
- [How Generative AI models such as ChatGPT can be (Mis)Used in SPC Practice, Education, and Research? An Exploratory Study](https://arxiv.org/abs/2302.10916) (Feb 2023)
- [Can ChatGPT Understand Too? A Comparative Study on ChatGPT and Fine-tuned BERT](https://arxiv.org/abs/2302.10198) (Feb 2023)
- [A Prompt Pattern Catalog to Enhance Prompt Engineering with ChatGPT](https://arxiv.org/abs/2302.11382) (Feb 2023)
- [Zero-Shot Information Extraction via Chatting with ChatGPT](https://arxiv.org/abs/2302.10205) (Feb 2023)
- [ChatGPT: Jack of all trades, master of none](https://arxiv.org/abs/2302.10724) (Feb 2023)
- [A Pilot Evaluation of ChatGPT and DALL-E 2 on Decision Making and Spatial Reasoning](https://arxiv.org/abs/2302.09068) (Feb 2023)
- [Netizens, Academicians, and Information Professionals' Opinions About AI With Special Reference To ChatGPT](https://arxiv.org/abs/2302.07136) (Feb 2023)
- [Linguistic ambiguity analysis in ChatGPT](https://arxiv.org/abs/2302.06426) (Feb 2023)
- [ChatGPT versus Traditional Question Answering for Knowledge Graphs: Current Status and Future Directions Towards Knowledge Graph Chatbots](https://arxiv.org/abs/2302.06466) (Feb 2023)
- [What ChatGPT and generative AI mean for science](https://www.nature.com/articles/d41586-023-00340-6) (Feb 2023)
- [Applying BERT and ChatGPT for Sentiment Analysis of Lyme Disease in Scientific Literature](https://arxiv.org/abs/2302.06474) (Feb 2023)
- [Exploring AI Ethics of ChatGPT: A Diagnostic Analysis](https://arxiv.org/abs/2301.12867) (Jan 2023)
- [ChatGPT for Good? On Opportunities and Challenges of Large Language Models for Education](https://www.edu.sot.tum.de/fileadmin/w00bed/hctl/_my_direct_uploads/ChatGPT_for_Good_.pdf) (Jan 2023)
- [The political ideology of conversational AI: Converging evidence on ChatGPT's pro-environmental, left-libertarian orientation](https://arxiv.org/abs/2301.01768) (Jan 2023)
- [Techniques to improve reliability - OpenAI Cookbook](https://github.com/openai/openai-cookbook/blob/main/techniques_to_improve_reliability.md)
- [Awesome ChatGPT Prompts](https://github.com/f/awesome-chatgpt-prompts)
- [Introducing ChatGPT](https://openai.com/blog/chatgpt) (Nov 2022)

---
[이전 섹션(애플리케이션)](./prompts-applications.md)

[다음 섹션(적대적 프롬프트)](./prompts-adversarial.md)
