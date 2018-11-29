---
layout: post
title:  "Resources for Deep Learning"
date:   2017-08-14 22:00:00 -0600
categories: [ml]
tags: [ml, pca, svm, kmeans, coursera]
---
Many people have asked me how they should learn how to use deep learning, so I figured I should consolidate my answer in one place. First off, I think the best way to understand deep learning is to have a strong mathematical background. That doesn't mean that you have to have taken a formal course in calculus or linear algebra, but learning those things on your own is helpful. The best resource for this is [Khan Academy][khan-academy].

After building a strong mathematical foundation, my recommendation isn't to jump straight into deep learning, but to first start with classical machine learning. This includes algorithms such as Principal Component Analysis, Support Vector Machines, K-Means clustering, and some others. While these aren't directly used in deep learning, they are a lot simpler to understand and more grounded in theory. These algorithms provide a good understanding data analysis, supervised learning, and unsupervised learning. The best course for this is Andrew Ng's [Machine Learning course][ng-coursera]. I took this course in my third year of high school and found it to be very useful. The course also goes over important topics such as how to engineer a machine learning system to properly learn from data without having issues such as underfitting or overfitting.

Finally, after building up skills in math and classical machine learning, you'd be ready to dive into deep learning. I personally haven't taken any course in deep learning because Andrew Ng hadn't built one when I was learning it, so I learned it by poring over code and papers for months. However, to learn deep learning properly, I would suggest Andrew Ng's new [Deep Learning course series][ng-deeplearning]. Though it came too late for me to take it, I did look carefully at the syllabus for each of the 5 courses he has built.

This may all seem like a lot. Plenty of people do jump straight to deep learning without the mathematical foundation or the understanding of classical machine learning. However, I believe this doesn't provide sufficient insight into the machinery of deep learning to be able to use it most effectively. I find that I personally benefit in my work from understanding the math behind deep learning and the classical techniques that were used prior to deep learning becoming a popular tool.

[khan-academy]: https://www.khanacademy.org/
[ng-coursera]: https://www.coursera.org/learn/machine-learning
[ng-deeplearning]: https://www.deeplearning.ai/
