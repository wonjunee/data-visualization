
#### Note:
**2008.csv** file is too large so it is not in my git repository. If you want to run the scripts then you may have to download the file from http://stat-computing.org/dataexpo/2009/the-data.html.

# Parsing the data
### Final Project - Data Visualization Course
### Udacity Data Analyst Nanodegree

For this final project I will use 2008 flights data that I downloaded from http://stat-computing.org/dataexpo/2009/the-data.html. The file doesn't have the geographic coordination of each airport so I downloaded the file that contains the information of coordinates of each airport from https://gist.githubusercontent.com/tdreyno/4278655/raw/7b0762c09b519f40397e4c3e100b097d861f5588/airports.json.


First I will load the json file and save it as a dictionary.


```python
### import necessary libraries
import json

### Create an empty dictionary
airport_dict = {}

### load the json file
count = 0
with open("data/airport.json") as F:
    for i in json.load(F):
        try:
            airport_dict[i['code']] = {'lon': i['lon'], 'lat': i['lat']}
        except:
            print i['code'], ":", i['name']
            count += 1
```


```python
print "Saved airports:",len(airport_dict)
print "Excluded airports:", count
```

    Saved airports: 3885
    Excluded airports: 0


Now we will load the flights file and parse the data.

Since there are too many records in this file I will filter the january data only.

When you look at the data in **2008.csv** the same Destination and Origin are repeated at the same day with different hours. I will include only one of them and exclude redundant data.


```python
### load csv library
import csv

### List for avoiding redundancy
redun = ()

### load csv data
id_count = 0
with open("data/2008.csv") as csvfile:
    with open("data/airport_coord.csv", "wb") as wfile:
        fieldnames = ['id', 
                      'Dest', 'Dest_lon', 'Dest_lat', 
                      'Origin', 'Origin_lon', 'Origin_lat']
        writer = csv.DictWriter(wfile, fieldnames=fieldnames)
        writer.writeheader()
        
        ### write rows
        for i in csv.DictReader(csvfile):
            if int(i["Month"]) == 2:
                if i['Dest'] != 'OGD':
                    tmp_dict = {}
                    tmp_dict['Dest'] = i['Dest']
                    tmp_dict['Dest_lon'] = airport_dict[i['Dest']]['lon']
                    tmp_dict['Dest_lat'] = airport_dict[i['Dest']]['lat']
                    tmp_dict['Origin'] = i['Origin']
                    tmp_dict['Origin_lon'] = airport_dict[i['Origin']]['lon']
                    tmp_dict['Origin_lat'] = airport_dict[i['Origin']]['lat']
                    
                    ### Check if the item is redundant
                    if tmp_dict['Dest']+tmp_dict['Origin'] not in redun and \
                       tmp_dict['Origin']+tmp_dict['Dest'] not in redun:
                        redun+=((tmp_dict['Dest']+tmp_dict['Origin']),)
                        tmp_dict['id'] = id_count

                        ### Write rows
                        writer.writerow(tmp_dict)
                        id_count += 1
```

Only the records containing **OGD** are excluded. Everything else is saved as a csvfile.


```python
id_count-1
```




    2338



There are total **2338** records in the saved csv file. Stil there are too many so I will filter the data further. First I will check the most frequently appeared **Destination** and **Origin**.


```python
### Create a dictionary for Destinations and Origins
Dest_dict = {}

### Load airport_coord.csv file
with open("data/airport_coord.csv") as F:
    for i in csv.DictReader(F):
        ### Count Destinations
        if i["Dest"] in Dest_dict.keys():
            Dest_dict[i["Dest"]]+=1
        else:
            Dest_dict[i["Dest"]]=1

### Sort by value
import pprint
import operator
Dest_sorted = sorted(Dest_dict.items(), key=operator.itemgetter(1))

### Print out the result
print "Destinations"
top_Dest = ()
for i in range(1,31):
    print Dest_sorted[-i*3]
    top_Dest += (Dest_sorted[-i*3][0],)
```

    Destinations
    ('PHX', 75)
    ('LAS', 54)
    ('IAH', 49)
    ('SLC', 43)
    ('MEM', 37)
    ('MCI', 30)
    ('CLE', 29)
    ('BOS', 27)
    ('IAD', 25)
    ('BWI', 22)
    ('SAN', 21)
    ('SEA', 20)
    ('JAX', 17)
    ('CMH', 16)
    ('ORF', 15)
    ('CLT', 14)
    ('PBI', 14)
    ('SJC', 13)
    ('BNA', 13)
    ('OMA', 11)
    ('LIT', 11)
    ('TYS', 10)
    ('TUL', 9)
    ('GEG', 9)
    ('DAY', 8)
    ('ELP', 8)
    ('SAV', 8)
    ('PWM', 7)
    ('RNO', 7)
    ('MAF', 7)


