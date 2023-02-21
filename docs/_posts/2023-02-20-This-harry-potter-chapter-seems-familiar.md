The other day, I re-read chapter 6 of Harry Potter and the Deathly
Hallows. Afterwards, I had the feeling that this chapter seemed similar
to some other chapter I had read in one of the previous books.

I thought I’d try my hand at a data-driven approach to see how similar
each of the chapters of Deathly Hallows is to other chapters in the
series.

I used a pretty basic Jaccard similarity score based on the word content
in each chapter. One way to search for similar chapters is to go
book-by-book. So, take a chapter in Deathly Hallows and get the
similarity score between it and each chapter in Sorcerer’s Stone (or
Philosopher’s Stone depending on which side of the Atlantic you’re on).
Then, find the maximum score among all those chapters in Sorcerer’s
Stone. This is the chapter in Sorcerer’s Stone that is *most similar* to
the chapter from Deathly Hallows.

Below, I plot these book-by-book *most similar* chapter similarity
scores for every chapter in Deathly Hallows. The way to read the plot is
like this:

1.  Take a Deathly Hallows chapter on the vertical axis (say chapter 6).
2.  Follow that chapter across from left to right. The first panel shows
    the max similarity score for that chapter with chapters in
    Philosopher’s Stone.
3.  The number in parentheses is the chapter in Philosopher’s Stone that
    is most similar to Deathly Hallows chapter 6 among all the chapters
    in Philosopher’s Stone (in this case it’s Philosopher’s Stone
    chapter 13).
4.  The length of the bar is the similarity score - the longer the bar,
    the more similar the chapters.

<img src="/assets/unnamed-chunk-3-1.png" width="110%" />

Some questions came out of this for me. First, I want to go read the
chapters in Deathly Hallows that are outliers - what happens in Deathly
Hallows chapter 31 that is so similar to Chamber of Secrets chapter 11?

Second, some chapters in each of the books seem to be the most similar
for a lot of chapters in Deathly Hallows. For example, chapters 20, 21,
and 22 in Prisoner of Azkaban seem to be the most similar to a handful
of Deathly Hallows chapters - why?

Finally, I wonder if there is a pattern in the books. Are the earlier
chapters in one book more similar to the earlier chapters in other
books? All the time at the Dursley’s seems to be concentrated at the
start of books, surely that would make the earlier chapters more similar
to each other?
