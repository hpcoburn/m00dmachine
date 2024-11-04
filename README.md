# -*- coding: utf-8 -*-
"""TheMoodMachine.ipynb

# *THE MOOD MACHINE*

## SET-UP
"""

!pip install -U pip setuptools wheel
!pip install -U spacy
!python -m spacy download en_core_web_sm

!pip install -qq "spacy >= 3.0.6"
!pip install -qq wandb --upgrade

import os
import re

import spacy
from spacy.matcher import Matcher
from spacy.morphology import Morphology
from spacy.strings import StringStore

from google.colab import drive
drive.mount('/content/drive')

modal_advbs = ['maybe', 'perhaps', 'possibly', 'probably',
               'likely', 'instead', 'apparently', 'conceivably',
               'supposedly', 'reportedly', 'reputedly',
               'purportedly', 'putatively', 'arguably'
               'hopefully', 'surely', 'evidently']

knowing_advbs = ['really', 'certainly', 'definitely',
                 'undoubtedly', 'indisputably', 'inarguably']

conditionals = ['if', 'whether', 'unless']

modal_verbs = ['might', 'would', 'could', 'should', 'ought', 'may']

potential_verbs = ['will','can','shall', 'must', 'have', 'need', 'try',
                   'attempt', 'endeavor','endeavour', 'mean', 'intend', 'require'] #in order to

sub_verbs = ['wish', 'want', 'hope', 'dream',
             'desire', 'imagine', 'think',
             'suppose', 'believe','doubt',
              'report', 'claim',  'trust',
             'declare','represent', 'seem',
              'submit', 'maintain', 'advise',
             'ask', 'command', 'demand', 'insist',
             'order', 'receive', 'profess',
              'pretend', 'guess', 'request',
             'recommend', 'suggest', 'propose',
             'prefer', 'decide', 'judge',
              'expect','hypothesise',
             'contend', 'postulate',
             'hypothesize', 'critique', 'presume',
              'dread', 'fear', 'prevaricate',
             'theorise', 'theorize', 'suspect',
             'sense', 'require', 'beg',
              'simulate', 'dissimulate', 'assure',
             'apprehend', 'delude', 'deceive',
             'trick', 'deny', 'confess', 'admit',
              'prevaricate', 'assure', 'promise',
             'guarantee', 'allege', 'purport']

wishing_verbs = ['wish', 'want', 'hope', 'dream',
             'desire', 'crave', 'long', 'beg', 'pray']

knowing_verbs = ['know', 'say', 'tell', 'prove', 'demonstrate',
                 'show', 'present', 'understand', 'comprehend']

knowing_adjs = ['certain', 'sure', 'convinced', 'aware']

sub_nouns = ['wish', 'want', 'hope','dream', 'desire', 'belief',
             'knowledge', 'imagination', 'comprehension',
              'lie', 'simulation', 'tale', 'story', 'claim',
             'report', 'declaration', 'proof', 'pretence', 'view',
              'advice', 'question', 'command', 'demand', 'prospect',
             'order', 'insistence', 'preference', 'thought',
              'proposal', 'request', 'recommendation', 'suggestion',
             'knowledge', 'supposition', 'requirement',
              'dread', 'fear', 'hypothesis', 'contention', 'theory',
             'postulation', 'guess',
              'suspicion', 'comprehension', 'contention',
             'inkling', 'sense', 'feeling', 'aspiration',
              'delusion', 'apprehension', 'deception', 'trick',
             'denial', 'confession', 'critique', 'criticism',
              'remark', 'postulatum', 'notion', 'idea', 'admission',
             'stipulation', 'proviso', 'provision', 'representation',
              'assurance', 'promise', 'guarantee', 'facade', 'doubt',
             'allegation', 'awareness', 'wariness', 'probability']

sub_adjs = ['likely', 'probable', 'possible', 'conceivable',
            'impossible', 'improbable', 'unlikely', 'uncertain',
            'desirable', 'advisable', 'mandatory', 'required',
            'unthinkable', 'untenable', 'tenable',
            'controversial', 'questionable', 'doubtful',
            'sure', 'believable', 'unbelievable',
            'insistent', 'indispensable']
 #do corpus-wide search: what words most often precede 'that'? / correspondence if/whether

