## What is this Machine Learning thing anyway?

By Geraldine_VdAuwera

<p>Machine learning or ML is one of the hottest buzzwords (buzzphrases?) in genomics today, along with data science, artificial intelligence (AI) and deep learning (DL). And as with all good buzzwords, it's very unfashionable to admit that you don't know exactly what they mean. So here's an intro-level overview of these terms and where they fit in the GATK world. If after reading this you find yourself craving more substance about the exciting new ML methods being developed in GATK4, don't despair -- we plan to follow up next week with a more detailed post written by Lee Lichtenstein, GATK's leader of somatic computational method development and all-around data science nerd.</p>

<hr></hr><blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">"Data Science is statistics on a Mac." <br>
  -- <a rel="nofollow" href="https://twitter.com/bigdataborat/status/372350993255518208">@BigDataBorat</a></p>
</div></blockquote>

<p>At a high level, data science is the overall discipline that deals among other things with building models in order to make statements and predictions about the data and what it represents. Within that context, machine learning and statistics can be seen as two subfields of data science, utilizing similar tools but with different goals and strategies, as explained in <a rel="nofollow" href="https://www.svds.com/machine-learning-vs-statistics/">this article</a>. One of the key differentiators is that statisticians focus more on the theoretical underpinnings of the modeling process, while practitioners of machine learning put more emphasis on "making it work", <em>i.e.</em> generating predictions that fit their training data, as well as ensuring computational efficiency and scalability to very large datasets.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">I’ve decided that I’m cool with describing today’s tech as Artificially Intelligent, provided we all agree that we’re talking about ‘intelligence,’ like bunnies have for finding a way around my garden fence to eat my kale.<br>
  ‘Clever bunny’ levels of intelligence.  That’s 2018.<br>
  -- Chris Dwan <a rel="nofollow" href="https://twitter.com/fdmts/status/993890659936407552">@fdmts</a></p>
</div></blockquote>

<p>So where do artificial intelligence and deep learning fit in? In a nutshell, artificial intelligence, <em>i.e.</em> the ability of machines to make smart decisions without being given step-by-step instructions from a human, is the end goal of all machine learning. Meanwhile, deep learning is a subfield of machine learning that uses techniques based on "neural nets", a type of algorithm that mimics neural pathways in animal brains. Deep learning has been around for a long time, but until recently, neural nets were too computationally intensive to tackle anything more than toy problems. Now, thanks to recent technological developments they can tackle much bigger problems, and have become intensely popular as a way to pursue artificial intelligence.</p>

<hr></hr><h3>Machine learning in the GATK</h3>

<p>Now let's have a look at how these terms apply to GATK methods. Our analytical tools all make use of some kind of statistical technique or combination of techniques; I dare say if you take a stroll through our documentation, you're very likely to come across some Bayesian formulas. For a subset of our tools, the key algorithms belong to the machine learning family. There is sometimes a bit of debate around what exactly pushes a particular technique over the fence into ML territory (try googling "is pca machine learning") but my rule of thumb is that if I get a real whopper of a headache the first time I try to understand it, it's probably machine learning.</p>

<p>Classic GATK machine learning methods that have been around since the early days of GATK include base recalibration (BQSR) and variant recalibration (VQSR). In the case of VQSR, the core algorithm is a Gaussian mixture model that aims to classify variants based on how their annotation values cluster, given a training set of high-confidence variants. It has long been the method of choice for variant filtering in our Best Practices pipeline for germline short variant discovery, despite many shortcomings (including easily violated assumptions, unhelpful error messages and an insatiable appetite for more data) but after exploring various alternatives over the years, we have finally nailed down a new approach based on deep learning that we expect will replace VQSR in our Best Practices pipeline within the coming months.</p>

<p>This new deep learning based approach uses two-dimensional convolutional neural nets (2D CNN) to classify variant candidates coming out of the variant calling pipeline, with the intent of making it a drop-in replacement for VQSR. The tools involved are still in beta-stage development (publicly accessible in GATK4 but not yet "blessed" for production use), but in our tests the new method outperforms VQSR significantly, delivering greater precision without reducing sensitivity. Specifically, for indel calling on a single genome, the 2D CNN approach improves accuracy up to ~30% over VQSR. This huge effect on single-sample indel calling is great news because while germline SNPs are largely a solved problem, indels have long remained a thorn in everyone's side due to the prevalence of technical artifacts that are difficult to filter out without losing real indels. The ability to retain sensitivity while eliminating these artifacts represents an important improvement for anyone who needs to prioritize variants of possible clinical importance. So we really look forward to bringing this approach to maturity in the near future.</p>

<p>That being said, new advances in ML-based approaches in genomics are not limited to deep learning. We have a brand new germline copy number variant discovery (gCNV) pipeline in GATK4, currently in beta status, that uses a type of machine learning algorithm called a Probabilistic Graphical Model (PGM) to deliver germline CNV calling on either a single sample or a cohort of samples that significantly outperforms established methods in the field, both in terms of accuracy and of computational scalability.</p>

<blockquote class="UserQuote blockquote"><div class="blockquote-content">
  <p class="blockquote-line">"Data Scientist (n.): Person who is better at statistics than any software engineer and better at software engineering than any statistician" <br>
  -- Josh Wills <a rel="nofollow" href="https://twitter.com/josh_wills/status/198093512149958656">@josh_wills</a></p>
</div></blockquote>

<p>As we build out the portfolio of GATK4 tools and pipelines, we continually re-evaluate our approaches based on the research needs that we're responsible for satisfying as well as the latest advances in the field of data science, in order to select the right tooling to tackle each problem. That takes a lot of exploratory research, and the best asset we have to help us in that endeavor is the amazing diversity of the GATK developers' professional backgrounds. Supported by a small pocket of "traditional" software engineers who build out the GATK's engine core, the GATK development team is a conglomeration of~30 data scientists assembled from all corners of data science. A few were already in the domain of biological sciences, though rarely genomics; but most came from other disciplines, including high-energy physics, astronomy, mathematics and digital art. This produces a team dynamic that values fresh perspectives and cultivates the kind of novel thinking that we need to keep pushing the boundaries of genomic science.</p>

<p>Finally, if I've learned anything about ML from watching my GATK colleagues work their magic, it's that it's one thing to develop an algorithm that produces the answer you want, and quite another to make it usable by others. Most of the new machine learning work we are doing in GATK4 relies on open-source libraries that are widely used in the data science ecosystem, like <a rel="nofollow" href="https://github.com/pymc-devs/pymc3">PyMC3/theano</a>, <a rel="nofollow" href="https://keras.io/">Keras/TensorFlow</a> and <a rel="nofollow" href="http://scikit-learn.org/stable/">scikit-learn</a>, which come with all sorts of requirements and dependencies. So a lot of work goes into implementing these algorithms as tools that can be readily deployed at scale in a variety of environments -- not the most glamorous side of data science, but vital to the process of turning cool ideas into impactful technology.</p>