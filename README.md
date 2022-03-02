# super-bowl-history-pt2
Continued to use the Wikipedia API but interacted with it directly (previously used an intermediate API) to continue pulling up Super Bowl data from Wikipedia.

For part 2 will attempt to get more specific in game data such

*   as points per quarter (and if applicable Overtime)
*   the MVP: player and position
*   Head Coach
*   Favorite Team Going into match
*   Cost of 30 second Commercial: Cost at time

To this end note that each wikipedia page for a superbowl is in the following form:
https://en.wikipedia.org/wiki/Super_Bowl_?? where ?? is the name of a particular Super Bowl. So if we intend to get data from Superbowl I, the link would be https://en.wikipedia.org/wiki/Super_Bowl_I,
for Superbowl II: https://en.wikipedia.org/wiki/Super_Bowl_II and so on. This time around we still want to use the Wikpedia API but now we want to use it directly (before were using a Python module to do the heavy work for us but now we have to do it ourselves). 


```python
import pandas as pd
import requests
!pip install wikitextparser
import wikitextparser as wtp
```

    Requirement already satisfied: wikitextparser in /usr/local/lib/python3.7/dist-packages (0.48.0)
    Requirement already satisfied: wcwidth in /usr/local/lib/python3.7/dist-packages (from wikitextparser) (0.2.5)
    Requirement already satisfied: regex in /usr/local/lib/python3.7/dist-packages (from wikitextparser) (2019.12.20)
    


```python
# Python3 program to convert
# integer value to roman values
  
# Function to convert integer to Roman values
# Adapted from https://www.geeksforgeeks.org/python-program-to-convert-integer-to-roman/
def printRoman(number):
    num = [1, 4, 5, 9, 10, 40, 50, 90,
        100, 400, 500, 900, 1000]
    sym = ["I", "IV", "V", "IX", "X", "XL",
        "L", "XC", "C", "CD", "D", "CM", "M"]
    i = 12
    roman_number = ""
    while number:
        div = number // num[i]
        number %= num[i]

        while div:
            roman_number += sym[i]
            div -= 1
        i -= 1
    return roman_number
```

Wikipedia allows us to get information from 50 pages at a time. To do so we need to pass in the names of the pages separated by a "|" character. In the following code we create the full string of pages that we will request data from.


```python
titles_param = ""
sup_text = "Super_Bowl_"
for i in range(1,49):
  #full_text = sup_text + printRoman(i)
  titles_param += sup_text + printRoman(i) + "|"
print(titles_param)
```

    Super_Bowl_I|Super_Bowl_II|Super_Bowl_III|Super_Bowl_IV|Super_Bowl_V|Super_Bowl_VI|Super_Bowl_VII|Super_Bowl_VIII|Super_Bowl_IX|Super_Bowl_X|Super_Bowl_XI|Super_Bowl_XII|Super_Bowl_XIII|Super_Bowl_XIV|Super_Bowl_XV|Super_Bowl_XVI|Super_Bowl_XVII|Super_Bowl_XVIII|Super_Bowl_XIX|Super_Bowl_XX|Super_Bowl_XXI|Super_Bowl_XXII|Super_Bowl_XXIII|Super_Bowl_XXIV|Super_Bowl_XXV|Super_Bowl_XXVI|Super_Bowl_XXVII|Super_Bowl_XXVIII|Super_Bowl_XXIX|Super_Bowl_XXX|Super_Bowl_XXXI|Super_Bowl_XXXII|Super_Bowl_XXXIII|Super_Bowl_XXXIV|Super_Bowl_XXXV|Super_Bowl_XXXVI|Super_Bowl_XXXVII|Super_Bowl_XXXVIII|Super_Bowl_XXXIX|Super_Bowl_XL|Super_Bowl_XLI|Super_Bowl_XLII|Super_Bowl_XLIII|Super_Bowl_XLIV|Super_Bowl_XLV|Super_Bowl_XLVI|Super_Bowl_XLVII|Super_Bowl_XLVIII|
    


```python
# Code from the WIKI-API best practices section, create a session instance to
# increase likelihood our request is completed. Pass our generated titles Paramter
# along with a request to get our data back in json format.
S = requests.Session()

URL = "https://en.wikipedia.org/w/api.php"

PARAMS = {
	"action": "query",
	"format": "json",
	"prop": "revisions",
	"titles": titles_param,
	"rvprop": "content",
	"rvslots": "*"
}

R = S.get(url=URL, params=PARAMS)
DATA  = R.json()
```


```python
# Example method of accessing a page's xml data 
DATA["query"]["pages"]["1227585"]["revisions"][0]["slots"]["main"]["*"]
```




    '{{Short description|2007 National Football League championship game}}\n{{Redirect|2007 Super Bowl|the Super Bowl that was played at the completion of the 2007 season|Super Bowl XLII}}\n{{Use mdy dates|date=January 2019}}...