articles_gerunds_pro_nouns_adjs_tags = ['NN', 'NNP', 'NNPS', 'NNS', 'PRP', 'PRP$'\
                                       'JJS', 'JJR', 'WP', 'WP$', 'DT', 'VBG']

verbs_tags = ['VB', 'VBD', 'VBN', 'VBZ']

#CharacterSorting
susan_sentence = []
bernard_sentence = []
rhoda_sentence = []
neville_sentence = []
jinny_sentence = []
louis_sentence = []
nature_sentence = []

susan = 'Susan'
bernard = 'Bernard'
rhoda = 'Rhoda'
neville = 'Neville'
jinny = 'Jinny'
louis = 'Louis'
the_sun = 'The sun'
now_the_sun = 'Now the sun'

said_susan = 'said Susan'
said_bernard = 'said Bernard'
said_rhoda = 'said Rhoda'
said_neville = 'said Neville'
said_jinny = 'said Jinny'
said_louis = 'said Louis'

last_character = 'sun'

for sentence in doc.sents:
  if said_susan in sentence.text:
    susan_sentence.append(sentence)
    last_character = 'Susan'
  if said_bernard in sentence.text:
    bernard_sentence.append(sentence)
    last_character = 'Bernard'
  if said_rhoda in sentence.text:
    rhoda_sentence.append(sentence)
    last_character = 'Rhoda'
  if said_neville in sentence.text:
    neville_sentence.append(sentence)
    last_character = 'Neville'
  if said_jinny in sentence.text:
    jinny_sentence.append(sentence)
    last_character = 'Jinny'
  if said_louis in sentence.text:
    louis_sentence.append(sentence)
    last_character = 'Louis'
  if the_sun in sentence.text:
    nature_sentence.append(sentence)
    last_character = 'sun'
  if now_the_sun in sentence.text:
    nature_sentence.append(sentence)
    last_character = 'sun'
  if last_character in susan:
    if not sentence in susan_sentence:
      susan_sentence.append(sentence)
  if last_character in bernard:
    if not sentence in bernard_sentence:
      bernard_sentence.append(sentence)
  if last_character in rhoda:
    if not sentence in rhoda_sentence:
      rhoda_sentence.append(sentence)
  if last_character in neville:
    if not sentence in neville_sentence:
      neville_sentence.append(sentence)
  if last_character in jinny:
    if not sentence in jinny_sentence:
      jinny_sentence.append(sentence)
  if last_character in louis:
    if not sentence in louis_sentence:
      louis_sentence.append(sentence)
  if last_character in the_sun:
    if not sentence in nature_sentence:
      nature_sentence.append(sentence)

#nature_sentence

"""## STEP 0 - SET STRIP"""

subj_list = []
optative_list = []
interrogative_list = []
conditional_list = []
modal_verb_list = []
modal_adverb_list = []
modal_clause_list = []
imperative_list = []
question_mark = ['?']

book_path = '/content/drive/MyDrive/Source Texts/Woolf/The Waves.txt'
contents = ""
nlp = spacy.load("en_core_web_sm")
nlp.max_length = 3000000
if os.path.exists(book_path):
  with open(book_path, 'r') as file_in:
    contents = file_in.read()
    contents = " ".join(contents.split())

def strip_waves_boilerplate(self):
    return(contents[106772:530161])

discontents = strip_waves_boilerplate(contents)
#discontents[:5000]
doc = nlp(discontents)

doc = nlp(discontents)

"""## STEP 0.5: WORD/SENTENCE COUNT"""

counter = 0
for sentence in doc.sents:
  counter+=1
print(counter)

counter = 0
for word in doc:
  counter+=1
print(counter)

"""## STEP 1: OPTATIVES"""

