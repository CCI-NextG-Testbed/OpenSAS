# Open Source Spectrum Access System (OpenSAS)
## What is this?
This is the forked version for the Virginia Tech SAS, called the OpenSAS. The role of the SAS is to allow
spectrum management of CBSDs, activation of dynamic protection zones and environmental sensing for incumbent protection. The OpenSAS strives
to adhere to WinnForum and FCC regulations on SAS and CBRS operations.

## Getting started Method 1 (local clone)
In this method, the repository is cloned locally. This is the way to go if HTTPS is required. 

First clone the repository using git
```
git clone https://github.com/CCI-NextG-Testbed/OpenSAS
```

Next, create the CA and server/client certificates using the create_ssl_certs.sh script. Go into the /Core/Certs folder and run the script. Enter the IP of the machine running the OpenSAS if making CBSD requests externally, if making the requests locally, the IP/hostname can be 127.0.0.1
```
cd OpenSAS/Core/Certs
sudo chmod +x create_ssl_certs.sh
```
Before running the script, make sure to delete the existing ca.cert and all other .key,.crt and .csr files. The only to files remaining should be create_ssl_certs.sh and create_client_certs.sh. The create_client_certs.sh can be used to create client certs for each new client. Once existing certs are deleted, run the script. for server hostname- give the ip of machine running the server
```
./create_ssl_certs.sh
```
This will create the CA, server and client certificates in the Certs folder. The ca.cert, client-<IP/hostname>-0.cert and client-<IP/hostname>-0.key need to be copied to the client machine to make HTTPS requests.
Next, the path to the server cert and key is updated in the Core/server.py. The following code snipped show which paths to update. 
```
   httpd = HTTPServer(('0.0.0.0', 1443), SimpleHTTPRequestHandler)
    httpd.socket = ssl.wrap_socket (httpd.socket, 
           keyfile="Certs/server_10.147.20.60.key",                       //Update this to reflect the new server key
           certfile='Certs/server_10.147.20.60.crt', server_side=True)    //Update this to reflect the new server cert
    print("Listening on port 1443")
    httpd.serve_forever()    
```
Finally, before starting the server, install all the requirements (packages) by running pip3 install as follows
```
pip3 install -r requirements.txt
```
This will install all the required packages such as requests, python-engine.io. For the communication between the frontend and core to work the python-socketio and vue-socket.io versions should be compatible. The versions specified in the requirements.txt are tested to be compatible.
Finally, run the server by going into the Core directory
```
python3 server.py
```
This starts the SAS server. Now, the frontend can be started to view the GUI. Please refer https://github.com/CCI-NextG-Testbed/OpenSAS-dashboard on instructions to start the frontend/GUI

The CBSDs can access the SAS via the following URL endpoints:
```
https://<IP/hostname>:1443/sas-api/<request>

Examples: 

https://127.0.0.1:1443/sas-api/registration or https://192.168.0.110:1443/sas-api/registration

https://localhost:1443/sas-api/spectrumInquiry

https://localhost:1443/sas-api/grant

https://localhost:1443/sas-api/heartbeat

https://localhost:1443/sas-api/relinquishment

https://localhost:1443/sas-api/deregistration
```

## Getting started Method 2 (using Docker image)
Please refer to https://github.com/CCI-NextG-Testbed/OpenSASDocker for the dockerfile to create a image for this repository. The dockerfile also clones the 
OpenSAS-dashboard repo and runs it.

The dockerfile is the easiest way to get started.

## File Structure
### Core
The Core/ folder contains everything required to launch the SAS Core
Server. This is the true SAS.  Regardess of your institution, this contains the code that is of
primary interest for SAS researchers.





