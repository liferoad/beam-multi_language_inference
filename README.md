<!--
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# Test for multi-lang Python RunInference

Followed [this example](https://github.com/apache/beam/tree/master/sdks/python/apache_beam/examples/inference/multi_language_inference).

Run this example using a Linux machine.

Use Beam `2.46.0` because the current Python code does not work due to the `transformers` package.

## Set up Python virtual environment

```bash
# install torch and transformer
# python 3.10
pip install transformers==4.26.0 torch==1.13.1 "apache-beam[gcp]==2.46.0"

# create tar gz file
cd multi_language_custom_transform
python setup.py sdist
```

## Running the Java pipeline

Use your own information below. You should change `pom.xml` when switching Beam.

You need to get the model and `imdb_reviews.csv` to run this example.

```bash

# Java 11

export GCP_PROJECT=apache-beam-testing
export GCP_BUCKET=apache-beam-x-lang-testing
export GCP_REGION=us-central1
export MODEL_NAME=bert-base-uncased
export LOCAL_PACKAGE=/usr/local/google/home/xqhu/playground/beam-multi_language_inference/multi_language_custom_transform/dist/multi-language-custom-transform-0.1.tar.gz

cd last_word_prediction
mvn compile exec:java -Dexec.mainClass=org.apache.beam.examples.MultiLangRunInference \
    -Dexec.args="--runner=DataflowRunner \
                 --project=$GCP_PROJECT\
                 --region=$GCP_REGION \
                 --gcpTempLocation=gs://$GCP_BUCKET/temp/ \
                 --inputFile=gs://$GCP_BUCKET/input/imdb_reviews.csv \
                 --outputFile=gs://$GCP_BUCKET/output/ouput.txt \
                 --modelPath=gs://$GCP_BUCKET/input/bert-model/bert-base-uncased.pth \
                 --modelName=$MODEL_NAME \
                 --localPackage=$LOCAL_PACKAGE" \
    -Pdataflow-runner
```
