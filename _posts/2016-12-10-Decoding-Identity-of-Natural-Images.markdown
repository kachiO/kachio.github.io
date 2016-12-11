---
layout: post
title: "Decoding Identity of Natural Images From Mouse Visual Cortex"
date: 2016-12-10
---

<h1>Introduction</h1>

The [Allen Institute for Brain Science](https://www.alleninstitute.org/) recently released a [rich dataset](http://observatory.brain-map.org/visualcoding) containing neural responses from many visual brain areas and cortical layers. Here is a nice [video](https://www.youtube.com/watch?v=fSaOCDmRs1Q) introducing the [Brain Observatory project](http://observatory.brain-map.org/visualcoding).This unprecedented dataset is an important resource and testbed for generating hypotheses about visual cortical function.

Mice are relatively [new players to the visual neuroscience game](http://www.nature.com/news/neuroscience-through-the-eyes-of-a-mouse-1.13901). Mice have a number of secondary visual areas which are not only retinotopic, but also have preferences for spatial and temporal frequency of grating stimuli. Beyond that very little is know about what kind of information can be readout from neural populations within mouse secondary visual areas. Linear readout (i.e. linear combinations) of neurons is an implicit assumption in systems neuroscience about how downstream brain areas *could* make use the information represented in the neural responses of upstream brain areas to generate behavior.

I set out on a data mission to investigate whether neurons in mouse visual cortex could linearly readout (i.e. discriminate) the identity of natural images.

<h1>The Data & Methods</h1>

Mice were presented with a total of 118 natural scenes. Each scene was presented for 250mss and randomly repeated 50 times, one immediately after the other with no blank screen in between. (I thought this was a bit unusual that they would present the scenes one right after the other without a blank screen in between. Maybe they were worried about stimulus onset transients?) A blank screen, however, was presented after ever 100 scenes. More details about the experimental procedures are discussed in the [technical whitepaper](http://help.brain-map.org/display/observatory/Documentation).

I had to do a bit of data wrangling to extract and save the responses from different brain areas, cell types, and scenes. I created a matrix, _XT_, of size _n_ x _c_ x _t_  where n = number of trials, c = number of cells in a visual area (or cell type), and t = time (or frames).This matrix was created for each visual area. The number of cells per area were ranged between 2157 (PM) to 4189 (V1) (see table below). There were 5900 trials total (118 images, 50 repeats each). Blank stimulus trials were excluded. The total number of frames included per cell was 21, which included 7 frames pre-stimulus, 7 frames during stimulus, and 7 frames post-stimulus. Approximately 7 imaging frames were acquired per 250ms. The analyses in this project were done using the relative changes in fluorescence (${\Delta} F/F$) as the neuronal response instead of spikes.

**Table 1. Number of Cells Per Area:**
| PM   | AL   | LM   | V1   |
|------|------|------|------|
| 2157 | 2323 | 2606 | 4189 |

To measure performance on identifying a natural image, I trained a linear support vector classifier ([one-vs-rest classifier scheme](https://en.wikipedia.org/wiki/Multiclass_classification#One-vs.-rest)) on the average response during 200-300ms post stimulus, and computed cross-validated test accuracy for the trained classifier for each area. The classifier was trained on 70% of the trials in an area, and tested on the remaining 30%. In most cases I repeated this procedure 10 times to obtain an average and computed the standard error of the mean (SEM).

The code is available on Juptyer Notebook on the Github Repo.

<h1>Results</h1>

<h2>Primary Visual Cortex (V1) Achieved Highest Performance on Natural Image Identity Task</h2>
Although all the visual areas performed well above chance (0.85%), V1 exhibited the highest performance (>75%) in linearly classifying the natural images, followed by area LM (>50%). Areas PM and LM performed similarly (> 30%) to each other.

<center><img src="/assets/blog/natural_image_identity/Decoder_accuracy_all_neurons.png" height="400" title="Figure 1a. Natural Image Identity Performance for Each Area, all neurons included"></center>


You might have noticed from Table 1 that each area has a different number of neurons (features), and that the best performing area V1 has the most number of neurons. In other words V1 might have outperformed other areas because it had more neurons. However, when I repeat the classifier using 2100 neurons (features) per area, V1 still outperformed all the other areas.

[**Figure 1b.**]: /assets/blog/natural_image_identity/Decoder_accuracy_2100_neurons.png "Natural Image Identity Performance for Each Area, 2100 neurons included"

<center><img src="/assets/blog/natural_image_identity/Decoder_accuracy_2100_neurons.png" height="400" alt= "Figure 1b." title="Natural Image Identity Performance for Each Area, 2100 neurons included"></center>


<h2>Natural Image Identity Performance Increases With Population Size for All Visual Areas</h2>
As you might have noticed from the above figures, the performance of the classifier depends on the size of the population of neurons in a given area. This is illustrated more clearly in the figure below.

[**Figure 2a**]: /assets/blog/natural_image_identity/accuracy_per_population_size.png "Performance per Population Size"

Another way to evaluate and compare each classifier's performance is to assess the [mutual information](https://en.wikipedia.org/wiki/Mutual_information) between the classifier's predicted class label and the true class label. To calculate mutual information I computed a confusion matrix, which reflects the proportion of times the classifier guessed the true class label i.e. the conditional probability ($P(predicted|true)$) of the classifier predicted label given the true label.

From the normalized confusion matrix, you will notice that there is a close agreement between each area classifier's predicted class label (i.e. image identity) and the true label. The image labels (1-118) is the same for each matrix. Next time around, I would like to make an interactive plot that let's you visualize which images an area classifier predicts well.

[**Figure 2b - confusion matrix and mutual information per population size**]: /assets/blog/natural_image_identity/mutual_info.png "Confusion Matrix and Mutual information "

<h2>Natural Image Identity Performance Accuracy Across Time </h2>
The next question I asked was whether different visual areas might achieve higher accuracy at different times from the stimulus onset. I found three ways to approach this question.

The first approach To evaluate the performance of the classifier across time, I trained The decoder using the mean response from 200-300ms post-stimulus and tested the classification accuracy using the activity at each time point (or frame).

[**Figure 3a.**]: /assets/blog/natural_image_identity/accuracy_across_time_mean_frames.png "Performance across time, trained with mean frames from 200-300ms post-stimulus onset"

The second approach was to take a single frame, approximately 200ms post-stimulus, to train a classifier and compute the cross-validated test accuracy for each frame. This worked surprisingly well that the neural activity within a single frame (approximately 30ms) achieved high prediction accuracy, comparable to using the average activity between a larger time window of 100ms.

[**Figure 3b.**]: /assets/blog/natural_image_identity/accuracy_across_time_single_frame.png "Performance across time, trained with single frame at 200ms post-stimulus onset"

Given that the single frame approach was informative enough for classifier, I trained independent classifiers at each time point (frame), and compute the cross-validated accuracy for each frame. Again, very similar temporal profile.

[**Figure 3c.**]: /assets/blog/natural_image_identity/accuracy_across_time_independent_frames.png "Performance across time, independently trained at each time point"


<h2>Natural Image Categorization</h2>
Given that neural responses in these areas can be used to perform natural image identity task. What if I, instead, now asked the visual areas to categorize the natural images into two groups? My prediction was that putative higher visual areas, such as PM or AL, would perform much better than V1. I devised two simple categories, animals vs. non-animals. Although the visual areas all performed above chance (50%), they each performed similarly, including V1.

[**Figure 4**]: /assets/blog/natural_image_identity/binary_categorization_accuracy.png "Animal vs Non-animal Categorization Performance"

<h1>Discussion</h1>

I was surprised to find that V1 outperformed all other visual areas on the natural image identity task, considering V1 is lower in the putative mouse visual hierarchy. The observation that V1 could linearly classify natural images is consistent with a previous study from [Kampa and colleagues (2011)](http://journal.frontiersin.org/article/10.3389/fncir.2011.00018/full). In the present work, I find that V1 is not only great at natural image identity task but it does a much better job that other visual areas. This suggests that V1 neurons might be encoding features that linearly discriminate individual natural images.

There are several possible explanations for the V1 advantage. V1 is a low-level feature detector i.e. neurons in V1 respond well to low level features such as contrast, edges, luminance, and orientation. Although the experimenters accounted for low-level features such as contrast and luminance during stimulus presentation, V1 could still be sensitive to other low-level features such as edges or spatial frequency.
Other potential explanation for V1's high accuracy in image identification and avenues for future work include:
* More high firing rate neurons in V1, i.e. higher signal to noise. One way to address this is to infer spike events using available [deconvolution methods](https://scholar.google.com/scholar?hl=en&q=deconvolution+calcium+imaging+&btnG=&as_sdt=1%2C5&as_sdtp=) to estimate the overall spike event rates per area. One could then randomly remove spikes and evaluate performance on the natural image identity task.
* Smaller receptive field size of neurons. V1 typically has smaller receptive fields than other higher visual areas. I could verify this with the dataset from the [Locally Sparse Noise stimulus](http://observatory.brain-map.org/visualcoding/stimulus/locally_sparse_noise). This could be fun.
* Incomplete visual information in secondary visual areas. This is an interesting idea that has to do with the possibility that individual mouse secondary visual areas might not have complete coverage of visual space ([Garrett et al 2014](https://www.ncbi.nlm.nih.gov/pmc/articles/pmid/25209296/)), and therefore only sample a portion of the visual scene. This would suggest that combining responses from multiple secondary visual areas could outperform V1. I have not yet tested this idea, but I could run the classifier analysis combining neurons from areas LM and PM or AL and comparing the performance to V1.

The one major caveat is that it has not yet been established that there exists a mouse visual hierarchy, analogous to the way vision scientist think about the primate visual hierarchy. However several parallels do exist between the mouse and primate visual system (for review, see [Glickfeld et al 2014](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4398969/)).

Regarding the categorization task (Figure 4), performance could be improved by creating more image categories. I used a trivial categorization scheme, and there are a number of sophisticated approaches I could use to assign image categories including structured similarity index ([SSIM](https://en.wikipedia.org/wiki/Structural_similarity)), [perceptual distance metric](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3586997/), or neural responses correlation similarity (e.g. [representational dissimilarity metric](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2605405/)).

In these experiments, I assessed the passive decoding of natural image identity i.e.  animals were not actively engaged in a behavioral task. My prediction is that the V1 advantage would disappear under actively-engaged task conditions.
[Spencer Smith's lab](http://www.slslab.org/) recently showed that [mice can, behaviorally, discriminate natural images](http://www.slslab.org/nid/2016/08/11/introduction-to-this-project/). It would be interesting to compare how well each area performs on natural image identity and categorization when the animal is engaged in a behavioral task.   

#Resources
* Myers & Kreiman - [Tutorial on Pattern Classification](http://klab.tch.harvard.edu/publications/PDFs/8404_019.pdf) - Chapter 19 of Visual Population Codes. This was a helpful tutorial on performing classifier analyses on neural data.

##Acknowledgements
I would like to thank Sashank Pisupati, Arka Banerjee, Matt Kaufman, and Anne Churchland for helpful discussions and suggestions. And also thanks to the [Allen Institute](alleninstitute.org) for acquiring and curating this awesome dataset.
