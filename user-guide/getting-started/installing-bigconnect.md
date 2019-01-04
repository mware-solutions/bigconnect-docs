# Installing BigConnect

Currently BigConnect is provided in a `.tar.gz` package format for Linux and MacOS systems and `.zip` format for Windows and are the easiest choice for getting started.

The provided packages include all the necessary binaries and files to run the platform on your local machine, so dependencies should be required.

{% hint style="info" %}
BigConnect only supports 64 bit operating systems.

Please make sure Java 8 64bit is installed and the `java` command is included in the user PATH
{% endhint %}

## Install with `.tar.gz` format

BigConnect is provided for Linux and MacOS as a `.tar.gz` package. These packages are the easiest formats to use when trying out the platform and are free to use under the Apache 2.0 License. 

The latest stable version of BigConnect can be found on the [Download BigConnect](https://www.bigconnect.io/download-free) page.

### Download and install the Linux package

The `.tar.gz` archive for the latest BigConnect Linux SQL release can be downloaded and installed as follows:

```bash
wget http://bits.bigconnect.io/downloads/bc/bc-A.B.C-linux-x86_64.tar.gz
shasum -a 512 bc-A.B.C-linux-x86_64.tar.gz
tar -xzf bc-A.B.C-linux-x86_64.tar.gz
cd bc-A.B.C-sql-linux-x86_64/
```

A.B.C is the latest BigConnect release version found on the [Download](https://www.bigconnect.io/download-free) page.

Compare the SHA produced by or `shasum` with the [published SHA](http://bits.bigconnect.io/downloads/bc/bc-3.5.0-x86_64.tar.gz.sha512) value.

The folder `bc-A.B.C-linux-x86_64/` will be referenced further as `$BIGCONNECT_DIR`

### Directory layout of Linux `.tar.gz` packages

The `.tar.gz` packages are entirely self-contained. All files and directories are, by default, contained within `$BIGCONNECT_DIR` — the directory created when unpacking the archive.

This is very convenient because you don’t have to create any directories to start using BigConnect, and uninstalling BigConnect is as easy as removing the `$BIGCONNECT_DIR` directory. However, it is advisable to change the default locations of the config and data directories so that you do not delete important data later on.

| Type | Description |
| :--- | :--- |
| **config** | Configuration files |
| **datastore** | The location of the data files written to disk by BigConnect |
| **log** | Log files |
| **lib** | System libraries needed by BigConnect |
| **lib/ext** | Additional libraries added by users \(eg. database drivers and plugins\) |
| **webapp** | BigConnect Web Console files |



