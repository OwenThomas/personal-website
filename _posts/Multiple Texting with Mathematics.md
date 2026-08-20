#### What is the probability of finding a short text randomly embedded within a longer one?

The following treated tweet has emerged on social media.

<figure>
  <img src="/assets/images/2026-08-20-MTM/Pelican.jpg">
  <figcaption>Figure 1: The sensitive subtext to [Trump’s original tweet](https://x.com/realdonaldtrump/status/757573051215147008).</figcaption>
</figure>

I am not completely sure why I found it so engaging: it suggests that a positive mindset and a little creativity can find a micro-instance of serene nature poetry, even within the narcissistic and self-pitying sea of political discourse that floods our lives. Alternatively, it suggests that Trump has a secretive sensitive side that is forced to hide poetic works in plain sight amid his usual grandiose bluster.

It kindles the question of how unusual the presence of this hidden message is: how many of the texts we engage with on a regular basis are hiding little moments of beauty? Surely any sufficiently long text should contain any sufficiently short other text, letter-by-letter, assuming both are drawn from overlapping letter distributions?

Well, it is relatively easy to brute-force such a question with a little pythonic string-handling. A search will confirm the presence of Gerald Manley Hopkins’ [The Windhover](https://www.poetryfoundation.org/poems/44402/the-windhover) in the [first seven pages of Trump’s state of the union](https://drive.google.com/file/d/1yBIFJigIeNqgTn-SgKbOzFDQRAtACV2u/view?usp=sharing) address, adding further credibility to the theory of Trump as a secret poet.

A little further exploration reveals some more pleasingly embedded works: consider Adrian Mitchell’s pleasingly smutty miniature [“Celia Celia”](https://www.poeticous.com/adrian-mitchell/celia-celia) scattered among the Song of Solomon found [here](https://drive.google.com/file/d/1MXAm3gQH8CutdiNvKKSS_gVir1s_QTe5/view?usp=sharing), Spike Milligan’s “There are holes in the sky” amidst the script to film Bee Movie found [here](https://drive.google.com/file/d/1DutPY2x-NZck-ZvJtV6sndSdC9ghN3ly/view?usp=sharing), or even Bernadette Meyer’s poem “Turning prose into poetry” found aptly within an instruction manual for a microwave oven available [here](https://drive.google.com/file/d/1MT-xWR8O71sBDG4N1Zont2YtM7PBbAS4/view?usp=sharing).

However, perhaps we can resolve some doubt through the use of statistics: several questions involved are relatively well-formed probability problems, susceptible to existing analytical methods.

First, a couple of assumptions. Let’s say we have a shorter text of length $N_s$ and a longer text of length $N_b$. Let’s assume that letters in each text are drawn independently from the same distribution of letters of the alphabet: further, that the $i$-th letter of the alphabet is drawn with known probability $p_i$. Such an assumption of independence is not completely watertight: some pairs of letters are more likely to be drawn together (TH, QU and so on in English), but the results of this assumption can be expected to be second order in magnitude.

Letter frequencies in English and other languages are quite well-studied, and to some degree have entered into broader culture: ETAOIN SHRDLU was a mechanical-typesetting-era printing artefact and historic meme, which can also be used as a challenging mnemonic for the order of the most commonly used letters in English.

<figure>
  <img src="/assets/images/2026-08-20-MTM/shitposting.png">
  <figcaption>Figure 2: Shitposting à la 1916.</figcaption>
</figure>

So the different probabilities are available to us for our analysis: the question remains what to do with them. We can answer this by imagining the process of finding the smaller text within the larger one: for each letter in the shorter text, we progress sequentially through the longer text until we find the corresponding letter, then restart the process with the next letter.

### **HERE BE MATHEMATICS**

##### (Feel free to skip ahead if that’s not your thing)

We can frame the whole question in terms of characterising the distribution of a variable called _f_: the point in the longer text at which the final letter of the shorter text is located. What can we say about the likely properties of the distribution of _f_?

The process of repeatedly testing for an event with a known probability _p_ until we find a success suggests a geometric distribution. The sum of many geometric distributions with different _p_ parameters does not result in a standard distribution (a negative multinomial is nearly there, but does not guarantee the ordering of the relevant letters from the shorter text).

Still, since the resulting distribution is a sum of familiar distribution, we can characterise its mean and variance through the sum of the means and variances of the component distributions.

Let’s do this via a tractable example.

Consider the word “QUITE”: its component letters have normalised frequencies of $[0.00978, 0.02758, 0.06966, 0.09056, 0.12702]$, respectively. For each of these, with a geometric distribution assumption, we would expect a number equal to the inverse of each probability before each of the letters are encountered, in this case $[102.25, 36.26, 14.36, 11.04, 7.87]$, giving a total of 171.78 letters expected before we find the word in a larger text. To put this as an equation:

$$
E(f) = \sum_i^{N_s} E(f_i) = \sum_i^{N_s} 1/p_i
$$
where $p_i$ is the probability associated with the letter in the $i$-th position in the shorter text, and $f_i$ is the length of longer text necessary to find said letter. We can calculate the variance on the estimate, again given the known properties of the geometric distribution:

$$
V(f) = \sum_i^{N_s} V(f_i) = \sum_i^{N_s} (1-p_i)/p_i^2
$$
in this case equal to 11,987,83, corresponding to a standard deviation of 109.49. Also, since the final variable we are targeting is known to be the sum of those associated with each individual letter, we can cite a central limit theorem and claim that the distribution will be Gaussian for a shorter text of any substantial length. This is probably not entirely secure in the case of the five-letter “quite”, but should hold for anything long enough to be interesting, and as such we will not consider any higher order moments of $f$.

The analysis becomes more elegant if we consider unspecified shorter texts: given an unknown shorter text of length $N_s$ drawn from a standard letter distribution, what is the probability of finding it in a longer text of length $N_b$? We would effectively be averaging over the easiest problem of finding a string of very common letters (e.g. “eeeee”) and very rare ones e.g. (“zzzzz”) and every string in between.

We have a similar approach of characterising the means and variances, but with each component of the sum itself being an average across possible letters, weighted by their probability of occurring, i.e:

$$
E(f) = \sum_i^{N_s} E(f_i) = \sum_i^{N_s} \sum_j^{N_a} p_j E(f_i) = \sum_i^{N_s} \sum_j^{N_a} p_j/p_j = N_s N_a
$$
where $p_j$ is now the probability of occurrence associated with the $j$-th letter in the alphabet, and assuming an alphabet of length $N_a$. So, pleasingly, the explicit distribution of letters disappear, and we can reasonably claim that for a shorter English text of non-trivial size $N_s$, we would expect to find it within a longer text after scanning through $26N_s$ letters.

The variance result is slightly less elegant

$$
V(f) = \sum_i^{N_s} V(f_i) = \sum_i^{N_s} \sum_j^{N_a} p_j V(f_i) = N_s (\sum_j^{N_a} 1/p_j - N_a)
$$
So the variance depends on the distribution $p_j$ across letters, but is at least constant for each language. The format suggest that a language with a relatively uniform distribution of letter frequencies would return small variances, and languages with a large discrepancy between largest and smallest frequencies would return a high standard deviation. In English, the value of the standard deviation happens to be 67.24 empirically.

Further, if we run with the Gaussianity property implied by the c.l.t., we have now characterised the entire distribution of $f$, and as such can generate a closed form result for the probability of finding an unspecified shorter text of length $N_s$ in a longer text of length $N_b$ with the standard normal cumulative distribution function Φ:
$$
\int_0^{N_b} p(f) df = \Phi\big((N_b - 26N_s)/67.24\big)
$$
Hooray!

### **MATHEMATICS OVER. THE VILLAGERS REJOICE.**

It’s curious to think how the result depends on the properties of the alphabet of the language in question. In terms of alphabet length, we would expect a Norwegian text drawn from an alphabet of length 29 ([don’t forget that æøå](https://www.youtube.com/watch?v=f488uJAQgmw)) to take more time to find in a longer text, whereas a language such as Hawaiian with a short alphabet of length 13 would make it easy to find a microtext within a macrotext. The most extreme example I could find was the alphabet of the Khmer language with 74 letters, so it would take nearly three times as much time to perform the search as in English. However, languages with shorter alphabets often need longer texts to make the same point, so it is possible that their values of $N_s$ will be larger to represent an interesting subtext.

The variance result is also interesting: a language with a heavily non-uniform distribution of letters incurs a large uncertainty when performing the search task, whereas a language in which each letter is used with the same frequency would result in the least possible uncertainty on how long the search task demands.

It is clearly possible to generate adversarial examples that violate the assumptions involved in generating these results: submitting George Perec’s [“A Void”](https://en.wikipedia.org/wiki/A_Void) as a longer novel within which to search would make it impossible to find any shorter text containing the letter “e”, for example. In these examples, the assumption that the texts are drawn from the same distribution of letters has been violated.

It is also interesting to consider languages which do not strictly have letters: Chinese characters are not generally considered an alphabet: there are said to be about 20,000 in general use, and they fall within a middle ground between letters, words, syllables and concepts. Performing the same analysis given a choice of characters rather than letters would presumably lead to a much more daunting search task.

This train of thought leads to the question of how a word-based version of this problem would work: can we find a shorter text word-for-word within a longer one? Previous derived results would still be valid, but with the number of letters in the alphabet being replaced with number of words in the dictionary: general estimates of which vary up to 228,132 total words in English, suggesting that this is a much less likely search problem to succeed. The presence of very rarely used words in language also vastly increases the variance associated with any estimate.

I was determined to find at least one example: starting with the short poem “Risk”, often attributed to Anaïs Nin:

> And the day came when the risk to remain tight in a bud was more painful than the risk it took to blossom.

I failed to find this sequentially word-for-word in some classic lengthy texts, so I took the adversarial approach and consulted some friends who are better-read than me to suggest some appropriate works, eventually prompting the discovery of the text within the apparently flower-heavy “Mayor of Casterbridge”. So a success, but a definitely non-random one.

There is also a curious word-level analogue to the original Trump tweet in the form of Tom Phillips’ “A Humument: A Treated Victorian Novel”. The artist involved found a forgotten Victorian novel named “A Human Document”, and proceeded to creatively paint over the vast majority of the text, leaving a second discovered text left exposed: in the words of one of the pages, “I have to hide to reveal”:

<figure>
  <img src="/assets/images/2026-08-20-MTM/a_humument.png">
  <figcaption>Figure 3: The cover page of Tom Phillips' "A Humument: A Treated Victorian Novel"</figcaption>
</figure>

Strictly speaking, the analysis presented here only applies to the Trump poem and texts like “A Humument” in artistic retrospect. The probabilities presented are those of finding a given shorter text within a second longer one: with these two creative works, they have started with long texts and then actively searched for a smaller text of artistic interest or value within it. This demands both more aesthetic judgement and probably a shorter long text: A relevant, difficult question would be “What proportion of randomly generated strings of letters are artistically interesting sentences?” You can probably make some traction on this question with some of [the reasoning used to derive the entropy of the English language](http://languagelog.ldc.upenn.edu/myl/Shannon1950.pdf), but that would only tell you something about the probability of finding valid sentences. I cannot quite imagine a formal metric by which to judge their aesthetic value: there is, of course, no (ac-)counting for taste.

Note: This article appeared originally on an now-defunct blog of mine in 2018, and is reproduced here with minor edits.
