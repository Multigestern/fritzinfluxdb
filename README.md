# Fritz InfluxDb

Fritz InfluxDb is a tiny daemon written in python to fetch data from a fritz box router and writes it to influxdb.
It is equal capable as fritzcollectd and directly writing to influxdb.
Original created by [yunity](https://github.com/yunity/fritzinfluxdb) and changed so it is able to monitor multiple fritz box routers.

# Requirements
* python3.6 or newer
* influxdb
* fritzconnection >= 1.3.4

# Setup
* here we assume we install in ```/opt```

## Ubuntu 18.04
```
sudo apt-get install virtualenv python3-lxml
cd /opt
git clone https://github.com/multigestern/fritzinfluxdb.git
cd fritzinfluxdb
virtualenv --system-site-packages -p python3 .venv
. .venv/bin/activate
pip3 install -r requirements.txt
```

## RHEL/CentOS 7 with EPEL
```
yum install git python36-virtualenv python36-lxml
cd /opt
git clone https://github.com/multigestern/fritzinfluxdb.git
cd fritzinfluxdb
virtualenv-3 --system-site-packages .venv
. .venv/bin/activate
pip3 install -r requirements.txt
```

* modify your configuration and test it
```
./fritzinfluxdb.py
```

## Install as systemd service
Ubuntu
```
cp /opt/fritzinfluxdb/fritzinfluxdb.service /etc/systemd/system
```
RHEL/CentOS
```
sed -e 's/nogroup/nobody/g' /opt/fritzinfluxdb/fritzinfluxdb.service > /etc/systemd/system/fritzinfluxdb.service
```

```
systemctl daemon-reload
systemctl start fritzinfluxdb
systemctl enable fritzinfluxdb
```

## Setup taskchecker
As some routers may fail to report data from time to time I created a ```taskchecker.sh``` which contains a simple lookup for the running tasks against an integer. This integer has to be changed to the number of routers plus the base task. So in my case three routers and the base task is a four.

If the tasks are less, then the service gets restarted and every routerdata is collected again.
I run this as a cronjob.

## Run with Docker
```
git clone <this_repo_url>
cd fritzinfluxdb
docker build -t fritzinfluxdb .
```

Copy the config from the [example](default.ini) to ```my-frtizinfluxdb.ini``` and edit
the settings.

Now you should be able to run the image with following command
```
docker run -d -v /PATH/TO/my-frtizinfluxdb.ini:/app/idefault.ini --name fritzinfluxdb fritzinfluxdb
```

## Upgrading
If you upgrade from a version < 0.3 make sure to perform following steps

* update your virtual env `pip3 install -r requirements.txt`
* use the updated config and add the credentials and addresses from your old config

# Grafana

Since I'm using Chronograf to show my InfluxDB data I haven't done any changes to the original Grafana Dashboard.
So it is not included, but to make it runable with multible fritz box routers shouldn't be that hard.

# Configure more attributes

check here to find a overview of more attributes which probaly could be added
https://wiki.fhem.de/w/index.php?title=FRITZBOX

# License
>You can check out the full license [here](LICENSE.txt)

This project is licensed under the terms of the **MIT** license.
