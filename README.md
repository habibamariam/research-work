# research-work
import json
import urllib2
import os
import pandas as pd
import urllib
from matplotlib.pyplot import imshow
import numpy as np
from PIL import Image
%matplotlib inline
import math
Step 0 - Load Data
In [2]:
MAIN_FOLDER = 'IOP_data_last'
FUNDING_FILE = MAIN_FOLDER+'/Funding Data/32.physics-funding-innovateUK-10yr.csv'
In [3]:
# Load the whole dataset into a Pandas Data Frame
data_frame = pd.read_csv(FUNDING_FILE)
# Describe the content, fields, values, mean
print 'There are ' + str(len(data_frame)) + ' lines in the dataset.'
print data_frame.describe()
# Show the first lines so we can get an idea
data_frame.head(n=3)
There are 14360 lines in the dataset.
       leadOrgDepartment        amount
count                0.0  1.436000e+04
mean                 NaN  3.517925e+05
std                  NaN  2.641695e+06
min                  NaN  0.000000e+00
25%                  NaN  1.420975e+04
50%                  NaN  7.334450e+04
75%                  NaN  1.726248e+05
max                  NaN  1.762500e+08
Out[3]:
project_id	title	status	grantCategory	leadOrgDepartment	leadOrg_id	name	postCode	postCode1	postCode2	fund_id	start	end	amount	fundOrg_id	fundrOrg_name	Subject
0	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	Construction Site Monitoring	Closed	Innovation Voucher	NaN	5AC92CD6-6FD8-40BD-996B-5B17733A4627	Suave Uav Enterprises Ltd	WN4 0XH	WN4	0XH	3ECE74BB-599F-449A-A5B8-6F0882AEEA24	2013-10-01+01:00	2014-04-30+01:00	5000	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	Innovate UK	No specified subject
1	0004CBDD-8A27-4FE1-B261-6018E79CFA49	Project COMVIDIA: COrrelated Movement and Vide...	Closed	Feasibility Study	NaN	AD736215-65EC-4F2C-8C1B-7818B83F6310	Iproov Limited	WC1V 7HP	WC1V	7HP	BC3B480B-9F97-44D5-A7AA-73ACF355DDE3	2015-04-01+01:00	2016-03-31+01:00	108834	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	Innovate UK	No specified subject
2	00053689-2879-4985-8E5C-4A3169D6CDE8	De Montfort University And Association for Pub...	Closed	Knowledge Transfer Partnership	NaN	DF259B19-FD17-4588-BFEF-AD08F1193451	De Montfort University	LE1 9BH	LE1	9BH	1EC04504-18A2-42C0-AED2-D64151F7E1B7	2011-01-01Z	2013-01-31Z	83564	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	Innovate UK	No specified subject
In [4]:
# Example of fields
pd.DataFrame.from_dict(data_frame.loc[0:3]).T
Out[4]:
0	1	2	3
project_id	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	0004CBDD-8A27-4FE1-B261-6018E79CFA49	00053689-2879-4985-8E5C-4A3169D6CDE8	000E3525-5B75-4935-BA4D-A87366270A15
title	Construction Site Monitoring	Project COMVIDIA: COrrelated Movement and Vide...	De Montfort University And Association for Pub...	ECO-ZORB
status	Closed	Closed	Closed	Closed
grantCategory	Innovation Voucher	Feasibility Study	Knowledge Transfer Partnership	Smart - Proof of Concept
leadOrgDepartment	NaN	NaN	NaN	NaN
leadOrg_id	5AC92CD6-6FD8-40BD-996B-5B17733A4627	AD736215-65EC-4F2C-8C1B-7818B83F6310	DF259B19-FD17-4588-BFEF-AD08F1193451	D21CE261-84AA-43BA-9CF8-F938939B423F
name	Suave Uav Enterprises Ltd	Iproov Limited	De Montfort University	Mid-UK Recycling Limited
postCode	WN4 0XH	WC1V 7HP	LE1 9BH	NG34 8RS
postCode1	WN4	WC1V	LE1	NG34
postCode2	0XH	7HP	9BH	8RS
fund_id	3ECE74BB-599F-449A-A5B8-6F0882AEEA24	BC3B480B-9F97-44D5-A7AA-73ACF355DDE3	1EC04504-18A2-42C0-AED2-D64151F7E1B7	60F3E9F3-6EAE-42EA-8FE0-27EA813E29D1
start	2013-10-01+01:00	2015-04-01+01:00	2011-01-01Z	2013-04-01+01:00
end	2014-04-30+01:00	2016-03-31+01:00	2013-01-31Z	2014-06-30+01:00
amount	5000	108834	83564	99996
fundOrg_id	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74
fundrOrg_name	Innovate UK	Innovate UK	Innovate UK	Innovate UK
Subject	No specified subject	No specified subject	No specified subject	No specified subject
In [5]:
data_frame['grantCategory'].groupby([data_frame["grantCategory"]]).count().plot(kind="bar",figsize=(15,3))
Out[5]:
<matplotlib.axes._subplots.AxesSubplot at 0x112b40fd0>

