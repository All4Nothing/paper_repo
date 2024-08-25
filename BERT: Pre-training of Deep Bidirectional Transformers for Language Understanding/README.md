- ***Link :*** https://arxiv.org/abs/1810.04805

# BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding  

> 💡 핵심 아이디어가 뭐야?

- BERT(Bidirectional Encoder Representations from Transformers)는 입력된 문장에서 각 단어의 문맥을 양방향으로 이해하기 위해 multi-layer bidirectional Transformer encoder를 MLM(Masked Language Model)과 NSP(Next Sentence Prediction) 방식을 이용해 모델을 사전 학습시키고, 학습된 모델을 fine-tuning하여 다양한 언어 task에 사용할 수 있다.
- unlabeled text를 이용해 deep bidirectional representations을 사전에 학습하고, 학습한 모델을 fine-tuning하여 다양한 언어 tasks에서 SOTA를 기록했다.

BERT는 크게 4가지 측면에서 살펴볼 수 있다.

## **Model Architecture**
![1](https://github.com/user-attachments/assets/fd7f3686-606e-419a-821e-807e2427805f)


- BERT는 multi-layer bidirectional Transformer encoder를 베이스로 한다.
- BERT의 Transformer는 bidirectional self-attention을 사용하지만, GPT Transformer는 constrained self-attention을 사용한다. 그래서 GPT Transformer는 각 토큰의 왼쪽 문맥만을 고려할 수 있다.

## **Input/Output Representations**

![2](https://github.com/user-attachments/assets/002b3128-b600-46b2-bed1-5149f39e446c)

- BERT의 input embeddings은 3가지 embedding vector인 Token Embedding, Segment Embeddings, Position Embeddings의 합이다.
- 모든 input sequence의 첫번째 토큰은 classification token인 [CLS]이며, [CLS] 토큰의 최종 hidden state는 classification task를 위한 종합 sequence representation이다.
- Token Embeddings은 WordPiece embedding을 사용한다.
    - WordPiece embedding이란? : 기존의 단어 임베딩 기법은 단어 단위로 임베딩을 생성한다. 이런 방식은 사전에 없는 단어(OOV, Out Of Vocabulary)가 등장할 때 처리가 어렵고, 언어에는 너무 많은 단어가 존재하기에 모든 단어를 임베딩 벡터로 표현하기는 어렵다. 이러한 문제를 해결하기 위해, WordPiece Embedding은 단어를 subword 단위로, 우리말로 하면 형태소 단위로, 단어를 분해한다. 예를 들어, “unhappiness”라는 단어는 “un”, “happi”, “ness”와 같이 나눌 수 있다. 이렇게 학습하면, 새로 등장한 단어도 subword의 조합으로 표현할 수 있고, 공간의 효율성이 높아지고, 다양한 접두사, 접미사 등을 처리하기 좋다.
- 또한 input sequence는 한 쌍의 문장으로 구성되는데, 각 문장은 [SEP] 토큰으로 분리된다. 이때, 분리된 각 문장을 구분하기 위해 Segment Embeddings를 사용한다. 예를 들어, A 문장의 토큰은 $E_A$로 임베딩하고, B 문장의 토큰은 $E_B$로 임베딩하는 식이다.
- Position Embeddings는 단어의 순서를 부여하기 위해 사용한다. Transformer의 attention은 문장 내 단어의 순서를 고려하지 않기에, 단어의 순서를 부여하기 위해 사용한다. 맨처음 토큰부터 $E_0, E_1,E_2...$식으로 부여한다.

## **Pre-Training**

### **Masked Language Model, MLM**

- 입력 문장에서 전체 단어의 15%를 랜덤하게 선택해 [MASK] 토큰으로 마스킹하고, 마스킹된 토큰을 예측하도록 모델을 학습시킨다.
    - 예를 들어, “I don’t think that I like her”이라는 문장이 주워지면, ”I dont [MASK] that I like her”과 같이 변형한다.
- Self-attention을 통해 [MASK] 토큰과 다른 토큰과의 연관성을 계산해 [MASK] 토큰을 예측한다. 이러한 방식으로 문장의 좌우 문맥을 고려할 수 있도록 deep bidirectional Transformer를 학습시킬 수 있다.
- 하지만, 우리가 사전 학습한 모델을 가지고 downstream task를 수행할 때(fine-tuning시)는 사전학습때와는 달리 input sequence에 [MASK] 토큰이 존재하지 않는다. 따라서, 토큰을 [MASK] 토큰으로 바꾸는(80%만큼) 것만이 아닌, 랜덤한 토큰으로 바꾸거나(10%만큼), 토큰을 바꾸지 않기도(10%만큼) 한다.

### **Next Sentence Prediction, NSP**

- 추가적으로, 문장 간 관계를 이해하기 위해 Next Sentence Prediction을 학습한다.
- 질의응답(Question Answering, QA)나 자연어 추론(Natural Language Inference, NLI)와 같은 task는 문장 간의 관계를 이해하는 것이 중요하다.
- 따라서 모델이 문장 간의 단어를 학습할 수 있도록 사전학습 시, 문장 A와 이어질 문장 B를 달리 하여 학습한다. 문장 B가 실제로 문장 A 뒤에 이어지는 문장(labeled as IsNext)인지 50%, 이어지지 않는 문장(labeled as NotNext)인지 50%로 구성하여 학습한다.

## **Fine-tuning**

![3](https://github.com/user-attachments/assets/8d29efe2-8fea-4cc8-9ac8-a912c1014613)

- 수행하고자 하는 downstream task에 따라 input을 넣어 fine-tuning을 한다.
    
    1) Paraphrasing : sentence pairs
    
    2) Entailment : Hypothesis-Premise pairs
    
    3) Question Answering Question-Passage pairs
    
    4) Text Classification or Sequence Tagging : None pair 
    