Above is the list of top **30** names of airports that will be used in the data visualization project.


```python
### open and filter the data
id_num = 0
with open("data/airport_coord.csv") as F:
    with open("data/airport_filtered.csv", "wb") as WF:
        fieldnames = ['id',
                      'Dest', 'Dest_lon', 'Dest_lat', 
                      'Origin', 'Origin_lon', 'Origin_lat']
        writer = csv.DictWriter(WF, fieldnames=fieldnames)
        writer.writeheader()
        
        ### write rows
        for i in csv.DictReader(F):
            if i["Dest"] in top_Dest and i["Origin"] in top_Dest:
                i['id'] = id_num
                writer.writerow(i)
                id_num+=1
```


```python
id_num-1
```




    178



There are now total **178** records in the file. 

In this data visualization project, nodes and links properties in **D3.js** will be used to indicate the flight paths. So, I will parse the data to create arrays for nodes and links.


```python
### Create arrays of the number of edges of each node
edges_count = {}
with open("data/airport_filtered.csv") as F:
    for line in csv.DictReader(F):
        if line['Origin'] in edges_count.keys():
            edges_count[line['Origin']] += 1
        else:
            edges_count[line['Origin']] = 1
        
        if line['Dest'] in edges_count.keys():
            edges_count[line['Dest']] += 1
        else:
            edges_count[line['Dest']] = 1

pprint.pprint(edges_count)
```

    {'BNA': 14,
     'BOS': 17,
     'BWI': 18,
     'CLE': 17,
     'CLT': 20,
     'CMH': 12,
     'DAY': 4,
     'ELP': 5,
     'GEG': 6,
     'IAD': 16,
     'IAH': 27,
     'JAX': 9,
     'LAS': 25,
     'LIT': 6,
     'MAF': 2,
     'MCI': 15,
     'MEM': 17,
     'OMA': 8,
     'ORF': 7,
     'PBI': 6,
     'PHX': 21,
     'PWM': 3,
     'RNO': 8,
     'SAN': 17,
     'SAV': 5,
     'SEA': 15,
     'SJC': 9,
     'SLC': 19,
     'TUL': 7,
     'TYS': 3}



```python
### Create arrays of airports in the file
airports_list = {}
count = 0

import csv

### load airport_filtered file and save nodes
with open("data/airport_filtered.csv") as F:
    for line in csv.DictReader(F):
        if line['Dest'] not in airports_list.keys():
            airports_list[line['Dest']] = {"ind":count, "lon": float(line['Dest_lon']), "lat":float(line['Dest_lat'])}
            count += 1
        if line['Origin'] not in airports_list.keys():
            airports_list[line['Origin']] = {"ind":count, "lon": float(line['Origin_lon']), "lat":float(line['Origin_lat'])}
            count += 1

### Sort the dictionary by ind
airports_tuple = sorted(airports_list.iteritems(), key=lambda x:x[1])

### Save the tuple as a csv file
with open("data/nodes.csv", "wb") as F:
    fieldnames = ['code', 'id', 'size', 'lon', 'lat'] 
    writer = csv.DictWriter(F, fieldnames=fieldnames)
    writer.writeheader()

    ### write rows
    for k in airports_tuple:
        tmp_dict = {}
        tmp_dict['code'] = k[0]
        tmp_dict['id'] = k[1]['ind']
        tmp_dict['lon'] = k[1]['lon']
        tmp_dict['lat'] = k[1]['lat']
        tmp_dict['size'] = edges_count[k[0]]
        writer.writerow(tmp_dict)

### Save the edges as a csv file
with open("data/edges.csv", "wb") as F:
    fieldnames = ["source", "target", "Dest", "Origin"]
    writer = csv.DictWriter(F, fieldnames = fieldnames)
    writer.writeheader()
    
    ### load airport_filtered and save edges
    with open("airport_filtered.csv") as csvfile:
        for line in csv.DictReader(csvfile):
            tmp_dict = {}
            tmp_dict["source"] = airports_list[line['Origin']]['ind']
            tmp_dict["target"] = airports_list[line['Dest']]['ind']
            tmp_dict["Dest"] = line["Dest"]
            tmp_dict["Origin"] = line["Origin"]
            writer.writerow(tmp_dict)
```

Now the files are ready to be used in **D3.js** for data visualization. You can see the result in **final_project.html**.
