# Source code structure

BigConnect is hosted at [GitHub](http://www.github.com) and uses [Git](http://git-scm.com/) for source control. In order to obtain the source code, you must first install Git on your system. GitHub provides [instructions for installing and setting up Git](https://help.github.com/articles/set-up-git).

The source code is divided into multiple repositories that make up the platform as a whole:

* [bigconnect](https://github.com/mware-solutions/bigconnect) - contains the source code for the core of the platform and the graph engine
* [dw-plugins](https://github.com/mware-solutions/dw-plugins) - contains various Data Workers that can be used for different purposes
* [bigconnect-web](https://github.com/mware-solutions/bigconnect-web.git) - contains the Web UI

## The _bigconnect_ repository

Contains the core of the platform and has all the code for the graph engine, data workers, long running processes, ontology and more.

Directory structure:

* `common` - common code shared among the multiple BigConnect components
  * `core` - core BigConnect services for users, data workers, notifications, workspaces etc.
  * `cypher` - support for Cypher queries
  * `elastic` - Search Index implementation for ElasticSearch
  * `graph` - core graph engine API
  * `security` - shared files for the security model
  * `sql` - support for SQL queries
* `plugins` - various plugins such as RocksDB storage, Spark integration, Proxy etc.
* `proxyserver-api` - Java client driver
* `proxyserver` - Java server
* `shell` - Groovy shell
* `test` - testing harness

## The _dw-plugins_ repository

Contains various required and optional Data Worker plugins:

* `audio-metadata` - extracts audio metadata \(size and duration\) using [ffprobe](https://ffmpeg.org/ffprobe.html)
* `audio-mp4-encoder` - Encodes audio data into MP4 format using [ffmpeg](https://ffmpeg.org/)
* `audio-ogg-encoder` - Encodes audio data into OGG format using [ffmpeg](https://ffmpeg.org/)
* `azure-image-ocr` - Uses [Azure Computer Vision](https://azure.microsoft.com/en-us/services/cognitive-services/computer-vision/) API to extract text from image resources
* `azure-image-tags` - Uses [Azure Computer Vision](https://azure.microsoft.com/en-us/services/cognitive-services/computer-vision/) API to extract tags from image resources
* `dictionary-extractor` - Extracts terms from text using a dictionary
* `google-nlp` - Uses [Google Cloud Natural Language](https://cloud.google.com/natural-language/) API to extract named entities
* `google-s2t` - Uses [Google Cloud Speech-to-Text](https://cloud.google.com/speech-to-text/) API to extract audio transcripts
* `groovy` - Runs Groovy scripts as data workers
* `image-metadata-extractor`  - Extracts image metadata using the [Drewnoakes ](https://github.com/drewnoakes/metadata-extractor)library
* `location-extractor` - Extracts location from text using the [CLAVIN Open Source Geotagger](https://clavin.bericotechnologies.com/)
* `mime-type-ontology-mapper` - Maps MIME types to an ontology concepts
* `opencv-object-detector` - Detects objects in images \(like faces\) using [OpenCV](https://opencv.org/)
* `opennlp-me-extractor` - Extracts terms from text using an [OpenNLP ](https://opennlp.apache.org/)maximum entropy
* `phone-number-extractor` - Extracts phone numbers from text using [libphonenumber](https://github.com/googlei18n/libphonenumber)
* `regex-extractor` - Extracts terms from text based on Regex expressions
* `tika-mime-type`  - Uses [Apache Tika](https://tika.apache.org/) to determine content MIME type
* `tika-text-extractor` - Uses [Apache Tika](https://tika.apache.org/) to extract text from various content types \(pdf, doc etc.\)
* `video-audio-extract` - Extracts the audio stream from a video using [ffmpeg](https://ffmpeg.org/)
* `video-frame-extract` - Extracts video frames for image processing using [ffmpeg](https://ffmpeg.org/)
* `video-metadata` - extracts video metadata \(geolocation, date, device, width etc.\) with [ffprobe](https://ffmpeg.org/ffprobe.html)
* `video-mp4-encoder` - Encodes video into MP4 format with [ffmpeg](https://ffmpeg.org/)
* `video-poster-frame` - Gets a video thumbnail by extracting a frame from the video using [ffmpeg](https://ffmpeg.org/)
* `video-webm-encoder` - Encodes video into WebM format with [ffmpeg](https://ffmpeg.org/)

## The bigconnect-web repository

Contains all the code for the web console and its plugins

* `config` - configuration files for running the web console
* `plugins` - bundled plugins for the web console
  * `advanced-dictionary-manager` - plugin for managing dictionaries for the _dictionary-extractor_ data worker 
  * `auth-username-only` - plugins for authentication using only the username
  * `auth-username-password` -  plugins for authentication using username/password and forgot password.
  * `graph-product` - graph analysis plugin
  * `map-product` - spatial analysis plugin
  * `rest-explorer` - plugins for browsing and interacting with the REST API endpoints
  * `terms-of-use` - plugin for displaying and agreeing to initial terms & conditions
* `tomcat-server` - contains all dependencies for running the embedded Tomcat server
* `war` - source code for the web front-end \(HTML, LESS, JavaScript\)
* `web-base` - source code for the web back-end



