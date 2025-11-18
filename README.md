# mlfs-book
O'Reilly book - Building Machine Learning Systems with a feature store: batch, real-time, and LLMs


## ML System Examples


[Dashboards for Example ML Systems](https://featurestorebook.github.io/mlfs-book/)


# Run Air Quality Tutorial

See [tutorial instructions here](https://docs.google.com/document/d/1YXfM1_rpo1-jM-lYyb1HpbV9EJPN6i1u6h2rhdPduNE/edit?usp=sharing)
    
# Create a conda or virtual environment for your project before you install the requirements
    pip install -r requirements.txt


##  Run pipelines with make commands

    make aq-backfill
    make aq-features
    make aq-train
    make aq-inference
    make aq-clean

or 
    make aq-all



## Feldera


mkdir -p /tmp/c.app.hopsworks.ai
ln -s  /tmp/c.app.hopsworks.ai ~/hopsworks
docker run -p 8080:8080 \
  -v ~/hopsworks:/tmp/c.app.hopsworks.ai \
  --tty --rm -it ghcr.io/feldera/pipeline-manager:latest


## Introduction to ML
I wrote a brief introduction to machine learning [here](./introduction_to_supervised_ml.pdf)


# Lab 1 description

## Introduction
For grade E, we followed the tutorial given for training an air quality predictor model, used it to make predictions for the next week and build a dashboard to display our predictions. Notebook 1 is responsible for downloading the historical data from the air quality sensor of our choice (we chose the one at Sollentuna E4 Häggvik) as a csv file. This data is used as the labels during training. It also downloads historical weather data from Meteo for the relevant dates, and turns them into feature groups on Hopswork. These two contributes our training dataset. Notebook 2 handles the daily features pipeline which downloads one days data at a time, and inserts it into our previously defined feature groups. The training takes place in notebook 3, which outputs the trained XGBoost model and saves it in the model registry on hopsworks. Notebook 4 uses this model to make predictions for the next 7 days, creates a forecast graph for the dashboard. It also plots a hindcast for monitoring purposes. These are published [here](https://kinga-anna.github.io/mlfs-book/air-quality/). Notebook 2, 3 and 4 are run daily by github actions.

## Challenge for Grade C; Lagged air quality
Adding lagged air quality included by adding calculation of lag_mean to the backfill step, in this case using .shift(1) and then .rolling(window=3) to take the mean of the 3 previous rows of pm25. Similar changes were made to feature pipeline to fill in the new data with means from the latest days. 

The training pipeline also required minor changes and only included adding pm25_lag_mean as a feature for the model.

The inference however required some more changes as inference is now done sequentially as the prediction of the day after tomorrow is dependent on the predicted value of tomorrow (and so on).
