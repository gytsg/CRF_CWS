# CRF_CWS Using CRF to deal with CWS(Chinese words segmentation) problem
========================
<br>
1.download CRF++ from https://taku910.github.io/crfpp/#tips   &&  corpus from http://sighan.cs.uchicago.edu/bakeoff2005/ <br>
2.-> Extract  <br>
`$ cd CRF++-0.58` <br>
`$ ./configure` <br>
`$ sudo make` <br>
`$ sudo make install`<br>
//if raise `ImportError: libcrfpp.so.0: cannot open shared object file: No such file or directory`<br>
//use `ln -s /usr/local/lib/libcrfpp.so.0 /usr/lib/`<br>
<br>
3.The training data in backoff2005 was converted into the required training data format of CRF++, <br>
and a 4-tag(B(Begin, prefix), E(End, suffix), M(Middle, Middle), S(Single, Single word) tag set <br>
was used to process the utf-8 encoded text.<br>
Turn training set "/icwb2-data/training/pku_training.utf8" to crf training set.<br>
`$ Python make_crf_train.py`<br>
Get train.utf8<br>

4.To execute this command, you can create a new folder outside the installation file. <br>
Template is the template file, and model is the model file after the training is completed. <br>
You only need to put the template and training data into the new folder.<br>
`$ crf_learn -f 3 -c 4.0 ./template ./train.utf8 model`<br>
<br>
////////////////////template file//////////////////<br>
```
# Unigram<br>
U00:%x[-2,0]<br>
U01:%x[-1,0]<br>
U02:%x[0,0]<br>
U03:%x[1,0]<br>
U04:%x[2,0]<br>
U05:%x[-2,0]/%x[-1,0]/%x[0,0]<br>
U06:%x[-1,0]/%x[0,0]/%x[1,0]<br>
U07:%x[0,0]/%x[1,0]/%x[2,0]<br>
U08:%x[-1,0]/%x[0,0]<br>
U09:%x[0,0]/%x[1,0]<br>
<br>
# Bigram<br>
B<br>
```
<br>
5.With the model, we still need to prepare a test corpus for cr ++, and then use the testing tool of cr ++, crf_test, <br>
for word annotation. The original test corpus is icwb2-data/testing/pku_test.utf8<br>
`$ python make_crf_test.py`<br>
Get test.utf8<br>
Then execute crf_test to get the word annotation result<br>
`$ crf_test -m ./model  ./test.utf8 > test.tag.utf8`<br>
<br>
6.Next convert the marked bit information into participle results.<br>
`$ python crf_2_word.py`<br>
<br>
7.Evaluate the segmentation effect.<br>
`$ perl score ./icwb2-data/gold/pku_training_words.utf8 ./icwb2-data/gold/pku_test_gold.utf8 test.tag.utf8 > score_crf.ut8`<br>
<br>
`F measure on pku set is : 0.930`<br>
  1
  2
