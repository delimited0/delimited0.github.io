---
layout: post
title:  "Topic Modeling the Daily Princetonian"
categories: prince, topic-models
---
One of my favorite ways to waste time is reading internet comments. You can observe all kinds of angry people there and wonder at their pathologies. While I was still in school I particularly enjoyed reading the comments section of the Daily Princetonian because of its relevance to me. Though I only worked for a brief time formatting the print edition of the paper, I got to know some of the journalists, opinion writers, and editors through that work and through classes. Writing for a college newspaper requires a thick skin; the commenters are almost always out to get you. 

At some point I decided I would try to learn something about internet news and comments, perhaps so I could convince myself all those hours were not wasted. A basic question we can ask about news and comments is what are the paper and readers talking about? This kind of question, of what topics underly a corpus, is what probabilistic topic modeling can help answer. A frequently used method for topic modeling is latent Dirichlet allocation {% cite Blei:2003 %}. There have been many extensions to this original model, including some that focus on internet posts and their comments. In this post I describe a model introduced in {% cite Yano:2009 %} and apply it to a corpus of Daily Princetonian articles and their comments.

# Data

I focus in particular on the opinion pieces and letters of the Daily Princetonian from fall 2013 to spring 2015--opinion pieces because in my experience these columns tend to attract the most, and the most rabid, comments, and that timespan because the Prince migrated its content to Wordpress in spring 2013 and all comments from before then have been lost. Using `beautifulsoup` and `Selenium` I scraped 962 articles from the opinion section with a total of 3576 comments. Using the `tm` package in `R` I removed capitalization, whitespace, numbers, and punctuation, stemmed the words using the Porter stemmer {% cite Porter:1980 %}, and converted the documents to the [bag of words][bag-of-words] representation required by Latent Dirichlet allocation.

# Model

{% cite Yano:2009 %} describe this model as LinkLDA. I do not use the other model they propose, CommentLDA, because it depends on commenter identity. The Disqus commenting system that the Prince uses allows guest commenters who can use anything as their usernames; thus it's not possible to identify guests, who posted about 40% of the comments I consider here. 

The hierarchical representation of the model is:

$$
\begin{align*}
	\theta_d &\vert \alpha \sim Dir(\alpha) \\
	z_{dn} &\vert \theta_d \sim Mult(\theta_d) \\
	\beta_k &\vert \eta \sim Dir(\eta) \\
	w_{dn} &\vert z_{dn}, \beta \sim Mult(\beta_{z_{dn}}) \\\
	z'_{dcn} &\vert \theta_d \sim Mult(\theta_d) \\
	\beta'_k &\vert \eta' \sim Dir(\eta') \\
	w'_{dcn} &\vert z'_{dcn}, \beta' \sim Mult(\beta'_{z'_{dcn}})
\end{align*}
$$

And in plate notation:

![Model in plate notation]({{ site.baseurl }}/assets/2016-07-04-Daily-Prince-Actm/actm-gm.jpg)

I implemented the model with `Rcpp`.

# Article and Comment Topics



Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

{% bibliography %}

[bag-of-words]: https://en.wikipedia.org/wiki/Bag-of-words_model
[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
