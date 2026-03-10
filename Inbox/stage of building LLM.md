![[Pasted image 20260305140514.png]]
data preparation process, pretraining an LLM to create a foundation model, and finetuning for specific usage
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