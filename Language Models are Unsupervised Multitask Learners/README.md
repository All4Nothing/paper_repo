# Language Models are Unsupervised Multitask Learners
- ***Link :*** https://d4mucfpksywv.cloudfront.net/better-language-models/language-models.pdf

<aside>
💡 기존 문제는 뭐야?
</aside>

기존 시스템은 유능한 제너럴리스트보다 특정 테스크에 특화된 전문가를 만드는 것으로 비유할 수 있다. 

<aside>
💡 핵심 아이디어가 뭐야?
</aside>

Language Models are Unsupervised Multitask Learners(2019)에서는 OpenAI의 GPT-2 모델을 소개합니다. 핵심 아이디어는 큰 모델을, 크고 다양한 데이터셋으로 학습하면 많은 도메인에서 좋은 성능을 낼 수 있다는 것이다. GPT-2는 GPT-1과 구조가 거의 동일하며 더욱 많은 파라미터를 가지고 있고, Transformer 레이어가 더 많이 쌓인 형태를 가지고 있습니다.

Pretraining에서는 GPT-1과 동일한 방법을 사용했지만, 훨씬 많은 데이터와 퀄리티 높은 데이터를 사용하기 위해 데이터를 필터링하여 양질의 데이터셋을 만들었습니다.

**Multitask Learning as Question Answering.** GPT-2에서는 추가의 Fine-tuning없이 zero-shot 세팅으로 여러 NLP task들을 수행하기 위해 task를 QA의 형태로 변환하였습니다. 예를 들어, 요약문제를 “(글), 이 글을 요약하면?”, 번역문제를 (문장), 이 문장을 영어로 번역하면?”과 같은 방식으로 말이죠. 

- Summarization : TL;DR text를 article의 마지막에 주고, TL;DR이 나오면 앞에 나온 글을 요약하는 테스크 수행 → Fine-tuning없이 zero-shot setting에서 요약을 수행할 수 있게함.
- Transaltion : 문장 끝에 “they say in French:”와 같은 어구(context of example pairs of the format : English sentence = French sentence)를 붙여 번역 테스크를 수행

이런 방식을 통해 추가 학습 없이 여러 task를 수행할 수 있었습니다. 물론 특정 task를 추가적으로 학습하면 더 좋은 성능을 기록하지만, 추가 학습이 없이 수행할 수 있다는 것은 특정 task를 위한 데이터를 수집할 필요가 없다는 뜻이기에 많은 시간과 비용을 아낄 수 있습니다.