# webMUSHRA

a MUltiple Stimuli with Hidden Reference and Anchor ([MUSHRA](https://en.wikipedia.org/wiki/MUSHRA)) compliant web audio API based experiment software.

<img width="1000" alt="screen shot 2017-03-08 at 12 38 20" src="https://cloud.githubusercontent.com/assets/72940/23702580/2a49bc82-03fc-11e7-99ea-22d550604a73.png">

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
---
- [Introduction](#introduction)
- [Features](#features)
- [External Data](#external-data)
- [Contributions](#contributions)
- [Analog](#analog)
- [Analog Parametric](#analog-parametric)
- [Digital](#digital)
- [Digital Parametric](#digital-parametric)
- [Sources](#sources)
<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

Listening tests are widely used to assess the quality of audio systems. In the last few years, conducting listening experiments over the Internet, as so called web-based experiments, has become popular. Until now, it was only possible to implement a limited number of listening test types as web-based experiments because web standards were missing some crucial features, e.g. sample manipulation of audio streams. MUSHRA tests are designed to compare the audio quality of several test conditions with intermediate impairments to a high quality reference. With the rise of [Web Audio API](http://webaudio.github.io/web-audio-api/), for the first time MUSHRA experiments can be carried out within the web browser while at the same time being compliant to the ITU-R Recommendation BS.1534 (MUSHRA).

##### [View Demo](https://audiolabs.github.io/webMUSHRA)



## Features

* page based experiments supporting:
  * MUSHRA (ITU-R BS.1534)
  * AB (ITU-R BS.1116)
  * Likert scale questionaires
  * training/introduction
  * spatial attributes, such as ASW, LEV, and localization (experimental)
* compliant to ITU recommendations (looping, fade-in/out, sample accurate switching)
* finish page to gather the results and send them to a provided PHP service
* client side processing using the Web Audio API
* simple configuration using YAML preference files
* automatically generates ITU-R compliant lower anchor files on the fly
* keyboard shortcuts for interaction with main UI elements



## Supported Browsers

 * Google Chrome on Windows, Mac and Linux



## Experiment configuration

webMUSHRA uses [YAML](https://en.wikipedia.org/wiki/YAML) to configure experiments. 

A simple MUSHRA test in YAML looks like this:

```yaml
pages:
  - type: mushra
    id: Item 1
    name: Orchestra
    content: Add additional notes for the participants
    showWaveform: true
    reference: reference.wav
    createAnchor35: true
    createAnchor70: true
    stimuli:
      C1: codec1.wav
      C2: codec2.wav
      C3: codec3.wav
```

The specific parameters are described in the [Experimenters Manual](doc/experimenter.md).


## Run Locally using PHP's builtin webserver

1. Download or clone repo:

```bash
cd path/to/parent/folder
git clone https://github.com/mcomunita/webMUSHRA-frontend.git
```

2. Install PHP

MacOS:
```bash
brew update
brew install php
php -v
```

Linux (Ubuntu/Debian):
```bash
sudo apt update
sudo apt install php
php -v
```

1. Start PHP development server

MacOS/Linux:
```bash
cd /path/to/webMUSHRA/folder
php -S localhost:8000
```

4. Access webMUSHRA: open a browser and go to http://localhost:8000 (or other chosen port number).

5. Choose experiment: experiment configurations are stored in the `configs/` folder. To load a configuration/experiment, specify the `/?config=test_config.yaml` argument in the url (e.g., `http://localhost:8000/?config=mushra_nowav.yaml`). If no argument is specified `configs/default.yaml` is loaded.

6. Define new experiment: see `doc/experimenter.md` for documentation on how to configure a `.yaml` file for a new experiment.
7. Save results locally: a PHP script is available in `service/write.php` to save results in the `results` folder. Just make sure to specify `remoteService: service/write.php` in the experiment config file.



## Run Online - Frontend (GitHub Pages) + Backend (Render) + Persistent Storage (Amazon S3)

To run webMUSHRA online it is necessary to:

* Deploy the frontend (i.e., web app) to a web host to run the user interface. GitHub Pages is free and easy to use.
* Deploy the backend (i.e., web server) to a cloud application platform to save the results. Render is free and easy to use. Heroku is also a valid alternative.
* Setup a cloud storage service to have a persistent storage of the results. We make use of Amazon S3. Google Cloud Storage is also a valid alternative.

:warning: Keep both frontend and backend repositories private

### Deploy Frontend to GitHub Pages

1. Download or clone frontend repo:
```bash
cd path/to/parent/folder
git clone https://github.com/mcomunita/webMUSHRA-frontend.git
cd webMUSHRA-frontend
```

2. Create new repo on github, change remote URL and push:
```bash
git remote set-url origin https://github.com/yourusername/webMUSHRA-frontend.git
git push origin master
```

3. Go to the **Settings** tab of your repository on GitHub.
4. In the **Pages** section: set the **Source** to Deploy from a branch, choose the **Branch** to deploy from
5. After a few moments, GitHub Pages will provide a URL like `https://yourusername.github.io/webMUSHRA-frontend/`, where your test will be publicly accessible.


### Deploy Backend to Render

1. Download or clone backend repo:
```
cd path/to/parent/folder
git clone https://github.com/mcomunita/webMUSHRA-backend.git
cd webMUSHRA-backend
```

2. Create new repo on github, change remote URL and push:
```
git remote set-url origin https://github.com/yourusername/webMUSHRA-backend.git
git push origin master
```

3. Sign up on [Render.com](Render.com)
4. Create a new **Web Service**
5. Connect it to your GitHub account
6. Select your backend repository: `yourusername/webMUSHRA-backend`
7. Set the following configuration for your Render deployment:
    * Language: Docker
    * Branch: select branch (e.g., master)
    * Region: choose the nearest or most accessible region for your users
    * Instance Type: Free
8. **Deploy Web Service** and check **Logs** for a successful deployment


### Setup Persistent Storage on Amazon S3 Bucket

**Create an Amazon S3 Bucket**

1. Sign up on [https://aws.amazon.com/](https://aws.amazon.com/)
2. Navigate to **S3 Storage** (or search S3 on the search bar)
3. Click **Create bucket** and provide a unique name for your bucket (e.g., `webmushra-results`).
4. Choose the AWS region closest to your audience.
5. Leave other settings as default, but ensure **Block all public access** is checked for security.
6. Click **Create bucket** to finalize the setup.

**Create IAM User**

1. Go to the **IAM** console in AWS (or search IAM on the search bar)
2. Select **Users**
3. Click **Create user**, enter a username (e.g., `webmushra-storage`) and **Next**
4. Select **Attach policies directly**, search for **AmazonS3FullAccess**, select the policy, click **Next**

**Get Access Keys**

1. Go to the **IAM** console in AWS (or search IAM on the search bar)
2. Select **Users**
3. Click on the user for whom you want to create or retrieve the access keys
4. Go to the **Security credentials** tab and click **Create access key**
5. Select **Other** and click **Next**
6. Click **Create access key**
7. Copy or Download .csv file and store securely **Access key** and **Secret access key**
8. Click **Done**

**Install the AWS SDK for PHP**

1. Install Composer
```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

2. Inside the backend repository directory, run `composer require aws/aws-sdk-php`
3. After installation, ensure the `vendor` directory and `vendor/autoload.php` are present, as you’ll need them to load the AWS SDK.

**Modify Backend script to upload results to S3 Bucket**

1. Open you backend repository: `yourusername/webMUSHRA-backend`
2. Open AWS S3 PHP script: `service/write_aws-s3.php`
3. Modify `header("Access-Control-Allow-Origin: https://yourusername.github.io");` with your username
4. Modify AWS S3 configuration:
     * `$bucketName = 'YOUR_BUCKET_NAME';` (e.g., `webmushra-results`)
     * `$region = 'YOUR_BUCKET_REGION';`, see **S3 Storage** page (e.g., `eu-north-1`)
     * `$accessKey = 'YOUR_ACCESS_KEY';`
     * `$secretKey = 'YOUR_SECRET_KEY';`

### Finalize

1. Push all Backend changes:

```bash
git add .
git commit -m "setup aws s3 storage"
git push origin master
```

2. Setup Frontend experiment and push changes:
    * Choose/create experiment configuration `.yaml` file
    * Update experiment configuration to use Backend AWS S3 script on Render `remoteService: https://webmushra-backend.onrender.com/write_aws-s3.php`
    * Push Frontend changes

3. Deploy latest commit on Render (wait for completion)
4. Open Frontend webpage specifying the `config` file
5. Open browser developer tools to check for errors
6. Complete the test and check there are no errors when clicking `Send Results`
7. Check the results file has been saved in your S3 Bucket
8. A new `results` file will be saved for each participant



## Documentation

 * [Experimenters Manual](doc/experimenter.md)
 * [Participants Manual](doc/participant.md)



## Citation

If you use webMUSHRA in your publication, please cite it using the following reference:

> Schoeffler, M. et al., (2018). webMUSHRA — A Comprehensive Framework for Web-based Listening Tests. Journal of Open Research Software. 6(1), p.8.



## References

* [Journal of Open Research Software Paper](http://doi.org/10.5334/jors.187)
* [Web Audio Conference 2015 Paper](http://wac.ircam.fr/pdf/wac15_submission_8.pdf)
* [Web Audio Conference 2015 Presentation](http://www.audiolabs-erlangen.de/content/resources/webMUSHRA/slides.html#/)



## Copyright/Licence

(C) AudioLabs 2020

This source code is protected by copyright law and international treaties. This source code is made available to you subject to the terms and conditions of the Software License for the webMUSHRA.js Software. Said terms and conditions have been made available to you prior to your download of this source code. By downloading this source code you agree to be bound by the above mentionend terms and conditions, which can also be found [here.](LICENSE.txt)
Any unauthorised use of this source code may result in severe civil and criminal penalties, and will be prosecuted to the maximum extent possible under law.

---
---
---
---
---

# OLD README - webMUSHRA
[![GitHub release](https://img.shields.io/github/release/audiolabs/webMUSHRA.svg)](https://github.com/audiolabs/webMUSHRA/releases/latest)
[![Github All Releases](https://img.shields.io/github/downloads/audiolabs/webMUSHRA/total.svg)](https://github.com/audiolabs/webMUSHRA/releases/latest)
[![Build Status](https://travis-ci.org/audiolabs/webMUSHRA.svg?branch=master)](https://travis-ci.org/audiolabs/webMUSHRA)
[![DOI](https://zenodo.org/badge/81722942.svg)](https://zenodo.org/badge/latestdoi/81722942)

a MUltiple Stimuli with Hidden Reference and Anchor ([MUSHRA](https://en.wikipedia.org/wiki/MUSHRA)) compliant web audio API based experiment software.

<img width="1011" alt="screen shot 2017-03-08 at 12 38 20" src="https://cloud.githubusercontent.com/assets/72940/23702580/2a49bc82-03fc-11e7-99ea-22d550604a73.png">

## Introduction

Listening tests are widely used to assess the quality of audio systems. In the last few years, conducting listening experiments over the Internet, as so called web-based experiments, has become popular. Until now, it was only possible to implement a limited number of listening test types as web-based experiments because web standards were missing some crucial features, e.g. sample manipulation of audio streams. MUSHRA tests are designed to compare the audio quality of several test conditions with intermediate impairments to a high quality reference. With the rise of [Web Audio API](http://webaudio.github.io/web-audio-api/), for the first time MUSHRA experiments can be carried out within the web browser while at the same time being compliant to the ITU-R Recommendation BS.1534 (MUSHRA).

##### [View Demo](https://audiolabs.github.io/webMUSHRA)


### Download

We provide two version of webMUSHRA.

* __webMUSHRA__ provides the version targeted for normal usage and experimenters. The javascript files are compressed which makes it faster to load/serve. The documentation is provided as PDFs.

* __webMUSHRA-dev__ is targeted to developers and experienced users who want to customize experiments. This version is comparable to cloning the git repository

[Download Package Here](https://github.com/audiolabs/webMUSHRA/releases/latest)

## Features

* page based experiments supporting:
  * MUSHRA (ITU-R BS.1534)
  * AB (ITU-R BS.1116)
  * Likert scale questionaires
  * training/introduction
  * spatial attributes, such as ASW, LEV, and localization (experimental)
* compliant to ITU recommendations (looping, fade-in/out, sample accurate switching)
* finish page to gather the results and send them to a provided PHP service
* client side processing using the Web Audio API
* simple configuration using YAML preference files
* automatically generates ITU-R compliant lower anchor files on the fly
* keyboard shortcuts for interaction with main UI elements

## Supported Browsers

 * Google Chrome on Windows, Mac and Linux
 
## Getting started: Setting up webMUSHRA using PHP's builtin webserver

To load audio files and save the results as csv text files, webMUSHRA needs to run on a web server. If you already have `php` installed on your system (for example on Mac OS X), you can run a php development server on port 8000 from the terminal using `php -S localhost:8000`.

Now you can run webMUSHRA using the following URL: http://localhost:8000

The experiment configurations are stored in the `configs/` folder. To load a configuration/experiment, specify the `config` argument in the url http://localhost:8000/?config=mushra_showresults.yaml. `configs/default.yaml` is the configuration loaded when no config is specified.

### Docker

You can use docker to set up webMUSHRA quickly. Just run
`docker-compose -f docker-compose.yml build` to build the webMUSHRA docker container.

To run the container use webMUSHRA `docker-compose -f docker-compose.yml up`. We configured the docker image so that the `configs` and the `results` folder is mounted inside the container so that you can modify it on the fly and receive results within the `results` folder.

#### Note for Docker on Windows

When using Docker Toolbox/Machine on Windows, volume paths (to mount the `configs` and `results` folder) are not converted by default. To enable this conversion set the environment variable COMPOSE_CONVERT_WINDOWS_PATHS=1 e.g. by `env:COMPOSE_CONVERT_WINDOWS_PATHS=1` in the power shell.

### Apache + PHP
Another custom way to run webMUSHRA would be to install a complete web server stack like [XAMPP](https://www.apachefriends.org/download.html).

### Python Backend
A python based backend to save the results in provided by [pymushra](https://github.com/nils-werner/pymushra).

#### Change or add a configuration

webMUSHRA uses [YAML](https://en.wikipedia.org/wiki/YAML) to configure experiments. Since YAML is using whitespace indentation (no tab characters!), we recommend to use a text editor like [Atom](http://atom.io) that ships with YAML support.

A simple MUSHRA test in YAML looks like this:

```yaml
pages:
  - type: mushra
    id: Item 1
    name: Orchestra
    content: Add additional notes for the participants
    showWaveform: true
    reference: reference.wav
    createAnchor35: true
    createAnchor70: true
    stimuli:
      C1: codec1.wav
      C2: codec2.wav
      C3: codec3.wav
```

The specific parameters are described in the [Experimenters Manual](doc/experimenter.md).

## Documentation

 * [Experimenters Manual](doc/experimenter.md)
 * [Participants Manual](doc/participant.md)

## Citation

If you use webMUSHRA in your publication, please cite it using the following reference:

> Schoeffler, M. et al., (2018). webMUSHRA — A Comprehensive Framework for Web-based Listening Tests. Journal of Open Research Software. 6(1), p.8.

## References

* [Journal of Open Research Software Paper](http://doi.org/10.5334/jors.187)
* [Web Audio Conference 2015 Paper](http://wac.ircam.fr/pdf/wac15_submission_8.pdf)
* [Web Audio Conference 2015 Presentation](http://www.audiolabs-erlangen.de/content/resources/webMUSHRA/slides.html#/)

## Copyright/Licence

(C) AudioLabs 2020

This source code is protected by copyright law and international treaties. This source code is made available to you subject to the terms and conditions of the Software License for the webMUSHRA.js Software. Said terms and conditions have been made available to you prior to your download of this source code. By downloading this source code you agree to be bound by the above mentionend terms and conditions, which can also be found [here.](LICENSE.txt)
Any unauthorised use of this source code may result in severe civil and criminal penalties, and will be prosecuted to the maximum extent possible under law.
