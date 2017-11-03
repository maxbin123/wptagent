# wptagent
Cross-platform WebPageTest agent

## Supported Platforms/Browsers
Chrome is the only browser that currently supports manipulating requests (changing headers, blocking requests, etc).  Chrome, IE and Microsoft Edge are the only browsers that currently support capturing response bodieas and running optimizarion checks.  All browsers should support basic page loading, scripts and video capture on all platforms.  Traffic-shaping is supported on all platforms as well.

### Linux (with display or headless with Xvfb)
* Chrome (Stable, Beta and Unstable)
* Firefox (Stable and Nightly)
* Opera (Stable, Beta and Developer)

### Windows
* Chrome (Stable, Beta, Dev and Canary)
* Firefox (Stable, ESR, Developer Edition, Beta and Nightly)
* Microsoft Edge
* Internet Explorer
* Opera (Stable, Beta and Developer)

### OSX
* Chrome (Stable and Canary)
* Firefox (Stable and Nightly)

### Android (requires a tethered host - Raspberry Pi's preferred)
* Chrome (Stable, Beta, Dev and Canary)
* Several browsers run as "black box" tests (single page load, visual metrics only):
    * Chrome (Stable, Beta, Dev and Canary)
    * Opera
    * Opera Mini
    * UC Browser
    * UC Mini
    * Samsung Browser
    * Firefox (Stable and Beta)

## Known Issues
* Not all features have been implemented yet (see list below)
* Microsoft Edge and Firefox do not (yet) support manipulating requests (adding headers, blocking requests, etc)

## Installation
* Install docs are [here](docs/install.md)

## Run with docker
Check out [the docker instructions](docs/docker.md) for information on how to
run the agent in a docker container.

## Command-line options
### Basic agent config
* **-v** : Increase verbosity (specify multiple times for more). -vvvv for full debug output.
* **--name** : Agent name (defaults to the machine's hostname).
* **--exit** : Exit after the specified number of minutes.
    * Useful for running in a shell script that does some maintenence or updates periodically (like hourly).
* **--dockerized**: The agent is running inside a docker container.
* **--ec2** : Load config settings from EC2 user data.
* **--gce** : Load config settings from GCE user data.
* **--log** : Log critical errors to the given file.

### Video capture/display settings
* **--xvfb** : Use an xvfb virtual display for headless testing (Linux only).
* **--fps** : Video capture frame rate (defaults to 10). Valid range is 1-60. (Linux only).

### Server/location configuration
* **--server** (required): URL for WebPageTest work (i.e. http://www.webpagetest.org/work/).
* **--validcertificate**: Validate server certificates (HTTPS server, defaults to False).
* **--location** (required): Location ID (as configured in locations.ini on the server).
* **--key** : Location key (if configured in locations.ini).

### Traffic-shaping options (defaults to host-based)
* **--shaper** : Override default traffic shaper. Current supported values are:
    * none - Disable traffic-shaping (i.e. when root is not available).
    * netem,\<interface\> - Use NetEm for bridging rndis traffic (specify outbound interface).  i.e. --shaper netem,eth0
    * remote,\<server\>,\<down pipe\>,\<up pipe\> - Connect to the remote server over ssh and use pre-configured dummynet pipes (ssh keys for root user should be pre-authorized).

### CPU Throttling
* **--throttle**: Enable cgroup-based CPU throttling for mobile emulation (Linux only).

### Android testing options
* **--android** : Run tests on an attached android device.
* **--device** : Device ID (only needed if more than one android device attached).
* **--rndis** : Enable reverse-tethering over rndis (Android < 6.0).  Valid options are:
    * <ip>/<network>,<gateway>,<dns1>,<dns2>: Static Address.  i.e. --rndis 192.168.0.8/24,192.168.0.1,8.8.8.8,8.8.4.4
    * dhcp
* **--simplert** : Use [SimpleRT](https://github.com/vvviperrr/SimpleRT) for reverse-tethering.  The APK should be installed manually (adb install simple-rt/simple-rt-1.1.apk) and tested once manually (./simple-rt -i eth0 then disconnect and re-connect phone) to dismiss any system dialogs.  The ethernet interface and DNS server should be passed as options:
    * <interface>,<dns1>: i.e. --simplert eth0,192.168.0.1

### Options for authenticating the agent with the server:
* **--username** : User name if using HTTP Basic auth with WebPageTest server.
* **--password** : Password if using HTTP Basic auth with WebPageTest server.
* **--cert** : Client certificate if using certificates to authenticate the WebPageTest server connection.
* **--certkey** : Client-side private key (if not embedded in the cert).

## Currently supported features
* Feature complete except as noted below (for Windows, Linux, Mac and Android devices)
* Supported Script Commands:
    * navigate
    * exec (execAndWait)
    * block
    * sleep
    * logData
    * combineSteps
    * setEventName
    * setUserAgent
    * setBrowserSize
    * setViewportSize
    * setDeviceScaleFactor
    * setActivityTimeout
    * setTimeout
    * blockDomains
    * blockDomainsExcept
    * setDns
    * setDnsName
    * setHeader
    * addHeader (can not add multiple values for the same header, effectively the same as setHeader)
    * resetHeaders
    * setCookie
    * setABM
    * click (clickAndWait)
    * selectValue
    * sendClick
    * setInnerHTML
    * setInnerText
    * setValue
    * submitForm

## Not yet supported (actively being worked on)
* Browser installs/updates
* Windows general cleanup/health (temp files, downloads, killing processes, etc)
* Script Commands:
    * firefoxPref

## Not Supported (no plans to implement)
* Script Commands:
    * sendKeyDown
    * setDOMElement
    * waitForComplete
    * overrideHostUrl
    * ignoreErrors
    * logErrors
    * loadFile
    * loadVariables
    * minInterval
    * endInterval
    * expireCache
    * requiredRequest
    * setDOMRequest
    * waitForJSDone (change semantics to console log message)
    * overrideHost (depends on support being added to dev tools)
    * if/else/endif

