## General questions about dMRI modeling



- What’s the difference between ODF, diffusion ODF and fibre ODF?

- Can we say all dMRI modeling approaches are model-based and that they use deconvolution? What's their difference then?


- What are the response functions?
  They are typically delta functions.

- Is there any evidence of differences of the response function between different regions of the brain in a healthy subject (or dependences with other parameters like density of fibres in a region or something like that)?
I'm not aware of any papers showing spatial variation of the WM response function, but there are papers claiming that the characteristic axon diameter varies with location. That suggests the response function probably should vary, but I don't know how to go from axon diameter to response function, or how to measure the response function outside the corpus callosum (Etienne St-Onge).

    Hidden away in Chantal Tax’s thesis (chapter 6) she shows that there is indeed a spatial variation of the response function. It varies across pathways more than across subjects (https://www.narcis.nl/publication/RecordID/oai:dspace.library.uu.nl:1874%2F337377).

    Tournier has a paper suggesting that this kernel is not accurate for the whole brain but it is really

    Sorry for long answers: Also my favorite work From Oxford (https://pubmed.ncbi.nlm.nih.gov/31315062/) using histology! The kernel seems varies across the brain. Butttt it may not matter (https://www.sciencedirect.com/science/article/pii/S1053811919310511) - this is the MRTrix paper Etienne refers to. A single kernel is probably adequate

    Interesting view on response function (Ex vivo validation, good stuff):
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6525086

- When to use model-based and model-free? What are their main differences in terms of tractography performance?



- Why is the difficulty of dealing with multiple tissues (GM,WM, CSF) appearing 
only in multi-shell acquisition and not in single shell?
  The difficulty is there as well for single-shell deconv. But single-shell 
deconv only gives WM fODFs, it cannot produce GM and CSF fODFs as it lacks the 
multi b-values to do so properly.

- What's the difference between CSD and BedpostX (Ball&Sticks)?
  In terms of comparison with bedpostx, hard to say for sure, as there is a lack of comparative papers. For me, they produce similar results but the output and methods are conceptually quite different. I recommend you read our review paper: Jeurissen et al NMR Biomed 2017 where we discuss this point - M. Descoteaux.

- What's the point in using a deterministic tractography approach rather than a probabilistic one that can give us a measure of uncertainty?
  Deterministic tractography is useful if the data dMRI quality is low (e.g .very few measurements, low SNR). It is also useful for visualization/quality check, and for analysis focusing on the main trajectory of bundles. Also, some researchers prefer to have less variability, and may prefer to have less False positive at te cost of more False negative. So its a sensitivity specificity trade-off. However this also affect post-processing (filtering, segmentation etc).


- What about global tractography approaches (rather than local propagation of streamlines)?
    Global approaches are very interesting, but more complex (to implement, to use and to understand the parameters), however the still have similar "local" limitation as step-wise tracking, and are a lot more computationaly intensive. Nevertheless, they have a huge potential for future development. They work very well in control environments, but on real data, they often lack surface coverage or miss part of bundles, or small bundles. Semi-global approaches are certainly among the best method for whole-brain tractography estimation (e.g. COMMIT/SIFT2 where streamlines method are used to estimate the trajectories, then streamlines are weighted/pruned following a microstructure model).

- Is there any evidence of asymmetry in the structural connectivity, i.e. that from A to B is different than from B to A (e.g., diffusivity is not the same in +x and -x)?
  Local diffusion is symetric, however starting from A and reach B might be "easier" from the reverse (B->A) because of crossing and other local complexity of FOD. This is amplified when using interface / surface seeding (and stepwise tractography). It is important to note that this is an effect of pathway/trajectory complexity and configurations over tractography, not brain connectivity assymetry. Most people tend to afterward symmetrize the matrix, or do as I mentionned in my presentation A-B = B-A (count for both).
  You can construct a synthetic configuration where the number of streamline between A-B is different depending on the seeding location. This is a bias of the seeding, and a bais of tracking, preferring some configurations over other (e.g. straight bundles).