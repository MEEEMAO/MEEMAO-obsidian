![[Pasted image 20260211123548.png]]
# Transformer
better then RNN (記住所有過去): Attention 只記住重要的過去
+ Encoder
+ Decoder
+ 訓練方式: 專為語言翻譯設計 seq2seq
# BERT-bidirectional encoder representations from transformers
+ 架構: transformer 的變形-encoder part
+ 訓練方式: masked word prediction
+ 強項:  text classification tasks
	+ 根據整句話的前後部分來預測被遮掩的詞，因此能更好地理解句子的上下文
	+ 雙向（bidirectional）的訓練使得 BERT 更適合處理需要全局上下文理解的任務，而文檔分類或情感預測正是對於上下文語義理解要求高的應用。
# GPT-generative pretrained transformers
http://cdn.openai.com/researchcovers/language-unsupervised/language_understanding_paper.pdf
+ 架構: transformer 的變形-decoder part
+ 訓練方式: next word prediction (text completion)
	![[Pasted image 20260211123812.png]]
+ 湧現- emergent behavior: 只是train to predict next word, 在新任務 zero-shot/few-shot 也有不錯表現
	 `The ability to perform tasks that the model wasn't explicitly trained to perform`

