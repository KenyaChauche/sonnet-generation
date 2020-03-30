# Much Ado About Training
### Natural Language Generation with Shakespearean Sonnets

## The Problem
Natural Language Generation is an emerging and largely mysterious field. The boundaries of how NLG can perform with highly structured language have only been poked, not pushed. This project aims to test the capabilities of NLG to successfully generate compositions that fit the format of Shakespearean sonnets without additional controls.

## Background
### Sonnet Structure
Shakespearean sonnets have a very particular structure; everything has a rule by which to abide, down to the syllable. Each sonnet is composed of three quatrains and finished by one couplet at the end. A quatrain is a stanza of four lines, often focusing on one particular idea and having an alternating rhyme scheme. A couplet is a stanza with only two lines. These add up to a sonnet with 14 total lines.

Shakespearean sonnets have the rhyme structure of
> ABAB CDCD EFEF GG

This means that each quatrain will have an alternating rhyme scheme within itself, and that the rhyme structure does not extend between sonnet elements. Though there may be lines that rhyme with each other in different quatrains, this is inadvertent.

Going further into the structure, each sonnet is written in iambic pentameter, which dictates how the words of each line are to be composed. An iamb is a specific metric, where one unstressed syllable is followed by a stressed syllable, very similar to the *ba-BOOM* of a heartbeat. Each line is made up of five iambs, making each line have exactly 10 syllables. Sometimes Shakespeare gets clever and slurs syllables together or skips over them to make it fit this format (ie, using *'gainst* instead of *against*).

## Data Formatting and Modeling
### Data Source, Formatting, and Cleaning
Sonnets were scraped from MIT's online directory of the [complete works of William Shakespeare] (http://shakespeare.mit.edu/).  

At first, data was broken up so that each line of a sonnet would be its own row in a dataframe. The data was put through a few versions of cleaning and formatting. In cleaning there were attempts to keep the apostrophes, as they were used in words where syllables were compromised, though in the final model all punctuation was ultimately removed. In some rounds of formatting syllable counts were incorporated using NLTK's cmudict to identify distinct syllables within words. This was done with the intent of incorporating syllable counts into the modeling process, but this was ultimately done away with.

In all versions, newlines were preserved in the data. This was done in an effort to allow the model to learn where to insert a newline on its own. In versions where the punctuation was removed, newlines were replaced with the word "newline", which was not part of the original vocabulary. In processing of the model output, "newline" was replaced with a newline.

The words in each line were later divided, using a moving window technique. Data was reformatted to include a single word, its preceding word, and the word coming after it. This was done for two, three, and four word windows. Although more words would allow for more context, this would go beyond the typical length of a sonnet line.

### Modeling
Initially, the input for the models were transformed into binary arrays with one-hot encoding. This was then used to train a neural network with LSTM and Dense layers. These models were trained with two and three word starter phrases and their following words. In all cases, these models did not produce meaningful results. In one case, the only word a model would predict was "wardrobe".

This led to a revamping of how modeling had been approached. Rather than preprocessing the data with a one-hot encoder, the data was preprocessed using Keras's Sequential tool, and an embedding layer was added to the model. This model was then trained on four-word sequences and their following word. The results of this were much more cogent and meaningful.  

## Observations
There were a few hurdles in this project, most of which focused on the data formatting and what the model would be trained on. These challenges were deciding how many words should be included in the starter text, what format the data should be in to train the model on, deciding to include an embedding layer, and other such difficulties. From the modeling (and remodeling) process it is gleamed that it is much better to preprocess the input text by sequencing rather than one-hot encoding, since one-hot encoding is best meant for categorical data. While a case can be made that natural language generation is categorical since it is about determining what word in the present vocabulary *should* come next, it is also about the relationship between words and the context they are placed in. Sequencing words and using an embedding layer allow for word relationships and context to be taken into account.

This resulted in a neural network that produced this, among other, generated poetry samples:
(this generated sample has not be reformatted other than to replace "newline" with an actual newline)
>rose whose beauty yea i
i least wilt
and
hear that trees makest seek
i do of your desire

The results from this model are cogent and have some meaning to a human. This generated text could be expressing a desire for someone so strong it is crushing, causing the person to wilt. It could be touching on how in the search for someone's affections hope springs eternal.

As thought-inpsiring as the output from this model may be, it does not abide by the strict structure of Shakespearean sonnets. There is no rhyming, and the syllable count for each line is varied. This means that unregulated output from a NLG model will not naturally abide by the strict structure of a Shakespearean sonnet.

## Next Steps
From here there are a few different avenues to pursue. In the interest of generating consistent sonnets that fit the Shakespearean structure, restrictions can be placed on what generated word will be part of the final product. With these restrictions controls can be put in place for both rhyme and metrics. 
