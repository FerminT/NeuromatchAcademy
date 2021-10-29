# Neuromatch Academy-Deep Learning
Code and slides for the Tanuki group project in NMA-Deep Learning 2021. Based on [Stringer C, Michaelos M, Tsyboulski D, Lindo SE, Pachitariu M. *High-precision coding in visual cortex*. Cell. 2021](https://pubmed.ncbi.nlm.nih.gov/33857423/) dataset, which contains neural recordings from the V1 cortex of mice in an orientation decoding task.

Authors: Fermín Travi, Marco Peña, Rubén Martínez.

## Hypothesis
In the aforementioned work, slight differences were observed in the decoding results depending on the mouse's locomotion state (i.e. if it was running or not).

Here, we further explore these results, looking for differences in the neural recordings of the V1 cortex by dividing them based on the mouse's speed. If the results are consistent, the neurons' activity should be different in the stationary trials compared to the running trials.

Additionally, we look into the possiblity of improving the decoding process by adding the neurons' spatial information into the mix.

## Methodology
In order to test this hypothesis, a threshold to the mouse's speed was set to classify a given trial as a running or stationary trial. Roughly 50% of the trials remained for each group and a random subset was chosen to train the models. The aim is to correctly decode the angle of the given stimuli.

We train two models: one on the stationary data, and the other one on the running data. We test them on their corresponding trials and evaluate their performance. Once this has been done, we switch the input: we test the stationary model on running data and the running model on the stationary data, and evaluate how much the performance changes. If the hypothesis is correct, there should be a notable decrease in performance.

We use a linear regression as our baseline model and a multi-layer perceptron.

With regard to the spatial information, we build a 3D cube, where each cell contains the mean activation of all the neurons that fall under that given cell (the data was not split in this case). We then train a 3D CNN on this grid and analyse its performance in decoding the correct angle.

## Results
The closer the points are to the diagonal, the better the performance.
### Linear regression
![Linear_regr](https://i.postimg.cc/8c5MGqGn/Linear-regression.png)

rMSE for stationary model on stationary testing data: 0.783 radians

rMSE for running model on running testing data: 0.764 radians

### Multi-layer perceptron
![MLP](https://i.postimg.cc/nrGFdLgy/MLP.png)

rMSE for stationary model on stationary testing data: 0.543 radians

rMSE for running model on running testing data: 0.627 radians

### Switching the input: running model on stationary data and viceversa
### Linear regression
![Linear_regr_switched](https://i.postimg.cc/G38v69QW/Linear-regression-switched.png)

rMSE for stationary model on running data: 0.875 radians

rMSE for running model on stationary data: 1.024 radians

### Multi-layer perceptron
![MLP_switched](https://i.postimg.cc/6p3ZL3yb/MLP-switched.png)

rMSE for stationary model on running data: 0.875 radians

rMSE for running model on stationary data: 0.794 radians

The **error rate increases roughly by 20%**. This suggests the data follow different distributions for each group. It is important to note that this difference can not be explained by the mouse's head movements while running, for it was fixed at all times during the experiments.

We then trained a **classifier** based on these results, which receives as input the neural activity of a given trial and as output it predicts if the mouse was running or not: it achieved **~90% accuracy**.

### 3D CNN
![3D_CNN](https://i.postimg.cc/V6cBRwyS/3D-CNN.png)

rMSE on testing data: 0.681 radians

To test if the spatial information was adding something to the predictions, we **shuffled the grid** to see how much the performance varied.

![3D_CNN_shuffled](https://i.postimg.cc/NjcHS4JV/3-D-CNN-shuffled.png)

rMSE on testing data: 0.593 radians

Not only did the **performance** not get worse, but it actually **improved**. As surprising as this is, it is actually consistent with previous results where shuffling the data improved the decoder's results.

## Conclusions
 - Neural activity in the V1 cortex does seem to follow different patterns depending on the movement of the mouse.
 - Spatial information didn’t improve the precision of the decoder, suggesting it’s not relevant for the task.
