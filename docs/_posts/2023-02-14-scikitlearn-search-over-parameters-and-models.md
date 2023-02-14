    from sklearn import linear_model
    from sklearn.model_selection import GroupShuffleSplit
    from sklearn import metrics
    from sklearn.metrics import RocCurveDisplay
    from sklearn.model_selection import GroupKFold
    from sklearn.model_selection import GridSearchCV
    from sklearn.model_selection import ParameterGrid
    from sklearn.pipeline import Pipeline
    from sklearn.svm import SVC
    from sklearn.preprocessing import StandardScaler
    from sklearn.exceptions import DataConversionWarning
    from sklearn import ensemble

    import xgboost as xgb

    import pandas as pd
    import numpy as np

    gs = GroupShuffleSplit(n_splits=2, test_size=.6, random_state=0)
    train_ix, test_ix = next(gs.split(df.drop(["result.eventType_any_out", "game_date", "game_pk", "pitch_seq"], axis=1),
                                      df["result.eventType_any_out"], 
                                      groups=df["game_pk"]))

    X_train = df.drop(["result.eventType_any_out", "game_date", "game_pk", "pitch_seq"], axis=1).loc[train_ix]
    y_train = df["result.eventType_any_out"].loc[train_ix]

    X_test = df.drop(["result.eventType_any_out", "game_date", "game_pk", "pitch_seq"], axis=1).loc[test_ix]
    y_test = df["result.eventType_any_out"].loc[test_ix]

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

    #combine model list and model scores into one dict
    model_dict = dict(zip(model_list, model_score_list))
    #find the max score and associated model
    max_value_key = max(model_dict, key=model_dict.get)
    print(max(model_dict.values())) #this is the winning score
    print(max_value_key) #the winning model

    #plot the AUC
    fpr, tpr, thresholds = metrics.roc_curve(y_test_cv, y_pred_cv)
    roc_display = RocCurveDisplay(fpr=fpr, tpr=tpr).plot()
