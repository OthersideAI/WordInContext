# Re-Running Word in Context

In their initial paper [cite] the authors thought that davinci wasn't capable of word in context. Early bloggers had found that it was possible to do it using a thinkthru step, but hadn't done a readable writeup. We figured we'd see the current state of the models for conducting word in context to see what's what.

Kind of cool, in our initial runs we got

| Model Name         | Textual_10shot | JSON_10shot | Code_10shot | Code_0shot |
| ------------------ | -------------- | ----------- | ----------- | ---------- |
| Davinci (original) | **60%**        | 57%         | 55%         | 47%        |
| text-davinci-001   | **61%**        | 58%         | 52%         | 52%        |
| text-davinci-002   | 39%            | 63%         | **65%**     | 55%        |
| text-davinci-003   | 68%            | **69%**     | 67%         | 61%        |
