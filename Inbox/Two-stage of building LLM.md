# Pre-training & Fine-tuning
![[Pasted image 20260211115710.png]]
## pre-training
學習語言的基礎能力，包括語法、詞匯、語言結構，可以相對準確的預測下一個token
+ **few-shot capabilities** :  learn to perform new tasks based  on only a few examples instead of needing extensive training data
## fine-tune
利用特定領域的數據來讓模型適應某些特定的任務。
+ 2 way:
	+ *全權重的微調:* 對模型的所有預訓練權重進行調整，但由於權重已經經過預訓練，大多數情況下，微調只會對預訓練權重進行微小調整，而不是大幅度改變。這種方式能夠讓模型保持原有的語言生成能力，同時使其在特定任務上表現得更好。
	+ *凍結部分權重的微調:*，一般凍結低層（往往是學習到的基礎語言特征），對高層的權重進行調整。這種微調方式常在需要加速訓練，或者數據量較小，**全權重微調可能導致過擬合的情況下使用**。
# LLM 架構的big picture
![[Pasted image 20260211123548.png]]
### Transformer
better then RNN (記住所有過去): Attention 只記住重要的過去
+ Encoder
+ Decoder
### BERT-bidirectional encoder representations from transformers
+ 架構: transformer 的變形-encoder part
+ 訓練方式: masked word prediction
+ 強項:  text classification tasks
	+ Why?  更好地理解句子的上下文，因為它需要根據整句話的前後部分來預測被遮掩的詞。這種雙向（bidirectional）的訓練使得 BERT 更適合處理需要全局上下文理解的任務，而文檔分類或情感預測正是對於上下文語義理解要求高的應用。
### GPT-generative pretrained transformers
+ 架構: transformer 的變形-decoder part
+ 訓練方式: text completion
+ 強項: tasks that require generating texts
+ versatile capabilities: 除了 text completion外, 新任務on zero-shot/few-shot 也有不錯表現 ![[Pasted image 20260211123812.png]]

下次 1.5 利用大型数据集...
