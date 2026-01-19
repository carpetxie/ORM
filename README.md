# Rock Paper Scissors(RPS)
**Jeffrey Xie | Jan 19th**

*This is an assignment for my **ENGS106: Graduate Principles of Machine Learning Course at Dartmouth College.***

I have built a quick algorithm to play rock paper scizzors. I will explain my reasoning with an explanation of the methodology:

## Methodology

We have a history of all former games. The Occam's Razor Approach is simply to form a percentage occurence for each event on either side. 

We call this opponent ratio(`opp_ratio`) and our own ratio(`self_ratio`). 

They are presented in in the format of an array, where each index represents a move:

* `index 0` - rock
* `index 1` - paper
* `index 2` - scissors

With this weighted information, we can naively say that the percentage chance the AI plays move *i* is defined by `opp_ratio[i]`. 

We can also say naively that the AI will naively predict us to play any move *i* is defined by `self_ratio[i]`. 

Hence, we have a dual hierarchy. Consider move *i*. I ought to play move *i* if I believe the AI will play *i-1* (consider the index loops around from 0 -> 1 -> 2 -> 0, etc.). We then consider the other side: if the AI believes I will play *i-2*, then it will play *i-1* and I should play move *i*. 

We sum these values such that the probability of playing move *i* = `opp_ratio[i-1]` + `self_ratio[i-2]`. 

Now, there is one more strategy to consider:

*Should the weighting be a uniform distribution?* Surely, moves made 100 moves ago should not reflect our current strategy. Moves made most recently ought to hold heavier weights. Therefore I made custom weightings intervals for moves made some amount of moves ago. I wish I had some logic for these intervals, but I must admit that I chose them from vibes. 

---
## Limitations + Future Work 

If I had the bandwith to play 100 more games and track the results with the AI, I would likely use some loss weighting:

Consider that we are losing more games in the past 10% of games. Then that must mean the AI switched to a strategy that has beaten ours. Then we ought to use a weighting coefficient `*alpha*` to weigh our weighting intervals from above using the formula *`alpha = beta / (0.33 - loss_percent_x)`*, where `beta` is some constant and `loss_percent` is the losses incurred in the past `x` percent of games. We use 0.33 as that is the expected value of losses. 

Naturally, as the `loss_percent_x` increases, `alpha` will increase, hence increasing the weighting on our intervals, steering towards a new strategy. If `loss_percent_x` is relatively low, then perhaps our current strategy is working and we ought to steer less. 



---
## Results

With that, this is our simple algorithm for RSP. I will call it the Occam's Razor model(`ORM`). 

Using `ORM`, I played against the [AI](http://www.essentially.net/rsp). The overall win percentage for the AI is **62.07%**. Unfortunately, their win rate against `ORM` was **only 20.6% among 107 games**. The statistics are noted below:

* **43 Wins (40.2%)**
* **22 Losses (20.6%)**
* **42 Ties (39.3%)**

*Results of the game are stored in `evaluation.txt`.

The `ORM` was trained by `training.txt`, which consists of my personal strategy against the AI for 100 rounds. My personal strategy(*which was just vibes*) resulted in the following metrics:
* **30 Wins (30.0%)**
* **36 Losses (36.0%)**
* **34 Ties (34.0%)**

We note a **10.2%** increase against the AI using the `ORM` as opposed to my `vibes methodology`. Moreover, we reduced their global average win rate by more than **40%** using the `ORM`.


---

In writing this, I am noticing many hyperparameters that could be tuned to optimize the `ORM`. Unfortunately, this will remain as simple 'Occam's Razor model' for now until I find employment. 
