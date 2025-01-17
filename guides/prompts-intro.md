# 프롬프트 소개

프롬프트 엔지니어링은 다양한 애플리케이션과 연구 주제에 언어 모델(LM)을 효율적으로 사용할 수 있도록 프롬프트를 개발하고 최적화하는 비교적 새로운 분야입니다. 프롬프트 엔지니어링 기술은 대규모 언어 모델(LLM)의 기능과 한계를 더 잘 이해하는 데 도움이 됩니다. 연구자들은 프롬프트 엔지니어링을 사용하여 질문 답변 및 산술 추론과 같은 일반적이고 복잡한 다양한 작업에서 LLM의 역량을 향상시킵니다. 개발자는 프롬프트 엔지니어링을 사용하여 LLM 및 기타 도구와 인터페이스하는 강력하고 효과적인 프롬프트 기술을 설계합니다.

이 가이드에서는 표준 프롬프트의 기본 사항을 다루며 프롬프트를 사용하여 대규모 언어 모델(LLM)과 상호 작용하고 지시하는 방법에 대한 대략적인 아이디어를 제공합니다.

모든 예제는 달리 명시되지 않는 한 `텍스트-davinci-003`(OpenAI의 플레이그라운드 사용)으로 테스트되었습니다. 기본 구성(예: `temperature=0.7` 및 `top-p=1`)을 사용합니다.


