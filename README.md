I spent a week testing whether a retail account can extract anything from Polymarket and short-horizon crypto contracts. Four hypotheses, each pre-registered before looking at results. All four failed. Posting it so nobody repeats the work.

**The part worth your time isn't the results. It's that I found five separate "edges" that were all measurement errors.**

The best one: market was pricing "Up" at 49.5% while spot sat above the strike. My model said 61%. Twelve points. Enormous.

It was the strike being off by 2.3 basis points — USDT/USD basis, or a timing offset of a few seconds. At a 4-minute horizon with 30% annualised vol, that rounding difference moves the implied probability by ten points. If I'd traded it I'd have been confidently on the wrong side of every single fill.

The other four: prices read from a cached API field lagging by minutes (live book said 0.965, cache said 0.455, and I logged the cache for 7 hours); implied vol of 120,850% from sampling 2 seconds before settlement where the effective time term goes to zero; resolution lookups silently returning nothing because resolved markets drop off the slug endpoint; and one concern about time-clustering that I tested across 80 simulated runs and found didn't matter — included because testing a suspicion instead of asserting it is the same discipline.

**What I actually tested:**

1. *Copy the top wallet.* The account everyone posts about ($111 → $4.3M). Reconstructed 39,408 trades from the activity API. It bought 15,700 times a day across 3,491 markets, in slices of up to 217 per market, sold literally zero times. There is no discrete bet to mirror. Also it stopped trading on 26 Aug and the viral post was 7 months stale.

2. *Volatility model on the 5-minute crypto contracts.* These settle on a Chainlink 60s TWAP, so strike, spot and time are all known exactly and the only free parameter is vol. Collected 46h, 72,832 live order book quotes, 4,427 markets, reconstructed resolutions validated at 99.2% against prices recorded seconds before close. Result: −1.38 cents/trade over 3,745 trades. Calibration clean in 9 of 10 price bands. Half the loss is the spread, half is my vol estimate being worse than the market's.

   The most useful stat: the rule fired on 85% of markets. A filter that triggers 5 times in 6 isn't selecting anything, it's telling you your estimator is noisy.

3. *Sum arbitrage on multi-outcome events.* 949 order books, 142 events. Minimum sum of asks was exactly 1.0000. negRisk enforces it at the platform level. Also: with a 0.001 tick, an event with 128 outcomes can't sum below 1 even in principle — a hundred hopeless candidates cost 10c in tick floor alone.

4. *Monotonicity in cumulative "by date X" ladders.* Anything true by September is true by December, so price must rise with date. 17 ladders, 260 books, checked pairwise. Zero violations.

**Honest caveat on #2:** the block-bootstrap interval is −3.96 to +1.30c, so it includes zero. The rule is not *proven* to lose. What's established is that it shows no profit, and the independent calibration result points the same way. I'm not rounding that toward my conclusion.

Yes, my model was crude — 15-minute realised vol. That's the point. Bitcoin vol is continuously traded by firms whose whole business is estimating it. A retail estimator isn't going to beat that consensus, and the measurement says so instead of me guessing.
