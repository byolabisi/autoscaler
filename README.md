<div id="top"></div>
<!--


<!-- PROJECT LOGO -->
<br />
<div align="center">
    <img src="https://raw.githubusercontent.com/github/explore/80688e429a7d4ef2fca1e82350fe8e3517d3494d/topics/docker/docker.png" alt="Logo" width="80" height="80">
  </a>

  <h3 align="center">AUTOSCALER</h3>

  <p align="center">
    Deploying a scalable microservice, API or web application in Docker!
    <br />
    ·
    <a href="https://github.com/byolabisi/autoscaler/issues">Report Bug</a>
    ·
    <a href="https://github.com/byolabisi/autoscaler/issues">Request/Add Feature</a>
  </p>
</div>


<!-- ABOUT THE PROJECT -->
## About The Project


Suppose you have a microservice, API or web application that you need to deploy, but you are concerned about the possibility of your server not being capable of handling it. You could use AWS to scale horizontally your servers for you, the problem (and the biggest motivation for this project) is that AWS is expensive compared to another providers, for example, Digital Ocean, Linode, OVH, etc.


A Digital Ocean (for now) and CloudFlare accounts. We will use CloudFlare DNS to load balance to your machines, so, be sure that the root domain you wanted exposed in scaleme config file is signed in your CloudFlare account.

More details about the implementation can be found at the How it Works section in this page.


### Installation

Node.js is necessary to run this project. You should go to the Node.js Downloads Page and proceed with their instructions.

Then, just clone this repository and start it using npm (Node package manager):

```sh
git clone https://github.com/byolabisi/autoscaler.git
cd autoscaler/
sudo npm install -g
```
### Getting Started

Now you can use autoscaler CLI to start, list, monitor and delete your projects.

#### Starting a Project

To start a project, first you must create a config file in any folder you want (check out ```config.sm```). The basic structure of this file is this:

```javascript
{
	"project": "test", // project name
	"git" : "https://github.com/byolabisi/extract_article.git", // docker container app
	"port" : 3000, // port that your container runs
	"digital_ocean" : {
		"key": "YOUR_DO_KEY",
		"region" : "nyc3",
		"size": "512mb"
	},
	"cloudflare" : {
		"email": "your@email.com",
		"key": "YOU_CLOUDFLARE_KEY",
		"domain": "test.example.com"
	},
	"scaler_rules": {
		"min": 1, // min number of computers
		"max": 3, // max number of computers
		"interval" : 300, // interval to monitor and decide to scale (seconds)
		"up": {
			"metric": "cpu",
			"rule": "gt",
			"percent": 70
		},
		"down": {
			"metric": "cpu",
			"rule": "lt",
			"percent": 10
		}
	}
}
```


Then, run:

```sh
sudo autoscaler -s config.sm
```

Your project will start and after a couple of minutes you can check it out using the domain you provided, in this example ```test.example.com```.

#### Listing current projects

```sh
sudo autoscaler -l
```

#### Get monitoring information of a project

```sh
sudo autoscaler -m project_name
```

#### Delete a project

```sh
sudo autoscaler -d project_name
```

### How it Works?

autoscaler initially deploy two machines, one called the ```scaler``` and another the ```secondary```.

The job of the ```scaler``` is to monitor the secondary(s) using the rules defined in the config file, if the up rule is triggered then the scaler will deploy another slave machine if the maximum number of machines is not reached, and if the down rule is triggered it will delete one slave machine if the mininum number of machines is not reached.

Besides, another job of the scaler is to get the slaves ips and sign them at the CloudFlare DNS so that your machines can be acessible from the domain you provided. The CloudFlare DNS act like a load balancer, since we have more than one ip from the same domain, the DNS will deliver this ips in a round-robin fashion, distributing the load among all the secondary(s) you have at a given moment.

The job of the ```secondary``` is straight-forward, it is your docker application with a monitoring API exposed to enable the scaler to request monitoring informations and make scaling decisions.
.....


<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#top">back to top</a>)</p>

