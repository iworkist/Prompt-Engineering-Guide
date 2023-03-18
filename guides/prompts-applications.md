# 프롬프트 애플리케이션

이 가이드에서는 프롬프트 엔지니어링을 사용하여 유용하고 고급 작업을 수행할 수 있는 몇 가지 흥미로운 고급 방법을 다룹니다. 

**이 섹션은 현재 개발 중입니다.
주제
- [데이터 생성](#generating-data)
- [프로그램 지원 언어 모델](#pal-program-aided-language-models)
- [파이썬 노트북](#python-notebooks)


---
## 데이터 생성
LLM은 텍스트를 생성하는 강력한 기능을 가지고 있습니다. 효과적인 프롬프트 전략을 사용하면 모델이 더 정확하고 일관되며 사실에 입각한 응답을 생성하도록 유도할 수 있습니다. 또한 LLM은 모든 종류의 실험을 실행하는 데 유용한 데이터를 생성하는 데 특히 유용할 수 있습니다. 예를 들어 다음과 같이 감정 분류기를 위한 빠른 샘플을 생성하는 데 사용할 수 있습니다:

*프롬프트:*
```
감성 분석용 예시 10개를 생성합니다. 예시는 긍정 또는 부정으로 분류됩니다. 부정적인 예시 2개와 긍정적인 예시 8개를 생성합니다. 예시에는 이 형식을 사용합니다:
Q: <문장>
A: <감성어>
```

*출력:*
```
Q: 방금 최고의 소식을 받았어요!
A: 긍정적
Q: 방금 직장에서 급여를 인상받았어요!
A: 긍정적
Q: 오늘 제가 해낸 일이 정말 자랑스러워요.
A: 긍정적
질문: 오늘 최고의 하루를 보내고 있어요!
A: 긍정적
Q: 주말이 정말 기대돼요.
A: 긍정적
질문: 방금 최고의 선물을 받았어요!
A: 긍정적
Q: 지금 너무 행복해요.
A: 긍정적
Q: 멋진 가족이 있어서 정말 축복이에요.
A: 긍정적
질문: 바깥 날씨가 너무 우울해요.
A: 부정
질문: 방금 끔찍한 소식을 들었어요.
A: 부정
```

이 예제는 매우 유용합니다. 가이드의 다른 섹션에서 다른 테스트에 이 예제를 사용합니다.

---

## PAL(프로그램 지원 언어 모델)
 
[Gao 외, (2022)](https://arxiv.org/abs/2211.10435)는 자연어 문제를 읽고 중간 추론 단계로 프로그램을 생성하기 위해 LLM을 사용하는 방법을 제시합니다. 프로그램 지원 언어 모델(PAL)은 자유 형식 텍스트를 사용하여 해답을 구하는 대신 Python 인터프리터와 같은 프로그래밍 런타임에 해답 단계를 오프로드한다는 점에서 연쇄 사고 프롬프트와 다릅니다.

![](../img/pal.png)

LangChain과 OpenAI GPT-3를 사용한 예제를 살펴보겠습니다. 우리는 파이썬 인터프리터를 활용하여 질문을 해석하고 답을 제공할 수 있는 간단한 애플리케이션을 개발하고자 합니다. 

특히, 날짜 이해가 필요한 질문에 대해 LLM을 사용하여 답변할 수 있는 기능을 만드는 데 관심이 있습니다. 여기](https://github.com/reasoning-machines/pal/blob/main/pal/prompt/date_understanding_prompt.py)에서 채택한 몇 가지 예제를 포함하는 프롬프트를 LLM에 제공할 것입니다.  

다음은 필요한 임포트입니다:

```python
import openai
from datetime import datetime
from dateutil.relativedelta import relativedelta
import os
langchain.llms에서 OpenAI를 가져옵니다.
dotenv에서 load_dotenv를 가져옵니다.
```

먼저 몇 가지를 구성해 보겠습니다:


```파이썬
load_dotenv()
# API 구성
openai.api_key = os.getenv("OPENAI_API_KEY")
# LangChain의 경우
os.environ["OPENAI_API_KEY"] = os.getenv("OPENAI_API_KEY")
```

모델 인스턴스를 설정합니다:

```python
llm = OpenAI(model_name='text-davinci-003', temperature=0)
```

설정 프롬프트 + 질문:

```python
question = "오늘은 2023년 2월 27일입니다. 저는 정확히 25년 전에 태어났습니다. 제가 태어난 날짜는 월/일/년/월/일/년으로 어떻게 되나요?"
date_understanding_prompt = """
# Q: 2015년이 36시간 후에 다가옵니다. 오늘로부터 1주일 후의 날짜는 월/일/년/월/일로 어떻게 되나요?
# 2015년이 36시간 후에 다가온다면 오늘은 36시간 전입니다.
today = datetime(2015, 1, 1) - relativedelta(hours=36)
# 오늘로부터 일주일 후입니다,
one_week_from_today = today + relativedelta(weeks=1)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
one_week_from_today.strftime('%m/%d/%Y')
# Q: 2019년의 첫날은 화요일이고 오늘은 2019년의 첫 번째 월요일입니다. 오늘 날짜는 월/일/년/월/일(MM/DD/YYYY)로 어떻게 되나요?
# 2019년 첫날이 화요일이고 오늘이 2019년의 첫 번째 월요일인 경우 오늘은 6일 후입니다.
today = datetime(2019, 1, 1) + relativedelta(days=6)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
today.strftime('%m/%d/%Y')
# Q: 콘서트가 1943년 6월 1일에 열릴 예정이었으나 오늘로 하루 연기되었습니다. 10일 전의 날짜는 월/일/년/월/일/년으로 어떻게 되나요?
# 콘서트가 1943년 6월 1일로 예정되었으나 오늘로 하루 연기된 경우 오늘은 하루 후입니다.
today = datetime(1943, 6, 1) + relativedelta(days=1)
# 10일 전입니다,
ten_days_ago = today - relativedelta(days=10)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
ten_days_ago.strftime('%m/%d/%Y')
# Q: 오늘은 1969년 4월 19일입니다. 24시간 후의 날짜(MM/DD/YYYY)는 어떻게 되나요?
# 오늘 4/19/1969입니다.
today = datetime(1969, 4, 19)
# 24시간 후입니다,
later = 오늘 + 상대 델타(시간=24)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
today.strftime('%m/%d/%Y')
# Q: Jane은 오늘이 2002년 3월 11일이라고 생각했지만 실제로는 하루 후인 3월 12일입니다. 24시간 후의 날짜는 MM/DD/YYYY로 어떻게 되나요?
# 제인이 오늘이 2002년 3월 11일이라고 생각했지만 실제로는 3월 12일이라면 오늘은 2002년 3월 1일입니다.
today = 날짜/시간(2002, 3, 12)
# 24시간 후
later = 오늘 + 상대 델타(시간=24)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
later.strftime('%m/%d/%Y')
# Q: Jane은 2001년 2월 마지막 날에 태어났습니다. 오늘은 그녀의 16세 생일입니다. 어제 날짜(월/일/월/일/년)는 어떻게 되나요?
# Jane이 2001년 2월 마지막 날에 태어났고 오늘이 16세 생일인 경우 오늘은 16년 후입니다.
today = 날짜/시간(2001, 2, 28) + 상대 델타(년=16)
# 어제,
어제 = 오늘 - 상대 델타(일수=1)
# m/%d/%Y로 형식이 지정된 답변은 다음과 같습니다.
yesterday.strftime('%m/%d/%Y')
# Q: {질문}
""".strip() + '\n'
```

```python
llm_out = llm(DATE_UNDERSTANDING_PROMPT.format(question=question))
print(llm_out)
```

```python
exec(llm_out)
print(born)
```

그러면 다음과 같이 출력됩니다: `02/27/1998`


## 파이썬 노트북

|설명|노트북|
|--|--|
|언어 모델과 함께 파이썬 인터프리터를 사용하여 작업을 해결하는 방법을 배웁니다.|[프로그램 지원 언어 모델](../notebooks/pe-pal.ipynb)|

---

더 많은 예제가 곧 추가될 예정입니다!

[이전 섹션(고급 프롬프트)](./prompts-advanced-usage.md)

[다음 섹션(ChatGPT)](./prompts-chatgpt.md)
