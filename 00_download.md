# Downloading and starting the sample app

## Prerequisites

- NodeJS (nodejs.org)
- Visual Studio Code (https://code.visualstudio.com)
- Chrome (Recommended)
- Clear Cache Plugin for Chrome (Recommended)
- Git (in some cases, see below)

## Downloading the sample

1. If you are using Windows 64 bit, just download the zip file at XXX and extract it. After that you can skip the rest of this section.
2. In all other cases, download or clone the GIT repository at YYY:
	- Option 1: ``git clone YYY``
	- Option 2: Use the download button in the upper right corner at YYY to download a zip file. After that, extract it.
	- Option 3: If Option 1 and 2 doesn't work, download the zip file at ZZZ and extract it.
3. After downloading or cloning the GIT repository switch to its root folder and run
	
	```
	npm install
	```

## Running the sample

1. The root folder of the repository also contains the file ``tsconfig.json``
2. To start the project run:

	```
	npm start
	```

3. After the web server started, navigate to ``http://localhost:8080`` and use the application to search for flights from ``Hamburg`` (in Germany) to ``Graz`` (in Austria). Select one of the found flights.
 