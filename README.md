# webscraping-IMDBwebsite
Using the technologies of Python and BeautifulSoup to web scrap an IMDB website and access the top-rated tv shows and their rank, year and ratings


Followed the youtube tutorial - https://www.youtube.com/watch?v=LCVSmkyB4v8

*The video explains the ways in which we can perform web scraping in Python using BeautifulSoup and load the desired dataset in an excel sheet.*

**Prerequisite Modules - 
- Requests Module - To access the website
- BeautifulSoup - To parse the HTML and access the tags in the website.

To Start off - 
1.  *Install Requests Module* 
```
!pip install requests 
#this will install the requests module.
```

2. *Install BeautifulSoup*
```
!pip install bs4
# this will install the bs4 library
```

3. *import the modules*
``` 
from bs4 import beautifulSoup
import requests
# importing modules
```

**What we are trying to do :

Trying to load the name, rank and imdb rating of a tv show into an excel sheet.

*Accessing HTML*
```
source = requests.get("https://www.imdb.com/chart/toptv/")

# this will access the response object and save it in the variable 'source'. The response object contains the HTML source code of the webpage.
```
In order to raise/throw an error if there is a problem with the url being used, we use the code
				`source.raise_for_status()
				
that will help raise an error.

-> We put the above code in a try and except block to verify and throw the errors whenever necessary.
	visit the link for more info : https://www.w3schools.com/python/python_try_except.asp

The code in try and except block will look like:

```
try:
	source = requests.get("https://www.imdb.com/chart/toptv/")
	source.raise_for_status() 
	soup = BeautifulSoup(source.text, 'html.parser')
	print(soup)

	tvshows = soup.find('tbody', class_="lister-list")
	print(tvshows) 

	tvshows = soup.find('tbody', class_="lister-   list").find_all('tr')
	
except Exception as e:
	print(e)
```

-> To change the response object into the text, we use the code
	`soup = BeautifulSoup(source.text, 'html.parser')
This will change the response object of html into a text, parse it and return a beautifulsoup object and store it in the variable 'soup'.

Now, if we  `print(soup)` , we can see how a parsed HTML webpage looks like.

We next go to the webpage to analyze the it. We need to look at the tags to extract the data we want.
In this case, the  we can see that the ==(tr)== tag has the list of all the movies and its ranks and ratings. So, we will first extract this ==tr tag== and then extract the data that we want. Now, all the (tr) tags are in the ==(body) tag==. So, we will first start by accessing the (body) tag.

Now, if we use the codeline  
	`tvshows = soup.find('tbody', class_="lister-list")` 
we will get access to the ==(tr) tags==.

`print(tvshows)`  will show the section of tbody tag that we wanted.

Since, there is only one (tbody tag) in the entire html page, .find works well. But, since there are many (tr tags), we should use ".find_all". 
- .find_all returns a list.
		`tvshows = soup.find('tbody', class_="lister-list").find_all('tr')`
This will find the list of tv shows.

Now, we are going to iterate through each tr tag using for loop and get access to td tags. Here, td tags hold informtion about a single tv show.

## 1.1 Extracting the name of the tvshow: 

```
#iterate each tr tag, and access td tag that will have the name of the tvshow.
#this will return the first tr tag.
for tvshow in tvshows:
    print(tvshow)
```

Next, we try accessing the td tag that contains the tvshow name:
```
for tvshow in tvshows:
    name = tvshow.find('td', class_= "titleColumn")
    print(name)
    
# finding each titlecoulmn in td tag that contains the name.
```

Then, we access the (a tag) that will have the show name only:

```
for tvshow in tvshows:
    name = tvshow.find('td', class_= "titleColumn").find_all('a')
    print(name)
    
# finding each <a tags> in td tags, for the title of the tv show.

```

We, can then finally access only the title of the tv show.

```
#finally accessing/extracting the title

for tvshow in tvshows:
    name = tvshow.find('td', class_= "titleColumn").a.text
    print(name)
```

