## NOISE IN dMRI


#### PCA-based denoising

###### 1. MPPCA extracts n_gradients PCs and then do the selection on them. How is it done in NORDIC? Is the patch_radius the same in MPPCA and NORDIC? Should it be? Could this be “biasing” our comparisons if not? How do they decide the patch size? In MPPCA, the recommendation is to go for radius=2 (but I don’t know why)?

###### 2. We keep the larger eigenvalues because the higher variability should correspond to the signal. Can we assume this as true even in very low SNR scenarios? I.e., Why can’t be one of the biggest singular values related to noise (e.g. any property of the noise)?

###### 3. Assuming we are not having any structure in the residuals (either in MPPCA or Patch2self)…what distribution should follow the residuals maps? white noise or actually a rician / non-central chi distribution?
FROM DIPY: should follow the original dist…if rician, rician, if non-chi, then non-chi


###### 4. “We want to know at which resolution we are after denoising”…how can we check this quantitatively? In the fMRI paper they compare t-stats and said NORDIC didn’t induce blurring but why? how did they check it? Is not working with the covariance of the patch something “similar” to a spatial gaussian filter? In fact, they said it is identical to the averaged ones, so does not introduce blurring (Supp.Fig.10)! DIPY workshop: spatial smoothing is checking if the residuals present any structure (like in NLMeans). How to check whether “phase stabilization is smoothing or affecting the SNR at all”?

###### 5. Do MPPCA and/or NORDIC anything with the localization of the eigenvectors and the sparsity induced of data? (requirements of the RMT)

###### 6. In cases where the signal is really low and noise causes rectification, how does NORDIC and MPPCA teat these voxels?

###### 7. Is there a way to "hack" NORDIC and make it work for cases where only magnitude SENSE1 data are present? E.g. UK Biobank? If we assign the magnitude to the real channel and zero-mean noise to the imaginary, will it work? Could this be applied retrospectively to UK Biobank?

###### 7. does it make sense to model the noise floor (`--f0 --ardf0`), specially for the offline data?


###### 8. Why cannot we assume Gaussian noise (for SNR>3) instead of rician? Is the SNR and CNR we have relatively low?

###### 9. Jesper said “I would definitely not think it unlikely that they can add some real and useful information by having access to the phase” so…What extra information do they extract from the complex image? Why do they keep the phase? 

###### 10. Using complex data + phase-stabilization —> Without phase-stabilization, the diffusion phase has a high frequency fluctuation along the slice direction. This limits the efficacy of the LLR representation model, not allowing for an effectively low-rank representation.

###### 11. If it’s diffusion, are we working in k-space or q-space? Why have I read k-space in multiple places? Reconstruction of the images is the step from k-space to the actual image we processes, right? What’s the difference between doing it online and offline? The DICOM image is in k-space and the nifti in image space? spatial resolution (sampling k-space) or a higher angular resolution (sampling q-space angles)

###### 12. What else should we expect from NORDIC than from MPPCA? As far as I understood, it is the same approach but they correct the algorithm to take into account for finite-sized matrices and some statistical assumptions from the RMT theory, but the MPPCA paper said was OK in practice. Why such difference?

###### 13. Can we always assume additive noise? And what about statistical dependencies? Is the spatially varying property of noise because of the SENSE1 reconstruction with multi-channel?

###### 14. Why the noise is i.i.d. or stop being it?
  
###### 15. How is the SNR calculated in EddyQC? With the 2-regions approaches? It may be biased! (Dietrich et al. 2007).

###### 16. Jesper is less skeptical about k-space filtering than image-based ones. Why? What k-space filter methods there are? “The image based ones attempt to disentangle noise and signal with no more information at hand than that available to for example model-based or model-free fitting of the data. So I struggle to see how that can provide anything meaningful.
    - I think they are the Hanning, elliptical, etc. filters (see slides)

###### 17. Why local low-rank approximations instead of low-rank approximations of the whole matrices (volumes or whatever)? Because of the spatially varying noise? (The paper of the LLRMA of Google said that with local approximations the perform is better).


To work with the MP law, the noise level constant amongs all elements of X must be constant. Can we check this in the residual maps and in our data? 

