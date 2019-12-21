# noswear2 rapid prototype

Uses mycroft-precise as backend for its speech recognition.

Main challenges:
- gather data
- build dataset
- augment training data
- train model (using precise)
- tune model

1. gather data: see [deepestcyber/speech](https://github.com/deepestcyber/speech)
   for ways to crawl speech data

2. build dataset: datasets are currently built using [a notebook](/notebooks/build_datasets.ipynb)

3. augment training data: **TODO**; the idea is to create noisy samples from
   the existing ones; precise offers `precise-add-noise`, we can test this

4. train model: **TODO**; (we can use `precise-train` for this)

5. tune model: **TODO**; threshold tuning (`precise-calc-thresholds` and
   parameter tuning (e.g., hidden units))


# Installation

Set up and activate a virtual env:

	virtualenv -ppython3 noswear2
	source noswear2/bin/activate

Install [`mycroft-precise`](https://github.com/MycroftAI/mycroft-precise#source-install):

	git clone https://github.com/mycroftai/mycroft-precise
	cd mycroft-precise
	python setup.py develop

You will need some packages to be installed, see the mycroft installation
instructions for details.

Install noswear2:

	cd noswear2/
	python setup.py develop


# Usage

1. Download data (see "gather data" above)
2. Run `notebooks/build_dataset.ipynb`
3. Run `precise-train`
4. Run `precise-listen`
5. Enjoy (YMMV)

# Adding noise to the datasets

Example noise augmentation of the `fuck_and_non_swear_balanced` dataset:

	precise-add-noise \
		${NOSWEAR2_DATA_ROOT}/datasets/fuck_and_non_swear_balanced \
		${NOSWEAR2_DATA_ROOT}/noswear-noise-samples \
		-if 3 \
		${NOSWEAR2_DATA_ROOT}/datasets/fuck_and_non_swear_balanced_noisy_inflated

`-if` is the inflation factor, i.e how many samples are created from one
sample. Note that the tool will adhere to the train/test split so that there
is no leakage between train and test. Every sample may have a different noise
source and noise ratio (which defaults to a random choice between 0.0 and 0.4).

# precise-listen documentation

Example output of non-basic format:

    XXXXXXXXXXXXXXXXXXXXXxxxxxx---------------------------
    XXXXXXXXXXXXXXXXXXXXXxxx------------------------------
    XXXXXXXXXXXXXXX---------------------------------------
    XXXXXXXXXXXXXXXXXXXXXxxxxxx---------------------------

Meaning:

 - `-` is placeholder to show total line width, e.g. `----` means the model is 0% confident
 - `X` shows model confidence, e.g., 'XX--' means model is 50% confident
 - `x` shows configurable cutoff, e.g., `Xx--' means that model is 50% confident but is already in the confident zone via the sensitivity setting

## Sensitity

The `-s` parameter of `precise-listen` influences the chunk activation as follows:

	chunk_activated = prob > 1.0 - self.sensitivity

Where `prob` is the probability returned by the model. Thus, the higher
the sensitivy, the less the model needs to be sure. The default is 0.5,
which means that the model needs to be 50% confident that the chunk contains
the wake word to trigger the chunk. With `-s 0.9` the model only needs to be
10% confident.

## Trigger level

The trigger level switch `-l` sets the number of consecutive activated
chunks necessary to trigger a detection of the wake-word. Every processed
chunk updates an internal counter that decreases the activations. Once
a chunk is positive, the counter is increased. With `-l N` this needs to
happen `N` times in a row.
