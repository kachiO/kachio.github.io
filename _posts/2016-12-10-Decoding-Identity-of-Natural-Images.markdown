---
layout: post
title: "Decoding Natural Image Identity From Mouse Visual Cortex"
comments: true
date: 2016-12-10
excerpt: "Data project using Allen Institute Brain Observatory. The goal was to find out whether neurons in mouse visual cortical areas could linearly readout (i.e. discriminate) the identity of natural images."
mathjax: true
---

<h1>Intro</h1>

The [Allen Institute for Brain Science](https://www.alleninstitute.org/) recently released a [rich dataset](http://observatory.brain-map.org/visualcoding) containing neural responses from many visual brain areas and cortical layers. Here is a nice [video](https://www.youtube.com/watch?v=fSaOCDmRs1Q) introducing the [Brain Observatory project](http://observatory.brain-map.org/visualcoding).This unprecedented dataset is an important resource and testbed for generating hypotheses about visual cortical function.

Mice are relatively [new players to the visual neuroscience game](http://www.nature.com/news/neuroscience-through-the-eyes-of-a-mouse-1.13901). Mice have a number of secondary visual areas which are not only retinotopic, but also have preferences for spatial and temporal frequency of grating stimuli. Beyond that very little is know about what kind of information can be readout from neural populations within mouse secondary visual areas. Linear readout (i.e. linear combinations) of neurons is an implicit assumption in systems neuroscience about how downstream brain areas *could* make use the information represented in the neural responses of upstream brain areas to generate behavior.

I set out on a data mission to investigate whether neurons in mouse visual cortex could linearly readout (i.e. discriminate) the identity of natural images.

[IPython Notebooks for this project](https://github.com/kachiO/ipython-notebooks/tree/master/mouse%20natural%20images%20identity)

<h1>Methods & Data</h1>
Mice were presented with a total of 118 natural scenes. Each scene was presented for 250mss and randomly repeated 50 times, one immediately after the other with no blank screen in between. (I thought this was a bit unusual that they would present the scenes one right after the other without a blank screen in between. Maybe they were worried about stimulus onset transients?) A blank screen, however, was presented after ever 100 scenes. More details about the experimental procedures are discussed in the [technical whitepaper](http://help.brain-map.org/display/observatory/Documentation).

I had to do a bit of data wrangling to extract and save the responses from different brain areas, cell types, and scenes ([see Download Dataset IPython Notebook](https://github.com/kachiO/ipython-notebooks/blob/master/mouse%20natural%20images%20identity/download%20multiple%20datasets.ipynb)). I created a matrix, _XT_, of size _n_ x _c_ x _t_  where n = number of trials, c = number of cells in a visual area (or cell type), and t = time (or frames).This matrix was created for each visual area. The number of cells per area were ranged between 2157 (PM) to 4189 (V1) (see table below). There were 5900 trials total (118 images, 50 repeats each). Blank stimulus trials were excluded. The total number of frames included per cell was 21, which included 7 frames pre-stimulus, 7 frames during stimulus, and 7 frames post-stimulus. Approximately 7 imaging frames were acquired per 250ms. The analyses in this project were done using the relative changes in fluorescence (delta F/F) as the neuronal response instead of spikes.

<style>
    table {
        border-collapse: collapse;
        width: 60%;
    }
    th, td {
        border: 2px solid black;
        padding: 20px;
    }
</style>
        
<center><table>
    <tr>
        <th>PM</th>
        <th>AL</th>
        <th>LM</th>
        <th>V1</th>
    </tr>
    <tr>
        <td>2157</td>
        <td>2323</td>
        <td>2606</td>
        <td>4189</td>
    </tr>
</table></center>

To measure performance on identifying a natural image, I trained a linear support vector classifier ([one-vs-rest classifier scheme](https://en.wikipedia.org/wiki/Multiclass_classification#One-vs.-rest)) on the average response during 200-300ms post stimulus, and computed cross-validated test accuracy for the trained classifier for each area. The classifier was trained on 70% of the trials in an area, and tested on the remaining 30%. In most cases I repeated this procedure 10 times to obtain an average and computed the standard error of the mean (SEM). [see Natural Image Identity Classification IPython Notebook](https://github.com/kachiO/ipython-notebooks/blob/master/mouse%20natural%20images%20identity/Natural%20Image%20Identity%20Discrimination.ipynb)

<h1>Results</h1>

<h2>Primary Visual Cortex (V1) Achieved Highest Performance on Natural Image Identity Task</h2>
Although all the visual areas performed well above chance (0.85%), V1 exhibited the highest performance (>75%) in linearly classifying the natural images, followed by area LM (>50%). Areas PM and LM performed similarly (> 30%) to each other.

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/Decoder_accuracy_all_neurons.png" height="400" title="Natural Image Identity Performance for Each Area, all neurons included"><figcaption>Figure 1a. Natural Image Identity Performance for Each Area, all neurons neurons</figcaption>
</figure>
</center>

You might have noticed from Table 1 that each area has a different number of neurons (features), and that the best performing area V1 has the most number of neurons. In other words V1 might have outperformed other areas because it had more neurons. However, when I repeat the classifier using 2100 neurons (features) per area, V1 still outperformed all the other areas.

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/Decoder_accuracy_2100_neurons.png" height="400" title="Natural Image Identity Performance for Each Area, 2100 neurons included"><figcaption>Figure 1b. Natural Image Identity Performance for Each Area, 2100 neurons</figcaption>
</figure>
</center>

<h2>Natural Image Identity Performance Increases With Population Size for All Visual Areas</h2>
As you might have noticed from the above figures, the performance of the classifier depends on the size of the population of neurons in a given area. This is illustrated more clearly in the figure below.

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/accuracy_per_population_size.png" height="400" title="Performance per Population Size"><figcaption>Fig.2 Decoding Test Accuracy Increases with Population Size</figcaption>
</figure>
</center>

Another way to evaluate and compare each classifier's performance is to assess the [mutual information](https://en.wikipedia.org/wiki/Mutual_information) between the classifier's predicted class label and the true class label. To calculate mutual information I computed a confusion matrix, which reflects the proportion of times the classifier guessed the true class label i.e. the conditional probability (<em>P(predicted|true)</em>) of the classifier predicted label given the true label. [see Confusion Matrix and Mutual Information IPython Notebook](https://github.com/kachiO/ipython-notebooks/blob/master/mouse%20natural%20images%20identity/confusion%20matrix%20and%20mutual%20information.ipynb)

From the normalized confusion matrix, you will notice that there is a close agreement between each area classifier's predicted class label (i.e. image identity) and the true label. The image labels (1-118) is the same for each matrix. Next time around, I would like to make an interactive plot that let's you visualize which images an area classifier predicts well.

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/mutual_info.png" height="400" title="Confusion Matrix and Mutual information"><figcaption>Fig.3 Confusion matrix and Mutual Information for Each Visual Area</figcaption>
</figure>
</center>

<h2>Natural Image Identity Performance Accuracy Across Time </h2>
The next question I asked was whether different visual areas might achieve higher accuracy at different times from the stimulus onset. I found three ways to approach this question.

The first approach (a) To evaluate the performance of the classifier across time, I trained the decoder using the mean response from 200-300ms post-stimulus and tested the classification accuracy using the activity at each time point (or frame). The second approach (b) was to take a single frame, approximately 200ms post-stimulus, to train a classifier and compute the cross-validated test accuracy for each frame. This worked surprisingly well, i.e. that the neural activity within a single frame (approximately 30ms) achieved high prediction accuracy, comparable to using the average activity between a larger time window of 100ms. 

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/accuracy_across_time_all2.png" height="380" title="Performance across time"><figcaption>Fig.4 Decoding Test Accuracy Across Time (a) trained with average response frames between 200-300ms (b) trained with single frame at approximately 200ms post stimulus onset</figcaption>
</figure>
</center>

<h2>Natural Image Categorization</h2>
Given that neural responses in these areas can be used to perform natural image identity task. What if I, instead, now asked the visual areas to categorize the natural images into two groups. My prediction was that putative higher visual areas, such as PM or AL, would perform much better than V1. I devised two simple categories, animals vs. non-animals ([raw data](https://github.com/kachiO/ipython-notebooks/blob/master/mouse%20natural%20images%20identity/naturalscene%20content.xlsx)). All the areas performed above chance (50%) and the categorization performance was similar for all areas including V1.[see Natural Image Categorization IPython Notebook](https://github.com/kachiO/ipython-notebooks/blob/master/mouse%20natural%20images%20identity/Natural%20Scene%20Categorization.ipynb)

In a future extension of the project, I could assign image categories using more sophisticated approaches such as structured similarity index ([SSIM](https://en.wikipedia.org/wiki/Structural_similarity)), [perceptual distance metric](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3586997/), or neural responses correlation similarity (e.g. [representational dissimilarity metric](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2605405/)).

<center>
<figure>
    <img src="/assets/blog/natural_image_identity/binary_categorization_accuracy.png" height="400" title="Animal vs Non-animal  Categorization Performance"> <figcaption>Fig.5 Animal vs. Non Animal Categorization Performance</figcaption>
 </figure>
 </center>

<h1>Discussion</h1>

I was surprised to find that V1 outperformed all other visual areas on the natural image identity task, considering V1 is lower in the putative mouse visual hierarchy. The observation that V1 could linearly classify natural images is consistent with a previous study from [Kampa and colleagues (2011)](http://journal.frontiersin.org/article/10.3389/fncir.2011.00018/full). In the present work, I find that V1 is not only great at natural image identity task but it does a much better job that other visual areas. This suggests that V1 neurons might be encoding features that linearly discriminate individual natural images. 

There are several possible explanations for the V1 advantage. V1 is a low-level feature detector i.e. neurons in V1 respond well to low level features such as contrast, edges, luminance, and orientation. Although the experimenters accounted for low-level features such as contrast and luminance during stimulus presentation, V1 could still be sensitive to other low-level features such as edges or spatial frequency. The V1 neurons could be more active, i.e. higher signal to noise. One way to address this is to infer spike events using available <a href="https://scholar.google.com/scholar?hl=en&q=deconvolution+calcium+imaging+&btnG=&as_sdt=1%2C5&as_sdtp=">deconvolution methods</a> to estimate the overall spike event rates per area. One could then randomly remove spikes and evaluate performance on the   natural image identity task. V1 typically have smaller receptive fields than other higher visual areas, and therefore could cover more features of the visual scene. I could verify this with the dataset from the [Locally Sparse Noise stimulus](http://observatory.brain-map.org/visualcoding/stimulus/locally_sparse_noise). Related to the potentially smaller receptive field size in V1, the secondary visual areas might have incomplete coverage of visual space ([Garrett et al 2014](https://www.ncbi.nlm.nih.gov/pmc/articles/pmid/25209296/)) . This is an interesting idea to contemplate. If individual mouse secondary visual areas could only sample a portion of the visual scene, mutliple secondary visual areas might combine resources to gain full access to the visual scene and potentially outperform V1. I would be interested to test out this idea. Perhaps a first step would be to run the classifier analysis combining neurons from areas LM and PM or AL and comparing the combined multi-area performance to that of V1.

One major caveat is that it has not yet been established that there exists a mouse visual hierarchy, analogous to the way vision scientist think about the primate visual hierarchy. However several parallels do exist between the mouse and primate visual system (for review, see [Glickfeld et al 2014](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4398969/)).

In these experiments, I assessed the passive decoding of natural image identity i.e.  animals were not actively engaged in a behavioral task. My prediction is that the V1 advantage would disappear under actively-engaged task conditions.
[Spencer Smith's lab](http://www.slslab.org/) recently showed that [mice can, behaviorally, discriminate natural images](http://www.slslab.org/nid/2016/08/11/introduction-to-this-project/). It would be interesting to compare how well each area performs on natural image identity and categorization when the animal is engaged in a behavioral task. 

<h2>Resources</h2>
Myers & Kreiman - [Tutorial on Pattern Classification](http://klab.tch.harvard.edu/publications/PDFs/8404_019.pdf) - Chapter 19 of Visual Population Codes. This was a helpful tutorial on performing classifier analyses on neural data.

<h2>Thanks to:</h2>
Sashank Pisupati, Arka Banerjee, Matt Kaufman, and Anne Churchland for helpful discussions and suggestions. And also to the [Allen Institute](alleninstitute.org) for acquiring and curating this awesome dataset.


