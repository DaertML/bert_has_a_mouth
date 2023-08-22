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
Given the prompt:
~~~bash
In order to make a sandwich, first [MASK]
~~~

We obtain the following text generation:
~~~bash
In order to make a sandwich, first like thinking inside possible world two work once another right behind missing story me until was cool without deep formation building go on real formation shot line a step gone started lose on too back about strike ring as possible deep pit stop ii begins stopped track hit broke about so until on pulled safe once movement call formation deep port attack song we i went we told o names said was still boy dead of cancer over loose safe standing
~~~

Something that lacks a correct syntax; making less sense when considering the semantic meaning; and we can not even consider that this approximates to something meaningful
if we consider that we are prompting it to make a sandwich.

By asking a similarly small model, and with as much capabilities as the BERT model, from the GPT family; we get a semantically correct, and kind of meaningful response from the model:
Giving this prompt to gpt2:
~~~bash
In order to make a sandwich, first 
~~~

We obtain the following generation:
~~~bash
In order to make a sandwich, first you need to make sure you have a good amount of fat in your sandwich.
~~~

An absolutely unhealthy suggestion by gpt2, but with absolute semantic meaning in english, and speaking of something with absolute relationship: fat as an ingredient for a prepared meal.

With that in mind, and the first failure on our backs; we should consider different ways to improve this process of text generation with BERT models.

Something that has absolutely improved the performance of the process (I do not have empiric measures for it, but in practice, I find this useful) is to take advantage
of the way the model is trained, and attempt to mimic the process that it follows during training. Unless you fine tune the BERT model to predict the last word in the sentence
(something that should potentially boost the performance of the model and work just as fine as gpt2 if we compare them), we would be losing a lot of capabilities from BERT,
as such models are trained, by making the model to consider the future words and previous words to each [MASK] token, because of that, BERT will absolutely know about what we are
talking about, if we add context at the end of the sentence, at least some context.