In [6]:
# data_frame['Subject'].groupby([data_frame["Subject"]]).count().plot(kind="bar",figsize=(15,10))
In [7]:
print "Distribution of the amount of Fundings"
data_frame.hist(column='amount',bins=100, figsize=(15,3))
Distribution of the amount of Fundings
Out[7]:
array([[<matplotlib.axes._subplots.AxesSubplot object at 0x11168f150>]], dtype=object)

In [8]:
print "Distribution of the amount of Fundings, for fundings > 5000 GPB"
data_frame.ix[(data_frame['amount']>5000)].hist(column='amount',bins=200, figsize=(12,6))
len(data_frame.ix[(data_frame['amount']>5000)])
Distribution of the amount of Fundings, for fundings > 5000 GPB
/Users/xavier/anaconda/envs/py27/lib/python2.7/site-packages/ipykernel/__main__.py:2: DeprecationWarning: 
.ix is deprecated. Please use
.loc for label based indexing or
.iloc for positional indexing

See the documentation here:
http://pandas.pydata.org/pandas-docs/stable/indexing.html#deprecate_ix
  from ipykernel import kernelapp as app
Out[8]:
10910

In [ ]:

In [9]:
# Show the Department
pd.DataFrame.from_dict(data_frame['leadOrgDepartment'].value_counts()).head(n=10)
# To display them all or save it to csv 
#data_frame['leadOrgDepartment'].value_counts()
Out[9]:
leadOrgDepartment
In [ ]:

In [ ]:

In [10]:
pd.DataFrame.from_dict(data_frame['project_id'].value_counts()).head(n=10)
Out[10]:
project_id
DCB388E8-C3F9-42E0-82E6-62244C500E75	1
B8A11521-DD7E-4E62-A30E-C0A8F8F1E4A2	1
7501F114-E026-4074-A0E5-CE3E4E03E109	1
6D73CBF9-0C41-4814-B4E7-F294E99F9723	1
EBA69C6F-85F6-44F7-B253-21515609C2F7	1
082982C7-A9D3-4511-95CB-717E2FA1062D	1
FF90A4E3-AC9A-420E-BF07-0ABDD891825B	1
EC2D58C1-7276-41BD-BA0C-9572CF8F0FFF	1
B64DD874-0907-456C-B2F1-7F3E24149A67	1
7DB9FAA1-17D2-4692-9EF0-38FD2E407355	1
Step 1 - Remove fields not used
In [11]:
# Show the different fields available in the dataset
print ', '.join(list(data_frame))
pd.DataFrame.from_dict(data_frame.loc[0:3]).T
project_id, title, status, grantCategory, leadOrgDepartment, leadOrg_id, name, postCode, postCode1, postCode2, fund_id, start, end, amount, fundOrg_id, fundrOrg_name, Subject
Out[11]:
0	1	2	3
project_id	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	0004CBDD-8A27-4FE1-B261-6018E79CFA49	00053689-2879-4985-8E5C-4A3169D6CDE8	000E3525-5B75-4935-BA4D-A87366270A15
title	Construction Site Monitoring	Project COMVIDIA: COrrelated Movement and Vide...	De Montfort University And Association for Pub...	ECO-ZORB
status	Closed	Closed	Closed	Closed
grantCategory	Innovation Voucher	Feasibility Study	Knowledge Transfer Partnership	Smart - Proof of Concept
leadOrgDepartment	NaN	NaN	NaN	NaN
leadOrg_id	5AC92CD6-6FD8-40BD-996B-5B17733A4627	AD736215-65EC-4F2C-8C1B-7818B83F6310	DF259B19-FD17-4588-BFEF-AD08F1193451	D21CE261-84AA-43BA-9CF8-F938939B423F
name	Suave Uav Enterprises Ltd	Iproov Limited	De Montfort University	Mid-UK Recycling Limited
postCode	WN4 0XH	WC1V 7HP	LE1 9BH	NG34 8RS
postCode1	WN4	WC1V	LE1	NG34
postCode2	0XH	7HP	9BH	8RS
fund_id	3ECE74BB-599F-449A-A5B8-6F0882AEEA24	BC3B480B-9F97-44D5-A7AA-73ACF355DDE3	1EC04504-18A2-42C0-AED2-D64151F7E1B7	60F3E9F3-6EAE-42EA-8FE0-27EA813E29D1
start	2013-10-01+01:00	2015-04-01+01:00	2011-01-01Z	2013-04-01+01:00
end	2014-04-30+01:00	2016-03-31+01:00	2013-01-31Z	2014-06-30+01:00
amount	5000	108834	83564	99996
fundOrg_id	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74	E18E2F0F-AC7D-4E02-9559-669F7C8FEC74
fundrOrg_name	Innovate UK	Innovate UK	Innovate UK	Innovate UK
Subject	No specified subject	No specified subject	No specified subject	No specified subject
In [12]:
to_keep = ['project_id',        
'grantCategory',
'status',   
 'title',
 'name',
 'start',
'end',
 'amount']
