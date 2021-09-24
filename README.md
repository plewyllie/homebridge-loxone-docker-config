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
curl -O https://github.com/plewyllie/homebridge-loxone-docker-config/blob/main/config.json
curl -O https://raw.githubusercontent.com/plewyllie/homebridge-loxone-docker-config/main/package.json
```

Edit the config.json file and change the Loxone host, port, username and password parameters.

# Running the Oznu docker homebridge image
As stated on Oznu's github page: "Homebridge requires full access to your local network to function correctly which can be achieved using the --net=host flag. This image will not work when using Docker for Mac or Docker for Windows due to this and this."

With this in mind, we can start our container from our running directory containing our config.json and package.json files:

`sudo docker run --net=host --name=homebridge --rm -v $(pwd):/homebridge oznu/homebridge:latest`


