---
layout: default
title: Capgemini internship
parent: Experience
permalink: /Experience/capgemini_internship
---

# My experience as a Airbus Data Engineer

## Introduction

Yeah you've read correctly, Airbus not Capgemini, well it's a bit more complicated than that, here's how it works:

CapgeminI is a digital service company, founded in 1967 by Serge Kampf (it was known as Sogeti), to be more precise it's actually one of the biggest DSC in the world, with no less than 50 branches all around the globe : Australia, China, Denmark, France, I ndia, Norway etc...

But you may ask "ok that's great but what is the purpose of a DSC ?", as you know the digital world is in a constant growing state, but how a company may not lack human resource in this ? That's where DSC intervene, the purpose of those DSC is to have a huge pool of people with various skills in these domains (we're talking 250 000 employees). 

Let's say we have a Company X which needs to have a task Y done quickly:
 
 1. Company X issues a call for bids
 2. Every DSC which is interested can make an offer
 3. If the offer is accepted then the DSC will send a team to manage the project emitted by Company X

So you know why I said I was working with Airbus, Airbus made a call for bids and Capgemini won it.

{: .important-title}
> Disclaimer
>
> Since I'll work with Airbus you should know that there will be some detail I can't give you.
>
> Like name, content, or even pictures of my code it could be a leak dangerous for the company.



## The problematic

I joined a team of 10 people called HDM (Health Data Monitoring) which has for purpose to manage the data about aircraft to keep it simple.

The mission I was put on was to make a "text comparator on the different error which could happen during aircraft test flight, or assembly line", well to be more precise the real goal was to let the department which I was working with, have a sort of application that could answer or at least offer an answer for a given error by comparing it with another error who occurred in the past, so that way Airbus doesn't have to have one person manage each error, of course, since there could be some error in the answer someone should look over it.
Spoiler alert : I haven't reached the applicative stage.

I'll develop soon how I proceeded with the project, here's how the application should work:

 1. An engineer finds a NC (Non-Conformities just think of it as a problem) on an aircraft 
 2. S/he fills a form on their side asking for help on this NC
 3. If the NC concern the team I was on then the NC is sent to the application
 4. The application compares the similitude between the new NC and the past closed NC (closed mean it was already taken care of)
 5. It keeps the one with the most similarity and check what was the response given by the service 
 6. It then offer the response for the new NC
 (This part bellow would be before the application is approved by Airbus)
 7. One of the people of the service check if the answer is correct for the new NC 
 8. If it's the case bingo, if not the application should offer the second most similarity etc...

Said like that it seems pretty simple, but trust me, that's far from simple. After I more or less deducted those information i've tried to make a roadmap of how I should bring it down.
And that's what i'll be writing about here.

## Get familiar with the ecosystem

The first day was quite fast, in the morning I had a meetup with my manager and in the early afternoon I joined my team at Airbus, since the beginning I felt at home peoples were warm and kind, but we're not here to talk about that.
My internship supervisor and another colleague helped me set up my company computer, once that was done I would have to get familiar with the tools i've got to work with and here's the first problem.

I  had to use an Airbus platform called Skywise which is an aircraft option developed by Palantir Technologies, let's say it, using Skywise is neither fun or easy since it's an Airbus only tool, I had few tutorials and even less documentation. But nonetheless I managed to find my way around it, especially with Code Repository which allowed me basically to do python with Apache PySpark so it was okay.

Saying like that it may look like i've done my preparation for skywise in like 1 or 3 days but it took me honestly a while just to even find documentation, I consider I was "ready" about 2 to 3 weeks after my arrival...

But anyway, while I tried to learn how Skywise worked I wanted to advance the project. So I opened spyder and tried to do a Lookalike script.

{: .information}
> You'll find the source at the end of this article, if you're in despair with Skywise too

## Lookalike script

There were two rabbit holes for this project the first one as you may have guessed is this lookalike script.

Here's why : "How do you see if two texts are similar mathematically ?". Good for us someone helped with that, and I thank you very much Mr Vladimir Levenshtein who gave his name to his metric : _The Levenshtein distance_