matcher = Matcher(nlp.vocab)

  pattern1 = [{"LOWER": {"REGEX":"(oh |o )"}, "IS_SENT_START": True}, {"IS_PUNCT": True, "OP":"*"} , {"LEMMA":{"REGEX":"(that|for|to)"}}]
  matcher.add('Optative: o(hhhh,) that...', [pattern1])
  pattern2 = [{"LOWER": "if"}, {"LOWER":"only"}] #...if only for a moment
  matcher.add('Optative: If only...', [pattern2])
  pattern3 = [{"LOWER": "would"}, {"LOWER":"that"}]
  matcher.add('Optative: Would that...', [pattern3])
  pattern4 = [{"LOWER": "may"}, {"POS": {"REGEX": "(DET|NN|PRP)"}}]
  matcher.add('Optative: May...', [pattern4])
  pattern5 = [{"LOWER": {"REGEX":"(god|lord|jesus|christ|allah|jehova|jove|fate|destiny)"}}, {"TEXT": {"REGEX": "(bless|damn|save|love|curse|help|protect|preserve|preside|forbid|forgive|be|willing)"}}]
  matcher.add('Optative: GOD', [pattern5])
#  pattern7 = [{"LEMMA": "it"}, {"TEXT":{"REGEX": "(is|'s)"}}, {"LEMMA":"high", "OP": "*"}, {"LEMMA":"time"}]
#  matcher.add('Optative: Its TIIIIME', [pattern7])
  pattern8 = [{"LEMMA": "long", "IS_SENT_START": True}, {"LEMMA": {"REGEX":"(may|live)"}}]
  matcher.add('Optative: Long live...', [pattern8])
  pattern9 = [{"LOWER": "thank"}, {"LOWER":{"REGEX":"(god|goodness|heaven|heavens|christ|the stars|the lord)"}}]
  matcher.add('Optative: Thank GOD', [pattern9])
#hope wish desire want?
# for modal vbs?

  matches = matcher(doc)
  for match_id, start, end in matches:
      span_list = []
      string_id = nlp.vocab.strings[match_id]  # Get tag title from hash
      span = doc[start:end]  # The matched span/result as span object
      span_list.append(span.sent)
      span_list.append(string_id)
      if not span.sent in optative_list:
          optative_list.append(span.sent) #adding the span's sentence to a list

      for thing in span_list:
          print(thing)

# (A)
len(optative_list)

"""## STEP 2: INTERROGATIVES"""

for sentence in doc.sents:
  for token in sentence:
    if token.text in question_mark:
      if not sentence in conditional_list:
        interrogative_list.append(sentence)

#(B)
for sentence in interrogative_list:
  if not sentence in optative_list:
    subj_list.append(sentence)
len(subj_list)

"""## STEP 3: CONDITIONALS"""

for sentence in doc.sents:
    for token in sentence:
     if token.lemma_ in conditionals:
       if not sentence in conditional_list:
        conditional_list.append(sentence)

#faulty - had he/he had
  matcher = Matcher(nlp.vocab)

  cond_pattern1 = [{"TEXT":{"REGEX":'(were|had)'}, "IS_SENT_START": True}, {"POS": {"REGEX": "(DET|NN|PRP)"}}, {"POS": {"REGEX": "(VB|VBD|VBN|VBZ"}}]
  matcher.add('Subj Cond: Were/had we..', [cond_pattern1])

  matches = matcher(doc)
  for match_id, start, end in matches:
      string_id = nlp.vocab.strings[match_id]
      span = doc[start:end]
      if not span.sent in conditional_list:
          conditional_list.append(span.sent)

#(C)
len(conditional_list)

# (D)
counter = 0
for sentence in conditional_list:
  if not sentence in subj_list:
    subj_list.append(sentence)
    counter+=1
print(counter)

"""### STEP 3.5: CONDITIONAL/INTERROGATIVES"""

conditional_interrogative_list = []
for sentence in conditional_list:
  if sentence in interrogative_list:
    if not sentence in conditional_interrogative_list:
      conditional_interrogative_list.append(sentence)
len(conditional_interrogative_list)

for sentence in conditional_interrogative_list:
  print(sentence)

"""## STEP 4: MODAL VERBS"""

modalv_verb_marker = ['VERB']
  modalv_aux_marker = ['AUX']
  modalv_dep_marker =['aux']
  for sentence in doc.sents:
      for token in sentence:
        if token.lemma_ in modal_verbs:
          if token.pos_ in modalv_aux_marker and token.dep_ in modalv_dep_marker:
            for ancestor in token.ancestors:
              if ancestor.pos_ in modalv_verb_marker:
                if not sentence in modal_verb_list:
                  modal_verb_list.append(sentence)

