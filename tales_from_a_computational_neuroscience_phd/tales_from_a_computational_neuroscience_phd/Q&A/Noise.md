# QUESTIONS ABOUT NOISE

## whw
## 123
### gdsg

## PCA-based denoising

MPPCA extracts n_gradients PCs and then do the selection on them. How is it done in NORDIC?
Is the patch_radius the same in MPPCA and NORDIC? Should it be? Could this be “biasing” our comparisons if not?
How do they decide the patch size? In MPPCA, the recommendation is to go for radius=2 (but I don’t know why)?

We keep the larger eigenvalues because the higher variability should correspond to the signal…Can we assume this as true even in very low SNR scenarios? I.e., Why can’t be one of the biggest singular values related to noise (e.g. any property of the noise)?

If we plot the distribution of residuals in MPPCA and NORDIC, what distribution should they follow? Rician? Gaussian?

Can we get access to the NORDIC code? Can be useful to apply to make some comparisons (e.g. compare signal PCs from one and another, number of PCs selected, etc.)?

“We want to know at which resolution we are after denoising”…how can we check this quantitatively?
In the fMRI paper they compare t-stats and said NORDIC didn’t induce blurring but why? how did they check it? Is not working with the covariance of the patch something “similar” to a spatial gaussian filter? In fact, they said it is identical to the averaged ones, so does not introduce blurring (Supp.Fig.10)!
DIPY workshop: spatial smoothing is checking if the residuals present any structure (like in NLMeans).
How to check whether “phase stabilization is smoothing or affecting the SNR at all”?

Do MPPCA and/or NORDIC anything with the localization of the eigenvectors and the sparsity induced of data? (requirements of the RMT)

Shall we include comparisons to averaging?



To work with the MP law, the noise level constant amongs all elements of X must be constant. Can we check this in the residual maps and in our data? 

if n_gradients is too big, the PCA methods become ill-conditioned…WHY??
if n_gradients is too low (~30), it is very hard to estimate the correct rank of the local SVD is quite hard and you will not get good denoising (because M~npoints).

Global smoothness of images used for the fMRI time series for Standard (red) and NORDIC (blue), before (left panel) and after preprocessing related interpolations.
https://www.biorxiv.org/content/10.1101/2020.11.04.368357v3.full.pdf

## P2S
“PCA-based methods place the assumptions on the signal, which can be really hard to model in fact. In patch2self, we place it in the noise: it is safe to assume that thermal noise in one volume is independent of the noise in the next and previous volume.”
Is not the MP law and so on actually making the assumption on a Random Matrix (i.e. in the noise)?
In fact, making assumptions for the noise model is not the same than making it in the data (or this only applies for Gaussian noise because x+N~N)?

If the noise is independent, how can we actually predict it? Is it not making a kind of“imputation”?

Why using ALL-BUT-ONE volumes instead of a typical 80-20 splitting? Is there any control/risk for overfitting (especially for long acquisition datasets like the HCP)?
Have they tried k-fold and checked whether the error were consistent?
Actually, they said “there is no minimum n_vols for patch2self in contrast to MPPCA (>30)” so…why not use less volumes to make it faster?

They don’t use patches (radius=0 is the recommended)…shall we observe any covariance then if we analyse the residuals of neighbor voxels?

 They said it makes sense to apply PCA-based + patch2self. If yes…it would not matter which one use first, right?

- At then end, patch2self is a linear regression. Residuals of linear regression have a series of assumptions (same variance, etc.). Could we extract any info from here?