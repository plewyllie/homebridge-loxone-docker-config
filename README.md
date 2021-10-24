# homebridge-loxone-docker-config
Documenting and saving homebridge configuration for Loxone using Docker

# Links
The original homebridge Github page: https://github.com/homebridge/homebridge

Docker image built by Oznu: https://github.com/oznu/docker-homebridge

Loxone plugin: https://www.npmjs.com/package/homebridge-loxone-connect

# Setting up the docker run folder
We need to create a new folder which will hold the following files:
- config.json : Homebridge configuration file. We will need to add our Loxone credentials in here.
- package.json : This file keeps track of which plugins should be installed to homebridge.

Minimal configuration files can be found on this github:
```
curl -O https://raw.githubusercontent.com/plewyllie/homebridge-loxone-docker-config/main/config.json
curl -O https://raw.githubusercontent.com/plewyllie/homebridge-loxone-docker-config/main/package.json
```

Edit the config.json file and change the Loxone host, port, username and password parameters.

# Running the Oznu docker homebridge image
As stated on Oznu's github page: "Homebridge requires full access to your local network to function correctly which can be achieved using the --net=host flag. This image will not work when using [Docker for Mac](https://docs.docker.com/docker-for-mac/) or [Docker for Windows](https://docs.docker.com/docker-for-windows/) due to [this](https://github.com/docker/for-mac/issues/68) and [this](https://github.com/docker/for-win/issues/543)."

With this in mind, we can start our container from our running directory containing our config.json and package.json files:

`sudo docker run --net=host --name=homebridge --rm -v $(pwd):/homebridge oznu/homebridge:latest`

# Modifying the Loxone Plugin
The Loxone plugin is getting more and more complete, but might not fit all your needs. I am using ActiveByte's Loxone plugin as it seems to be the most maintained and up-to-date plugin for Loxone. I forked the repository over [here](https://github.com/plewyllie/homebridge-loxone-connect). Feel free to fork mine or the original repo if you would like to modify it.

The plugin is based on npm, so make sure to have npm installed on your machine. On OSX you can install it with `brew install npm`.

We will modify the `package.json` file in the forked repo and change the package name and references, so it will be possible to publish it and use the modified version in our Oznu Docker image. Make sure to change the following elements:
- name, this needs to be a unique name on the npm package manager
- point the git URL to your fork
- it would be best practise to also update the links towards issues, readme to your fork as you have modified the package

I will now take you through my process of modifying ActiveByte's plugin to add "EIBBlindsItem" to control KNX blinds.
* Adding the `EIBBlindsItem.js` to the `items` folder
* Modifying `Itemsfactory.js` to look for the blinds and register them using the newly defined item
  * Adding a new modulexport `moduleexports.EIBBlinds = require('../items/EIBBlindsItem.js');`
  * Adding an if condition to look for Screens `if (item.name.indexOf("Screen") !== -1)[...]`
  * Adding `this.list_child_pos_UUID = {};` a list used to store the action UUIDs for the blinds.
  * TODO: Remove Need to check for specific "Screen Slaapkamer" with `if (this.itemList[key].name.indexOf("Screen Slaapkamer")`
* Modifying `index.js` with 

For this EIBBlinds modification to work, note the following:
* Loxone items should be named "Screen [ROOM] [function]"
* [function] == Op_Neer => Main EIBBlindsItem which will have actions, status and be used in HomeKit.
* [function] == Positie FeedBack => Dummy item (EIBBlindsPositionItem) receiving the value and updating Op_Neer EIBBlindsItem.


