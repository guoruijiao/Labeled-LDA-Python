## Implement of L-LDA Model(Labeled Latent Dirichlet Allocation Model) with python


References:
   * *Labeled LDA: A supervised topic model for credit attribution in multi-labeled corpora, Daniel Ramage...*
   * *Parameter estimation for text analysis, Gregor Heinrich.*
   * *Latent Dirichlet Allocation, David M. Blei, Andrew Y. Ng...*
   
### An efficient implementation based on Gibbs sampling

**The following descriptions come from *Labeled LDA: A supervised topic model for credit attribution in multi-labeled corpora, Daniel Ramage...***

##### Introduction:
Labeled LDA is a topic model that constrains Latent Dirichlet Allocation by defining a one-to-one correspondence between LDA’s latent topics and user tags.
Labeled LDA can directly learn topics(tags) correspondences.

##### Gibbs sampling:
* Graphical model of Labeled LDA:
<!-- ![https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/graphical-of-labeled-lda.png](https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/graphical-of-labeled-lda.png) -->

<img src="https://github.com/JoeZJH/Labeled-LDA-Python/blob/master/assets/graphical-of-labeled-lda.png" width="400" height="265"/>

* Generative process for Labeled LDA:
<!-- ![https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/generative-process-for-labeled-lda.png](https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/generative-process-for-labeled-lda.png) -->
<img src="https://github.com/JoeZJH/Labeled-LDA-Python/blob/master/assets/generative-process-for-labeled-lda.png" width="400" height="400"/>

* Gibbs sampling equation:
<!-- ![https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/gibbs-sampling-equation.png](https://github.com/JoeZJH/Labeled-LDA/blob/master/assets/gibbs-sampling-equation.png) -->
<img src="https://github.com/JoeZJH/Labeled-LDA-Python/blob/master/assets/gibbs-sampling-equation.png" width="400" height="85"/>

### Usage
* new llda model
* training
* ?is_convergence
* update
* inference [optional multi-process]
* save model to disk
* load model from disk


##### Example 
```
# @source code: example/exapmle.py

import model.labeled_lda as llda

# data
labeled_documents = [("example example example example example", ["example"]),
                     ("test llda model test llda model test llda model", ["test", "llda_model"]),
                     ("example test example test example test example test", ["example", "test"]),
                     ("good perfect good good perfect good good perfect good ", ["positive"]),
                     ("bad bad down down bad bad down", ["negative"])]

# new a Labeled LDA model
# llda_model = llda.LldaModel(labeled_documents=labeled_documents, alpha_vector="50_div_K", eta_vector=0.001)
# llda_model = llda.LldaModel(labeled_documents=labeled_documents, alpha_vector=0.02, eta_vector=0.002)
llda_model = llda.LldaModel(labeled_documents=labeled_documents)
print llda_model

# training
# llda_model.training(iteration=10, log=True)
while True:
    print("iteration %s sampling..." % (llda_model.iteration + 1))
    llda_model.training(1)
    print "after iteration: %s, perplexity: %s" % (llda_model.iteration, llda_model.perplexity)
    if llda_model.is_convergent:
        break

# update
print "before updating: ", llda_model
update_labeled_documents = [("new example test example test example test example test", ["example", "test"])]
llda_model.update(labeled_documents=update_labeled_documents)
print "after updating: ", llda_model

# train again
# llda_model.training(iteration=10, log=True)
while True:
    print("iteration %s sampling..." % (llda_model.iteration + 1))
    llda_model.training(1)
    print "after iteration: %s, perplexity: %s" % (llda_model.iteration, llda_model.perplexity)
    if llda_model.is_convergent:
        break

# inference
# note: the result topics may be different for difference training, because gibbs sampling is a random algorithm
document = "example llda model example example good perfect good perfect good perfect"
# topics = llda_model.inference(document=document, iteration=10, times=10)
topics = llda_model.inference_multi_processors(document=document, iteration=10, times=10)
print topics

# save to disk
save_model_dir = "../data/model"
llda_model.save_model_to_dir(save_model_dir)

# load from disk
llda_model_new = llda.LldaModel()
llda_model_new.load_model_from_dir(save_model_dir)
print "llda_model_new", llda_model_new
print "llda_model", llda_model
```


