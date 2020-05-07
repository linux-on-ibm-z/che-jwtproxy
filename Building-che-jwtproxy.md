# Building che-jwtproxy

The instructions provided below specify the steps to build [che-jwtproxy](https://github.com/eclipse/che-jwtproxy) on Linux on IBM Z for following distributions:
*    RHEL 7.7
â€‹
### Prerequisites:
* Docker packages are provided for SLES, Ubuntu and RHEL (7.5 or higher) in their respective repositories. You may use the same instructions for RHEL (7.5, 7.6, 7.7) as the binaries are expected to be compatible. More information about Docker CE can be found [here](https://docs.docker.com/install/).

_**General Notes:**_
*   _When following the steps below please use a standard permission user unless otherwise specified._
*   _A directory `/<source_root>/` will be referred to in these instructions, this is a temporary writable directory anywhere you'd like to place it._
â€‹
### Step 1: Install dependencies
```
export SOURCE_ROOT=/<source_root>/
```
*    RHEL 7.7
     ```
     sudo yum install -y git wget curl
     wget -q https://raw.githubusercontent.com/linux-on-ibm-z/scripts/master/Go/1.13.5/build_go.sh
     bash build_go.sh -y
     export GOPATH=$SOURCE_ROOT
     ```
     
### Step 2: Build che-jwtproxy binary
```
go get github.com/eclipse/che-jwtproxy
cd $SOURCE_ROOT/src/github.com/eclipse/che-jwtproxy
git checkout fd94e604f62485196503a44e0ec2e1c8e0108e20
go build -o jwtproxy cmd/jwtproxy/main.go
sudo cp jwtproxy /usr/local/bin
```

### Step 3: Build che-jwtproxy docker image
```
cd $SOURCE_ROOT/src/github.com/eclipse/che-jwtproxy
docker build -t che-jwtproxy:fd94e60 .
```

### Step 4: Run Test cases (Optional)
```
go test ./...
```

### Step 5: Verification
```
cd $SOURCE_ROOT
git clone https://github.com/quay/jwtproxy.git
cd jwtproxy/examples/httpserver
```
*Note: Following commands are to be run in separate terminal sessions*  
```
go get github.com/rif/spark && $GOPATH/bin/spark -port 8081 "Welcome to this authenticated web service."
jwtproxy -config signer.yaml
jwtproxy -config verifier.yaml
curl --proxy localhost:3128 http://localhost:8080/
```
Output should be "Welcome to this authenticated web service."


### References:
[che-jwtproxy](https://github.com/eclipse/che-jwtproxy)<br/>
[Example usage](https://github.com/quay/jwtproxy/tree/master/examples/httpserver)