matcher = Matcher(nlp.vocab)

  modalv_pattern1 = [{"LOWER":{"REGEX":"(better|best)"}}, {"POS":{"REGEX": "(VB|VBD|VBN|VBZ)"}}]
  matcher.add('MOD VB: better/best', [modalv_pattern1])

  matches = matcher(doc)
  for match_id, start, end in matches:
      string_id = nlp.vocab.strings[match_id]
      span = doc[start:end]
      if not span.sent in modal_verb_list:
          modal_verb_list.append(span.sent)

#(E)
len(modal_verb_list)

#(F)
counter = 0
for sentence in modal_verb_list:
  if not sentence in subj_list:
    subj_list.append(sentence)
    counter+=1
print(counter)

"""### STEP 4.5: MODAL VERBS/CONDITIONALS"""

modalv_conditional_list = []
for sentence in modal_verb_list:
  if sentence in conditional_list:
    if not sentence in modalv_conditional_list:
      modalv_conditional_list.append(sentence)
len(modalv_conditional_list)

modalv_interrogative_list = []
for sentence in modal_verb_list:
  if sentence in interrogative_list:
    if not sentence in modalv_interrogative_list:
      modalv_interrogative_list.append(sentence)
len(modalv_interrogative_list)

"""## STEP 5: MODAL ADVERBS"""

for sentence in doc.sents:
    for token in sentence:
     if token.lemma_ in modal_advbs:
      if not sentence in modal_adverb_list:
        modal_adverb_list.append(sentence)

matcher = Matcher(nlp.vocab)

  modaladv_pattern1 = [{"LOWER":"with"}, {"LOWER": "luck"}]
  matcher.add('MOD ADV: WITH LUCK', [modaladv_pattern1])

  matches = matcher(doc)
  for match_id, start, end in matches:
      string_id = nlp.vocab.strings[match_id]
      span = doc[start:end]
      if not span.sent in modal_adverb_list:
          modal_adverb_list.append(span.sent)

#(G)
len(modal_adverb_list)

# (H)
counter = 0
for sentence in modal_adverb_list:
  if not sentence in subj_list:
    subj_list.append(sentence)
    counter+=1
print(counter)

"""### STEP 5.5: MODAL ADVERBS/MODAL VERBS"""

modalv_adv_list = []
for sentence in modal_verb_list:
  if sentence in modal_adverb_list:
    if not sentence in modalv_adv_list:
      modalv_adv_list.append(sentence)
len(modalv_adv_list)

conditional_mod_adv_list = []
for sentence in conditional_list:
  if sentence in modal_adverb_list:
    if not sentence in conditional_mod_adv_list:
      conditional_mod_adv_list.append(sentence)
len(conditional_mod_adv_list)

"""## STEP 6: MODAL CLAUSES"""

sub_ccomp_marker = ['ccomp']
  sub_verb_marker = ['VERB']
  sub_adj_marker = ['ADJ']
  sub_noun_marker = ['NOUN']
                                                #verb clauses
  for sentence in doc.sents:
      for token in sentence:
        if token.lemma_ in sub_verbs and token.pos_ in sub_verb_marker:
          for child in token.children:
            if child.dep_ in sub_ccomp_marker:
              if not sentence in modal_clause_list:
                modal_clause_list.append(sentence)
                                                #adjs
        elif token.pos_ in sub_verb_marker and token.dep_ in sub_ccomp_marker:
            for ancestor in token.ancestors:
              if ancestor.pos_ in sub_adj_marker:
                if ancestor.lemma_ in sub_adjs:
                  if not sentence in modal_clause_list:
                    modal_clause_list.append(sentence)
              else:
                for child in ancestor.children:
                  if child.pos_ in sub_adj_marker and child.lemma_ in sub_adjs:
                    if not sentence in modal_clause_list:
                      modal_clause_list.append(sentence)
                                                #noun
        elif token.lemma_ in sub_nouns and token.pos_ in sub_noun_marker:
          for ancestor in token.ancestors:
            for child in ancestor.children:
              if child.dep_ in sub_ccomp_marker:
                if not sentence in modal_clause_list:
                  modal_clause_list.append(sentence)

