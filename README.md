# data_integrity
Models and algorithms for measuring data quality for opinion mining in web-based documents.

# Goals
Models for spam classification, spam label, data quality
Explore sqlite vs DuckDB + Arrow combination
Streamlined inference e.g. via Hugging Face Inference Endpoints -> Local via Docker, or web via request

# Process

Initially no cleaning steps - cleaning steps can hide spam signal, so let the model learn from data.

What is spam?
> Spam on social media refers to unwanted, irrelevant, or unsolicited content that is repeatedly posted or sent to a large number of users, often for the purposes of advertising, phishing, spreading malware, or other deceptive practices. 

## First model - binary spam  classifier
Inputs string of text
Outputs spam/no spam classification

- [x] Search HF Hub and other sources for publicly available datasets which may comprise part of the corpus. One issue is public datasets tend to have bad labels. 
- [ ] Collect & label examples of spam vs not spam from previous project work
- [ ] Create training, test, holdout splits
- [ ] Create + expand products & companies dictionary to mask those tokens (or convert to company, product etc.)
- [ ] Use transfer learning approach to fine tune an encoder-only (probably RoBERTa) model to serve as base model
- [ ] Evaluate base model - tune probability cut-off against precision & recall
- [ ] Dataset ablations - e.g. not using any publicly available data vs combining datasets
- [ ] Use logprobs to identify problematic labels and refine dataset, then re-evaluate (precision/recall trade-offs)
- [ ] Re-train, evaluate
- [ ] Save to hub + deploy via HF Inference Endpoints

- [ ] Explore distilling model in to simpler ML model (feature extraction e.g. # of hashtags, links etc. + SVM/RF )

## Second model - multi-class
Inputs string of text
Outputs list of probabilities text belongs to class

Takes spam as input and outputs the type of spam e.g. promotion, phishing, adult content, stock ticker, memeing etc. (output categories derived from data)

- [ ] Clustering/topic modelling on spam (for discrete variable for data analysis & multi-class problem)
- [ ] Synthetic data generation via GPT3.5/4-o to address imbalances in topics/clusters performance

## Third model - data quality (not certain)
Inputs string of text
Outputs classification of low vs high quality/analytic value i.e. is an opinion presented, does it appear to be irony/sarcasm

Prompt first -> fine-tune
