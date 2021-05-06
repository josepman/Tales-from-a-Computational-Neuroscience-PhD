## Constrained Spherical Deconvolution

- Why deconvolution? 


- What is the meaning of convolution in dMRI modeling?


- Is spherical deconvolution a model-free (because you have a response function of the WM)?  
  No. CSD is based in Spherical Harmonics (SH). SH is model free, but when you use it in CSD, CSD is not a model-free because you are using a model for the response function. The FRF is a model.


- Why is it not possible to have a negative component? Would not it mean the same direction?


- Is there any difference between the MRtrix and dipy implementation of CSD or SH?


- Is it fair to say that CSD is better for fiber reconstruction than QBall? Why might one use QBall over CSD, or is the former just an antecedent to CSD?

    "You you would never use qball over CSD for tractography purposes. Never. As mentioned in my talks, qball is i) an antecedent, yes. ii) Used only to have a model-free approach (no assumptions required, no response function required) to examine the content of your DWI signal transformed as diffusion ODF. The qball model is NOT good for tractography. It gives a dODF. Tractography needs a fODF" - Maxime Descoteaux.


-  Is CSD compatible with probabilistic tractography? 
    Yes. We always use CSD with probabilistic tractography.

- When defining the FRF for multiple-tissue multi-shell CSD model, how it should be?
    1. You would have a S0 per tissue (i.e. S0_WM, S0_GM, S0_CSF) which you need to estimate from your data. Having good segmentation tissue masks can help.
    2. You must tune your FRF carefully. GM and CSF FRFs are assumed to be spheres. The b-value will naturally make the spheres smaller or bigger. For the WM FRF, you can put a sphere for the smallest b-value, as even in WM, for low b-values, the FRF looks like a ball (but you may need to tune this for more refined models).
    3. Assume that the diffusivities are ordered with the principal diffusivity first. Then, the response function is positioned along the z-direction and the deconvolution is performed. This is to simply the maths and the use of rotational harmonics.