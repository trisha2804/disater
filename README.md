#event extraction for disater response
import nltk
import re
import spacy
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer
from nltk.tokenize import word_tokenize

# Loading the spaCy model for English language
nlp = spacy.load('en_core_web_sm')

with open('news_article.txt', 'r') as f:
    text = f.read().lower()

# Removing non-alphabetic characters and digits
text = re.sub('[^a-zA-Z]', ' ', text)

# Tokenizing the news article into words
words = word_tokenize(text)

# Lemmatizing the words using spaCy
lemmatized_words = []
for word in words:
    lemma = nlp(word)[0].lemma_
    lemmatized_words.append(lemma)

# Stemming the lemmatized words to their base form
stemmer = PorterStemmer()
stemmed_words = [stemmer.stem(word) for word in lemmatized_words]

# Removing stop words from the news article
stop_words = set(stopwords.words('english'))
filtered_words = [word for word in stemmed_words if word not in stop_words]

# Map stemmed words to disaster-related categories
category_dict = {
    'earthquak': 'earthquake',
    'tsunam': 'tsunami',
    'flood': 'flood',
    'fire': 'fire',
    'hurrican': 'hurricane',
    'cyclon': 'cyclone',
    'typhoon': 'typhoon',
    'tornado': 'tornado',
    'avalanch': 'avalanche',
    'landslid': 'landslide',
    'drought': 'drought',
    'pandem': 'pandemic',
    'outbreak': 'outbreak'
}

# Add synonyms and related words to each disaster-related category
synonym_dict = {
    'earthquake': ['tremor', 'quake', 'seismic activity'],
    'tsunami': ['tidal wave', 'seismic sea wave'],
    'flood': ['deluge', 'inundation', 'flash flood'],
    'fire': ['blaze', 'inferno', 'wildfire'],
    'hurricane': ['cyclone', 'typhoon', 'tropical storm'],
    'avalanche': ['snowslide', 'snowstorm', 'blizzard'],
    'landslide': ['rockslide', 'mudslide', 'rockfall'],
    'drought': ['aridness', 'dry spell', 'water shortage'],
    'pandemic': ['epidemic', 'outbreak', 'plague', 'virus'],
    'outbreak': ['epidemic', 'pandemic', 'infection', 'disease']
}

# Categorize filtered words based on their base form and synonyms
category_words = {}
for word in filtered_words:
    root_word = stemmer.stem(word)
    added_to_category = False
    for category, syn_list in synonym_dict.items():
        if root_word in syn_list or word in syn_list:
            if category in category_words:
                category_words[category].add(word)
            else:
                category_words[category] = set([word])
            added_to_category = True
            #print(f"{word}: {category}")
            break
    if not added_to_category:
        if root_word in category_dict:
            category = category_dict[root_word]
            if category in category_words:
                category_words[category].add(word)
            else:
                category_words[category] = set([word])
            added_to_category = True
            #print(f"{word}: {category}")
            break
    
# Printing the category-wise words
for category, words in category_words.items():
    print(f"Category: {category}")
    #print("Words:", ", ".join(words))
    print()