In [13]:
# Remove fields
for a in list(data_frame):
    if a not in to_keep:
        del data_frame[a]
data_frame.head(n=2)
Out[13]:
project_id	title	status	grantCategory	name	start	end	amount
0	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	Construction Site Monitoring	Closed	Innovation Voucher	Suave Uav Enterprises Ltd	2013-10-01+01:00	2014-04-30+01:00	5000
1	0004CBDD-8A27-4FE1-B261-6018E79CFA49	Project COMVIDIA: COrrelated Movement and Vide...	Closed	Feasibility Study	Iproov Limited	2015-04-01+01:00	2016-03-31+01:00	108834
Step 2 - Remove duplicates
No percentage anymore

In [14]:
len(data_frame)
Out[14]:
14360
In [ ]:

Step 3 - Add Latitude and Longitude
Based on LeadOrg and Google Places Api

In [15]:
FOLDER_GOOGLE_API = 'GOOGLE_API'
GOOGLE_API_KEY = "AIzaSyC5Tos_Ymyikbo_2ahqJYBSut578gyjwlQ"
In [16]:
# Google API Funtion
def usePlacesApi (query_text):
    text = str(query_text)
    if not len(text) > 0:
        print 'ERROR ' + query_text
        return 'ERROR'
    text = text.replace('/',' ').replace('\n',' ').replace('\\',' ').replace('"',' ').replace(' ','%20')
    if len(text.replace('%20','')) == 0:
        return 'ERROR'
    FOLDER = FOLDER_GOOGLE_API
    if os.path.isfile(FOLDER+'/'+text+'.json'):
        with open(FOLDER+'/'+text+'.json') as f:
            return json.load(f)
    
    elif os.path.isfile(FOLDER+'/'+str(query_text)+'.json'):
        with open(FOLDER+'/'+str(query_text)+'.json') as f:
            return json.load(f)
        
    else:
        API_KEY = GOOGLE_API_KEY
        url = "https://maps.googleapis.com/maps/api/place/textsearch/json?query="+text+"&key="+API_KEY
        data = json.load(urllib2.urlopen(url))
        if(data['status'])=='OK' or data['status'] == 'ZERO_RESULTS':
            with open(FOLDER+'/'+text+'.json', 'w') as outfile:
                json.dump(data, outfile)
                return data
        else:
            print 'ERROR: ' + query_text
In [17]:
data_frame['Latitude'] = 0.
data_frame['Longitude'] = 0.

# Do the job
for index, row in data_frame.iterrows():
    data = usePlacesApi(row['name'])['results']
#     print row['org']
    if len(data) > 0:
        data_ = data[0]
        data_frame.set_value(index,'Longitude',data_["geometry"]["location"]["lng"])
        data_frame.set_value(index,'Latitude',data_["geometry"]["location"]["lat"])
data_frame.head()


## JUST THE GOOGLE PLACES API, WITH COUNTER
# total = len(data_frame)
# progression = 0