matcher = Matcher(nlp.vocab)

  pattern1 = [{"LOWER":"so"}, {"LOWER": "they"}, {"LOWER": "say"}]
  matcher.add('MOD VERB: so they say', [pattern1])
  pattern2 = [{"LOWER":"so"}, {"LOWER": "i"}, {"LEMMA": "have", "OP":"*"}, {"LEMMA": "hear"}]
  matcher.add('MOD VERB: so I hear', [pattern2])
  pattern3 = [{"LOWER":"according"},{"TEXT": "to"}]
  matcher.add('MOD ADVERB: according to', [pattern2])
  pattern4 = [{"LOWER":"by"}, {"TEXT": {"REGEX":"(popular|common)"}}, {"TEXT": "belief"}]
  matcher.add('Subj Cond: by popular belief', [pattern4])
  pattern5 = [{"LOWER":"by"}, {"TEXT": "all"}, {"TEXT": "accounts"}]
  matcher.add('Subj Cond: by all accounts', [pattern5])
  #not + knowing verbs,nouns,
  #list vb/noun/adj + that/to/noun etc.?

  matches = matcher(doc)
  for match_id, start, end in matches:
      string_id = nlp.vocab.strings[match_id]
      span = doc[start:end]
      if not span.sent in modal_clause_list:
          modal_clause_list.append(span.sent)

# (I)
len(modal_clause_list)

# (J)
counter = 0
for sentence in modal_clause_list:
  if not sentence in subj_list:
    subj_list.append(sentence)
    counter+=1
print(counter)

"""### STEP 6.5: MODAL CLAUSES/MODAL ADVERBS

## STEP 7: IMPERATIVES
"""

matcher = Matcher(nlp.vocab)

  imp_match_list = []

  imp_pattern1 = [{"TAG": 'VB', "IS_SENT_START": True}, {"LEMMA":"not", "OP":"*"}]
  matcher.add('Imperative', [imp_pattern1])
  imp_pattern2 = [{"LEMMA": "do", "POS": "AUX", "IS_SENT_START": True}, {"LEMMA":"not"}, {}]
  matcher.add('Neg Imperative', [imp_pattern2])
  #imp_pattern3 = [{"POS": {"REGEX": "(PRON|ADV)"}, "IS_SENT_START": True, "DEP": 'advmod'}, {"TAG": 'VB'}]
  #matcher.add('Imperative?', [imp_pattern3])
  imp_pattern4 = [{"LEMMA": {"REGEX":'(please|pray|prithee)'}}, {"POS": {"REGEX": '(VB|VBD|VBN|VBZ|AUX)'}}]
  matcher.add('Please', [imp_pattern4])

  matches = matcher(doc)
  for match_id, start, end in matches:
      string_id = nlp.vocab.strings[match_id]
      span = doc[start:end]
      if not span.sent in imp_match_list:
        imp_match_list.append(span.sent)

  for sentence in imp_match_list:
    if not sentence in subj_list:
      imperative_list.append(sentence)

# (K)
len(imperative_list)

"""## STEP 8: INDICATIVES"""

counter = 0
for sentence in doc.sents:
  if not sentence in optative_list:
    if not sentence in interrogative_list:
      if not sentence in conditional_list:
        if not sentence in modal_verb_list:
          if not sentence in modal_adverb_list:
            if not sentence in modal_clause_list:
              if not sentence in imperative_list:
                counter+=1
print(counter)

# (L)
counter = 0
for sentence in doc.sents:
  if not sentence in subj_list:
    counter+=1
print(counter)

len(subj_list)

"""## Other slices"""

select_list = []
keywords = ['perhaps']
for sentence in doc.sents:
  for token in sentence:
    if token.lemma_ in keywords:
      if not sentence in conditional_list:
        select_list.append(sentence)

len(select_list)

shared_select_list = []
for sentence in slect_list:
  if sentence in modal_adverb_list: #e.g.
    if not sentence in shared_select_list:
      shared_select_list.append(sentence)
len(modalv_adv_list)
