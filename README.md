# Re-Running Word in Context

In OpenAI's initial GPT3 paper [Language Models are Few-Shot Learners (2020)](https://arxiv.org/abs/2005.14165), the authors thought that davinci wasn't capable of word in context (WIC). Later that year, one of our devs had [posted on an obscure wiki](http://gptprompts.wikidot.com/linguistics:word-in-context#toc5) that it was possible to do it using a thinkthru step, but hadn't done a readable writeup. GPT's ability to do WIC isn't particularly special or controversial, but it's an interesting emergent capability that's pretty cool. We figured we'd see the current state of the models for conducting word in context to help show that some features of GPT3 have been around for a while and are continuing to improve.

All of our code and results are in notebooks and json files in the included folders. We ran against the dev set as done in the 2020 paper. We should have a blog post up on this at some point but for now, here's the readme.

## Running the Code

To run the code, download this repo. Then run

`pip install -r requirements.txt`

Once that's installed, run

`jupyter notebook`

and open up the notebook you want to run from any of the folders. They're labeled by which model they use (which is pretty much the only difference between the .ipynb files in each of the folders - the prompts are the same within each folder). They also save the results to a handy json folder which is already included.

# Results

Newer versions of davinci have generally outperformed older ones, with the exception of text-davinci-002 seeming to do poorly on one of them. We should look into that in the JSON files to see what's going on because it's a bit weird. Also we can see it looks like the code version improved performance for 002 over JSON but not for 003.

| Model Name         | Textual_10shot | JSON_10shot | Code_10shot |
| ------------------ | -------------- | ----------- | ----------- |
| Davinci (original) | **60%**        | 57%         | 55%         |
| text-davinci-001   | **61%**        | 58%         | 52%         |
| text-davinci-002   | 39%            | 63%         | **65%**     |
| text-davinci-003   | 68%            | **69%**     | 67%         |

Since we've been using code a lot for our prmopts, we dove a bit more into how different ways of implementing code prompts change the performance. First, we tried a [0 shot](#Code_0shot_scratch) that output a JSON including a thinkthru output of meaning. Then, we tried just having it [hallucinate that it computed that intermediary step](#code_0shot_thinking). Finally, we just tried having it generate [without any intermediary steps](#code_0shot_noscratch) to see if that hallucinated helped anything.

Code Prompts

| Model Name         | Code_0shot_scratch | Code_0shot_thinking | Code_0shot_noscratch |
| ------------------ | ------------------ | ------------------- | -------------------- |
| Davinci (original) | 47%                | -                   | -                    |
| text-davinci-001   | 52%                | -                   | -                    |
| text-davinci-002   | 55%                | 50%                 | 50%                  |
| text-davinci-003   | 61%                | 60%                 | 59%                  |

# Few Shot Prompts

The few shot prompts in this all use examples from the tail of the training set. These are divided into sets of 10 for verbs and 10 for nouns.

## Textual_10shot

The Textual_10shot prompt is a 10 shot prompt that can be found in any of the notebooks in the [ReadablePrompt](ReadablePrompt) folder. It looks like this:

```
Contexts: 'The French doors admit onto the yard .'; 'He admitted his errors .'
Term: 'admit'
Meaning: In the first sentence, 'admit' means to provide passage. In the second, 'admit' means to take responsibility.
They are dissimilar

Contexts: 'The company agrees to meet the cost of any repairs .'; 'Does this paper meet the requirements for the degree ?'
Term: 'meet'
Meaning: In the first sentence, 'meet' means to fulfill. In the second, 'meet' means to take fulfill.
They are similar

...
```

## JSON_10shot

The JSON_10shot prompt is a 10 shot prompt that can be found in any of the notebooks in the [JSONPrompt](JSONPrompt) folder. It looks like this:

```
{
"Sense_1": "The French doors admit onto the yard .",
"Sense_2":"He admitted his errors .",
"Term": "admit",
"Meaning_1": "In the first sentence, 'admit' means to provide passage.",
"Meaning_2": "In the second, 'admit' means to take responsibility.",
"Similar": true
}

{
"Sense_1": "The company agrees to meet the cost of any repairs .",
"Sense_2": "Does this paper meet the requirements for the degree ?",
"Term": "meet",
"Meaning_1": "In the first sentence, 'meet' means to fulfill.",
"Meaning_2": "In the second, 'meet' means to fulfill.",
"Similar": true
}

{
"Sense_1": "You anger too easily .",
"Sense_2": "He angers easily .",
"Term": "anger",
"Meaning_1": "In the first sentence, 'anger' means to enrage.",
"Meaning_2": "In the second, 'anger' means to enrage.",
"Similar": true
}
...
```

## Code_10shot

Finally, the Code 10 shot is a variant of the JSON_10shot prompt that can be found in any of the notebooks in the [CodePrompt](CodePrompt) folder. It looks like this:

```
interface comparison{
    "Sense1": str, // the sense of the word in the first context
    "Sense2": str, // the sense of the word in the second context
    "areSimilar": bool, // whether the two senses are similar
}

determineWordSimilarSense(word, context1, context2) : comparison =>{
    return ai.compare(word, context1, context2) // return the comparison object
}

determineWordSimilarSense("The French doors admit onto the yard .", "He admitted his errors .", "admit")
>>> {
"Sense1": "In the first sentence, 'admit' means to provide passage.",
"Sense2": "In the second, 'admit' means to take responsibility.",
"Similar": true
}

determineWordSimilarSense("The company agrees to meet the cost of any repairs .", "Does this paper meet the requirements for the degree ?", "meet")
>>> {
"Sense1": "In the first sentence, 'meet' means to fulfill.",
"Sense2": "In the second, 'meet' means to fulfill.",
"Similar": true
}
...
```

# 0 Shot Prompts

The 0 shot prompts have no examples. They start off from most complex to least complex as we test whether the more complex things we started with actually improved on anything. We started with more complex prompts because we tested it after running the few shot code prompt above and realized we wanted to see how giving less instructions changed things.

Nouns and verbs use the same 0 shot prompts.

## Code_0shot_scratch

The Code_0shot_scratch prompt is a 0 shot prompt that can be found in any of the notebooks in the [CodePrompt0Shot](CodePrompt0Shot) folder. It looks like this:

```
interface comparison{
    "Sense1": str, // the sense of the word in the first context
    "Sense2": str, // the sense of the word in the second context
    "areSimilar": bool, // whether the two senses are similar
}

determineWordSimilarSense(word, context1, context2) : comparison =>{
    return ai.compare(word, context1, context2) // return the comparison object
}

```

## Code_0shot_thinking

The Code_0shot_thinking prompt is a 0 shot prompt that can be found in any of the notebooks in the [CodePromptConditional](CodePromptConditional) folder.

Instead of explicitly writing out the `Sense1` and `Sense2` steps from the `Code_0shot_scratch` prompt, we instead have the model hallucinate that it computed those steps. It looks like this:

```
interface comparison{
    "Similar": bool, // whether the two meaings of the word are similar or used to mean different things, should be true | false
}

determineWordSimilarSense(word, context1, context2) : comparison =>{
    senseOfFirstWord = ai.computeSense(word, context1) // return the sense of the word in the first context
    senseOfSecondWord = ai.computeSense(word, context2) // return the sense of the word in the second context
    return ai.compare(word, senseOfFirstWord, senseOfSecondWord) // return the comparison object
}

//This returns the keys inside of double quotes ("KEY") so we can parse with JSON
```

## Code_0shot_noscratch

Finally, we check without having it hallucinate to see whether the hallucination helped or not. See the [CodePromptYolo](CodePromptYolo) folder for the code.

The prompts look like this:

```
interface comparison{
    "Similar": bool, // whether the two meaings of the word are similar or used to mean different things, should be true | false
}

determineWordSimilarSense(word, context1, context2) : comparison =>{
    return ai.compare(word, context1, context2) // return the comparison object for if the sense of thr word in both contexts is similar
}

//This returns the keys inside of double quotes ("KEY") so we can parse with JSON
```
