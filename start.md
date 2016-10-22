# Angular Migration Workshop

## Prerequisites

- NodeJS ([http://nodejs.org](http://nodejs.org))
- Visual Studio Code ([https://code.visualstudio.com](https://code.visualstudio.com))
- Git ([https://git-scm.com/](https://git-scm.com/))
- Chrome (Recommended)
- Clear Cache Plugin for Chrome (Recommended; [https://goo.gl/njgr7](https://goo.gl/njgr7))

## Contents

1. [Download the Starterkit](00_download.html)

   If you have the starterkit already, please run the following commands to get a clean starterkit with the newest version:

   ```
   git pull
   git reset --hard 
   npm install
   ```
   
   To run the sample use the following command:

   ```
   npm start
   ```

   After that, navigate to ``http://localhost:8081`` and try to find some flights and passengers. 

   You can change the port within the file ``webpack.conf.js`` (search for ``port``).

2. [Refactoring to Components](01_refactoring-to-components.html)

3. [Migration with ngUpgrade](02_ngUpgrade.html)

4. [Further Exercises](04_Bonus.html)