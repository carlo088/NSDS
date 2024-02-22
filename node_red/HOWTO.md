Node-Red is a UI accessible at localhost
To spin up an istance, run:

docker run -it -p 1880:1880 -v node_red_data:/data --name mynodered nodered/node-red:1.0.0-1-10-arm32v6

NOTE: version 1.0.0-1-10-arm32v6 is the Node-Red version I have installed locally