- 또한, task에 따라 output layer에 넣어줄 output이 다르다.
    
    1) for token level tasks such as sequence tagging or question answering : token representations
    
    2) for classification such as entailment or sentiment analysis : [CLS] representation
    
> 💡 기존 아이디어와의 차이는 뭐야?

<img width="811" alt="4" src="https://github.com/user-attachments/assets/2df6b223-f27f-4493-b1fa-6665eaad448f">

- “I don’t OOOOO that I like her”이라는 문장에서 00000에 들어갈 단어를 예측하고자 할 때, 우리는 OOOOO 단어 좌우 문맥 모두를 고려해서 단어를 예측하고는 한다. 직관적으로 생각해볼 때, 단어의 좌우 문맥을 모두 고려하는 것이 더 정확히 예측하는데 도움이 될 것이다.
- 기존 bidirectional한 학습은 RNN이나 LSTM에서도 사용되었는데, 이 모델들에서는 forward(left-to-right)와 backward(right-to-left)를 별도로 학습하여, 두 방향의 출력을 합쳐 단어의 표현을 만들었다. 이러한 방식은 각 방향에서 반대 방향의 정보를 고려하지 못하는 단점이 있다. 예를 들어 왼쪽에서 오른쪽으로 단어를 이해해가기에, 단어의 오른쪽에 있는 문맥을 같이 고려하지는 못한다. 대표적으로 이 논문에서도 언급하는 ELMo와 같은 모델이 그러하다.
- BERT는 기존 RNN이나 LSTM과 같이 순차적인 양방향 처리를 하는 것이 아닌, 문장 전체를 한 번에 입력받고, 마스킹된 단어를 예측하기 위해 문장 내 모든 단어의 상호 관계를 학습한다.
- Transformer의 Self-Attention 메커니즘을 통해 문장 내 단어가 문장 전체에서 다른 단어들과의 연관성을 계산할 수 있다. 즉 단어 간의 forward와 backward문맥 모두를 고려할 수 있다.
    - 예를 들어, “The way that you love me”라는 문장에서 “you”라는 단어는 문장 내의 모든 단어를 참고해 문맥을 이해할 수 있다. 즉, “you”라는 단어의 좌측 문맥 “The way that”과 우측 문맥 “love me” 모두를 고려할 수 있다.
  
> 💡 그럼 GPT-1은? 그것도 Transformer를 사용했지 않아?  

- GPT-1에서 또한 transformer의 self-attention을 사용했지만, GPT-1에서는 예측하고자 하는 토큰의 좌측 토큰들만을 이용해 예측을 수행했다. 이를 통해 텍스트를 생성할 때 자연스러운 순서를 유지할 수 있지만, 특정 단어를 예측할 때는 한쪽 방향의 문맥만을 사용할 수 있었다.

![5](https://github.com/user-attachments/assets/4990be59-5dc1-4311-9668-d2561ef145b8)
*GPT-1, Improving Language Understanding by Generative Pre-Training*