if n_gradients is too big, the PCA methods become ill-conditioned…WHY??
if n_gradients is too low (~30), it is very hard to estimate the correct rank of the local SVD is quite hard and you will not get good denoising (because M~npoints).

Global smoothness of images used for the fMRI time series for Standard (red) and NORDIC (blue), before (left panel) and after preprocessing related interpolations.
https://www.biorxiv.org/content/10.1101/2020.11.04.368357v3.full.pdf


<!-- #### P2S -->
<!--  -->
<!-- ###### 1. “PCA-based methods place the assumptions on the signal, which can be  --><!-- really hard to model in fact. In patch2self, we place it in the noise: it is  --><!-- safe to assume that thermal noise in one volume is independent of the noise in  --><!-- the next and previous volume.” Is not the MP law and so on actually making the  --><!-- assumption on a Random Matrix (i.e. in the noise)? In fact, making assumptions  --><!-- for the noise model is not the same than making it in the data (or this only  --><!-- applies for Gaussian noise because x+N~N)? -->
<!--  -->
<!-- ###### 1. If the noise is independent, how can we actually predict it? Is it not  --><!-- making a kind of“imputation”? -->
<!--  -->
<!-- ###### 1. Why using ALL-BUT-ONE volumes instead of a typical 80-20 splitting? Is  --><!-- there any control/risk for overfitting (especially for long acquisition datasets  --><!-- like the HCP)? Have they tried k-fold and checked whether the error were  --><!-- consistent? Actually, they said “there is no minimum n_vols for patch2self in  --><!-- contrast to MPPCA (>30)” so…why not use less volumes to make it faster? -->
<!--  -->
<!-- ###### 1. They don’t use patches (radius=0 is the recommended)…shall we observe  --><!-- any covariance then if we analyse the residuals of neighbor voxels? -->
<!--  -->
<!-- ###### 1. They said it makes sense to apply PCA-based + patch2self. If yes…it  --><!-- would not matter which one use first, right? -->
<!--  -->
<!-- ###### 1. At then end, patch2self is a linear regression. Residuals of linear  --><!-- regression have a series of assumptions (same variance, etc.). Could we extract  --><!-- any info from here? -->
<!--  -->
<!--  -->
<!-- ###### 1. Jesper Andersson commented 2 interesting things: -->
<!-- 	1. After the training they then go on to make the predictions that form the  --><!-- basis for their denoised data. Now, I might have misunderstood this because  --><!-- it sounds so strange, but this is how I understand it. They again use the  --><!-- data from all volumes except one to make the prediction for the left out  --><!-- one. Which would mean that when you predict volume j, you don't actually use  --><!-- any of the information in volume j. I hope I have misunderstood it. -->
<!-- 	2. “Anyhoo, my strong prediction here is that similarly to replacing all the  --><!-- data with "model" predictions from eddy or SHARD, this will make any  --><!-- inference on the data (such as number of fibres) difficult or impossible.” —  --><!-- WHY??? -->
<!--  -->
<!-- ###### 1. I’m not sure about this (nor if it was covered in the lecture): if you  --><!-- are effectively fitting a regression model with patch2self, are you assuming  --><!-- some statistical properties in the residuals ()? If yes, would it make sense to  --><!-- apply it to complex data?  -->
<!-- - They don’t have support for complex data yet, but it should be fine applying  --><!-- it to magnitude, they don’t assume anything (but as previous question, they  --><!-- haven’t checked this) -->
<!--  -->
<!--  -->
<!-- ###### 1. Finally, is Patch2self modifying the distribution of data? I mean, in  --><!-- some pre-processing and modelling steps you are assuming a noise model (e.g.  --><!-- gaussian in the ball&sticks). Is this modifying these properties? Can we expect  --><!-- any type of conflict or something we should take care of? — MPPCA affects  --><!-- because includes correlated noise and EDDY can fail. Patch2self does not in  --><!-- theory. -->
<!--  -->
<!--  -->
<!-- ###### 1. How have you validated or controlled the possible spatial smoothing  --><!-- induced during denoising with Patch2self?  -->
<!-- - Checking the residuals qualitatively (Abs(residuals) should not have any edge  --><!-- or structure) and they didnt see any structure (as you could see in non-local  --><!-- means). -->