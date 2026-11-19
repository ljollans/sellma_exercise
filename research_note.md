# Lee Jollans SELLMA exercise 18_11_26


### Sparks of Weight-Space Programming: LLMs can natively read & write neural network weights
https://rikiyat.github.io/blog/weight-space-programming/


-------------------

### Hypothesis: 
Comparing attention maps in the middle layers when a model fails and when it succeeds can be used by an LLM to make targeted changes and improve swedish language comprehension.

--------------------

_Pretrained model:_ distilbert-base-multilingual-cased

_Swedish language dataset:_ Swedish ABSAbank

_LLM used for editing:_ Claude Sonnet 5.5

Full Claude input and output in separate files.

---------------------------

### __Experiment 1__

_Can prompt engineering improve responses?_

#### _experiment 1a:_ 
Model performance for simple factual questions in swedish taken from https://drivetest.se/sv/blog/korkortsfragor-gratis/
- Du kör inne i en tätort. Det finns inga hastighetsskyltar. Hastighet som gäller är ?  kmh. Rätt svar:  bashastigheten i tätort är 50 km/h om inget annat är skyltat

distilbert output:          
``` 
100  0.013
180  0.012
160  0.012
220  0.011
50  0.011
```

-Du kör på en väg utanför tätort utan hastighetsskyltar. Högsta tillåtna hastighet är ? kmh. Rätt svar: utanför tätort är bashastigheten 70 km/h.

distilbert output:          
``` 
100  0.016
180  0.015
160  0.015
50  0.013
200  0.011
```

#### _experiment 1b:_ 
Model performance when Claude is asked to engineer the prompts. 

Claude suggests the following alternative formulations: 

Om det inte finns några hastighetsskyltar är högsta tillåtna hastighet inom en tätort [MASK] km/h.

distilbert output:          
``` 
100  0.038
50  0.019
1000  0.017
60  0.016
180  0.014
```


Om det inte finns några hastighetsskyltar är högsta tillåtna hastighet på en väg utanför tätort [MASK] km/h.

distilbert output:          
``` 
100  0.026
1000  0.017
50  0.016
60  0.014
500  0.012
```


På en väg inne i en tätort utan hastighetsskyltar får du köra högst [MASK] km/h.

distilbert output:          
``` 
100  0.036
180  0.028
160  0.024
50  0.020
150  0.019
```


På en väg utanför tätort utan hastighetsskyltar får du köra högst [MASK] km/h.

distilbert output:          
``` 
100  0.027
180  0.024
160  0.022
50  0.017
150  0.015
```





--------------------
__Experiment 2:__ Model performance for sentiment labeling with the ABSA dataset:

_2a:_ performance after training a classification head for 10 epochs while freezing layer 1-3 
```
'test_loss': 0.9249855875968933, 
'test_spearman': 0.004354671421994384, 
'test_pearson': -0.0031653717160224915, 
'test_krippendorff_alpha': -0.00716413077396072
```



_2b:_ performance after changing layer 4 and 5 weights based on claude feedback. Prompt given to claude: 
>"The uploaded files represent layer 4 and layer 5 of a distilbert model. This model performed extremely poorly on a sentiment analysis task in the swedish language, information about which can be found here: https://sprakbanken.se/en/resources/absabank-imm. In addition to these weights I had uploaded some hidden states from the model. Attempt to edit the model weight tensors to make the model perform better. You are allowed to run small-scale tests."

```
'test_loss': 1.0194649696350098, 
'test_spearman': 0.01941931706948329, 
'test_pearson': 0.03886289522051811, 
'test_krippendorff_alpha': -0.030257819855837642

```

## Conclusion:
This test was too small to really see any capabilities that Claude may have had in this scenario. The option to apply reinforcement learning would have been useful, as would longer finetuning of the model. 
An important point that Claude made in experiment 1 was that the distilbert model likely answered the factual questions wrong due to lacking context, simply not knowing the information.

## Next steps:
The sentiment dataset is a very interesting case since it requires grading the degree of sentiment. In a larger scale test it may be possible for weight-space programming/analysis to at least narrow down where in the model this implicit meaning is correctly or incorrectly encoded.

## Possible future directions:
Giving an english language model the ability to engineer prompts and provide context where needed for a swedish-language model trained on a large swedish corpus may be a viable approach.