주제:
- [기본 프롬프트](#기본-프롬프트)
- [LLM 설정에 대한 한마디](#a-word-on-llm-settings)
- [표준 프롬프트](#표준-프롬프트)
- [프롬프트 요소](#elements-of-a-prompt)
- [프롬프트 디자인을 위한 일반 팁](#일반-팁-디자인-프롬프트)
---

## 기본 프롬프트

프롬프트를 통해 이미 많은 것을 얻을 수 있지만 결과의 품질은 얼마나 많은 정보를 제공하느냐에 따라 달라집니다. 프롬프트에는 모델에 전달할 `지시 사항`이나 `질문`과 같은 정보를 포함할 수 있으며 `입력` 또는 `예제`와 같은 기타 세부 정보도 포함할 수 있습니다.

다음은 간단한 프롬프트의 기본 예입니다:

*Prompt*
```
오늘 하늘은
```

*Output:*
```
제가 지금 답변을 작성하는 시간대에 따라서 오늘 하늘의 상태가 달라질 수 있기 때문에, 정확한 답변을 드리기 어렵습니다. 하지만 만약 현재 시간대가 낮 시간대라면, 오늘 하늘은 맑고 햇살이 화사한 날씨일 것입니다. 만약에 밤 시간대라면, 반짝이는 별들과 차분한 달빛이 반기는 멋진 밤하늘일 수도 있습니다.
```

보시다시피 언어 모델은 "하늘은"이라는 문맥에 맞는 문자열의 연속을 출력합니다. 출력은 예상치 못한 것이거나 수행하려는 작업과 거리가 멀 수도 있습니다.

이 기본 예는 구체적으로 무엇을 달성하고자 하는지에 대한 더 많은 컨텍스트나 지침을 제공해야 할 필요성을 강조합니다.

조금 더 개선해 봅시다:

*프롬프트:*
```
문장을 완성하세요: 

오늘 하늘은
```

*출력:*

```
맑고 푸르름이 가득한 아름다운 날씨입니다.
```

이제 좀 나아졌나요? 모델에 문장을 완성하라고 지시했더니 모델에 지시한 대로 결과가 훨씬 더 좋아 보입니다. 모델에 작업을 수행하도록 지시하는 최적의 프롬프트를 설계하는 이러한 접근 방식을 프롬프트 엔지니어링이라고 합니다.

위의 예는 오늘날 LLM으로 가능한 것을 보여주는 기본적인 예시입니다. 오늘날의 LLM은 텍스트 요약부터 수학적 추론, 코드 생성에 이르기까지 모든 종류의 고급 작업을 수행할 수 있습니다.

---

## LLM 설정에 대해 한마디

프롬프트로 작업할 때는 API를 통해 또는 직접 LLM과 상호 작용하게 됩니다. 몇 가지 매개변수를 구성하여 프롬프트에 대해 다양한 결과를 얻을 수 있습니다.

**온도(Temperature)** - 간단히 말해, 온도가 낮을수록 항상 가장 높은 확률의 다음 토큰이 선택된다는 의미에서 결과가 더 결정적입니다. 온도를 높이면 무작위성이 높아져 더 다양하고 창의적인 결과가 나올 수 있습니다. 기본적으로 다른 가능한 토큰의 가중치를 높이는 것입니다. 애플리케이션 측면에서, 사실에 기반한 QA와 같은 작업에는 온도를 낮게 설정하여 보다 사실적이고 간결한 응답을 유도할 수 있습니다. 시 창작이나 기타 창의적인 작업의 경우 온도를 높이는 것이 유리할 수 있습니다.

**Top_p** - 마찬가지로 top_p를 사용하면 모델이 응답을 생성할 때 얼마나 결정론적인지 제어할 수 있습니다. 정확하고 사실적인 답변을 찾고 있다면 이 값을 낮게 유지하세요. 보다 다양한 응답을 원한다면 더 높은 값으로 높입니다.

일반적으로 두 가지 모두 변경하지 말고 한 가지만 변경하는 것이 좋습니다.

몇 가지 기본 예제를 시작하기 전에 사용 중인 LLM 버전에 따라 결과가 달라질 수 있다는 점을 염두에 두세요.

---

## 표준 프롬프트

위에서 매우 간단한 프롬프트를 시도해 보았습니다. 표준 프롬프트의 형식은 다음과 같습니다:

```
<질문>?
```
 
이 데이터는 다음과 같이 많은 QA 데이터 세트에서 표준으로 사용되는 QA 형식으로 포맷할 수 있습니다:

```
Q: <질문>?
A: 
```

위의 표준 형식이 주어질때, 널리 사용되고 있는 효과적인 프롬프트 기법은 퓨샷(few-shot) 프롬프트라고 합니다. 퓨샷 프롬프트의 형식은 다음과 같습니다:


```
<질문>?
<답변>

<질문>?
<답변>

<질문>?
<답변>

<질문>?

```

QA 형식 버전은 다음과 같을 것이라는 것을 이미 짐작할 수 있습니다:


```
Q: <질문>?
A: <답변>

Q: <질문>?
A: <답변>

Q: <질문>?
A: <답변>

Q: <질문>?
A:
```

반드시 QA 형식을 사용해야 하는 것은 아닙니다. 형식은 당면한 작업에 따라 다릅니다. 예를 들어 간단한 분류 작업을 수행하고 다음과 같이 작업을 보여주는 예시를 제공할 수 있습니다:

*프롬프트:*
```
정말 멋지네요! : 긍정
이건 나쁘다! : 부정
와우, 그 영화 끝내줬어요! : 긍정
정말 끔찍한 쇼! : ?

```

*Output:*
```
"정말 끔찍한 쇼!"는 부정적인 감정을 나타내는 문장입니다.
```

퓨샷(Few-Shot) 프롬프트로 몇 개의 예제로 학습되는 인-컨텍스트 러닝이 가능합니다. 향후 가이드에서 이 기능에 대해 더 자세히 살펴볼 예정입니다.

---

## 프롬프트의 요소 ##

프롬프트 엔지니어링으로 가능한 더 많은 예제와 애플리케이션을 다루면서 프롬프트를 구성하는 특정 요소가 있다는 것을 알게 될 것입니다.

프롬프트에는 다음 구성 요소가 포함될 수 있습니다:

**명령어(Instruction)** - 모델이 수행하기를 원하는 특정 작업 또는 명령어

**컨텍스트(Context)** - 모델이 더 나은 응답을 하도록 유도할 수 있는 외부 정보 또는 추가 컨텍스트를 포함할 수 있습니다.

**입력 데이터(Input Data)** - 응답을 찾고자 하는 입력 또는 질문입니다.

**출력 표시기(Output Indicator)** - 출력의 유형 또는 형식을 나타냅니다.

프롬프트에 모든 구성 요소가 필요한 것은 아니며 형식은 작업에 따라 다릅니다. 다음 가이드에서 좀 더 구체적인 예를 다루겠습니다.

---

## 프롬프트 디자인을 위한 일반적인 팁 ##

다음은 프롬프트를 디자인할 때 염두에 두어야 할 몇 가지 팁입니다:

### 간단하게 시작하기
프롬프트 디자인을 시작할 때, 최적의 결과를 얻기 위해 많은 실험이 필요한 반복적인 과정이라는 점을 명심해야 합니다. OpenAI나 Cohere와 같은 간단한 플레이그라운드를 사용하는 것이 좋은 출발점이 될 수 있습니다.

간단한 프롬프트로 시작하여 더 나은 결과를 목표로 더 많은 요소와 컨텍스트를 계속 추가할 수 있습니다. 이러한 이유로 프롬프트의 버전 관리가 매우 중요합니다. 가이드를 읽다 보면 구체성, 단순성, 간결성을 통해 더 나은 결과를 얻을 수 있는 많은 예를 볼 수 있습니다.

여러 가지 하위 작업이 포함된 큰 작업의 경우, 작업을 더 간단한 하위 작업으로 세분화하여 더 나은 결과를 얻을 때까지 계속 쌓아나갈 수 있습니다. 이렇게 하면 처음에 프롬프트 디자인 프로세스에 너무 많은 복잡성을 추가하는 것을 방지할 수 있습니다.

### 안내
"쓰기(Write)", "분류(Classify)", "요약(Summarize)", "번역(Translate)", "정렬(Order)" 등과 같이 모델에 원하는 작업을 지시하는 명령을 사용하여 다양한 간단한 작업에 대한 효과적인 프롬프트를 디자인할 수 있습니다.

무엇이 가장 효과적인지 알기 위해서는 많은 실험이 필요하다는 점을 명심하세요. 다양한 키워드, 컨텍스트, 데이터로 다양한 안내를 시도해 보고 특정 사용 사례와 작업에 가장 적합한 것이 무엇인지 확인하세요. 일반적으로 컨텍스트가 수행하려는 작업과 더 구체적이고 관련성이 높을수록 더 좋습니다. 다음 가이드에서 샘플링과 더 많은 컨텍스트 추가의 중요성에 대해 다룰 예정입니다.

프롬프트의 시작 부분에 지침을 배치하는 것이 좋다는 의견도 있습니다. 또한 '###'과 같은 명확한 구분 기호를 사용하여 지침과 문맥을 구분하는 것이 좋습니다.

예를 들어

*프롬프트:*
```
### 지침 ###
아래 텍스트를 스페인어로 번역하세요:

텍스트: "hello!"
```

*Output:*
```
텍스트 "hello!"의 스페인어 번역은 "¡hola!"입니다.
```

### 구체성
모델이 수행하기를 원하는 명령과 작업에 대해 매우 구체적으로 설명하세요. 프롬프트가 더 설명적이고 상세할수록 더 나은 결과를 얻을 수 있습니다. 이는 원하는 결과나 생성 스타일이 있을 때 특히 중요합니다. 더 나은 결과로 이어지는 특정 토큰이나 키워드는 없습니다. 좋은 형식과 설명적인 프롬프트가 더 중요합니다. 실제로 프롬프트에 예시를 제공하는 것은 특정 형식의 원하는 결과물을 얻는 데 매우 효과적입니다.

프롬프트를 디자인할 때는 프롬프트의 길이에 제한이 있으므로 프롬프트의 길이도 염두에 두어야 합니다. 얼마나 구체적이고 상세해야 하는지도 고려해야 할 사항입니다. 불필요한 세부 정보가 너무 많다고 해서 반드시 좋은 방법은 아닙니다. 세부 사항은 관련성이 있어야 하며 당면한 작업에 기여해야 합니다. 이것은 많은 실험이 필요한 부분입니다. 애플리케이션에 맞게 프롬프트를 최적화하기 위해 많은 실험과 반복을 권장합니다.

예를 들어 텍스트에서 특정 정보를 추출하는 간단한 프롬프트를 만들어 보겠습니다.

*프롬프트:*
```
다음 텍스트에서 장소 이름을 추출합니다.

원하는 형식입니다:
장소: <컴마로 구분된_회사명_목록>

입력: "이러한 발전은 연구자들에게 고무적이지만 여전히 많은 부분이 미스테리입니다. "우리는 종종 뇌와 말초에서 보이는 효과 사이에 블랙박스가 존재합니다."라고 리스본에 있는 샹팔리모드 미지의 센터의 신경면역학자인 헨리크 베이가-페르난데스는 말합니다. "치료적 맥락에서 이를 사용하려면 실제로 그 메커니즘을 이해해야 합니다.""
```

*Output:*
```
장소: 리스본의 샹팔리모드 미지의 센터
```

입력 텍스트는 [이 네이처 기사](https://www.nature.com/articles/d41586-023-00509-z)에서 가져온 것입니다.


### 부정확성 방지

상세하게 작성하고 형식을 개선하는 방법에 대한 위의 팁을 고려할 때, 프롬프트에 대해 너무 똑똑하게 해서 부정확한 설명을 만들 수 있는 함정에 빠지기 쉽습니다. 구체적이고 직접적으로 설명하는 것이 더 좋은 경우가 많습니다. 이는 효과적인 커뮤니케이션과 매우 유사하며, 직접적일수록 메시지가 더 효과적으로 전달됩니다.

예를 들어 프롬프트 엔지니어링의 개념을 배우고 싶을 수 있습니다. 다음과 같이 시도해 볼 수 있습니다:


```
### 아주 간단하게 설명하세요 ###

프롬프트 엔지니어링의 개념을 설명하세요. 
```

위의 프롬프트에서는 몇 개의 문장을 사용해야 하는지, 어떤 스타일을 사용해야 하는지 명확하지 않습니다. 위의 프롬프트로도 어느 정도 좋은 응답을 얻을 수 있지만, 더 좋은 프롬프트는 매우 구체적이고 간결하며 요점을 파악할 수 있는 프롬프트입니다. 예를 들면 다음과 같습니다:

```
프롬프트 엔지니어링은 기계 학습 모델이 원하는 결과를 생성하도록 유도하는 효과적인 프롬프트를 설계하는 과정을 말합니다. 이는 입력 프롬프트를 선택하고 형식을 지정하여 모델이 원하는 출력을 높은 정확도로 생성하도록 안내하는 과정을 포함합니다.
```

### 해야 할 일과 하지 말아야 할 일?
프롬프트를 디자인할 때의 또 다른 일반적인 팁은 하지 말아야 할 것을 말하지 말고 대신 해야 할 것을 말하는 것입니다. 이렇게 하면 보다 구체적이고 세부적인 사항에 집중하여 모델의 좋은 반응을 이끌어낼 수 있습니다.

다음은 영화 추천 챗봇이 하지 말아야 할 것에 초점을 맞춰 명령어를 작성했기 때문에 원하지 않는 작업을 정확히 수행하지 못한 예입니다.


*프롬프트:*
```
다음은 고객에게 영화를 추천하는 상담원입니다.
관심사를 묻지 마세요.
개인 정보를 묻지 마세요.

고객님: 제 관심사에 맞는 영화를 추천해 주세요.
상담원:
```

*Output:*
```
알겠습니다. 어떤 느낌이나 분위기의 영화를 보시고 싶으신가요?
```

더 나은 프롬프트는 다음과 같습니다:

*Prompt:*
```
다음은 고객에게 영화를 추천하는 상담원입니다. 상담원은 글로벌 인기 영화 중에서 영화를 추천할 책임이 있습니다. 상담원은 사용자의 선호도를 묻지 않고 개인 정보를 요구하지 않아야 합니다. 상담원이 추천할 영화가 없는 경우 "죄송합니다, 오늘 추천할 영화를 찾을 수 없습니다."라고 응답해야 합니다.

고객님: 제 관심사에 맞는 영화를 추천해 주세요.
상담원: 네:
```

*Output:*
```
전 세계적으로 인기 있는 영화 중에서 추천해드릴게요. "오션스 8"이라는 영화는 여성 감독의 작품으로 굉장히 멋진 카테고리의 영화입니다. 추천해드릴만한 다른 영화가 없어서 죄송합니다.
```

Some of the examples above were adopted from the ["Best practices for prompt engineering with OpenAI API" article.](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)


---
[Next Section (Basic Prompting)](./prompts-basic-usage.md)
