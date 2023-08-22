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

Something that should be noted before we move on, fine tuning on a specific dataset, to make the BERT model predict words for that field, is something that has been done over
and over again in the state of the art, and is much more than useful. So yep, you are much more than welcomed to fine tune a BERT model on a certain dataset, before moving on if you wish.

With that being said, let's take advantage of the way BERT is trained; our prompts for BERT will not go in the same direction as the prompts that we use for GPT, in which we provide an
instruction or sentence, and we wait for the model to answer back. In this case we are going to prompt it using [MASK] tokens, and seeing which are the model's suggestions.

Let's go back to the example below, with this prompt for BERT:
~~~bash
In order to make a sandwich, first slice some [MASK], then put together [MASK], add [
[MASK] sauce, and add [MASK] and [MASK].
~~~

I am using the model located at huggingface, to do this example (https://huggingface.co/bert-base-uncased), because of that, if I input all the MASK tokens at the same time, it is uncapable
of predicting anything useful, and will output just "undefined" as the token. I do not know a lot of the internals of BERT models, so I do not know if it is possible to predict
multiple MASK tokens at the same time. Somehow, I told you we are going to give that prompt directly to the model, as if it were a prompt to a GPT like model, and we are going to see
the results that we obtain. In order to do that, I have removed the "[MASK]" of each [MASK] after the first [MASK] in the prompt, and kept the spaces, as the tokens were generated,
I filled in [MASK] in the places they were originally.

This is the iterative evolution of the prompt, being filled by BERT:
~~~bash
Original prompt: In order to make a sandwich, first slice some [MASK], then put together [MASK], add [
[MASK] sauce, and add [MASK] and [MASK].

1st iter: In order to make a sandwich, first slice some [MASK], then put together , add 
 sauce, and add  and .

2nd iter: In order to make a sandwich, first slice some bread, then put together [MASK], add 
 sauce, and add  and  as you desire.

3rd iter: In order to make a sandwich, first slice some bread, then put together it, add [MASK]
 sauce, and add  and  as you desire.

4th iter: In order to make a sandwich, first slice some bread, then put together it, add tomato
sauce, and add [MASK] and  as you desire.

5th iter: In order to make a sandwich, first slice some bread, then put together it, add tomato
sauce, and add cheese and .

6th iter: In order to make a sandwich, first slice some bread, then put together it, add tomato
sauce, and add cheese and [MASK].

7th iter: In order to make a sandwich, first slice some bread, then put together it, add tomato
sauce, and add cheese and [MASK].

8th iter: In order to make a sandwich, first slice some bread, then put together it, add tomato
sauce, and add cheese and ham.
~~~

And... TADA! That's a much more tasty and healthy sandwich than the one suggested by gpt2. The steps to write the prompt over and over
with the [MASK] tokens can be easily automated with code, and the answer is much more meaningful than the one provided by gpt2.

We can even enjoy the process of considering the output from the model, and choose one or another, depending on our preferences,
making the experience richer than just waiting for a GPT like model to write the response.

This absolutely feels like a win! We are moving forward with BERT models: much harder to tame, but with quite a lot
of control over it.

As a last experiment that I have gone through these last days, and some of the ideas that popped up to make this BERT thing speak! I would
like to highlight that possibly we could consider BERT models to behave as an error correcting code for text generations. That is, in a similar
way as a diffusion model starts from noise (all tokens set to PAD, or random tokens in a sentence) we could eventually feed the text to BERT,
generate such first generation of random tokens, or set them to PAD, and provide a context; the context could be taken by a retrieval mechanism
like LlamaIndex, or with classic NLP models like (GloVe or ElMo) to keep the thing small; from a corpus of data. That context (as BERT reads tokens from the future and the past
of the [MASK] token) would be read by BERT, and BERT would know that on the future, the interest are "sandwich making" generations, or "football" generations,
whatever that is, and BERT would start filling the [MASK] with meaningful contents. We could iterate on the process for a certain amount of epochs until the
text generation would be stable, or until a certain amount of epochs is reached.

Here is a quick example of the experiment; in this case, I do not provide random tokens at first, but show that the model can fill in the MASK tokens
by knowing the context given in the second sentence (in this case, I provide the rest of the MASK tokens withot [], to avoid the issue in huggingface, which is
probably a common issue in BERT models, as I yet do not know with confidence if they can predict multiple [MASK] tokens at once:
~~~bash
In order to [MASK] a MASK we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make a [MASK] we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make a sandwich we need to add [MASK]. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make a sandwich we need to add butter. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
~~~

In this next experiment, we follow the same steps, but remove even more information from the contents:
~~~bash
In order to [MASK] MASK MASK we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make [MASK] MASK we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make a [MASK] we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
In order to make a sandwich we need to add butter. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
~~~

And in this example we try to adversarially confuse it (in this case, consider that the tokens in this subsentence will be used as adversarial
and eventually replaced by a [MASK] token and then by a word by BERT: "PAD cheese Meal Food Ham"):
~~~bash
[MASK] PAD cheese Meal Food Ham we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On [MASK] cheese Meal Food Ham we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the [MASK] Meal Food Ham we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard [MASK] Food Ham we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast [MASK] Ham we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast Food [MASK] we need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich [MASK] need to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add [MASK]. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
~~~

If we consider to substitute the whole thing up until the "." we would get:
~~~bash
On the standard fast food sandwich you [MASK] to add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need [MASK] add MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to [MASK] MASK. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add [MASK]. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made of bread and other ingredients like ham, cheese, lettuce, tomato...
~~~

Absolutely reproducing the generated text in the previous example, but what happens if we continue?
~~~bash
On the standard fast food sandwich you need to add meat. [MASK] are made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches [MASK] made of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are [MASK] of bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made [MASK] bread and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with [MASK] and other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat [MASK] other ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and [MASK] ingredients like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other [MASK] like ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients [MASK] ham, cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients like [MASK], cheese, lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients like onion, [MASK], lettuce, tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients like onion, garlic, [MASK], tomato...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients like onion, garlic, pepper, [MASK]...
On the standard fast food sandwich you need to add meat. Sandwiches are made with meat and other ingredients like onion, garlic, pepper, tomatoes...
~~~

As we can see, it regenerates a valid response, providing a slightly new recipe for the sandwich :)

It feels like an epic win after all this work, as a last test that I have found interesting, one could provide adjectives or tags for the kind of sandwich that they would like to prepare,
and depending on such, the ingredients will be different, if we choose to procide tags related with how healthy it should be, the predicted words are different:
~~~bash
Sandwiches are made of bread and other ingredients like [MASK], [MASK], [MASK], [MASK]... healthy, vegetables, delicious, fit, energy.
Sandwiches are made of bread and other ingredients like meat, fish, [MASK], MASK... healthy, vegetables, delicious, fit, energy. 
Sandwiches are made of bread and other ingredients like meat, fish, vegetables, [MASK]... healthy, vegetables, delicious, fit, energy. 
Sandwiches are made of bread and other ingredients like meat, fish, vegetables, meat... healthy, vegetables, delicious, fit, energy. 
~~~

~~~bash
Sandwiches are made of bread and other ingredients like [MASK], [MASK], [MASK], [MASK]... unhealthy, fat, obesity, hamburger, bad, obese.
Sandwiches are made of bread and other ingredients like bread, [MASK], MASK, MASK... unhealthy, fat, obesity, hamburger, bad, obese. 
Sandwiches are made of bread and other ingredients like bread, sausage, [MASK], MASK... unhealthy, fat, obesity, hamburger, bad, obese. 
Sandwiches are made of bread and other ingredients like bread, sausage, bacon, chicken... unhealthy, fat, obesity, hamburger, bad, obese. 
~~~

For each of the experiments, I have mostly used the most likely result provided by BERT, in some occasions I have chosen not repeated words, or
the ones I liked most in my sandwich :) Also, remember, no finetuning has been done, and all the tests were done with bert-base-uncased (no BERTs were trained during the experiments :) )
