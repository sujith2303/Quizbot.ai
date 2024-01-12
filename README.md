# What is QuizBot.AI
<p>This initiative presents a platform that swiftly generates questions and answers by analyzing video, image, or PDF files. Users can easily upload their files through a user-friendly interface, and within seconds, a diverse set of questions is generated.
   These questions include essay prompts, multiple-choice queries, true or false statements, and fill-in-the-blank exercises. The system also incorporates an automated grading feature, providing instant feedback on user responses.
  Tailored for students, this project serves as an invaluable tool for mock testing before examinations. As a freely accessible resource, users can take advantage of multiple attempts across various topics, enhancing their confidence and preparation for upcoming exams.
</p>
<p>
An extension of this project introduces an <b>AI-based interview system</b>, where the AI engages with candidates, formulates questions based on their resumes, and assesses their responses in real-time. The model leverages up-to-date data to evaluate candidate responses and adaptively generate follow-up questions. The motivation behind developing this project stems from the prevalent issue of candidates being rejected by Applicant Tracking System (ATS) software, limiting their opportunities. Many candidates resort to tailoring their resumes to match job descriptions, effectively "hacking" the ATS tracker. To address this, the AI_Interviewer project aims to provide a fairer assessment by utilizing a machine learning model. This approach enables parallel usage and scalability, facilitating simultaneous interviews for multiple candidates. The system evaluates scores to determine whether an in-person interview is warranted, thereby mitigating the reliance on ATS-driven rejections and promoting a more comprehensive candidate evaluation process.
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

# Input Format

The input format is very simple. Before directly giving the context follow the input Format as mentioned 
* Generate Essay Answers  ** *context followed by \n Generate Essay answers .* **
* Generate MCQS  ** *context followed by \n  Generate MCQS .* **
* Generate True or False ** *context followed by \n Generate True or False .* **
* Generate Fill in the blanks ** *context followed by \n Generate FIBs .* **
Note that these were formats used while fine-tuning the model and can be anything.

<br>

# Dataset used

This project used a a custom [dataset](https://huggingface.co/datasets/Sujithanumala/AI_Interviewer). We have collected the context from different sources especially used the context from SQuAD_V2 dataset and have generated custom Questions and Custom answers carefully. You can go through the [Create_Dataset](Create_Dataset.py) and can create a custom dataset for your project and train your model.

<br>

# Code
<p>
No prior knowledge of Machine learning frameworks like **Pytorch** ,**Tensorflow** ,**Jax** , **HuggingFace**,etc.. are required. Just write 2 lines of code and your QuizBot is ready
</p>
(AI Inteviewer and also explain architecture )
