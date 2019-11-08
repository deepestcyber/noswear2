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

2. build dataset: datasets are currently built using [a notebook](/notebooks/build_dataset.ipynb)

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
