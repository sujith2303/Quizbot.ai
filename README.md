# What is QuizBot.AI
<p>This initiative presents a platform that swiftly generates questions and answers by analyzing video, image, or PDF files. Users can easily upload their files through a user-friendly interface, and within seconds, a diverse set of questions is generated.
   These questions include essay prompts, multiple-choice queries, true or false statements, and fill-in-the-blank exercises. The system also incorporates an automated grading feature, providing instant feedback on user responses.
  Tailored for students, this project serves as an invaluable tool for mock testing before examinations. As a freely accessible resource, users can take advantage of multiple attempts across various topics, enhancing their confidence and preparation for upcoming exams.
</p>
<p>
An extension of this project introduces an <b>AI-based interview system</b>, where the AI engages with candidates, formulates questions based on their resumes, and assesses their responses in real time. The model leverages up-to-date data to evaluate candidate responses and adaptively generate follow-up questions. The motivation behind developing this project stems from the prevalent issue of candidates being rejected by Applicant Tracking System (ATS) software, limiting their opportunities. Many candidates resort to tailoring their resumes to match job descriptions, effectively "hacking" the ATS tracker. To address this, the AI_Interviewer project aims to provide a fairer assessment by utilizing a machine learning model. This approach enables parallel usage and scalability, facilitating simultaneous interviews for multiple candidates. The system evaluates scores to determine whether an in-person interview is warranted, thereby mitigating the reliance on ATS-driven rejections and promoting a more comprehensive candidate evaluation process.
</p>

<br>

# Architecture and Models

This is a sequence to sequence task. We have released three versions of QuizBot.AI.
### 1. QuizBot-small
### 2. QuizBot-Base
### 3. QuizBot-Large

