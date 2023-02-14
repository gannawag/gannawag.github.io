When training a machine learning model, we often use cross validation to
search over parameters within a given model type. For example, choosing
the best `alpha` in a lasso model. But what if we don’t know the best
model type to use? SciKitLearn makes searching over different models
straightforward.

I wrote the following code to search over a tuples where the first entry
in each tuple is the type of model and the second entry is the set of
parameters to search over for that model. Each tuple has different
parameters since each model requires different parameters.

    #loop over model types and associated parameter sets
    model_param_set = [
        (
         linear_model.Lasso(),
         {'alpha':[0.01, 0.05]}
        ),
        (
         linear_model.Ridge(),
         {'alpha':[0.1, 0.05]}
        ),
        (
            ensemble.GradientBoostingRegressor(),
         {"n_estimators": [100, 150],
          "max_leaf_nodes": [4, 10],
          "max_depth": [None],
          "random_state": [2],
          "min_samples_split": [5, 10]}
         ),
        (
           xgb.XGBClassifier(),
         {'n_estimators':[10, 100, 200],
          'tree_method':['auto'],
          'subsample':[0.67, 0.33, 0.25],
          'colsample_level':[0.06, 0.03, 0.01],
          'verbose':[0],
          'n_jobs':[6],
          'random_state':[1234]}   
        )
    ]

To add models/parameters, just add another tuple or edit the existing
tuples.

Then, loop over the tuples with sklearn, saving the resulting scores to
find the model+parameter combination that performs the best in the cross
validation stage.

    #empty lists to populate with best models/params
    model_list = []
    model_score_list = []
    for m,p in model_param_set:

      clf = GridSearchCV(m, #model
                         p, #parameters
                         cv = GroupKFold(n_splits=3), #use this type of cross validation
                         scoring='roc_auc') #how to score models

      clf.fit(X_train, #training features
              y_train, #training labels
              groups = df["groups"].loc[train_ix] #specifies groups for cross validation
              )

      print('max score: ', clf.best_score_)
      print('best model: ', clf.best_estimator_)

      #add best of each model to list
      model_score_list.append(clf.best_score_)
      model_list.append(clf.best_estimator_)

I use a grouped cross validation to keep groups together, but the
approach works for more traditional cross validation.

Finally, choose the best model+parameter tuple and plot the ROC for that
set.

    #combine model list and model scores into one dict
    model_dict = dict(zip(model_list, model_score_list))
    #find the max score and associated model
    max_value_key = max(model_dict, key=model_dict.get)
    print(max(model_dict.values())) #this is the winning score
    print(max_value_key) #the winning model

    #plot the AUC
    fpr, tpr, thresholds = metrics.roc_curve(y_test_cv, y_pred_cv)
    roc_display = RocCurveDisplay(fpr=fpr, tpr=tpr).plot()
