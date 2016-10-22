# Downloading and starting the starter kit for the Migration Workshop

Please download the starter kit for the workshop and try to run it.

## Prerequisites

- NodeJS ([http://nodejs.org](http://nodejs.org))
- Visual Studio Code ([https://code.visualstudio.com](https://code.visualstudio.com))
- Git (in some cases, see below; [https://git-scm.com/](https://git-scm.com/))
- Chrome (Recommended)
- Clear Cache Plugin for Chrome (Recommended; [https://goo.gl/njgr7](https://goo.gl/njgr7))

## Downloading the sample

1. If you are using Windows 64 bit, just download the zip file at [http://www.softwarearchitekt.at/downloads/migration/migration-workshop-starter.zip](http://www.softwarearchitekt.at/downloads/migration/migration-workshop-starter.zip) and extract it. After that you can **skip the rest** of this section and precede with the next one.
2. In all other cases, download or clone the GIT repository with the starter kit for the workshop:
	- Option 1: ``git clone https://github.com/manfredsteyer/ng-upgrade-workshop-starterkit``
	- Option 2: Use the download button in the upper right corner at [https://github.com/manfredsteyer/ng-upgrade-workshop-starterkit](https://github.com/manfredsteyer/ng-upgrade-workshop-starterkit) to download a zip file. After that, extract it.
	- Option 3: If Option 1 and 2 doesn't work, download the zip file at [http://www.softwarearchitekt.at/downloads/migration/migration-workshop-starter.zip](http://www.softwarearchitekt.at/downloads/migration/migration-workshop-starter.zip) and extract it. After that, delete the folder ``node_modules``.
3. After downloading or cloning the GIT repository switch to its root folder and run
	
	```
	npm install
	```

## Running the sample

1. Navigate to the root folder of the repository. This is the folder that contains the file ``tsconfig.json``.
2. To start the project run:

	```
	npm start
	```

3. After the web server started, navigate to ``http://localhost:8080`` and use the application to search for flights from ``Hamburg`` (in Germany) to ``Graz`` (in Austria). Select one of the found flights.

## Appendix - Configuring corporate proxy servers

If you are behind a corporate proxy server and if you have troubles with using ``git`` or ``npm``, use the following commands:

```
npm config set proxy [http|https]://[proxy-address]:[port]
npm config set https-proxy [http|https]://[proxy-address]:[port]

git config --global http.proxy [http|https]://[proxy-address]:[port]
```

Please note, that you can directly include an url encoded username and an url encoded password in the url:

```
[http|https]://[username]:[passwort]@[proxy-address]:[port]
```

To configure the tool ``typings`` that is part of the starter kit, create a file ``.rctypings`` in your home directory (e. g. ``c:\users\max`` on Windows):

```
proxy=[http|https]://[proxy-address]:[port]
rejectUnauthorized=false
```