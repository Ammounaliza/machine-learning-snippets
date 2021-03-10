>**Note**: This is a generated markdown export from the Jupyter notebook file [classification_with_automl.ipynb](classification_with_automl.ipynb).

# Classification with AutoML (auto-sklearn)


```python
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

from sklearn import datasets, metrics, model_selection, preprocessing, pipeline

import warnings
warnings.simplefilter(action='ignore', category=FutureWarning)

import autosklearn.classification
```


```python
wine = datasets.load_wine()
print(wine.DESCR)
```

    .. _wine_dataset:
    
    Wine recognition dataset
    ------------------------
    
    **Data Set Characteristics:**
    
        :Number of Instances: 178 (50 in each of three classes)
        :Number of Attributes: 13 numeric, predictive attributes and the class
        :Attribute Information:
     		- Alcohol
     		- Malic acid
     		- Ash
    		- Alcalinity of ash  
     		- Magnesium
    		- Total phenols
     		- Flavanoids
     		- Nonflavanoid phenols
     		- Proanthocyanins
    		- Color intensity
     		- Hue
     		- OD280/OD315 of diluted wines
     		- Proline
    
        - class:
                - class_0
                - class_1
                - class_2
    		
        :Summary Statistics:
        
        ============================= ==== ===== ======= =====
                                       Min   Max   Mean     SD
        ============================= ==== ===== ======= =====
        Alcohol:                      11.0  14.8    13.0   0.8
        Malic Acid:                   0.74  5.80    2.34  1.12
        Ash:                          1.36  3.23    2.36  0.27
        Alcalinity of Ash:            10.6  30.0    19.5   3.3
        Magnesium:                    70.0 162.0    99.7  14.3
        Total Phenols:                0.98  3.88    2.29  0.63
        Flavanoids:                   0.34  5.08    2.03  1.00
        Nonflavanoid Phenols:         0.13  0.66    0.36  0.12
        Proanthocyanins:              0.41  3.58    1.59  0.57
        Colour Intensity:              1.3  13.0     5.1   2.3
        Hue:                          0.48  1.71    0.96  0.23
        OD280/OD315 of diluted wines: 1.27  4.00    2.61  0.71
        Proline:                       278  1680     746   315
        ============================= ==== ===== ======= =====
    
        :Missing Attribute Values: None
        :Class Distribution: class_0 (59), class_1 (71), class_2 (48)
        :Creator: R.A. Fisher
        :Donor: Michael Marshall (MARSHALL%PLU@io.arc.nasa.gov)
        :Date: July, 1988
    
    This is a copy of UCI ML Wine recognition datasets.
    https://archive.ics.uci.edu/ml/machine-learning-databases/wine/wine.data
    
    The data is the results of a chemical analysis of wines grown in the same
    region in Italy by three different cultivators. There are thirteen different
    measurements taken for different constituents found in the three types of
    wine.
    
    Original Owners: 
    
    Forina, M. et al, PARVUS - 
    An Extendible Package for Data Exploration, Classification and Correlation. 
    Institute of Pharmaceutical and Food Analysis and Technologies,
    Via Brigata Salerno, 16147 Genoa, Italy.
    
    Citation:
    
    Lichman, M. (2013). UCI Machine Learning Repository
    [https://archive.ics.uci.edu/ml]. Irvine, CA: University of California,
    School of Information and Computer Science. 
    
    .. topic:: References
    
      (1) S. Aeberhard, D. Coomans and O. de Vel, 
      Comparison of Classifiers in High Dimensional Settings, 
      Tech. Rep. no. 92-02, (1992), Dept. of Computer Science and Dept. of  
      Mathematics and Statistics, James Cook University of North Queensland. 
      (Also submitted to Technometrics). 
    
      The data was used with many others for comparing various 
      classifiers. The classes are separable, though only RDA 
      has achieved 100% correct classification. 
      (RDA : 100%, QDA 99.4%, LDA 98.9%, 1NN 96.1% (z-transformed data)) 
      (All results using the leave-one-out technique) 
    
      (2) S. Aeberhard, D. Coomans and O. de Vel, 
      "THE CLASSIFICATION PERFORMANCE OF RDA" 
      Tech. Rep. no. 92-01, (1992), Dept. of Computer Science and Dept. of 
      Mathematics and Statistics, James Cook University of North Queensland. 
      (Also submitted to Journal of Chemometrics).
    



```python
X = pd.DataFrame(wine.data, columns=wine.feature_names)
y = wine.target
```


```python
X_train, X_test, y_train, y_test = model_selection.train_test_split(X, y, train_size=0.5, stratify=y)

df_train = pd.DataFrame(y_train, columns=['target'])
df_train['type'] = 'train'

df_test = pd.DataFrame(y_test, columns=['target'])
df_test['type'] = 'test'

df_set = df_train.append(df_test)

_ = sns.countplot(x='target', hue='type', data=df_set)     

print('train samples:', len(X_train))
print('test samples', len(X_test))
```

    train samples: 89
    test samples 89



    
![png](classification_with_automl_files/classification_with_automl_4_1.png)
    


**Note:** We do some restrictions here running time and number of ensembles, because the model fitting would not take much longer. So this is just an example how you could run AutoML.


```python
model = autosklearn.classification.AutoSklearnClassifier(time_left_for_this_task=30, ensemble_size=3)
```


```python
%%capture
# ignore oput from model fit with capture magic command
model.fit(X_train, y_train)
```

Print the final ensemble constructed by auto-sklearn


```python
for m in model.get_models_with_weights():
    print(m)

```

    (0.6666666666666666, SimpleClassificationPipeline({'balancing:strategy': 'weighting', 'classifier:__choice__': 'extra_trees', 'data_preprocessing:categorical_transformer:categorical_encoding:__choice__': 'no_encoding', 'data_preprocessing:categorical_transformer:category_coalescence:__choice__': 'minority_coalescer', 'data_preprocessing:numerical_transformer:imputation:strategy': 'median', 'data_preprocessing:numerical_transformer:rescaling:__choice__': 'normalize', 'feature_preprocessor:__choice__': 'extra_trees_preproc_for_classification', 'classifier:extra_trees:bootstrap': 'False', 'classifier:extra_trees:criterion': 'gini', 'classifier:extra_trees:max_depth': 'None', 'classifier:extra_trees:max_features': 0.9292309396985746, 'classifier:extra_trees:max_leaf_nodes': 'None', 'classifier:extra_trees:min_impurity_decrease': 0.0, 'classifier:extra_trees:min_samples_leaf': 1, 'classifier:extra_trees:min_samples_split': 10, 'classifier:extra_trees:min_weight_fraction_leaf': 0.0, 'data_preprocessing:categorical_transformer:category_coalescence:minority_coalescer:minimum_fraction': 0.032719158639429445, 'feature_preprocessor:extra_trees_preproc_for_classification:bootstrap': 'False', 'feature_preprocessor:extra_trees_preproc_for_classification:criterion': 'entropy', 'feature_preprocessor:extra_trees_preproc_for_classification:max_depth': 'None', 'feature_preprocessor:extra_trees_preproc_for_classification:max_features': 0.9929881254946676, 'feature_preprocessor:extra_trees_preproc_for_classification:max_leaf_nodes': 'None', 'feature_preprocessor:extra_trees_preproc_for_classification:min_impurity_decrease': 0.0, 'feature_preprocessor:extra_trees_preproc_for_classification:min_samples_leaf': 1, 'feature_preprocessor:extra_trees_preproc_for_classification:min_samples_split': 2, 'feature_preprocessor:extra_trees_preproc_for_classification:min_weight_fraction_leaf': 0.0, 'feature_preprocessor:extra_trees_preproc_for_classification:n_estimators': 100},
    dataset_properties={
      'task': 2,
      'sparse': False,
      'multilabel': False,
      'multiclass': True,
      'target_type': 'classification',
      'signed': False}))
    (0.3333333333333333, SimpleClassificationPipeline({'balancing:strategy': 'none', 'classifier:__choice__': 'mlp', 'data_preprocessing:categorical_transformer:categorical_encoding:__choice__': 'no_encoding', 'data_preprocessing:categorical_transformer:category_coalescence:__choice__': 'no_coalescense', 'data_preprocessing:numerical_transformer:imputation:strategy': 'median', 'data_preprocessing:numerical_transformer:rescaling:__choice__': 'standardize', 'feature_preprocessor:__choice__': 'feature_agglomeration', 'classifier:mlp:activation': 'tanh', 'classifier:mlp:alpha': 0.0001363185819149026, 'classifier:mlp:batch_size': 'auto', 'classifier:mlp:beta_1': 0.9, 'classifier:mlp:beta_2': 0.999, 'classifier:mlp:early_stopping': 'valid', 'classifier:mlp:epsilon': 1e-08, 'classifier:mlp:hidden_layer_depth': 3, 'classifier:mlp:learning_rate_init': 0.00018009776276177523, 'classifier:mlp:n_iter_no_change': 32, 'classifier:mlp:num_nodes_per_layer': 115, 'classifier:mlp:shuffle': 'True', 'classifier:mlp:solver': 'adam', 'classifier:mlp:tol': 0.0001, 'feature_preprocessor:feature_agglomeration:affinity': 'euclidean', 'feature_preprocessor:feature_agglomeration:linkage': 'ward', 'feature_preprocessor:feature_agglomeration:n_clusters': 182, 'feature_preprocessor:feature_agglomeration:pooling_func': 'mean', 'classifier:mlp:validation_fraction': 0.1},
    dataset_properties={
      'task': 2,
      'sparse': False,
      'multilabel': False,
      'multiclass': True,
      'target_type': 'classification',
      'signed': False}))



```python
predicted = model.predict(X_test)

truth_table = pd.DataFrame(predicted, columns=['target_predicted'])
truth_table['target_truth'] = y_test

truth_table = truth_table.groupby(['target_predicted', 'target_truth']).size().unstack().fillna(0)

truth_table
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>target_truth</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
    <tr>
      <th>target_predicted</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>30.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>34.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
_ = sns.heatmap(truth_table, annot=True, cmap="Blues")
```


    
![png](classification_with_automl_files/classification_with_automl_11_0.png)
    



```python
print("accuracy: {:.3f}".format(metrics.accuracy_score(y_test, predicted)))
print("precision: {:.3f}".format(metrics.precision_score(y_test, predicted, average='weighted')))
print("recall: {:.3f}".format(metrics.recall_score(y_test, predicted, average='weighted')))
print("f1 score: {:.3f}".format(metrics.f1_score(y_test, predicted, average='weighted')))
```

    accuracy: 0.989
    precision: 0.989
    recall: 0.989
    f1 score: 0.989



```python

```