# GUAC Set up Instructions for Research Assistants

Note that these instructions are for Ubuntu.

## Before you start
Follow the instructions in the following pages to create an SSH key (estimated time to complete- 5 minutes) and Create a Droplet on DigitalOcean (estimated time to complete- 10 minutes)

[Create an SSH Key](docs/create_ssh_key/README.md)

[Create a Droplet (VM) on DigitalOcean](docs/create_droplet/README.md)

# Part 1: Install Required Packages

## Docker

Set up Docker’s `apt`  repository

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install the Docker packages

(note you will need to confirm installation by typing Y)

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify Docker Engine installation is successful:

```bash
sudo docker run hello-world
```

If you aren’t able to run the hello-world container, make sure Docker is running.

Start Docker using:

```bash
sudo service docker start
```

And then run `sudo docker run hello-world`

## Install Docker Compose Plugin

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

Test Docker Compose Installation

```bash
docker compose version
```

## Install jq

jq is in the official Debian and Ubuntu repositories. You can check if binary is available by running:

```bash
jq
```

If jq is unavailable, you can install it using:

```bash
sudo apt-get install jq
```

SSH with port forwarding to see the GraphQL playground: 

```bash
ssh -i <identity_file.ssh> root@<ip_address>
```

SSH with port forwarding to use the GUAC Visualizer: 

# Part 2: Download GUAC and Start the GUAC Server

Open a new command prompt window. We’ll SSH using tunneling so that we can interact with the GUAC GraphQL playground from our local machines.

```bash
ssh -i <ssh_private_key_file> -L 8080:localhost:8080 root@your_server_ip
```

Set up our GUAC Project

```bash
mkdir guac
cd guac
```

## Download GUAC

```bash
wget "https://github.com/guacsec/guac/releases/latest/download/guacone-linux-amd64"
```

Rename `guacone-linux-amd-64` to `guacone`

```bash
mv guacone-linux-amd64 guacone
```

Make the `guacone` binary executable:

```bash
chmod +x guacone
```

Add `guacone` binary to PATH:

```bash
vim .bashrc
```

Go to the very end of the file

Type `a` to enter into `INSERT` mode. 

Add the following line to the end of the file: 

```bash
export PATH="~/guac:$PATH"
```

Press the  `esc` key to exit `INSERT` mode

type `:wq` to save and quit

Run the following to execute the updates you made

```bash
source .bashrc
```

Now run

```bash
echo $PATH
```

You should see `~/guac`appended to the front of your PATH.

## Download GUAC compose yaml:

```bash
wget "https://github.com/guacsec/guac/releases/download/v0.10.0/guac-demo-compose.yaml" 
```

Download and unzip the demo data:

```bash
wget https://github.com/guacsec/guac-data/archive/refs/heads/main.zip
```

If you don’t have unzip installed, install it with:

```bash
apt install unzip
```

Unzip the demo data:

```bash
unzip main.zip
```

Delete the zip file:

```bash
rm main.zip
```

## Start the GUAC server

This is modified from original GUAC documentation to run in detached mode to have access to terminal.

```bash
docker compose -f guac-demo-compose.yaml -p guac up --force-recreate -d
```

Verify that GUAC is running

```bash
docker compose ls --filter "name=guac"
```

On your local machine, open a browser tab and navigate to: [http://localhost:8080](http://localhost:8080)

# Part 3: Ingest Data

Ingest data:

```bash
guacone collect files guac-data-main/docs
```

Check that everything is running:

```bash
curl 'http://localhost:8080/query' -s -X POST -H 'content-type: application/json' \
  --data '{
    "query": "{ packages(pkgSpec: {}) { type } }"
  }' | jq

```

# Part 4: Install the GUAC Visualizer

Open a new terminal tab or command prompt and ssh using tunneling over port 3000: 

```bash
ssh -i <your_key> -L 3000:localhost:3000 root@<ipaddress>
```

## Install GUAC Visualizer dependencies:

Now we’ll install the dependencies for the GUAC visualizer:

```bash
sudo apt install nodejs npm
```

Verify node installation by running:

```bash
node -v
```

Verify npm installed by running

```bash
npm -v
```

Use npm to install yarn:

```bash
npm install --global yarn
```

Verify yarn installation:

```bash
yarn --version
```

## Install GUAC Visualizer

Download and unzip the GUAC visualizer:

```bash
wget "https://github.com/guacsec/guac-visualizer/archive/refs/tags/v0.4.5.zip"
unzip v0.4.5.zip
rm v0.4.5zip
```

cd into `guac-visualizer`

```bash
cd guac-visualizer-0.4.5
```

install dependencies

```bash
yarn install
```

run the visualizer locally:

```bash
yarn dev
```

Then open [http://localhost:3000](http://localhost:3000) in your browser to start the visualizer.
