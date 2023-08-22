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

Prior to starting with the experiments taken, and the researched ideas in the ecosystem; a quick introduction on
how BERT models work, would be interesting:
- BERT models predict the words in a sentence marked with the special token "[MASK]".
- BERT models have access to the contents prior to the "[MASK]" tokens, and after the "[MASK]" tokens; they work a bit different to how GPT
like modeks work, as the GPT like models only have access to the contents prior to the word to be predicted.
- BERT models provide for each "[MASK]" token, a list with the most likely words to be the filling in each case, to make the sentence semantically correct and meaningful.

Following the ideas from different forums, blogs, and repositories, I have found many responses to the question
to suggest that by simply asking the BERT model to predict the next word, by masking a blank word, and then
appending it to the prompt, and recursively (in a loop) asking for the next word; BERT should be able to generate
semantically correct and meaningful text (and we could not resist the attempt to not try it :) ).

With that being said, we have found that by just adding one [MASK] at the end of the sentence, and asking BERT
for the most likely word, and repeating over and over in a loop, will not work to provide meaningful sentences.

Here is a quick experiment that you can attempt with the source code in the repository, and the obtained result:
~~~bash
In order to make a sandwich, first like thinking inside possible world two work once another right behind missing story me until was cool without deep formation building go on real formation shot line a step gone started lose on too back about strike ring as possible deep pit stop ii begins stopped track hit broke about so until on pulled safe once movement call formation deep port attack song we i went we told o names said was still boy dead of cancer over loose safe standing
~~~
