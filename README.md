# Re-Running Word in Context

In their the [Initial 2020 GPT3 Paper](https://arxiv.org/abs/2005.14165) the authors thought that davinci wasn't capable of word in context. Early bloggers had found that it was possible to do it using a thinkthru step, but hadn't done a readable writeup. We figured we'd see the current state of the models for conducting word in context to help show that some features of GPT3 have been around for a while and are continuing to improve.

Kind of cool, in our initial runs we got

| Model Name         | Textual_10shot | JSON_10shot | Code_10shot |
| ------------------ | -------------- | ----------- | ----------- |
| Davinci (original) | **60%**        | 57%         | 55%         |
| text-davinci-001   | **61%**        | 58%         | 52%         |
| text-davinci-002   | 39%            | 63%         | **65%**     |
| text-davinci-003   | 68%            | **69%**     | 67%         |

We dove a bit more into code :) basically 3 methods. First, we do an intermediary step on the generated JSON for meaning. Then, we tried just having it hallucinate that it computed that intermediary step. Finally, we just tried having it generate without it to see if that hallucinated helped anything.

Code Prompts

| Model Name         | Code_0shot_scratch | Code_0shot_thinking | Code_0shot_noscratch |
| ------------------ | ------------------ | ------------------- | -------------------- |
| Davinci (original) | 47%                | -                   | -                    |
| text-davinci-001   | 52%                | -                   | -                    |
| text-davinci-002   | 55%                | 50%                 | 50%                  |
| text-davinci-003   | 61%                | 60%                 | 59%                  |

## Examples of Prompts
