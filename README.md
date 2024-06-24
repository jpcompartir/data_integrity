# Data Integrity

Models and algorithms for measuring data quality for opinion mining in web-based documents. 

# Goals
Heuristics for data quality
Models for spam classification, spam label, data quality
Explore sqlite vs DuckDB + Arrow combination
Data labelling tool/interface e.g. Argilla/Doccano
Streamlined inference e.g. via Hugging Face Inference Endpoints -> Local via Docker, or web via request

# Documents:

R/01_data_acquisition.qmd - Searching the internet for datasets and collecting internal data
R/02_data_analysis.qmd - Exploring datasets
R/03_data_quality_heuristics.qmd - Implementing Meta's data-quality heuristics and investigating whether they work for us. Then creating our own heuristics.
R/04_data_labelling.qmd - Labelling data and storing verbatims.


# Process

Initially no cleaning steps - cleaning steps can hide spam signal, so let the model learn from data.

What is spam?
> Spam on social media refers to unwanted, irrelevant, or unsolicited content that is repeatedly posted or sent to a large number of users, often for the purposes of advertising, phishing, spreading malware, or other deceptive practices. 

## First model - binary spam  classifier
Inputs string of text
Outputs spam/no spam classification

- [x] Search HF Hub and other sources for publicly available datasets which may comprise part of the corpus. One issue is public datasets tend to have bad labels. 
- [ ] Collect & label examples of spam vs not spam from previous project work
  - [ ] Use data cleaning heuristics to shrink dataset size and save time labelling, e.g. our previous work + Meta text qual paper.
  - [ ] Implement into LimpiaR as per [Text quality issue](https://github.com/jpcompartir/LimpiaR/issues/65)
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


# Implementation To-do list

-   [ ] has_first_letter_caps       
-   [ ] all_caps                 
-   [ ] word_repetition_ratio_ge_0_2
-   [ ] digit_punctuation_ratio_0_25
-   [ ] no_special_characters       
-   [ ] stop_word_match_2           
-   [ ] javascript_flag             
-   [ ] token_count_ge_3            
-   [ ] word_count_3_256            
-   [ ] has_object                  
-   [ ] has_noun                    
-   [ ] has_determiner              
-   [ ] text_complexity_c1     
-   [ ] xxx


Setting up spaCy:

conda create --name spacy
conda activate spacy
pip install 'spacy[apple]'

# Download best-matching version of specific model for your spaCy installation
python -m spacy download en_core_web_sm


Setting up Doccano

1. install docker
2. run docker
3. open the terminal `docker pull doccano/doccano`
4. Set up an environment and install pip/doccano
  conda create --name doccano 
  conda activate doccano 
  conda install pip 
  pip install doccano # Takes a while
5. Run the container:
docker container create --name doccano \
  -e "ADMIN_USERNAME=admin" \
  -e "ADMIN_EMAIL=jack.penzer@sharecreative.com" \
  -e "ADMIN_PASSWORD=uncrackablepassword" \
  -v doccano-db:/data \
  -p 8000:8000 doccano/doccano
  
  docker container start doccano
  
  go to http://localhost:8000
  
  Create project
  
Creating new users: