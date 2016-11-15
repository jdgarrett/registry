Registry
========

[![Build Status](https://travis-ci.org/boundlessgeo/registry.svg?branch=master)](https://travis-ci.org/boundlessgeo/registry)
[![Coverage Status](https://coveralls.io/repos/github/boundlessgeo/registry/badge.svg?branch=master)](https://coveralls.io/github/boundlessgeo/registry?branch=master)

Registry is a web-based platform that captures geo-spatial content using CSW-T
protocol. Information is indexed into the Elasticsearch engine allowing fast
searches.

<p align="center"><img src="https://cloud.githubusercontent.com/assets/3285923/20119468/a58abe98-a5d6-11e6-89ad-b29903150324.png" width=80%> </p>

Installation
============

Assuming ubuntu 14.04 OS.

1. Install and configure elasticsearch

	```sh
	wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
	echo "deb https://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list
	sudo apt-get update && sudo apt-get install elasticsearch
	sudo sed -i -e 's/#ES_HEAP_SIZE=2g/ES_HEAP_SIZE=1g/' /etc/default/elasticsearch
	sudo service elasticsearch start
	```

2. Get registry source code

	```sh
	wget https://github.com/boundlessgeo/registry/archive/master.zip
	unzip master.zip
	cd registry-master/
	```

	Alternative using git

	```sh
	git clone https://github.com/boundlessgeo/registry.git
	cd registry
	```

3. Installation of registry modules

	```sh
	pip install -r requirements.txt
        pip install -e .
	```

4. Configure pycsw database

	```sh
	python registry.py pycsw -c setup_db
	```


Usage
=====

1. Run the server. The server will listen in port 8000
	```sh
	python registry.py runserver
	```

2. Create synthetic information.
	```sh
	python -c "from test_registry import construct_payload; print construct_payload(records_number=10);" > payload.xml
	```

3. Add records into the database and search engine via CSW-T
	```sh
	curl -XPOST -d @payload.xml  http://localhost:8000/
	```

4. Search api endpoint.

	```sh
	curl http://localhost:8000/registry/api/
	```

You should see the indexed information. The ```a.matchDocs``` value refers
to the number of layers returned by the search api.


Testing
=======

1. Start elasticsearch

2. Run tests

```sh
python setup.py test
```


Troubleshooting
================

1. Record parsing failed: 'Csw' object has no attribute 'repository'

	```xml
	<ows:ExceptionText>Transaction (insert) failed: record parsing failed: 'Csw' object has no attribute 'repository'</ows:ExceptionText>
	```

	**Reason 1:** Elasticsearch is not running. This makes pycsw to silent the error using exception. Start elasticsearch service.

	**Reason 2:** Database was not configured. Run in console ```python registry.py pycsw -c setup_db```

2. UNIQUE constraint failed: records.identifier.

	```xml
	<ows:ExceptionText>Transaction (insert) failed: ERROR: UNIQUE constraint failed: records.identifier.</ows:ExceptionText>
	```

	**Reason:** Records have been added previously into the database.


Features
========

 - [x] CSW-T support via pycsw
 - [x] Mirror information to Elasticsearch for faster searches
 - [x] OpenSearch based API to enable the use of facets on different fields (extending CSW standard).
 - [x] MapProxy support for easy TMS/WMTS access to any kind of resource
 - [ ] Multi-catalog support.
