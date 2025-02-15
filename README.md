# Sunportal

_Sunportal_ is a web based visualisation tool to display data of SMA solar inverters. It is based on the database of [SBFspot](https://github.com/SBFspot/SBFspot) and shows charts daily and monthly power production. It runs on a Raspberry Pi and can handle multiple inverters in one Speedwire or Bluetooth(R) network.

Inverters that do not support Speedwire or Bluetooth or that are not from the manufacturer SMA can be integrated through [s0-bridge](https://github.com/philipptrenz/s0-bridge). _s0-bridge_ also enables power consumption to be recorded by using electric meters with S0 interface.


Current features:

* Web based visualization of daily and monthly power production
* Displaying multiple inverters
* Translations: English, German
* Mail notification service for inverter disorders and lack of production

Planned features:

* Visualization of yearly power production
* Visualization of power consumption
* Validation of `config.yml` with [JSON Schema](https://json-schema.org/)
* Configuration of _sunportal_ via web interface
* REST-API, i.e. to integrate solar power plants into home automation applications

![sunportal example image](/static/img/sunportal.jpg?raw=true)

## Installation on a Raspberry Pi

First install and configure SBFspot as described [here](https://github.com/SBFspot/SBFspot/wiki/Installation-Linux-SQLite#sbfspot-with-sqlite), the _SBFspotUploadDaemon_ is not required. 

Also add a cronjob for SBFspot to run every 5 minutes. Execute `sudo crontab -e`, choose your preferred editor and add the following line:

```
*/5 * * * * /usr/local/bin/sbfspot.3/SBFspot -finq -nocsv > /dev/null
```

After that install _Sunportal_:

```bash
# install needed dependencies
sudo apt-get install git python3 python3-pip 
sudo pip3 install -r requirements.txt

# clone this repo
git clone https://github.com/aoffermann/sunportal
cd sunportal
```

## Configure _Sunportal_

All configuration parameters of _sunportal_ get stored inside the `config.yml` YAML file. To edit the preconfigured values copy the `config.default.yml` and edit the `config.yml`:

```bash
cp config.default.yml config.yml
nano config.yml
```

Under `mail` you can enable the integrated mail notification service by adding an SMTP server.

```yaml
---
database:
  path: "/home/pi/smadata/SBFspot.db"
co2_avoidance_factor: 0.7
mail:
  enabled: 'false'
  check_interval: 300
  sender: sunportal@example.com
  smtp_server:
    url: smtp.example.com
    port: 587
  recipients:
  - me@example.com
  starttls:
    enabled: 'false'
    user: ''
    password: ''
renaming:
  '10000000001': My Inverter 1

```

## Start _Sunportal_

```bash
sudo python3 sunportal.py
```

Open a browser and navigate to the IP address of the Raspberry Pi to see the website.

### Run _Sunportal_ on boot

```bash
# add the bash script to the service folder
sudo cp scripts/sunportal.sh /etc/init.d/sunportal
sudo chmod 755 /etc/init.d/sunportal
sudo update-rc.d sunportal defaults
```

Now _Sunportal_ can be controlled as a service (`sudo service sunportal status`) and it automatically starts on boot.

## Disclaimer

SMA, Speedwire are registered trademarks of SMA Solar Technology AG.
