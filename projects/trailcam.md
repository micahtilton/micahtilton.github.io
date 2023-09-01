---
layout: project
type: project
image: img/trailcam/trailcam-ml-2.png
title: "Fog Classification of Trail-Cam Footage in Hawai'i"
date: 2022
published: true
labels:
  - Machine Learning
  - Artificial Intelligence
  - Climate Research
summary: "I worked with a partner to create a machine learning model that will classify an image as foggy or clear."
---

## Overview:
I worked with a partner to create a machine learning model that will classify an image as foggy or clear. This research is important for monitoring the climate of Hawaii over long periods of time. Fog sensors can be large, expensive, and may be inaccessible for some people. Our tool could help people who have tens of thousands of trail-cam images to quickly classify their images into foggy or clear categories.

<img src="../img/trailcam/kaala-clear.jpg" width="800">

## Tools:
I had to learn Blender (3D Graphics Tool) to generate a simulated data set of foggy and clear images. I generated a data set of over 100 images which took a few hours to render. The reason why it took such a long time is because I used a volumetric fog simulation with a physically based path tracer for maximum realism. I trained a machine learning model on the generated data and observed its performance on real world data.

<img src="../img/trailcam/blender-clear.png" width="800">

```python
# load features
df_train = pd.DataFrame(pd.read_csv("features-generated.txt"))
df_test = pd.DataFrame(pd.read_csv("features.txt"))

X_train = []
y_train = df_train["category"].to_numpy()

# transform features
for i in df_train["location"].unique():
    site = df_train[df_train["location"] == i]
    X = site.iloc[:, [4, 5, 6, 7, 8, 9, 10]]
    X = StandardScaler().fit_transform(X)
    X_train.append(X)
X_train = np.concatenate(X_train, axis=0)

# train model
model = LogisticRegression()
model.fit(X_train, y_train)

# test model
for i in df_test["location"].unique():
    site = df_test[df_test["location"] == i]
    X_test = site.iloc[:, [4, 5, 6, 7, 8, 9, 10]]
    X_test = StandardScaler().fit_transform(X_test)
    y_true  = site["category"].to_numpy()
    auroc = roc_auc_score(y_true, model.predict_proba(X_test)[:,1])
```

## Results:
The logistic regression model trained with the data set of images generated with Blender yielded an average AUROC of 0.74. In other words, the model learned some of the main features of foggy images and was able to predict foggy images in real world data without having any real world data in its training set. Isn't that cool!