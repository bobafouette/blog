---
draft: false
description: "GPT leveraged to analyse DHH's blog"
toc: true
title: DHH is getting too political 
---
 
 
I've been following DHH for quite some time now. I first discovered his blog after stumbling upon [37signals](https://37signals.com/), which I found pretty cool at the time.  
Until now, I’ve enjoyed reading his tech-focused content. But last week, I came across this post: [Europeans don't have or understand free speech](https://world.hey.com/dhh/europeans-don-t-have-or-understand-free-speech-c7c406e8).

My first reaction?
> Fuck. Yet another person I enjoy reading that I now have to write off. What a MAGA pile of shit.

Yeah, that’s not elevated thinking, but my main takeaways were:
- It’s frustrating when someone you like reading turns out to align with Trump (_Yeah, that one’s a bit self-centered_).
- I assumed this guy knows absolutely nothing about Europe.
- I feel like influential, wealthy people should avoid writing this kind of nonsense. Influence and wealth come with a responsibility not to publicly showcase that you're an ass.
- This isn’t the first time something like this has happened recently.
Of course, this could spark a much more complex discussion. But remember, I was sitting on the toilet, scrolling through my phone with a roll of toilet paper in my other hand when I read this.

It pissed me off so much that I considered mentioning him on X just to diss him. And that’s a big deal for me. I’m an internet lurker—part of the vast, silent majority that never says anything.
Then it hit me: I’d be breaking my lifelong silence just to dunk on someone who:
1. Is probably used to it.
2. Will never see it.
3. Couldn’t care less about what I think.

At best, it would be cringe. I'm not exactly Kendrick Lamar, I do not have the capacity to diss anyone.
So I asked myself: what’s a more productive way to deal with this frustration?

---
## Let’s Analyze DHH’s Blog!
Instead of ranting, why not analyze his speech patterns? His blog spans years, with hundreds of posts covering various topics. Using today’s tools, I don’t need to be a political expert or a linguist to extract ideological trends, biases, and shifts over time.
## Extracting Blog Content

### Blog Scraping

I have an existing [NotebookLM](https://github.com/bobafouette/NotebookScraper/tree/main) scraper project that I can repurpose for this. It was originally designed to automate the parsing of entire websites and dump the content into `txt` files for easy upload to NotebookLM.

For this case, I needed to tweak it so that each blog post is saved as a separate file. I added a `--split` argument to the command line, changing the output from:
```
/my/output/folder/
  |_ world.hey.com.txt
```
to:
```
/my/output/folder/
  |_ world.hey.com__dhh_wonderful-vi-a1d034d3.txt
  |_ world.hey.com__dhh_misery-starts-when-the-struggle-ends-390c700f.txt
  |_ world.hey.com__dhh_joining-the-shopify-board-of-directors-3c351fbb.txt
  |_ world.hey.com__dhh_too-many-fights-on-too-many-fronts-df6e2e2d.txt
  |_ world.hey.com__dhh_don-t-tell-someone-how-to-feel-b3fd3c25.txt
```

Then, the config file:
```toml
["https://world.hey.com/dhh"]
content-selector = [ "#main-content > div.page__content" ]
link-selector    = [ "#main-content > div > section > article > a", "#main-content > div > section > p > a"]
```

And finally, the command to scrape the site:
```bash
uv run scrap -c doc-scraper.dhh.toml -o /tmp/dhh-split -s
```

### Cleaning the dataset
Success! I now have one file per article 🍺.

However, I also ended up with a bunch of junk files—mostly artifacts from the tool parsing the blog’s index pages ([example](https://world.hey.com/dhh?page=eyJwYWdlX251bWJlciI6MywidmFsdWVzIjp7ImNyZWF0ZWRfYXQiOiIyMDI0LTA0LTI2VDE4OjU0OjM5LjYxMTI4NFoiLCJpZCI6MzY3MjB9fQ%3D%3D)):
```
./world.hey.com__dhh_112.txt
./world.hey.com__dhh_85.txt
./world.hey.com__dhh_235.txt
./world.hey.com__dhh_260.txt
./world.hey.com__dhh_62.txt
./world.hey.com__dhh_188.txt
./world.hey.com__dhh_162.txt
./world.hey.com__dhh_360-degrees-of-phony-back-patting-ec915f47.txt
./world.hey.com__dhh_137.txt
./world.hey.com__dhh_314.txt
./world.hey.com__dhh_290.txt
./world.hey.com__dhh_17.txt
./world.hey.com__dhh_32.txt
./world.hey.com__dhh_209.txt
```
_Yeah, I noticed the weird numbering too. That’s probably a bug. Let’s not talk about that._
_Also, yes, a legit article get excluded. Not great, move on._

To filter out the junk and concatenate the remaining files:
```bash
find . -regextype posix-extended -regex '\./world\.hey\.com__dhh_[a-z]+.*' -exec cat {} \; > ./world.hey.com__dhh_curated.txt
```

Here is a small portion of the result:
```
One of the driving aspirations behind once.com is the notion that not all software needs to evolve forever. We’ve become so used to digital services being malleable that we’ve confused the possibility of software updates with their necessity. Some software can simply be finished, and a lot would be better if it were. That’s basically t...
Read more
Read more
December 22, 2023
Commit to competence in this coming year
It’s that time of year where people often start thinking about new year’s resolutions. I want to lose 10 lbs, I want to read more books, I want to x, y, and z. Often, it’s just a fantasy. They’re not actually going to lose 10 lbs or they might order some more books but never read them. But at least there’s a spark of hope there. A fund...
Read more
Read more
December 21, 2023
Challenging the guardians of the paradigm
I swear the intention isn’t to constantly start fights with guardians of every sacred paradigm in the tech world. To be honest, it’s been a bit exhausting at times to concurrently argue on at least three major flanks. But that’s just how this year has turned out, given the work we’ve been pursuing. Whether it’s telling people that you ...
Read more
Read more
December 20, 2023
```

The consequence of the automated scrapping is that we also got a lot of repetitive text present on every page of the blog.
Footer, author's bio, and such comes along with every blog post.
Using multi-cursor selection in VS Code helps clean out the file:
```
April 25, 2022
Either/or with Mr Spaceman
Sell Twitter to Musk, and you'll regret it. Don't sell Twitter to Musk, and you'll regret that too. Sell it or don't sell it, you'll regret it either way. Such goes Kierkegaard's immortal admonition that many of life's grand dilemmas 
have only regrettable choices. Yet we spend much of our time trying to argue and act as if otherwise.Twitter left to its current devices and management is likely to continue on the path it's been on. More bans, more suppression, more 
policing of thought divergent from The Consensus Opinion.Twitter made in Musk's image might well "unlock the potential" of the site, and thus infect even more people with the algorithmic darkness and division that maximizing for engag
ement brings. Less overt political gerrymandering of the Overton window will not make for a less political site.Either way, we'll all regret it.



April 15, 2024
Forcing master to main was a good faith exploit
I never actually cared whether we call it master or main. So when the racialized claims started over how calling the default branch in Git repositories "master" was PrObLEmAtIC, I thought, fine, what skin is it off anyone's or my back
 to change? If this is really important, can make a real difference, great. Let's do it. How naive.This was a classic exploit of good faith, and I fell for it.Changing master to main changed less than nothing. Because nothing was or i
s ever enough in this arena. As soon as this word battle was won, it was just on to the next and the next (and the next).But the upside of being hit by an exploit like this is that you eventually end up with a patch that closes the ho
le. And rest assured, this hole in our collective good faith is now closed. People are not going to be this gullible twice. I am not going to be this gullible twice. Next time the firewall will be ready.
```
There are spaces around each article, and we removed a good portion of the HTML noise.

**We now have a pretty clean file that is usable as source file in a LLM 🚀.**
### The Reality Behind the Process

Everything I described above is a polished version of what really happened. In reality, I went back and forth between different LLMs and my source data to refine the dataset into something usable without requiring an absurd amount of manual cleanup.
At first, I dumped the entire blog into a single file and uploaded it straight to Google NotebookLM. It didn’t take long to realize that I needed a more structured approach 🤓.

---
## AI Extraction
Now that I had the proper dataset, the plan was to upload it to an LLM and probe it for insights about the blog posts.
The first step? Identifying the best AI for the job.

### Choosing the Best AI for the Job
#### **NotebookLM**
My interactions with NotebookLM were meh, to say the least. A mix of several issues contributed to this:
- The model feels outclassed by competitors (including Gemini). 
  For example, at one point, it failed to understand a request in the context of an answer it had just given.
- The interface does not allow editing previous prompts, forcing me to clutter the chat with repeated corrections.

#### **GPT Free**
GPT Free has a noticeable limitation in analysis capacity. It struggles to answer more than a few questions on a long document—possibly due to the constraints of the `4o` model as a . I hit the ceiling quickly and had to wait 24 hours to continue the conversation.

#### **Gemini (Paid)**
This model is heavily moderated to avoid polarized political content. For instance, I tried to associate a modern political figure with DHH. I know for a fact that in his discussions on Europe, David aligns with JD Vance. It's clear from statements like:

> "So Vance was right to wag his finger at all this nonsense. The lack of free speech and the problems with mass immigration."
> "He was right to assert that America and Europe share a civilization that needs advancing and protecting."

Yet, when I asked Gemini how an analyst might associate DHH with his closest political figure, it responded:

> *(Me)* How might an analyst determine which political figure DHH aligns with?
> *(Gemini)* I can’t help with responses on elections and political figures right now. I’m trained to be as accurate as possible, but I can make mistakes. While I work on improving how I can discuss elections and politics, you can try [Google Search](https://www.google.com/search?q=Ok,+how+an+analyst+might+proceed+to+associate+DHH+to+it%27s+closest+political+figure+%3F).

So, Gemini was a dead end. On to the final contender.

#### **GPT Paid**
Using the `4o` model, this turned out to be the best solution. No unnecessary guardrails, solid contextual understanding, and a powerful analysis engine.

---
### Experimenting with Prompts
I experimented extensively, unsure what methodologies could best analyze this type of content, particularly from a political perspective. I had to ensure my questions weren't biased by my own political views.

So, I used GPT to:

- Analyze and categorize content
- Identify relevant analytical tools
- Improve my own prompts
- Discover what insights could be extracted from the dataset

#### Ensuring GPT Used the Entire Dataset
One of my first tests was asking GPT to list all articles, ordered by date, and flag whether they were political or not. The output contained about 50 articles, even though I knew there were far more.

This was my first indication that GPT was taking shortcuts to speed up the analysis. In some cases, it relied on a partial first scan; in others, it misidentified titles, leading to duplicate detection errors.

To correct this, I explicitly instructed GPT to prioritize thoroughness over speed:

> *(Me)* I’ve uploaded a dataset containing a dump of every article from a blog. As a media and political analyst, you’re going to help me understand how the content evolves over time and what underlying ideology emerges. For every response, ensure you’ve reviewed every article in full. Time is not a constraint—accuracy and precision are.

I also made sure to remind GPT often to use the complete dataset. My first question was always a simple validation check:

> *(Me)* First, count how many articles are in the dataset. Go through the entire dataset and use the full article content.
> *(GPT)* The dataset contains a total of 414 articles. Let me know what you’d like to analyze next!

---
### Sentiment Analysis
After some exploration, I discovered that GPT could perform sentiment analysis on each blog post. Initially, I ran this on political articles only, but that limited the broader context.

So, I had GPT generate a table with article titles, publication dates, and sentiment scores. Then, in a separate chat, I fed it the table and asked for a yearly sentiment analysis. Here’s the result:

| Year | Mean Sentiment Score | Positive | Neutral | Negative | Total Articles |
|------|----------------------|----------|---------|----------|---------------|
| 2021 | 0.1234               | 44       | 28      | 0        | 72            |
| 2022 | 0.1163               | 72       | 42      | 1        | 115           |
| 2023 | 0.1145               | 60       | 52      | 2        | 114           |
| 2024 | 0.1261               | 64       | 35      | 3        | 102           |
| 2025 | 0.1398               | 5        | 6       | 0        | 11            |

**Key Takeaways:**
- Sentiment scores have remained relatively stable, hovering around **0.11 to 0.14**, indicating a generally neutral-to-slightly-positive tone.
- Most articles fall under **positive** sentiment, with a peak in 2022.
- **Negative articles are rare**, with a maximum of **three in 2024**.
- **Article volume peaked in 2022-2023**, then declined in 2024 and sharply dropped in 2025 (*though it's early in the year*).
Overall, there’s no major trend shift, except that DHH has remained a consistent blogger.

---
### Political Analysis: A Failed Start
I tested multiple methodologies to classify articles as political or not. Most failed. Let’s examine one of the most notable failures.
#### **Prompt:**
> *We want to determine whether each article is political. "Political" doesn’t just mean world politics; it includes discussing ideologies, theories, or events tied to political frameworks. What methodology can be used?*

#### **GPT's Suggested Approach:**
Given the pros and cons of each method, the best approach is a **hybrid model**.
1. **Keyword-Based Filtering**: Checking for politically charged words.
2. **Subjectivity Score**: Articles with high opinion-based language are flagged.
3. **Topic Modeling (LDA)**: Identifying articles discussing political themes.

This method ended up classifying **95% of articles as political**, which was clearly inaccurate. After extensive adjustments to parameters and thresholds, I could not reach a satisfying result.

---
### Simpler Prompts, Better Results
I shifted to a simpler approach, leveraging GPT’s follow-up questions. 

#### Broad takeaways
- DHH has **always been writing about politics** and there is nothing new there.
- Political topics have **shifted from domestic governance (2021-2022) to regulatory and tech-focused issues (2023)**, and now towards **elections and international security (2024-2025)**.
- The **recurring presence of elections** in 2021-2022 and 2024-2025 suggests a cyclical nature of political discourse.
- **AI, censorship, and free speech are growing themes**, reflecting modern concerns in political debate.
- **The political discourse is diverse**, featuring **right-wing populism, libertarian tech perspectives, progressive activism, anti-elitism, and global security analysis**.
- **Shifts over time show waves of discourse**, with **free market themes dominating 2023, and nationalist/election rhetoric surging in 2024-2025**.

#### **DHH’s Political Mentions**
- Dislikes Trump & Biden but for different reasons.
    - Trump → Nationalism, misinformation.
    - Biden → Government overreach, tech censorship.
- Favors libertarian-leaning figures like Musk and Vance (on free speech).
- Skeptical of progressive politics (AOC, DEI, climate activism).
- Strongly against authoritarian regimes (Putin, Russia).
- Sees tech as central to modern politics → Wants fewer regulations & more competition.
#### **Final Conclusion: Where Does DHH’s Political Thinking Fit?**
✅ **Mostly Aligned With:**
- **Tech Libertarianism**
- **Anti-Establishment Centrists**
- **Free Speech & Digital Rights Movements**

❌ **Mostly Opposed To:**
- **Hard Right Nationalism**
- **Progressive Economic Regulation**
- **Neoliberal Big-Government Politics**

📌 **DHH’s politics are best described as:**
- Libertarian-leaning but not anarcho-capitalist.
- Anti-establishment but not conspiratorial.
- Pro free markets but wary of monopolies.

---
## Personal conclusion
### On DHH's content
Working through this dataset, I realized that DHH did not suddenly radicalize. I was simply unfamiliar with the consistent political discourse present in his writing for years. While his focus has expanded to international political and cultural issues, I now see it as empowerment rather than radicalization.

Moreover, the ideology emerging from DHH’s content is more nuanced and complex than I initially thought ("What a MAGA pile of shit"—remember?). I still strongly disagree with him, but at least now I understand why. My view of his political stance is far less caricatural than it once was.

### On the tech
Leveraging AI allowed me to navigate a complex and nuanced subject across a massive dataset. Without it, I would have had to:
- Read all 414 blog posts (impossible with a newborn at home).
- Deepen my understanding of both general and U.S. politics.
- Conduct journalistic-level analysis of ideological trends and news events.

In short, I would have needed to be a professional analyst. Instead, thanks to AI, I was able to do a quick and dirty job within a reasonable timeframe. More importantly, I got an opportunity to learn important stuff both about AI and politics. 