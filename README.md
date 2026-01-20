# Rock Paper Scissors (RPS)  
**Jeffrey Xie | Jan 19th**

*This is an assignment for my **ENGS106: Graduate Principles of Machine Learning** course at Dartmouth College.*

I have built a simple algorithm to play rock paper scissors. Below, I explain my reasoning and methodology.

## Methodology

We maintain a history of all former games. The Occam’s Razor approach here is to form a percentage occurrence for each event on either side.

We call this the opponent ratio (`opp_ratio`) and our own ratio (`self_ratio`).

They are presented in the form of an array, where each index represents a move:

* `index 0` – rock  
* `index 1` – paper  
* `index 2` – scissors  

With this weighted information, we can naively say that the percentage chance the AI plays move *i* is defined by `opp_ratio[i]`.

Similarly, we can naively say that the AI will predict us to play move *i* according to `self_ratio[i]`.

Hence, we have a dual hierarchy. Consider move *i*. I ought to play move *i* if I believe the AI will play *i − 1* (where indices wrap around from 0 → 1 → 2 → 0, etc.). Now consider the other side: if the AI believes I will play *i − 2*, then it will play *i − 1*, and again I should play move *i*.

We sum these values such that the probability of playing move *i* is:

`opp_ratio[i − 1] + self_ratio[i − 2]`

There is one more strategy to consider:

*Should the weighting be a uniform distribution?*  
Surely, moves made 100 games ago should not reflect our current strategy. More recent moves ought to carry heavier weight. Therefore, I created custom weighting intervals for moves made some number of games ago. I wish I had a more principled justification for these intervals, but I must admit that I chose them based on vibes.

---
## Limitations + Future Work

If I had the bandwidth to play 100 more games and track results against the AI, I would likely introduce loss-based weighting.

Consider the case where we are losing more games in the most recent 10% of matches. This likely indicates that the AI has switched to a strategy that beats ours. In response, we could introduce a weighting coefficient `alpha` to adjust our weighting intervals using the formula:

`alpha = beta / (0.33 − loss_percent_x)`

where `beta` is a constant and `loss_percent_x` is the loss rate over the past *x* percent of games. We use 0.33 because it is the expected value of losses.

Naturally, as `loss_percent_x` increases, `alpha` increases as well, placing more weight on recent intervals and steering the model toward a new strategy. If `loss_percent_x` is relatively low, then our current strategy may be working, and we ought to steer less aggressively.

---
## Results

With that, this is our simple algorithm for RPS, which I will call the Occam’s Razor Model (`ORM`).

Using `ORM`, I played against the AI at http://www.essentially.net/rsp. The AI’s overall win percentage is **62.07%**. However, its win rate against `ORM` was **only 20.6% across 107 games**. The results are shown below:

* **43 Wins (40.2%)**  
* **22 Losses (20.6%)**  
* **42 Ties (39.3%)**  

*Results are stored in `evaluation.txt`.*

The `ORM` was trained using `training.txt`, which consists of my personal strategy against the AI over 100 rounds. My personal strategy (*which was just vibes*) resulted in the following metrics:

* **30 Wins (30.0%)**  
* **36 Losses (36.0%)**  
* **34 Ties (34.0%)**  

We observe a **10.2% increase** in win rate against the AI using `ORM` compared to my vibes-based methodology. Moreover, we reduced the AI’s global average win rate by more than **40%** using `ORM`.

---
In writing this, I am noticing many hyperparameters that could be tuned to further optimize `ORM`. Unfortunately, this will remain a simple “Occam’s Razor model” for now until I find employment.