So how it works ? Let's take two words : Sheep and Shears
I 'm allowed to do as many operation I want between three possibilities :
 - Replace
 - Delete
 - Insert

With those three possibilities I should be able to go from "Sheep" to "Shears" let's test it:
 1. **insert** "s" at the end of sheep -> sheeps
 2. **replace** "ep" by "ar" -> shears

For each action I take, I have a weight which I should input in a matrix and do further processing... I won't explain all of Levenshtein distance but feel free to check the [source](#source) for further explanation.

I could manage text similarity by using three libraries : fuzzywuzzy, re, nltk.
Fuzzywuzzy allow you to make Levenshtein distance ratio on two texts, re is here to apply some good ol' regex on our string, and finally nltk let us lemmatize and stemmate.

I'm pretty sure you're wondering what is lemmatize and stemmate, it's two ways to get like the root of each word, while stemming cut off the suffix, lemmatization will have morphological information to keep the true root meant by the word 
Let's say you have the word "programmer":
 - Stemming :
 > While you know programmer is actually a person doing some developments here this would not matter for stemming so programmer -> program
 - Lemmatization : 
 > Here same situation but it matters so programmer -> programmer

It could seem trivial, but this small difference can completely change the meaning of your texts.

For testing my script I asked ChatGPT to generate 10 to 20 aircraft themed texts, two of them would have to get the same meaning but I should find only which ones with my script.

Here's how I proceeded :
 - I remove all punctuation (using re) and lower the text
 - Then we use nltk to remove stopwords and keep the only word with which we consider they have meanings

{: .information}
> In natural language processing a stopword is a word which happen so much in a text that it's useless to index it. (E.g you, me, i, them, their, etc...)

 - We loop over each word and, still with nltk, lemmatize them 
 - Finally with fuzzywuzzy we calculate the ratio of similarity between the two text

When I tried with lemmatize only, I had approximately 85 % of success, it was good but what would happen if I did a mean of Levenshtein distance with and without lemmatization

So I tried and the result was spectacular I managed to gain like 10 % on my intial 85 %.

I was satisfied with those 95%, so once I finished this script I got back to skywise and learning how it works.

## The eternal data pre-process

So here I am, three weeks in, I know how the end of the process should happen with my script, I learned my way around Skywise ( not perfect but it was good enough ), so before I could reach the end I've got to begin.

And if you're working in data subject I think you know how almost every project start : get to know with what data you're working with. And oh boy, the data was nasty.

NC could be seen like chat conversation but the difference was that chat conversation are almost straightforward, here I would have got new user coming into the NC text for no reason saying absolutely nothing, there were trailing punctuation everywhere some were useful other not, I could decipher three "type" of NC and each would not have the same treatment... 

I passed almost one month if not even more on the sole process of cleaning NC. But I'm pretty satisfied with the result, let's say for each NC that was inputted there were like a dozen of user and almost 50 to 100 lines, at the end of my process we were down by 6 to 8 users by NC and 20 to 30 line.

It can be further improved, but I couldn't lose more time back then.

For the cleansing it was nothing fancy I used a lot of regex, and can say proudly that I'm fluent with them now.

{: .information-title}
>post scriptum
>
> If you work or worked with regex you know that when I say " I'm fluent with regex" it just mean " I had to do a lot of them for one month and in two months I will forget everything about regex"

This part was pretty straight forward, so let's go to the "final" part.

## Language identification

Here's where I am as I'm writing those lines, I forgot to mention earlier for the lookalike script that I need one thing to remove stopwords in the text, to know in which language the text is.

But as you may know Airbus is also an international company so NC aren't written only in English or French but German too.

{: .information}
> They may be other language present in the NCs but I didn't find other than those three

But if each NC would be written only in one language it would be too easy, they're mainly English but you can find some in French and a few in German.

Anyway, then how do I detect the language in which the text is written ? 

First I decided to parse each text by line then I detect the language of each line.