```python
# example of section of page we will be scraping data from
parsed = wtp.parse(ex)
parsed.templates[3]
```




    Template('{{Infobox Super Bowl\n| type = sb\n| name = XLI\n| image = Super Bowl XLI logo.svg\n| visitor = [[2006 Indianapolis Colts season|Indianapolis Colts]]\n| visitor_abbr = IND\n| visitor_conf ...



NOTE: this Template() Object is generated from the wikitextparser module. As the name implies, this module is meant to help parse trough wiki-text. These templates are sections of the xml page that can be repeated for future use. The data we want is always in a "Infobox Super Bowl" Template so we just need to search for templates of this kind.


```python
wiki_templates = []
for page in DATA["query"]["pages"]:
  if int(page) != -1:
    page_template = wtp.parse(DATA["query"]["pages"][page]["revisions"][0]["slots"]["main"]["*"]).templates
    match = [s for s in page_template if "Infobox Super Bowl" in s]
    wiki_templates.append(match[0])

```

Now we need to get the remaining data from the remaining Super Bowls. Somewhat unfortunate for us Superbowl 50 used a different naming convention so we need to account for that otherwise code runs the same as before when we created the large string of pages we wanted to query.


```python
titles_param = ""
sup_text = "Super_Bowl_"
for i in range(49, 57):
  #full_text = sup_text + printRoman(i)
  if i == 50:
    titles_param += sup_text + str(50) + "|"
  elif i == 56:
    titles_param += sup_text+ printRoman(i)
  else:
    titles_param += sup_text + printRoman(i) + "|"
print(titles_param)
```

    Super_Bowl_XLIX|Super_Bowl_50|Super_Bowl_LI|Super_Bowl_LII|Super_Bowl_LIII|Super_Bowl_LIV|Super_Bowl_LV|Super_Bowl_LVI
    


```python
PARAMS = {
	"action": "query",
	"format": "json",
	"prop": "revisions",
	"titles": titles_param,
	"rvprop": "content",
	"rvslots": "*"
}

R = S.get(url=URL, params=PARAMS)
DATA  = R.json()
```


```python
# run this cell block to better see how data is returned but do note there
# is a lot of information
DATA
```


```python
# some newer wikipedia pages (ex the following) use a different naming convention
# for the template we want to use so just need to account for that
# wtp.parse(DATA["query"]["pages"]["53119758"]["revisions"][0]["slots"]["main"]["*"]).templates[3]
# run this block to understand how templates are structured
```

```python
# In short we want to search for the following templates in each Superbowl page
matchers = ["Infobox Super Bowl", "Infobox NFL game"]
for page in DATA["query"]["pages"]:
  if int(page) != -1:
    page_template = wtp.parse(DATA["query"]["pages"][page]["revisions"][0]["slots"]["main"]["*"]).templates

    match = [s for s in page_template if any(xs in s for xs in matchers)]
    # we should only get 1 match so we can just append the remaining data directly
    wiki_templates.append(match[0])
```


```python
# have now added all remaining superbowl data
# wiki_templates[-10:]
# can verify by uncommenting this code
```


```python
# In each template we have "Arguments" which are somewhat like json key-value
# pairs. In each template we have we will only look for specific data. The following shows
# some arguments
wiki_templates[16].arguments[0:5]
```




    [Argument('| type = sb\n'),
     Argument('| name = XLI\n'),
     Argument('| image = Super Bowl XLI logo.svg\n'),
     Argument('| visitor = [[2006 Indianapolis Colts season|Indianapolis Colts]]\n'),
     Argument('| visitor_abbr = IND\n')]



Simplest solution to working with this Argument class is just to use a string keyword to search for a specific argument. Not pretty but the Argument's class
doesn't have a method to access data directly (like Arguments['name'] is not possible) so this is the next best thing.


```python
 matchers = ["| name ","| visitor ", "| visitor_coach ", "| home ", "| home_coach ", "| visitor_qtr1 ", 
              "| visitor_qtr2 ", "| visitor_qtr3 ", "| visitor_qtr4 ", "| home_qtr1 ", 
              "| home_qtr2 ", "| home_qtr3 ", "| home_qtr4 ", "| odds ", "| MVP ", "| commercial ", "|commercial"]

dict_data = {}

for x in wiki_templates:
  
  matching = [s for s in x.arguments if any(xs in s for xs in matchers)]
  
  for y in matching:
    txt = str(y).split("=")
    if txt[0] in dict_data:
      dict_data[txt[0]].append(txt[1].rstrip('\n'))
    else:
      # unfortunately, all but 1 page follow the same format so we need to put a special case for it
      if txt[0] == "|commercial":
        dict_data["| commercial "].append(txt[1].rstrip('\n'))
      else:
        dict_data[txt[0]] = [txt[1].rstrip('\n')]
```


```python
superbowl_history_df = pd.DataFrame.from_dict(dict_data)
```


```python
# We have passed in all the data but clearly we will need to do some heavy cleaning
# to make the data useable
display(superbowl_history_df.info())
superbowl_history_df.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 56 entries, 0 to 55
    Data columns (total 16 columns):
     #   Column            Non-Null Count  Dtype 
    ---  ------            --------------  ----- 
     0   | name            56 non-null     object
     1   | visitor         56 non-null     object
     2   | home            56 non-null     object
     3   | visitor_coach   56 non-null     object
     4   | home_coach      56 non-null     object
     5   | visitor_qtr1    56 non-null     object
     6   | visitor_qtr2    56 non-null     object
     7   | visitor_qtr3    56 non-null     object
     8   | visitor_qtr4    56 non-null     object
     9   | home_qtr1       56 non-null     object
     10  | home_qtr2       56 non-null     object
     11  | home_qtr3       56 non-null     object
     12  | home_qtr4       56 non-null     object
     13  | odds            56 non-null     object
     14  | MVP             56 non-null     object
     15  | commercial      56 non-null     object
    dtypes: object(16)
    memory usage: 7.1+ KB
    


    None



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>| name</th>
      <th>| visitor</th>
      <th>| home</th>
      <th>| visitor_coach</th>
      <th>| home_coach</th>
      <th>| visitor_qtr1</th>
      <th>| visitor_qtr2</th>
      <th>| visitor_qtr3</th>
      <th>| visitor_qtr4</th>
      <th>| home_qtr1</th>
      <th>| home_qtr2</th>
      <th>| home_qtr3</th>
      <th>| home_qtr4</th>
      <th>| odds</th>
      <th>| MVP</th>
      <th>| commercial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>[[1966 Kansas City Chiefs season|Kansas City ...</td>
      <td>[[1966 Green Bay Packers season|Green Bay Pac...</td>
      <td>[[Hank Stram]]</td>
      <td>[[Vince Lombardi]]</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web|url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>[[1967 Green Bay Packers season|Green Bay Pac...</td>
      <td>[[1967 Oakland Raiders season|Oakland Raiders]]</td>
      <td>[[Vince Lombardi]]</td>
      <td>[[John Rauch]]</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web | url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>[[1968 New York Jets season|New York Jets]]</td>
      <td>[[1968 Baltimore Colts season|Baltimore Colts]]</td>
      <td>[[Weeb Ewbank]]</td>
      <td>[[Don Shula]]</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>[[Joe Namath]], [[quarterback]]</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>[[1969 Minnesota Vikings season|Minnesota Vik...</td>
      <td>[[1969 Kansas City Chiefs season|Kansas City ...</td>
      <td>[[Bud Grant]]</td>
      <td>[[Hank Stram]]</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>[[Len Dawson]], [[quarterback]]</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>[[1974 Pittsburgh Steelers season|Pittsburgh ...</td>
      <td>[[1974 Minnesota Vikings season|Minnesota Vik...</td>
      <td>[[Chuck Noll]]</td>
      <td>[[Bud Grant]]</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3&lt;ref&gt;{{cite news|last</td>
      <td>[[Franco Harris]], [[Fullback (American footb...</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
  </tbody>
</table>
</div>



Things to do:
*   Change All columns names into better ones
*   Change the scores for each quarter into integers
*   For visitor and home teams display only the team names i.e. right now its [[YEAR TEAM SEASON|TEAM]] so change to just TEAM
*   For visitor and home coach format is [[COACH]] change to just COACH (there are some where this is not true i.e. different format so fix as well)
*   Change odds format currently it is like TEAM by VALUE (extra information) so remove the extra information
*   Change MVP format: current form [[Player, Role]] to Player and Role in different columns
*   Change Commerical format: current form '$XX,XXX' change to just numerical number


```python
# create new names
new_names = {"| name ": "Game", "| visitor ": "Visitor Team", "| home ": "Home Team", "| visitor_coach ": "Visitor Coach", "| home_coach ": "Home Coach", "| visitor_qtr1 ": "Visitor Q1", "| visitor_qtr2 ": "Visitor Q2", "| visitor_qtr3 ": "Visitor Q3","| visitor_qtr4 ": "Visitor Q4","| home_qtr1 ": "Home Q1", "| home_qtr2 ": "Home Q2", "| home_qtr3 ": "Home Q3","| home_qtr4 ": "Home Q4", "| odds ": "Odds", "| MVP ": "MVP",	"| commercial ": "Commercial Cost"}
superbowl_history_df.rename(columns=new_names, inplace=True, errors="raise")
```


```python
# before calling the to_numeric() function we want to make sure we can even do so
# i.e. the data is actually all of type numeric
# we can see that this is not the case for any column. 
display(superbowl_history_df["Visitor Q1"].str.isnumeric().value_counts())
display(superbowl_history_df["Visitor Q2"].str.isnumeric().value_counts())
display(superbowl_history_df["Visitor Q3"].str.isnumeric().value_counts())
display(superbowl_history_df["Visitor Q4"].str.isnumeric().value_counts())
display(superbowl_history_df["Home Q1"].str.isnumeric().value_counts())
display(superbowl_history_df["Home Q2"].str.isnumeric().value_counts())
display(superbowl_history_df["Home Q3"].str.isnumeric().value_counts())
display(superbowl_history_df["Home Q4"].str.isnumeric().value_counts())
```


    False    56
    Name: Visitor Q1, dtype: int64



    False    56
    Name: Visitor Q2, dtype: int64



    False    56
    Name: Visitor Q3, dtype: int64



    False    56
    Name: Visitor Q4, dtype: int64



    False    56
    Name: Home Q1, dtype: int64



    False    56
    Name: Home Q2, dtype: int64



    False    56
    Name: Home Q3, dtype: int64



    False    56
    Name: Home Q4, dtype: int64



```python
# reason why we cant convert directly is due to some leading whitespace so just remove that
superbowl_history_df["Visitor Q1"] = superbowl_history_df["Visitor Q1"].str.strip()
superbowl_history_df["Visitor Q2"] = superbowl_history_df["Visitor Q2"].str.strip()
superbowl_history_df["Visitor Q3"] = superbowl_history_df["Visitor Q3"].str.strip()
superbowl_history_df["Visitor Q4"] = superbowl_history_df["Visitor Q4"].str.strip()
superbowl_history_df["Home Q1"] = superbowl_history_df["Home Q1"].str.strip()
superbowl_history_df["Home Q2"] = superbowl_history_df["Home Q2"].str.strip()
superbowl_history_df["Home Q3"] = superbowl_history_df["Home Q3"].str.strip()
superbowl_history_df["Home Q4"] =superbowl_history_df["Home Q4"].str.strip()
```


```python
superbowl_history_df["Visitor Q1"] = pd.to_numeric(superbowl_history_df["Visitor Q1"])
superbowl_history_df["Visitor Q2"] = pd.to_numeric(superbowl_history_df["Visitor Q2"])
superbowl_history_df["Visitor Q3"] = pd.to_numeric(superbowl_history_df["Visitor Q3"])
superbowl_history_df["Visitor Q4"] = pd.to_numeric(superbowl_history_df["Visitor Q4"])
superbowl_history_df["Home Q1"] = pd.to_numeric(superbowl_history_df["Home Q1"])
superbowl_history_df["Home Q2"] = pd.to_numeric(superbowl_history_df["Home Q2"])
superbowl_history_df["Home Q3"] = pd.to_numeric(superbowl_history_df["Home Q3"])
superbowl_history_df["Home Q4"] = pd.to_numeric(superbowl_history_df["Home Q4"])
```


```python
# sucessfully converted the columns into integer type
display(superbowl_history_df.info())
superbowl_history_df.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 56 entries, 0 to 55
    Data columns (total 16 columns):
     #   Column           Non-Null Count  Dtype 
    ---  ------           --------------  ----- 
     0   Game             56 non-null     object
     1   Visitor Team     56 non-null     object
     2   Home Team        56 non-null     object
     3   Visitor Coach    56 non-null     object
     4   Home Coach       56 non-null     object
     5   Visitor Q1       56 non-null     int64 
     6   Visitor Q2       56 non-null     int64 
     7   Visitor Q3       56 non-null     int64 
     8   Visitor Q4       56 non-null     int64 
     9   Home Q1          56 non-null     int64 
     10  Home Q2          56 non-null     int64 
     11  Home Q3          56 non-null     int64 
     12  Home Q4          56 non-null     int64 
     13  Odds             56 non-null     object
     14  MVP              56 non-null     object
     15  Commercial Cost  56 non-null     object
    dtypes: int64(8), object(8)
    memory usage: 7.1+ KB
    


    None






<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>[[1966 Kansas City Chiefs season|Kansas City ...</td>
      <td>[[1966 Green Bay Packers season|Green Bay Pac...</td>
      <td>[[Hank Stram]]</td>
      <td>[[Vince Lombardi]]</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web|url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>[[1967 Green Bay Packers season|Green Bay Pac...</td>
      <td>[[1967 Oakland Raiders season|Oakland Raiders]]</td>
      <td>[[Vince Lombardi]]</td>
      <td>[[John Rauch]]</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web | url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>[[1968 New York Jets season|New York Jets]]</td>
      <td>[[1968 Baltimore Colts season|Baltimore Colts]]</td>
      <td>[[Weeb Ewbank]]</td>
      <td>[[Don Shula]]</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>[[Joe Namath]], [[quarterback]]</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>[[1969 Minnesota Vikings season|Minnesota Vik...</td>
      <td>[[1969 Kansas City Chiefs season|Kansas City ...</td>
      <td>[[Bud Grant]]</td>
      <td>[[Hank Stram]]</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>[[Len Dawson]], [[quarterback]]</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>[[1974 Pittsburgh Steelers season|Pittsburgh ...</td>
      <td>[[1974 Minnesota Vikings season|Minnesota Vik...</td>
      <td>[[Chuck Noll]]</td>
      <td>[[Bud Grant]]</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3&lt;ref&gt;{{cite news|last</td>
      <td>[[Franco Harris]], [[Fullback (American footb...</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
  </tbody>
</table>
</div>
 



Things to do:
*   ~Change All columns names into better ones~
*   ~Change the scores for each quarter into integers~
*   For visitor and home teams display only the team names i.e. right now its [[YEAR TEAM SEASON|TEAM]] so change to just TEAM
*   For visitor and home coach format is [[COACH]] change to just COACH (there are some where this is not true i.e. different format so fix as well)
*   Change odds format currently it is like TEAM by VALUE (extra information) so remove the extra information
*   Change MVP format: current form [[Player, Role]] to Player and Role in different columns
*   Change Commerical format: current form '$XX,XXX' change to just numerical number


```python
# we can use a lambda expression to clean up the format of the Superbowl Teams
# basically just want the name after the "|" character in [[YEAR TEAM SEASON|TEAM]]
# so first we split by "|" and then parse the text with the lambda expression
superbowl_history_df["Visitor Team"] = superbowl_history_df["Visitor Team"].str.split("|").apply(lambda x: x[1][:-2])
superbowl_history_df["Home Team"] = superbowl_history_df["Home Team"].str.split("|").apply(lambda x: x[1][:-2])
```


```python
superbowl_history_df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>[[Hank Stram]]</td>
      <td>[[Vince Lombardi]]</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web|url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>[[Vince Lombardi]]</td>
      <td>[[John Rauch]]</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web | url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>[[Weeb Ewbank]]</td>
      <td>[[Don Shula]]</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>[[Joe Namath]], [[quarterback]]</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>[[Bud Grant]]</td>
      <td>[[Hank Stram]]</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>[[Len Dawson]], [[quarterback]]</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>Pittsburgh Steelers</td>
      <td>Minnesota Vikings</td>
      <td>[[Chuck Noll]]</td>
      <td>[[Bud Grant]]</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3&lt;ref&gt;{{cite news|last</td>
      <td>[[Franco Harris]], [[Fullback (American footb...</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
  </tbody>
</table>
</div>



```python
# Team names are not uniform as shown below: 
display(superbowl_history_df["Visitor Coach"][0])
display(superbowl_history_df["Visitor Coach"][25])
```


    ' [[Hank Stram]]'



    ' [[Bill Walsh (American football coach)|Bill Walsh]]'



```python
# using a lambda expression with if/else we can account for this data non-uniformity
superbowl_history_df["Visitor Coach"] = superbowl_history_df["Visitor Coach"].apply(lambda x: x.split("|")[1][:-2]  if "|" in x else x.lstrip()[2:-2])
superbowl_history_df["Home Coach"] = superbowl_history_df["Home Coach"].apply(lambda x: x.split("|")[1][:-2]  if "|" in x else x.lstrip()[2:-2])
```


```python
superbowl_history_df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web|url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14&lt;ref&gt;{{cite web | url</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>[[Joe Namath]], [[quarterback]]</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>[[Len Dawson]], [[quarterback]]</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>Pittsburgh Steelers</td>
      <td>Minnesota Vikings</td>
      <td>Chuck Noll</td>
      <td>Bud Grant</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3&lt;ref&gt;{{cite news|last</td>
      <td>[[Franco Harris]], [[Fullback (American footb...</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
  </tbody>
</table>
</div>
 


Things to do:
*   ~Change All columns names into better ones~
*   ~Change the scores for each quarter into integers~
*   ~For visitor and home teams display only the team names i.e. right now its [[YEAR TEAM SEASON|TEAM]] so change to just TEAM~
*   ~For visitor and home coach format is [[COACH]] change to just COACH (there are some where this is not true i.e. different format so fix as well)~
*   Change odds format currently it is like TEAM by VALUE (extra information) so remove the extra information
*   Change MVP format: current form [[Player, Role]] to Player and Role in different columns
*   Change Commerical format: current form '$XX,XXX' change to just numerical number


```python
# data for odds is also not uniform as shown below
display(superbowl_history_df["Odds"][0])
display(superbowl_history_df["Odds"][5])
```


    ' Packers by 14<ref>{{cite web|url'



    ' Colts by 2.5'



```python
# can account for this in our lambda expression
superbowl_history_df["Odds"] = superbowl_history_df["Odds"].apply(lambda x: x.split("<")[0][1:]  if "<ref" in x else x.lstrip())
```


```python
superbowl_history_df.head()
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>[[Bart Starr]], [[quarterback]]</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>[[Joe Namath]], [[quarterback]]</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>[[Len Dawson]], [[quarterback]]</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>Pittsburgh Steelers</td>
      <td>Minnesota Vikings</td>
      <td>Chuck Noll</td>
      <td>Bud Grant</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3</td>
      <td>[[Franco Harris]], [[Fullback (American footb...</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
  </tbody>
</table>
</div>
  


Things to do:
*   ~Change All columns names into better ones~
*   ~Change the scores for each quarter into integers~
*   ~For visitor and home teams display only the team names i.e. right now its [[YEAR TEAM SEASON|TEAM]] so change to just TEAM~
*   ~For visitor and home coach format is [[COACH]] change to just COACH (there are some where this is not true i.e. different format so fix as well)~
*   ~Change odds format currently it is like TEAM by VALUE (extra information) so remove the extra information~
*   Change MVP format: current form [[Player, Role]] to Player and Role in different columns
*   Change Commerical format: current form '$XX,XXX' change to just numerical number


```python
# similar situation of MVP data is not uniform but we use a different route to clean this data
# first remove text after <ref as that is just a reference link so not needed
superbowl_history_df["MVP"] = superbowl_history_df["MVP"].apply(lambda x: x.split("<ref")[0][1:] if "<ref" in x else x)
```


```python
# except for 1 superbowl, there has only been 1 MVP
superbowl_history_df["MVP"].apply(lambda x: len(x.split(","))).value_counts()
```




    2    55
    3     1
    Name: MVP, dtype: int64




```python
# because of this edge case, its best to use if/else statements to clean the
# data i.e. we could use lambda expressions but it gets to messy to read
new_mvp_data = []
for x in superbowl_history_df["MVP"]:
  # if there is only 1 MVP
  if len(x.split(",")) == 2:
    # create substrings. 
    # substring_one is the name
    # substring_two is the position
    substring_one, substring_two = x.split(",")
    # some data has a "|" character indicating a reference so need to parse
    # that out
    if "|" in substring_one:
      substring_one = substring_one.lstrip().split("|")[1][:-2]
    else:
        substring_one = substring_one.lstrip()[2:-2]
    if "|" in substring_two:
      substring_two = substring_two.lstrip().split("|")[1][:-2].lower()
    else:
      substring_two = substring_two.lstrip()[2:-2].lower()
    # create a final string of the previous substrings which is now in 
    # a better format
    final_string = "{} - {}".format(substring_one, substring_two)
    new_mvp_data.append(final_string)
  else:
    # since we literally only have edge case we can "cheat" and type the
    # data manually. Normally not ideal but again its only 1 edge case
    final_string = "Harvey Martin, Randy White - defensive end, defensive tackle"
    new_mvp_data.append(final_string)

superbowl_history_df["MVP"] = new_mvp_data
```


```python
superbowl_history_df.head(12)
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$42,000 (Both CBS and NBC)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>Joe Namath - quarterback</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>Len Dawson - quarterback</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>Pittsburgh Steelers</td>
      <td>Minnesota Vikings</td>
      <td>Chuck Noll</td>
      <td>Bud Grant</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3</td>
      <td>Franco Harris - fullback</td>
      <td>$107,000&lt;ref&gt;{{cite news|title</td>
    </tr>
    <tr>
      <th>5</th>
      <td>V</td>
      <td>Baltimore Colts</td>
      <td>Dallas Cowboys</td>
      <td>Don McCafferty</td>
      <td>Tom Landry</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>10</td>
      <td>3</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>Colts by 2.5</td>
      <td>Chuck Howley - linebacker</td>
      <td>$72,000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>VI</td>
      <td>Dallas Cowboys</td>
      <td>Miami Dolphins</td>
      <td>Tom Landry</td>
      <td>Don Shula</td>
      <td>3</td>
      <td>7</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>Cowboys by 6</td>
      <td>Roger Staubach - quarterback</td>
      <td>$86,000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>VII</td>
      <td>Miami Dolphins</td>
      <td>Washington Redskins</td>
      <td>Don Shula</td>
      <td>George Allen</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Redskins by 1</td>
      <td>Jake Scott - safety</td>
      <td>$88,000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>VIII</td>
      <td>Minnesota Vikings</td>
      <td>Miami Dolphins</td>
      <td>Bud Grant</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>14</td>
      <td>3</td>
      <td>7</td>
      <td>0</td>
      <td>Dolphins by 6.5</td>
      <td>Larry Csonka - fullback</td>
      <td>$103,000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>X</td>
      <td>Dallas Cowboys</td>
      <td>Pittsburgh Steelers</td>
      <td>Tom Landry</td>
      <td>Chuck Noll</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>14</td>
      <td>Steelers by 7</td>
      <td>Lynn Swann - wide receiver</td>
      <td>$110,000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>XI</td>
      <td>Oakland Raiders</td>
      <td>Minnesota Vikings</td>
      <td>John Madden</td>
      <td>Bud Grant</td>
      <td>0</td>
      <td>16</td>
      <td>3</td>
      <td>13</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>Raiders by 4</td>
      <td>Fred Biletnikoff - wide receiver</td>
      <td>$125,000</td>
    </tr>
    <tr>
      <th>11</th>
      <td>XII</td>
      <td>Dallas Cowboys</td>
      <td>Denver Broncos</td>
      <td>Tom Landry</td>
      <td>Red Miller</td>
      <td>10</td>
      <td>3</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>Cowboys by 6</td>
      <td>Harvey Martin, Randy White - defensive end, de...</td>
      <td>$162,000</td>
    </tr>
  </tbody>
</table>
</div>



```python
# using lambda expressions we can remove reference links, &nbsp; characters, 
# and leading whitespace from 'Commerical Cost' values
superbowl_history_df["Commercial Cost"] = superbowl_history_df["Commercial Cost"].apply(lambda x: x.split("<")[0][1:] if "<ref" in x else x)
superbowl_history_df["Commercial Cost"] = superbowl_history_df["Commercial Cost"].apply(lambda x: x.replace("&nbsp;", " ") if "&nbsp;" in x else x)
superbowl_history_df["Commercial Cost"] = superbowl_history_df["Commercial Cost"].str.lstrip()
```


```python
# we have a few issues. Such as text (first case) random letters in the values 
# (second case). Third case shows how we want to leave data
display(superbowl_history_df["Commercial Cost"][0])
display(superbowl_history_df["Commercial Cost"][16])
display(superbowl_history_df["Commercial Cost"][1])
```


    '$42,000 (Both CBS and NBC)'



    'S$2.6 million'



    '$54,000'



```python
# since there are only two issues we can manually fix them
superbowl_history_df.at[0, "Commercial Cost"] = superbowl_history_df["Commercial Cost"][0].split(" ")[0]
superbowl_history_df.at[16, "Commercial Cost"] = superbowl_history_df["Commercial Cost"][16][1:]
```


```python
# our data is much cleaner now
superbowl_history_df.head()
```



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$42,000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>Joe Namath - quarterback</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>Len Dawson - quarterback</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>IX</td>
      <td>Pittsburgh Steelers</td>
      <td>Minnesota Vikings</td>
      <td>Chuck Noll</td>
      <td>Bud Grant</td>
      <td>0</td>
      <td>2</td>
      <td>7</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>Steelers by 3</td>
      <td>Franco Harris - fullback</td>
      <td>$107,000</td>
    </tr>
  </tbody>
</table>
</div>




Things to do:
*   ~Change All columns names into better ones~
*   ~Change the scores for each quarter into integers~
*   ~For visitor and home teams display only the team names i.e. right now its [[YEAR TEAM SEASON|TEAM]] so change to just TEAM~
*   ~For visitor and home coach format is [[COACH]] change to just COACH (there are some where this is not true i.e. different format so fix as well)~
*   ~Change odds format currently it is like TEAM by VALUE (extra information) so remove the extra information~
*   ~Change MVP format: current form [[Player, Role]] to Player and Role in different columns~
*   ~Change Commerical format: current form '$XX,XXX' change to just numerical number~


```python
# there is some leading white space that wasn't notices earlier but no issue
superbowl_history_df["Game"] = superbowl_history_df["Game"].str.strip()
```


```python
# we load in our previous csv we created earlier
prev = pd.read_csv("superbowl.csv", index_col=0)
```


```python
prev.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61946</td>
      <td>Norm Schachter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75546</td>
      <td>Jack Vest</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>Tom Bell</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80562</td>
      <td>John McDonough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79204</td>
      <td>Norm Schachter</td>
    </tr>
  </tbody>
</table>
</div>



```python
# perform a merge to create an even larger dataset
final_data = prev.merge(superbowl_history_df, how="inner", on="Game")
```


```python
# as we can see we have more data for each game
final_data.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61946</td>
      <td>Norm Schachter</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$42,000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75546</td>
      <td>Jack Vest</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>Tom Bell</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>Colts by 19½</td>
      <td>Joe Namath - quarterback</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80562</td>
      <td>John McDonough</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>Vikings by 13½</td>
      <td>Len Dawson - quarterback</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79204</td>
      <td>Norm Schachter</td>
      <td>Baltimore Colts</td>
      <td>Dallas Cowboys</td>
      <td>Don McCafferty</td>
      <td>Tom Landry</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>10</td>
      <td>3</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>Colts by 2.5</td>
      <td>Chuck Howley - linebacker</td>
      <td>$72,000</td>
    </tr>
  </tbody>
</table>
</div>



```python
# as a sanity check, we should check that we loaded in all of the
# game score data correctly. To that end just want to check if the values for 
# each quarter sum up to either of the final socres
for index, row in final_data.iterrows():
  visitor_score = row['Visitor Q1'] + row['Visitor Q2'] + row['Visitor Q3'] + row['Visitor Q4']
  home_score = row['Home Q1'] + row['Home Q2'] + row['Home Q3'] + row['Home Q4']
  if row['Winner Score'] != visitor_score:
    if row['Winner Score'] != home_score:
      print("Check Winner Score for line number {}".format(index))
  if row['Loser Score'] != visitor_score:
    if row['Loser Score'] != home_score:
      print("Check Loser Score for line number {}".format(index))
```

    Check Winner Score for line number 50
    

From our code above we should note that for one of our rows (row number 50) the final score does not match the sum of the visitor/home quarter sum scores.


```python
final_data.iloc[50]
```




    Game                                    LI
    Date                            2017-02-05
    Winning team          New England Patriots
    Winner Score                            34
    Losing team                Atlanta Falcons
    Loser Score                             28
    Venue                          NRG Stadium
    City                               Houston
    State                                Texas
    Attendance                           70807
    Referee                      Carl Cheffers
    Visitor Team          New England Patriots
    Home Team                  Atlanta Falcons
    Visitor Coach               Bill Belichick
    Home Coach                       Dan Quinn
    Visitor Q1                               0
    Visitor Q2                               3
    Visitor Q3                               6
    Visitor Q4                              19
    Home Q1                                  0
    Home Q2                                 21
    Home Q3                                  7
    Home Q4                                  0
    Odds                         Patriots by 3
    MVP                Tom Brady - quarterback
    Commercial Cost              $5.02 million
    Name: 50, dtype: object



Indeed the Winner Score is 34 but both Visitor/Home socres sum to 28. A quick google search indicates that this is the only Super Bowl in history to go to overtime


```python
import numpy as np
```


```python
# Because of our 'discovery' we should create Visiitor/Home OT columns pre-allocated
# with nan values signifying that most games don't go to overtime
final_data.insert(19, "Visitor OT", np.nan)
final_data.insert(24, "Home OT", np.nan)
```


```python
final_data.head()
```






<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Visitor OT</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Home OT</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I</td>
      <td>1967-01-15</td>
      <td>Green Bay Packers</td>
      <td>35</td>
      <td>Kansas City Chiefs</td>
      <td>10</td>
      <td>Los Angeles Memorial Coliseum</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>61946</td>
      <td>Norm Schachter</td>
      <td>Kansas City Chiefs</td>
      <td>Green Bay Packers</td>
      <td>Hank Stram</td>
      <td>Vince Lombardi</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>NaN</td>
      <td>7</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>NaN</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$42,000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>II</td>
      <td>1968-01-14</td>
      <td>Green Bay Packers</td>
      <td>33</td>
      <td>Oakland Raiders</td>
      <td>14</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75546</td>
      <td>Jack Vest</td>
      <td>Green Bay Packers</td>
      <td>Oakland Raiders</td>
      <td>Vince Lombardi</td>
      <td>John Rauch</td>
      <td>3</td>
      <td>13</td>
      <td>10</td>
      <td>7</td>
      <td>NaN</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>NaN</td>
      <td>Packers by 14</td>
      <td>Bart Starr - quarterback</td>
      <td>$54,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>III</td>
      <td>1969-01-12</td>
      <td>New York Jets</td>
      <td>16</td>
      <td>Baltimore Colts</td>
      <td>7</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>75389</td>
      <td>Tom Bell</td>
      <td>New York Jets</td>
      <td>Baltimore Colts</td>
      <td>Weeb Ewbank</td>
      <td>Don Shula</td>
      <td>0</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>NaN</td>
      <td>Colts by 19½</td>
      <td>Joe Namath - quarterback</td>
      <td>$55,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IV</td>
      <td>1970-01-11</td>
      <td>Kansas City Chiefs</td>
      <td>23</td>
      <td>Minnesota Vikings</td>
      <td>7</td>
      <td>Tulane Stadium</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>80562</td>
      <td>John McDonough</td>
      <td>Minnesota Vikings</td>
      <td>Kansas City Chiefs</td>
      <td>Bud Grant</td>
      <td>Hank Stram</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>NaN</td>
      <td>3</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>NaN</td>
      <td>Vikings by 13½</td>
      <td>Len Dawson - quarterback</td>
      <td>$78,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>V</td>
      <td>1971-01-17</td>
      <td>Baltimore Colts</td>
      <td>16</td>
      <td>Dallas Cowboys</td>
      <td>13</td>
      <td>Miami Orange Bowl</td>
      <td>Miami</td>
      <td>Florida</td>
      <td>79204</td>
      <td>Norm Schachter</td>
      <td>Baltimore Colts</td>
      <td>Dallas Cowboys</td>
      <td>Don McCafferty</td>
      <td>Tom Landry</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>10</td>
      <td>NaN</td>
      <td>3</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>NaN</td>
      <td>Colts by 2.5</td>
      <td>Chuck Howley - linebacker</td>
      <td>$72,000</td>
    </tr>
  </tbody>
</table>
</div>






```python
# manually update the data
final_data.at[50, "Visitor OT"] = 6
final_data.at[50, "Home OT"] = 0
```


```python
# our final dataset
final_data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 56 entries, 0 to 55
    Data columns (total 28 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   Game             56 non-null     object 
     1   Date             56 non-null     object 
     2   Winning team     56 non-null     object 
     3   Winner Score     56 non-null     int64  
     4   Losing team      56 non-null     object 
     5   Loser Score      56 non-null     int64  
     6   Venue            56 non-null     object 
     7   City             56 non-null     object 
     8   State            56 non-null     object 
     9   Attendance       56 non-null     int64  
     10  Referee          56 non-null     object 
     11  Visitor Team     56 non-null     object 
     12  Home Team        56 non-null     object 
     13  Visitor Coach    56 non-null     object 
     14  Home Coach       56 non-null     object 
     15  Visitor Q1       56 non-null     int64  
     16  Visitor Q2       56 non-null     int64  
     17  Visitor Q3       56 non-null     int64  
     18  Visitor Q4       56 non-null     int64  
     19  Visitor OT       1 non-null      float64
     20  Home Q1          56 non-null     int64  
     21  Home Q2          56 non-null     int64  
     22  Home Q3          56 non-null     int64  
     23  Home Q4          56 non-null     int64  
     24  Home OT          1 non-null      float64
     25  Odds             56 non-null     object 
     26  MVP              56 non-null     object 
     27  Commercial Cost  56 non-null     object 
    dtypes: float64(2), int64(11), object(15)
    memory usage: 14.7+ KB
    


```python
# Home/Visitor OT must remain as floats becasuse of NaN values being categorized
# as floats. indeed if we try to use the to_numeric function our column remains 
# as floats
pd.to_numeric(final_data["Visitor OT"])
```




```python
# succesful data update
final_data.tail(10)
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Game</th>
      <th>Date</th>
      <th>Winning team</th>
      <th>Winner Score</th>
      <th>Losing team</th>
      <th>Loser Score</th>
      <th>Venue</th>
      <th>City</th>
      <th>State</th>
      <th>Attendance</th>
      <th>Referee</th>
      <th>Visitor Team</th>
      <th>Home Team</th>
      <th>Visitor Coach</th>
      <th>Home Coach</th>
      <th>Visitor Q1</th>
      <th>Visitor Q2</th>
      <th>Visitor Q3</th>
      <th>Visitor Q4</th>
      <th>Visitor OT</th>
      <th>Home Q1</th>
      <th>Home Q2</th>
      <th>Home Q3</th>
      <th>Home Q4</th>
      <th>Home OT</th>
      <th>Odds</th>
      <th>MVP</th>
      <th>Commercial Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>46</th>
      <td>XLVII</td>
      <td>2013-02-03</td>
      <td>Baltimore Ravens</td>
      <td>34</td>
      <td>San Francisco 49ers</td>
      <td>31</td>
      <td>Mercedes-Benz Superdome</td>
      <td>New Orleans</td>
      <td>Louisiana</td>
      <td>71024</td>
      <td>Jerome Boger</td>
      <td>Baltimore Ravens</td>
      <td>San Francisco 49ers</td>
      <td>John Harbaugh</td>
      <td>Jim Harbaugh</td>
      <td>7</td>
      <td>14</td>
      <td>7</td>
      <td>6</td>
      <td>NaN</td>
      <td>3</td>
      <td>3</td>
      <td>17</td>
      <td>8</td>
      <td>NaN</td>
      <td>49ers by 4</td>
      <td>Joe Flacco - quarterback</td>
      <td>$4 million</td>
    </tr>
    <tr>
      <th>47</th>
      <td>XLVIII</td>
      <td>2014-02-02</td>
      <td>Seattle Seahawks</td>
      <td>43</td>
      <td>Denver Broncos</td>
      <td>8</td>
      <td>MetLife Stadium</td>
      <td>East Rutherford</td>
      <td>New Jersey</td>
      <td>82529</td>
      <td>Terry McAulay</td>
      <td>Seattle Seahawks</td>
      <td>Denver Broncos</td>
      <td>Pete Carroll</td>
      <td>John Fox</td>
      <td>8</td>
      <td>14</td>
      <td>14</td>
      <td>7</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>8</td>
      <td>0</td>
      <td>NaN</td>
      <td>Broncos by 2</td>
      <td>Malcolm Smith - linebacker</td>
      <td>$4 million</td>
    </tr>
    <tr>
      <th>48</th>
      <td>XLIX</td>
      <td>2015-02-01</td>
      <td>New England Patriots</td>
      <td>28</td>
      <td>Seattle Seahawks</td>
      <td>24</td>
      <td>University of Phoenix Stadium</td>
      <td>Glendale</td>
      <td>Arizona</td>
      <td>70288</td>
      <td>Bill Vinovich</td>
      <td>New England Patriots</td>
      <td>Seattle Seahawks</td>
      <td>Bill Belichick</td>
      <td>Pete Carroll</td>
      <td>0</td>
      <td>14</td>
      <td>0</td>
      <td>14</td>
      <td>NaN</td>
      <td>0</td>
      <td>14</td>
      <td>10</td>
      <td>0</td>
      <td>NaN</td>
      <td>Pick 'em (even/toss-up)</td>
      <td>Tom Brady - quarterback</td>
      <td>$4.5 million</td>
    </tr>
    <tr>
      <th>49</th>
      <td>50</td>
      <td>2016-02-07</td>
      <td>Denver Broncos</td>
      <td>24</td>
      <td>Carolina Panthers</td>
      <td>10</td>
      <td>Levi's Stadium</td>
      <td>Santa Clara</td>
      <td>California</td>
      <td>71088</td>
      <td>Clete Blakeman</td>
      <td>Carolina Panthers</td>
      <td>Denver Broncos</td>
      <td>Ron Rivera</td>
      <td>Gary Kubiak</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>3</td>
      <td>NaN</td>
      <td>10</td>
      <td>3</td>
      <td>3</td>
      <td>8</td>
      <td>NaN</td>
      <td>Panthers by 5.5</td>
      <td>Von Miller - linebacker</td>
      <td>$5 million</td>
    </tr>
    <tr>
      <th>50</th>
      <td>LI</td>
      <td>2017-02-05</td>
      <td>New England Patriots</td>
      <td>34</td>
      <td>Atlanta Falcons</td>
      <td>28</td>
      <td>NRG Stadium</td>
      <td>Houston</td>
      <td>Texas</td>
      <td>70807</td>
      <td>Carl Cheffers</td>
      <td>New England Patriots</td>
      <td>Atlanta Falcons</td>
      <td>Bill Belichick</td>
      <td>Dan Quinn</td>
      <td>0</td>
      <td>3</td>
      <td>6</td>
      <td>19</td>
      <td>6.0</td>
      <td>0</td>
      <td>21</td>
      <td>7</td>
      <td>0</td>
      <td>0.0</td>
      <td>Patriots by 3</td>
      <td>Tom Brady - quarterback</td>
      <td>$5.02 million</td>
    </tr>
    <tr>
      <th>51</th>
      <td>LII</td>
      <td>2018-02-04</td>
      <td>Philadelphia Eagles</td>
      <td>41</td>
      <td>New England Patriots</td>
      <td>33</td>
      <td>U.S. Bank Stadium</td>
      <td>Minneapolis</td>
      <td>Minnesota</td>
      <td>67612</td>
      <td>Gene Steratore</td>
      <td>Philadelphia Eagles</td>
      <td>New England Patriots</td>
      <td>Doug Pederson</td>
      <td>Bill Belichick</td>
      <td>9</td>
      <td>13</td>
      <td>7</td>
      <td>12</td>
      <td>NaN</td>
      <td>3</td>
      <td>9</td>
      <td>14</td>
      <td>7</td>
      <td>NaN</td>
      <td>Patriots by 5</td>
      <td>Nick Foles - quarterback</td>
      <td>$5 million</td>
    </tr>
    <tr>
      <th>52</th>
      <td>LIII</td>
      <td>2019-02-03</td>
      <td>New England Patriots</td>
      <td>13</td>
      <td>Los Angeles Rams</td>
      <td>3</td>
      <td>Mercedes-Benz Stadium</td>
      <td>Atlanta</td>
      <td>Georgia</td>
      <td>70081</td>
      <td>John Parry</td>
      <td>New England Patriots</td>
      <td>Los Angeles Rams</td>
      <td>Bill Belichick</td>
      <td>Sean McVay</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>10</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>NaN</td>
      <td>Patriots by 2.5</td>
      <td>Julian Edelman - wide receiver</td>
      <td>$5.25 million</td>
    </tr>
    <tr>
      <th>53</th>
      <td>LIV</td>
      <td>2020-02-02</td>
      <td>Kansas City Chiefs</td>
      <td>31</td>
      <td>San Francisco 49ers</td>
      <td>20</td>
      <td>Hard Rock Stadium</td>
      <td>Miami Gardens</td>
      <td>Florida</td>
      <td>62417</td>
      <td>Bill Vinovich</td>
      <td>San Francisco 49ers</td>
      <td>Kansas City Chiefs</td>
      <td>Kyle Shanahan</td>
      <td>Andy Reid</td>
      <td>3</td>
      <td>7</td>
      <td>10</td>
      <td>0</td>
      <td>NaN</td>
      <td>7</td>
      <td>3</td>
      <td>0</td>
      <td>21</td>
      <td>NaN</td>
      <td>Chiefs by 1.5</td>
      <td>Patrick Mahomes - quarterback</td>
      <td>$5.6 million</td>
    </tr>
    <tr>
      <th>54</th>
      <td>LV</td>
      <td>2021-02-07</td>
      <td>Tampa Bay Buccaneers</td>
      <td>31</td>
      <td>Kansas City Chiefs</td>
      <td>9</td>
      <td>Raymond James Stadium</td>
      <td>Tampa</td>
      <td>Florida</td>
      <td>24835</td>
      <td>Carl Cheffers</td>
      <td>Kansas City Chiefs</td>
      <td>Tampa Bay Buccaneers</td>
      <td>Andy Reid</td>
      <td>Bruce Arians</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>0</td>
      <td>NaN</td>
      <td>7</td>
      <td>14</td>
      <td>10</td>
      <td>0</td>
      <td>NaN</td>
      <td>Chiefs by 3</td>
      <td>Tom Brady - quarterback</td>
      <td>$5.6 million</td>
    </tr>
    <tr>
      <th>55</th>
      <td>LVI</td>
      <td>2022-02-13</td>
      <td>Los Angeles Rams</td>
      <td>23</td>
      <td>Cincinnati Bengals</td>
      <td>20</td>
      <td>SoFi Stadium</td>
      <td>Inglewood</td>
      <td>California</td>
      <td>70048</td>
      <td>Ron Torbert</td>
      <td>Los Angeles Rams</td>
      <td>Cincinnati Bengals</td>
      <td>Sean McVay</td>
      <td>Zac Taylor</td>
      <td>7</td>
      <td>6</td>
      <td>3</td>
      <td>7</td>
      <td>NaN</td>
      <td>3</td>
      <td>7</td>
      <td>10</td>
      <td>0</td>
      <td>NaN</td>
      <td>Rams by 4</td>
      <td>Cooper Kupp - wide receiver</td>
      <td>$6.5–7 million</td>
    </tr>
  </tbody>
</table>
</div>



```python
# now we can download the data - uncomment as necessary
#from google.colab import files
#final_data.to_csv("superbowl-final.csv", index=False)
#files.download("superbowl-final.csv")
```


    <IPython.core.display.Javascript object>



    <IPython.core.display.Javascript object>


