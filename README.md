# Covid Vaccine Tracker

Vaccine tracker is a simple tool for tracking the availability of Covid vaccines in any state in India by pincode or district code.
It uses the [api](https://apisetu.gov.in/public/marketplace/api/cowin/cowin-public-v2#/Appointment%20Availability%20APIs/findByPin) from COWIN site to monitor for vaccine availability and sent an immediate email :envelope: to user.
 ✨

## Features

- Check availability of vaccine by providing pincode or district code of your area
- Run in the terminal and monitor the vaccine availability
- Send email to email address set by user immediately when there is vaccines available
- set age range to check availability in that range.


## Set Up

Vaccine-tracker requires [python3](https://www.python.org/downloads/) to run

Project dependencies can be installed using the following command:

``` sh
pip install -r requirements.txt
```

Note :notebook: For gmail, the less secure apps connection needs to be turned on for email to work from [here](https://myaccount.google.com/lesssecureapps).
for other mail providers the smpt address should be modified.



## Usage
For running these scripts you've to set the email details in [config file](config.yml) file.
Edit following part of code in [config file](config.yml) file
``` sh 
email:
  sent_from : "<email_id>" 
  email_password : "<password>"

  # [edit] enter receiver email details
  to : ["<email_id>"]
```

There are two ways in which you can see vaccine availability

### Using pincode.
Set your area pincode in [config file](config.yml) file as shown below. Find pincode of your area [here](https://www.indiapost.gov.in/VAS/Pages/findpincode.aspx)
``` sh 
area_info:
# [edit] enter your district code or pincode
  __district_code : "<district_code>" 
  __pincode : "<pincode>"
```

Edit [vaccineTracker](vaccineTracker.py) file and set 
```sh
query_type = "pincode"
```

Run  
```sh
python3 vaccineTracker.py

```
Then it'll search for vaccine availability in your area.


### Using district code
Set district code in the [config file](config.yml) file.
To know your district code follow these steps:

- First see your state code [here](https://cdn-api.co-vin.in/api/v2/admin/location/states) 

- Now edit your state code in this url https://cdn-api.co-vin.in/api/v2/admin/location/districts/{your_state_code} 
  e.g. https://cdn-api.co-vin.in/api/v2/admin/location/districts/16 for karnataka

- click on the url for your state you can see your district code by searching your district name.


Enter the district code in [config file](config.yml) file

``` sh 

area_info:
# [edit] enter your district code or pincode
  __district_code : "<district_code>"
``` 
Edit [vaccineTracker](vaccineTracker.py) file and set 
```sh
query_type = "district_code"
```
Run the script after setting the values 
```sh
python3 vaccineTracker.py

```
It'll search for availibility of vaccine centers in that area.

## How it works
The vaccineTracker Class gets initialized and the details passed in [config.yml](config.yml) are assigned to the instance variables.

The following line of code parses the ymlfile into a python object and stores it in <code>cfg</code> as a dictionary with the tree data.
```sh
cfg = yaml.safe_load(ymlfile)
```

The <code>set_params</code> method parses the dictionary stored in <code>cfg</code> and assigns the values to the instance variables.

The <code>vaccineTracker</code> object calls the <code>query</code> method with <code>root_url</code> and <code>query_type</code> as parameters. 
The <code>query</code> method modifies the <code>url</code> as per the <code>query_type</code> and calls the <code>call_api</code> method, with <code>modified_url</code> and <code>query_type</code> as parameters.
The <code>call_api</code> method sends a <code>GET</code> request and saves the response as a response object. If the response <code>status_code</code> is 200 i.e. success, we extract the data in a json format and pass the json object in the <code>parse_json_<query_type></code> method to parse the data that we need.
Then it calls the <code>send_email</code> method which sends a mail to the desired email id with the parsed data.

If, due to some reason, the <code>status_code</code> is anything but 200, i.e. fail, it repeats the entire process after the specified <code>time_delay</code>.