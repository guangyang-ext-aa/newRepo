aa
=============

The aa project is a collection of frontend code and generic code as a backbone for various scrapers. Originally this
project was used for iOS market only, but eventually other markets were added and therefor it is contains some
mixed code.

This document describes some commands you can use to get code running. Examples below illustrate python scraping,
JackFruit scraping and testing code.

# Architecture

See [global architecture](https://docs.google.com/a/appannie.com/drawings/d/1U8_o9OZyns2r7XtyCRtyDAexF9vhcsPdtwsC2qpsmxY)
for an utterly incomplete and incorrect picture of what the constituents of this repository are. The picture is on such a high-level
of abstraction that it will probably only be useful for someone who is new to the project. A rectangle indicates
some entity. Nesting rectangles induces meaning by some parent-child relationship which can be for example be something like: runs in, is part of, is governed by, is of type, etc.
The little colored rectangles in either the left or right top corner of a rectangle match color with a border of some other rectangle. Matching colors
add another layer of meaning to the figure. In the Google Drawing you can click on them, as well as all other parts of the diagram, and they will explain you what this relationship encompasses.
Last but not least the rectangles are organized in such a way that their "most" important system attribute is highlighted.

I highly suggest you pick some lovely knowledgeable person to guide you around the diagram such that your view on the
system becomes less rectangular and more hairy. Preferably you do this with more than one person, this will add some
more perspective.

# Debugging
You can debug your code by adding the following lines of code to enter the debugger when running (and not using PyCharm
for example).

```
import pdb; pdb.set_trace()
```

# Tests
## Python tests
Test python code example for a specific test function:
```
python -m unittest tests.unstable.scrape.test_review_spiders.TestReviewSpider.test_windows_phone_spiders
```

There are also the nosetests to run:
```
nosetests --nocapture tests
```

Or run Jenkins locally, you can run:
```
python manage.py test --settings=settings_test_ci /workspace/aa/tests/ci/unit/storestats/webanalytics/tasks/importers/test_windowsphone_detail_importer.py
```

## JackFruit tests
You can run the JackFruit test as follows:
```
node test.js -t templates/windows/
```

Or you can run the test emulator yourself on port 5000 and route your browser through the test proxy and test yourself:
```
node server.js -c ./tests/templates/windows/config.js -p ./tests/templates/windows/pages/
```

# JackFruit
Run a JackFruit template with the following command:
```
echo '{"options":{"username":"cliveciappara@live.com", "password":"******", "cookie":"CmlzB9Z315vQGG4kBfzucktWx2Drn2Qr0RDZoJipmmF3ltPKvbBsmpUQJHMobYXGTHvN75!YpeQ!VNmpftY9k*nMbiycwOwS5KNjnhlpATHGtze8l88q37qbT65mEgccLtoFSfT95nWMCPtpntdjWNbCl*0FarMu11r2A9tWaXB5"}, "settings":{}}' | node main.js --template "windows/scraper"
```

# Celery
Celery can be set up by makings sure you have the correct local settings, run the engine, the worker and add a task.

## Configuration
First make sure you have the following settings_local.py variable set:
```
DEBUG = False 			# turn off so that we won’t print too many logs
TEST = True 			# make sure we use a single queue for easier testing, at the same time, all the S3 operation will use local redis.
ENABLE_AN_SALES_SCRAPING_S3 = False
```

Also set the following properties in your celeryconfig_local.py file:
```
BROKER_URL = "redis://localhost:6379/0" 	# use local redis message broker
CELERY_ALWAYS_EAGER = False 				# turn off eager mode to make sure we test all the task life cycle
CELERY_DEFAULT_QUEUE = 'default_myqueue' 	# this config make all celery message into 'default_1', you can choose whatever queue name you want
```

## The engine
```
appannie/tasks/bin/tq launch launch
```

## The Celery worker
```
celery worker -l debug -Q default_myqueue
```

## Add Celery task
```
python appannie/tasks/bin/tq windowsphone_scrape_sign_in --task_type SIGNIN --send_email False --appcare_id 59551 --user_id 137795 --begin_date 2014/08/01 --end_date 2014/08/02
```

# Internal emails

In order to send the internal monitoring emails use the following command:

```
python scripts/notification.py
```

Note that during development you need to make sure that the `NOTIFY_\*` settings point
to the right email addresses.

# New MKT site

## Overview

https://docs.google.com/a/appannie.com/document/d/1IcLuQZMzIMDgafjoCbIzKBX2qijdxD07-fUcGMWY5Fc/edit

By default, it's not enabled in Development environment, if you REALLY want to turn on it,
make sure there is a file under aa's root:

~/workspace/appannie/aa $ touch .enable-new-mkt-site
