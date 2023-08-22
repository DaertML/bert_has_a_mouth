# bert_has_a_mouth
This repository is meant to be a place where we can experiment with BERT models to generate text.

Several works have studied the possibilities of generating text in a similar way as we do with other
LLMs like GPT or LLaMA models; somehow BERT capabilities were not meant to be used for long sentence
text generation; mainly because they are trained in a different way as the GPT like models are trained,
with a different objective than the GPT like models.

BERT models are mainly focused on the following tasks:
- Topic modelling.
- Token prediction given text before and after the token to predict.
- Predictive text in keyboards, search engines...
- Text classification
- Sentiment analysis

Because of that, BERT models struggle to generate text as we do with other models like GPT like architectures,
in addition, BERT models have not scaled to the sizes of GPT like models, so it is expected that their semantic
capabilities are somehow reduced.

Even though, there have been several attempts to achieve such objective that feels difficult with BERT models:
- BERT has a mouth, and it must speak: https://arxiv.org/pdf/1902.04094.pdf
- Distilling Knowledge Learned in BERT for Text Generation: https://aclanthology.org/2020.acl-main.705.pdf

Following the ideas from different forums, blogs, and repositories, I have found many responses to the question
to suggest that by simply asking the BERT model to predict the next word, by masking a blank word, and then
appending it to the prompt, and recursively (in a loop) asking for the next word; BERT should be able to generate
semantically correct and meaningful text.
