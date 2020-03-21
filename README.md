# CRF_CWS
Using CRF to deal with CWS(Chinese words segmentation) problem

1.download CRF++ from https://taku910.github.io/crfpp/#tips   &&  corpus from http://sighan.cs.uchicago.edu/bakeoff2005/
2.-> Extract  
$ cd CRF++-0.58 
$ ./configure 
$ sudo make 
$ sudo make install
//if raise "ImportError: libcrfpp.so.0: cannot open shared object file: No such file or directory"
//use "ln -s /usr/local/lib/libcrfpp.so.0 /usr/lib/"

3.The training data in backoff2005 was converted into the required training data format of CRF++, 
and a 4-tag(B(Begin, prefix), E(End, suffix), M(Middle, Middle), S(Single, Single word) tag set was used to process the utf-8 encoded text.
Turn training set "/icwb2-data/training/pku_training.utf8" to crf training set.
$ Python make_crf_train.py
Get train.utf8

4.To execute this command, you can create a new folder outside the installation file. 
Template is the template file, and model is the model file after the training is completed. 
You only need to put the template and training data into the new folder.
$ crf_learn -f 3 -c 4.0 ./template ./train.utf8 model

////////////////////template file//////////////////
///   # Unigram
///   U00:%x[-2,0]
///   U01:%x[-1,0]
///   U02:%x[0,0]
///   U03:%x[1,0]
///   U04:%x[2,0]
///   U05:%x[-2,0]/%x[-1,0]/%x[0,0]
///   U06:%x[-1,0]/%x[0,0]/%x[1,0]
///   U07:%x[0,0]/%x[1,0]/%x[2,0]
///   U08:%x[-1,0]/%x[0,0]
///   U09:%x[0,0]/%x[1,0]
///   
///   # Bigram
///   B
///////////////////////////////////////////////////

5.With the model, we still need to prepare a test corpus for cr ++, and then use the testing tool of cr ++, crf_test, 
for word annotation. The original test corpus is icwb2-data/testing/pku_test.utf8
$ python make_crf_test.py
Get test.utf8
Then execute crf_test to get the word annotation result
$ crf_test -m ./model  ./test.utf8 > test.tag.utf8

6.Next convert the marked bit information into participle results.
$ python crf_2_word.py

7.Evaluate the segmentation effect.
$ perl score ./icwb2-data/gold/pku_training_words.utf8 ./icwb2-data/gold/pku_test_gold.utf8 test.tag.utf8 > score_crf.ut8

F measure on pku set is : 0.930
