## Install Wireguard VPN Server Using Docker


### Step 1. Install Docker on your server

First, you must install Docker on your server. I used Digital Ocean to create a droplet where I would store my VPN. The droplet is $5/month, Ubuntu 20.04, basic, and has regular Intel CPU. I chose to use a password to secure the server. I chose NYC 1 as the data center location, but you should choose the location closest to you for the fastest connection. 

Now, you are ready to install Docker on your new server. You can choose to ssh into the droplet from your local host terminal or launch a console window directly from the Digital Ocean website. I tried both methods but I found it easier to launch a console window from the Digital Ocean website. If you choose to do the same, you should click on your new droplet in Digital Ocean, click "Access", and then "Launch Droplet Console", making sure you are logging in as root.

To install Docker, run the following commands:

```
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

### Step 2: Install Docker Compose

You will need to install Docker Compose so that you can run YAML files during the Wireguard installation. Run the following commands to install Docker Compose.

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```
Verify that your installation was successful by checking what version you have installed.

```
docker-compose --version
```

### Step 3: Install Wireguard

Now, you are ready to install and setup Wireguard.

Run the following commands to create directories for your Wireguard installation.

```
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
```

Next, create the docker-compose.yml block. Run teh following command to open your .yml file.

```
nano ~/wireguard/docker-compose.yml
```

Next, copy and paste the code block below.

```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=137.184.131.25
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
 ```
 
 In the above code block, your `SERVERURL` should be changed to the ipv4 address of your Digital Ocean droplet. Your `TZ` should be changed to your proper time zone, using [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to find the name of your timezone. `PEERS` can be changed depending on the names of the user-config-files that you want.
 
 Hit `CTRL` + `X`, `Y`, `ENTER` to save and exit the file.
 
 


```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/lbertaux1/Wireguard.Docker/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