For this detection I'll use Ngram and naive Bayes classification. Ngram is the concept of "slicing" a text by the portion of length n like this :
 - "Hello word" with a ngram of 2 would give -> ["He", "el", "ll", "lo", "o " , " w", "wo", "or", "rl", "ld"]

Why we do that you could ask, some people far more experienced than me in NLP, remarked that each language has some ngram which appears more often than in other language.
So a bit like text comparison we'll count the occurrence of each ngram and see if the probability of each ngram match more with a text in English, French or German.

To do this you need to do a model language, nltk will come to our help again, it allows you to download the Universal Declaration of Human Rights(UDHR) in 300 languages. Not bad to do a model language.
So the first step will be to get the content of this UDHR in our languages, then split them by ngram, count them, and see the probability that each ngram appear in a text in each language.
When it's done, we compare each probability of each ngram of our NC with those of our language model and see with which language we got the more matches.

Once we find a match, I'll append the language at the end of the string.

{: .warning-title}
>Attention
>
>Since I can have keywords in each sentence like the name of a part of an aircraft (let's say EVAC36o1) I've got to remove it, otherwise it can falsely match another language.

This part is honestly fun and great, if you have the occasion try to do a language identifier yourself.

## What's next ?

So here I am at the end of my internships, but with great experience, I had the luck to learn what was NLP how we work in this domain, also what was Skywise and Apache Spark, and a lot of other things !
It was a great experience, and I can't help to feel a bit sad about not be able to continue this project I would love to see what could it have been. If I could here's what I would do :

 - I would've improved the regex cleansing of NCs to have them much compact with lesser line and user.
 - See if the Levenshtein distance was sufficient enough or could be improved
 - Made an application for the team with Slate on Skywise
 - See if it could be adapted for other team inside Airbus
 - Why not propose something like ChatGPT but NC-wise

A lot could have been done, and could still be done. I  hope this project won't just go in the drain. And I thanks a lot CapgeminI and Airbus for the opportunity and the hospitality they offered me.

## Source

#### Skywise

[Palantir Learning](https://learn.palantir.com/)

[How to use Code Repositories](https://www.youtube.com/playlist?list=PLmKm_LhXXgqQanGZlzLud7GZsgpsAci-s)

[StackOverflow for Skywise](https://stackoverflow.com/questions/tagged/palantir-foundry)

[Official Documentation](https://www.palantir.com/docs/)

#### Levenshtein Distance

[Video on how it works](https://www.youtube.com/watch?v=MiqoA-yF-0M)

[Text Similarity with Levenstein Distance in Python](https://towardsdatascience.com/text-similarity-w-levenshtein-distance-in-python-2f7478986e75#:~:text=Levenshtein%20distance%20is%20a%20lexical,make%20it%20like%20string%202.)

[Step by Step guide](https://blog.paperspace.com/implementing-levenshtein-distance-word-autocomplete-autocorrect/)

#### Regex

[Python regex cheat sheet](https://www.dataquest.io/wp-content/uploads/2019/03/python-regular-expressions-cheat-sheet.pdf)

[Test your regex](https://regexr.com/)

[re official documentation](https://docs.python.org/3/library/re.html)

[how to use match object](https://note.nkmk.me/en/python-re-match-object-span-group/)

#### Nltk

[How to read UDHR](https://www.nltk.org/_modules/nltk/corpus/reader/udhr.html)

[Stemming and lemmatization with NLTK](https://www.datacamp.com/tutorial/stemming-lemmatization-python)

#### Naive Bayes Classifier

[Naive Bayes Classifier vulgarized](https://www.youtube.com/watch?v=O2L2Uv9pdDA)

[Language detection model using Naive Bayes](https://towardsdatascience.com/an-efficient-language-detection-model-using-naive-bayes-85d02b51cfbd)

[How to do language detection with Python, NLTK, and some easy statistics](https://towardsdatascience.com/how-to-do-language-detection-using-python-nltk-and-some-easy-statistics-6cec9a02148)

