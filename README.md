
# XML

## Introduction

In this lecture, you'll continue investigating new formats for datasets. Specifically, you'll investigate another of the most popular data formats for the web: XML. 

## Objectives
You will be able to:
* Use the XML module to load and parse XML data
* Compare and contrast JSON and XML as data interchange types

## XML

XML stands for 'Extensible Markup Language'. You may note the acronym's similarity to HTML; HyperText Markup Language. While HTML contains information for how to display a page, XML is used to store the data and content of the page itself. Like HTML, XML uses tags to separate and organize data in a hierarchical manner. Here's a brief preview of an XML file:  

<img src="images/xml_preview2.png" width="850">


## Loading XML Data

Prebuilt Python modules exist that will give you a powerful starting point for accessing and manipulating the underlying data within XML files. 

### The XML Module

You can check out the full details of the XML package here:  
https://docs.python.org/3.6/library/xml.html#  
but for now, you'll simply be using a submodule, ElementTree:   
https://docs.python.org/3.6/library/xml.etree.elementtree.html#module-xml.etree.ElementTree  

Notice the nested structure of the XML file:  

<img src="images/xml_preview2.png" width="850">

Compare and contrast this nested data structure with the brief preview of the same file above, now in JSON: 

<img src="images/json_preview.png" width="850">

JSON files are much simpler to read than XML files! Nonetheless, learning how to work with XML files will come in handy when learning to parse HTML, which you'll encounter soon in the section about web scraping.

### Parsing XML files

When parsing the data, you'll have to navigate through this hierarchical structure. This is the idea behind the `ElementTree` submodule. You'll start with a root note and then iterate over its children, each of which should have a tag (the name in <angle_brackets\>) and an associated attribute (the data between the two angle brackets <start\> data <stop\>).


```python
import xml.etree.ElementTree as ET
```

First you create the tree and retrieve the root tag.


```python
tree = ET.parse('nyc_2001_campaign_finance.xml')
root = tree.getroot()
```

Afterwards, you can iterate through the root node's children:


```python
for child in root:
    print(child.tag, child.attrib)
```

    row {}


Due to the nested structure, you often have to dig further down the tree:


```python
#Count is added here to limit the number of results
count = 0
for child in root:
    print('Child:\n')
    print(child.tag, child.attrib)
    print('Grandchildren:')
    for grandchild in child:
        count += 1
        if count < 10:
            print(grandchild.tag, grandchild.attrib)
    print('\n\n')
```

    Child:
    
    row {}
    Grandchildren:
    row {'_id': '1', '_uuid': 'E3E9CC9F-7443-43F6-94AF-B5A0F802DBA1', '_position': '1', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/1'}
    row {'_id': '2', '_uuid': '9D257416-581A-4C42-85CC-B6EAD9DED97F', '_position': '2', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/2'}
    row {'_id': '3', '_uuid': 'B80D7891-93CF-49E8-86E8-182B618E68F2', '_position': '3', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/3'}
    row {'_id': '4', '_uuid': 'BB012003-78F5-406D-8A87-7FF8A425EE3F', '_position': '4', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/4'}
    row {'_id': '5', '_uuid': '945825F9-2F5D-47C2-A16B-75B93E61E1AD', '_position': '5', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/5'}
    row {'_id': '6', '_uuid': '9546F502-39D6-4340-B37E-60682EB22274', '_position': '6', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/6'}
    row {'_id': '7', '_uuid': '4B6C74AD-17A0-4B7E-973A-2592D68A687D', '_position': '7', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/7'}
    row {'_id': '8', '_uuid': 'ABD22A5E-B8DA-446F-82BC-93AA11AF99DF', '_position': '8', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/8'}
    row {'_id': '9', '_uuid': '7CD36FB5-600F-44F5-A10C-CB3434B6805F', '_position': '9', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/9'}
    
    
    


Due to the nested structure, there is also a convenience method .iter() that allows you to iterate through all sub generations, regardless of depth.


```python
count = 0
for element in root.iter():
    count += 1
    if count < 10:
        print(element.tag, element.attrib)
```

    response {}
    row {}
    row {'_id': '1', '_uuid': 'E3E9CC9F-7443-43F6-94AF-B5A0F802DBA1', '_position': '1', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/1'}
    candid {}
    candname {}
    officeboro {}
    canclass {}
    row {'_id': '2', '_uuid': '9D257416-581A-4C42-85CC-B6EAD9DED97F', '_position': '2', '_address': 'https://data.cityofnewyork.us/resource/_8dhd-zvi6/2'}
    election {}


With some finesse, you could also extract all of these row tags into a dataframe....


```python
import pandas as pd
```


```python
dfs = []
for n, element in enumerate(root.iter('row')):
    if n > 0:
        dfs.append(pd.DataFrame.from_dict(element.attrib, orient='index').transpose())
df = pd.concat(dfs)
print(len(df))
df.head()
```

    285





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>_id</th>
      <th>_uuid</th>
      <th>_position</th>
      <th>_address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>E3E9CC9F-7443-43F6-94AF-B5A0F802DBA1</td>
      <td>1</td>
      <td>https://data.cityofnewyork.us/resource/_8dhd-z...</td>
    </tr>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>9D257416-581A-4C42-85CC-B6EAD9DED97F</td>
      <td>2</td>
      <td>https://data.cityofnewyork.us/resource/_8dhd-z...</td>
    </tr>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>B80D7891-93CF-49E8-86E8-182B618E68F2</td>
      <td>3</td>
      <td>https://data.cityofnewyork.us/resource/_8dhd-z...</td>
    </tr>
    <tr>
      <th>0</th>
      <td>4</td>
      <td>BB012003-78F5-406D-8A87-7FF8A425EE3F</td>
      <td>4</td>
      <td>https://data.cityofnewyork.us/resource/_8dhd-z...</td>
    </tr>
    <tr>
      <th>0</th>
      <td>5</td>
      <td>945825F9-2F5D-47C2-A16B-75B93E61E1AD</td>
      <td>5</td>
      <td>https://data.cityofnewyork.us/resource/_8dhd-z...</td>
    </tr>
  </tbody>
</table>
</div>



### Shew!
As you can see, parsing XML can get a bit complicated. It's a useful example for web scraping as HTML will have a similar structure that you'll need to exploit. That said, XML is an outdated format, and JSON is the new standard. 

Files using the JSON format are simpler and more flexible than files in XML format. The JSON format was introduced after XML, and was meant to streamline many data transportation issues existing at the time.

## Summary
As you can see, there's still a lot going on here with the deeply nested structure of some of these data files. In the upcoming lab, you'll get a chance to practice loading files and conducting some initial preview of the data as you did here.