## 1.2 Extracting the Rank of the TVshow:

To access the rank of the tvshow, we need to access the text of the td tag, because the rank not in any other tag, like the tvshow name, which is in a tag.

```
#extracting the rank of the tvshow
for tvshow in tvshows:
    rank = tvshow.find('td',class_= "titleColumn").text
    print(rank)
```

The above code will give all the details, because it will return the information of all the tags nested in the tag.

So, we will be using  `.get_text(strip=True)`   . `strip=True`  will strip all the newline characters and spaces.
This will give us a list of the tr tags neatly.

```#using .get_text,

for tvshow in tvshows:
    rank= tvshow.find('td',class_="titleColumn").get_text(strip=True)
    print(rank)

```


To get only the rank of the tvshow, we will use split

```
#split it at the dot to get only the rank.

for tvshow in tvshows:
    rank = tvshow.find('td',class_="titleColumn").get_text(strip=True).split(".")
    print(rank)

#this will return a list
```

Next, we can pass an index to get the element at that index Since the ranks are in the beginning i.e., in the zeroeth index, we can split it at zeroeth index.

```
#split at zeroeth index

for tvshow in tvshows:
    rank = tvshow.find('td',class_="titleColumn").get_text(strip=True).split(".")[0]
    print(rank)
```

## 1.3 Extracting the year:

To extract the year, we use the following code:

```
#extracting the year.

for tvshow in tvshows:
    year = tvshow.find('td',class_='titleColumn').span.text.strip('()')
    print(year)
```

## 1.4 Extracting the Ratings:

To extract the ratings, we can use the following piece of code;

```
#extracting the rating

for tvshow in tvshows:
    rating = tvshow.find('td',class_='ratingColumn imdbRating').strong.text
    print(rating)
```

###  🏁 The Whole Code Until This Point:

```
#the whole code

try:
    source = requests.get("https://www.imdb.com/chart/toptv/")
    source.raise_for_status()
    soup = BeautifulSoup(source.text,'html.parser')
    print(soup)
    
    for tvshow in tvshows:
        name = tvshow.find('td', class_= "titleColumn").a.text
        rank = tvshow.find('td',class_="titleColumn").get_text(strip=True).split(".")[0]
        year = tvshow.find('td',class_='titleColumn').span.text.strip('()')
        rating = tvshow.find('td',class_="ratingColumn imdbRating").strong.text
        print(rank, name, year, rating)
except Exception as e:
    print(e)
```

## 2. Loading the Excel Sheet
1. 
```
#creating and loading excel sheet

xl = openpyxl.Workbook()
print(xl.sheetnames)

#excelsheet has only one sheet
```
2. 
```
# changing the sheet title
sheet = xl.active
sheet.title = 'Top rated tvshows'

print(xl.sheetnames)
```
3. 
```
# the headings
sheet.append(['Rank','TVSHOW','Year','Rating'])
```


To save the excel file, we use    `xl.save('TVSHOW RATINGS')`

## 🆘 The Entire Code

```
#the whole code, including the excel files.

try:
    source = requests.get("https://www.imdb.com/chart/toptv/")
    source.raise_for_status()
    soup = BeautifulSoup(source.text,'html.parser')
    print(soup)
    
    for tvshow in tvshows:
        name = tvshow.find('td', class_= "titleColumn").a.text
        rank = tvshow.find('td',class_="titleColumn").get_text(strip=True).split(".")[0]
        year = tvshow.find('td',class_='titleColumn').span.text.strip('()')
        rating = tvshow.find('td',class_="ratingColumn imdbRating").strong.text
        print(rank, name, year, rating)
        sheet.append([rank, name, year, rating])
except Exception as e:
    print(e)
    
xl.save('IMDB tvshow ratings.xlsx')

```

Once you run this code, we can see that a new excel file in our project opens up, that contains the information of the imdb tvshows and its ratings.