As this is a sequence to sequence task with inputs as the context and the instructions and the output is the question and answer based on the instruction provided in the input prompt. We have used [T5](https://arxiv.org/pdf/1910.10683.pdf) model and have fine-tuned the model using the hyperparameters mentioned in the [Config file](config.json) on the [dataset](https://huggingface.co/datasets/Sujithanumala/AI_Interviewer).


The lighter version QuizBot-small is fine-tuned on [T5-small](https://huggingface.co/t5-small) , QuizBot-Base on [T5-base](https://huggingface.co/t5-base) and QuizBot-large on [T5-large](https://huggingface.co/t5-large).Based on the availabilty of the compute resources the user can download the model. 


<br>

# Input & Output Format

The input format is very simple. Before directly giving the context follow the input Format as mentioned 
* Generate Essay Answers  ***context followed by \n Generate Essay answers .***
* Generate MCQS  ***context followed by \n  Generate MCQS .***
* Generate True or False ***context followed by \n Generate True or False .***
* Generate Fill in the blanks ***context followed by \n Generate FIBs .***
Note that these were formats used while fine-tuning the model and can be anything.

The output format is as follows
* For any of the tasks the output format contains [QUESTION]: tag followed by the question and [ANSWER]: tag followed by the answer. Note these are purely based on experiments. I tried using <Question>: [Question]: tags but the [QUESTION]: tag worked out well. You can try your own when you fine-tune the model.
<br>

# Dataset used

This project used a custom [dataset](https://huggingface.co/datasets/Sujithanumala/AI_Interviewer). We have collected the context from different sources especially using the context from the SQuAD_V2 dataset and have generated custom Questions and Custom answers carefully. You can go through the [Create_Dataset](Create_Dataset.py) and create a custom dataset for your project and train your model.

**Data Creation part**
Data was carefully prepared because it affects a lot the model's performance. I have used synthetic data to create the dataset. The split goes this way
60% of data was collected from SQuADv2 and prepared synthetically using Google's Gemini as an API [Prepare_Dataset](Prepare_Dataset.py) ,chatgpt, and Meta's LLaMA model.
40% of data was collected from news articles Wikipedia and some books.



<br>

# Code

No prior knowledge of Machine learning frameworks like  **Pytorch**, **Tensorflow**, **Jax**, **HuggingFace**, etc.. are required. Just write 2 lines of code and your QuizBot is ready.

(AI Interviewer and also explain architecture )


# Steps for training and fine-tuning the model

## A. Choosing the Model 
Initially, I tried using the transformer architecture by simply implementing the architecture as mentioned in the [Attention is all you need](https://arxiv.org/pdf/1706.03762.pdf) paper. But in order to get the benefit of fine-tuning I have used five pre-trained models 

### 1.T5-small
[T5- small](https://huggingface.co/t5-small) was just 60.5M parameters and very easy to load(just 242 MB) and easy to train and in a few epochs it achieved a very less loss over both training and test data but the results when tested were worst. It just outputted [QUESTION]: and [ANSWER]: and some random text. I think the loss was less because it was able to correctly output [QUESTION] AND [ANSWER] and masks for fill-in-the-blanks but were meaning less. The reason was clear because the model didn't have a very good language understanding.
### 2.T5-base
I attempted to train the [T5-base](https://huggingface.co/t5-base) model which had 223M parameters, and it exhibited a slow training process, taking up around 892MB of memory. Despite the initial time investment, the model demonstrated a strong grasp of language, leading to promising results after just a few epochs. The model effectively recognized patterns in question formats, such as multiple-choice questions with four options and the requirement to output the correct one. It also performed well in tasks like filling in the blanks with meaningful words. However, the validation loss increased over epochs, primarily due to the limited size of the dataset. The understanding improved significantly by expanding the dataset size.
### 3.T5-large
Finally, I experimented with the [large model](https://huggingface.co/t5-large), which boasts a significantly better understanding compared to the T5-base requires approximately 3GB to load, and has 738M parameters. While this model yielded superior results, there were both advantages and disadvantages.

The model's strength lies in its enhanced comprehension, surpassing that of the T5-base. However, a major drawback surfaced when attempting direct fine-tuning—out-of-memory issues were inevitable due to its substantial size, featuring 24 hidden layers in both the encoder and decoder and 12 attention heads. To address this, I opted for the PEFT (Prompt-Enhanced Fine-Tuning) approach, which successfully prevented out-of-memory errors but extended the training time approximately tenfold compared to the base version.

Despite achieving satisfactory results, the large model fell short of its full potential. The dataset proved insufficient for such a massive model, requiring at least 20 times the parameters for optimal performance (read [chinchilla](https://arxiv.org/pdf/2203.15556.pdf) paper for more details). Given that I was fine-tuning with randomly initialized PEFT parameters, increasing the dataset size did enhance accuracy, but the training time also escalated considerably. The batch size was constrained to 4 due to memory constraints, and attempts to increase it resulted in out-of-memory errors. To mitigate GPU out-of-memory issues, I adjusted the GPU utilization fraction to 0.75, providing some relief but still necessitating a batch size of 4. In conclusion, while the large model exhibited performance on par with the T5-base, realizing its full potential demands ample data and extensive training.

### 4.Flan-T5-base
The results of the Flan-t5-base are comparable to the t5-base (since both of them were almost the same) but the t5-base worked well for this scenario.
### 5.Flan-T5-large
The same thing happened with the flan-t5-large model also. The t5-large was better compared to flan-t5-large.

### Hyperparameters
Full Fine-Tuning (for both the base and small model)

Learning rate:- 5.6e-3 with a decay of 0.01

Tried with smaller learning rates (1e-5,5.6e-5,1e-4,5e-4) but found that initially setting the learning rate high helped the model to converge soon.
I have chosen the same hyperparameters for all three models. The base model converged on training 10 epochs over 1000 examples. The large model converged on 30 epochs for 1000 examples.

## B.AutoGrader (Grades the user) 
Fine-tuned BERT model on [MRPC dataset ](https://huggingface.co/datasets/glue/viewer/mrpc). This dataset has 2 sentences and the output of the model is whether the two sentences mean the same. This way the user's essay answers are graded. Other answers like MCQs, fill in the blanks, and True or False are directly compared and given a 1 or 0 score. The score for essay answers is based on the score that the BERT model outputs (the prediction of similarity between two sentences).