# for index, row in data_frame.iterrows():
#     if math.floor(100*index/total) > progression:
#         progression = math.floor(100*index/total)
#         print str(progression) + ' %'
#     usePlacesApi(row['leadOrg'])
Out[17]:
project_id	title	status	grantCategory	name	start	end	amount	Latitude	Longitude
0	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	Construction Site Monitoring	Closed	Innovation Voucher	Suave Uav Enterprises Ltd	2013-10-01+01:00	2014-04-30+01:00	5000	53.495627	-2.680482
1	0004CBDD-8A27-4FE1-B261-6018E79CFA49	Project COMVIDIA: COrrelated Movement and Vide...	Closed	Feasibility Study	Iproov Limited	2015-04-01+01:00	2016-03-31+01:00	108834	0.000000	0.000000
2	00053689-2879-4985-8E5C-4A3169D6CDE8	De Montfort University And Association for Pub...	Closed	Knowledge Transfer Partnership	De Montfort University	2011-01-01Z	2013-01-31Z	83564	52.629226	-1.139687
3	000E3525-5B75-4935-BA4D-A87366270A15	ECO-ZORB	Closed	Smart - Proof of Concept	Mid-UK Recycling Limited	2013-04-01+01:00	2014-06-30+01:00	99996	53.024178	-0.588702
4	000F5D48-ED35-48EC-8399-FF2038D63036	IDEAhaus	Closed	Smart - Proof of Market	Green Triangle Studio Ltd	2014-02-01Z	2015-01-31Z	24999	53.437576	-2.267385
Step 3.5 - Filter what is not in the UK
In [18]:
print len(data_frame)
# Filter data not in the UK
data_frame = data_frame.ix[
    (data_frame['Latitude']>49.18) & 
    (data_frame['Latitude'] < 62.603) & 
    (data_frame['Longitude'] > -11.250) & 
    (data_frame['Longitude'] < 2.131)
]
'We now have ' + str(len(data_frame)) + ' lines in the dataset.'
14360
Out[18]:
'We now have 9868 lines in the dataset.'
Steps 4,5,6 - Remove NaN and 0
In [19]:
# data_frame = data_frame.ix[pd.notnull(data_frame['amount'])]
# # data_frame = data_frame.ix[(data_frame['amount'] > 5000)]
# data_frame = data_frame.ix[pd.notnull(data_frame['research_subject'])]
# data_frame = data_frame.ix[pd.notnull(data_frame['leadOrg'])]
# data_frame = data_frame.ix[pd.notnull(data_frame['title'])]
# data_frame = data_frame.ix[pd.notnull(data_frame['start'])]
'We now have ' + str(len(data_frame)) + ' lines in the dataset.'
Out[19]:
'We now have 9868 lines in the dataset.'
Optional, let's keep just one subject per university and title
Mainly for scholarships

In [20]:
# data_frame = data_frame.sort_values(by="percentage",ascending=False).drop_duplicates(subset=['title','leadOrg'],keep='first')
'We now have ' + str(len(data_frame)) + ' lines in the dataset.'
Out[20]:
'We now have 9868 lines in the dataset.'
Convert Date (start)
In [21]:
data_frame['start'] = pd.to_datetime(data_frame['start'].apply(lambda x:x.split('+')[0].split('Z')[0]))
In [22]:
data_frame.head(n=3)
Out[22]:
project_id	title	status	grantCategory	name	start	end	amount	Latitude	Longitude
0	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	Construction Site Monitoring	Closed	Innovation Voucher	Suave Uav Enterprises Ltd	2013-10-01	2014-04-30+01:00	5000	53.495627	-2.680482
2	00053689-2879-4985-8E5C-4A3169D6CDE8	De Montfort University And Association for Pub...	Closed	Knowledge Transfer Partnership	De Montfort University	2011-01-01	2013-01-31Z	83564	52.629226	-1.139687
3	000E3525-5B75-4935-BA4D-A87366270A15	ECO-ZORB	Closed	Smart - Proof of Concept	Mid-UK Recycling Limited	2013-04-01	2014-06-30+01:00	99996	53.024178	-0.588702
Step 8 - Rename Columns
In [23]:
data_frame_to_export = data_frame.rename(columns={
    "amount":"total",
    "name":"org",
    "grantCategory":"Grant Type",
    "status":"Status",
})
data_frame_to_export.head()
Out[23]:
project_id	title	Status	Grant Type	org	start	end	total	Latitude	Longitude
0	00006CB7-61E0-4946-B7DB-DAE09ED63DE4	Construction Site Monitoring	Closed	Innovation Voucher	Suave Uav Enterprises Ltd	2013-10-01	2014-04-30+01:00	5000	53.495627	-2.680482
2	00053689-2879-4985-8E5C-4A3169D6CDE8	De Montfort University And Association for Pub...	Closed	Knowledge Transfer Partnership	De Montfort University	2011-01-01	2013-01-31Z	83564	52.629226	-1.139687
3	000E3525-5B75-4935-BA4D-A87366270A15	ECO-ZORB	Closed	Smart - Proof of Concept	Mid-UK Recycling Limited	2013-04-01	2014-06-30+01:00	99996	53.024178	-0.588702
4	000F5D48-ED35-48EC-8399-FF2038D63036	IDEAhaus	Closed	Smart - Proof of Market	Green Triangle Studio Ltd	2014-02-01	2015-01-31Z	24999	53.437576	-2.267385
5	001A5DCA-B484-4621-9EDA-5B51FF7E11A3	Smart Combination Respiratory Medicines for Op...	Closed	Collaborative Research & Development	Prosonix Limited	2013-01-01	2014-07-31+01:00	1217637	51.716055	-1.218016
Step 9 - Export
In [24]:
data_frame_to_export.to_csv('static/data/fundings_innovate_UK.csv', header=True, encoding='utf-8')
PLAYGROUND
