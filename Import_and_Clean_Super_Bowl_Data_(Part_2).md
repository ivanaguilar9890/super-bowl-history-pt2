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




    '{{Short description|2007 National Football League championship game}}\n{{Redirect|2007 Super Bowl|the Super Bowl that was played at the completion of the 2007 season|Super Bowl XLII}}\n{{Use mdy dates|date=January 2019}}\n{{Infobox Super Bowl\n| type = sb\n| name = XLI\n| image = Super Bowl XLI logo.svg\n| visitor = [[2006 Indianapolis Colts season|Indianapolis Colts]]\n| visitor_abbr = IND\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Tony Dungy]]\n| visitor_record = 12–4\n| visitor_place = 3\n| home = [[2006 Chicago Bears season|Chicago Bears]]\n| home_abbr = CHI\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Lovie Smith]]\n| home_record = 13–3\n| home_place = 1\n| visitor_total = 29\n| home_total = 17\n| visitor_qtr1 = 6\n| visitor_qtr2 = 10\n| visitor_qtr3 = 6\n| visitor_qtr4 = 7\n| home_qtr1 = 14\n| home_qtr2 = 0\n| home_qtr3 = 3\n| home_qtr4 = 0\n| date = {{start-date|February 4, 2007}}\n| stadium = [[Hard Rock Stadium|Dolphin Stadium]]\n| city = [[Miami Gardens, Florida]]\n| attendance = 74,512<ref name="attendance">{{cite web|title=Super Bowl Summaries|url=https://nflcommunications.com/media/Publications/2019%20Postseason%20Media%20Guide.pdf#page=113|publisher=NFL Enterprises, LLC|work=2019 NFL Postseason Media Guide|date=January 2, 2020|access-date=May 23, 2020}}</ref>\n| weather = Rain\n| odds = Colts by 7<ref>{{cite news|last=DiNitto|first=Marcus|title=Super Bowl Betting History – Underdogs on Recent Roll|url=http://linemakers.sportingnews.com/nfl/2015-01-25/super-bowl-betting-history-patriots-vs-seahawks-point-spread-line-vegas-odds|magazine=[[Sporting News]]|date=January 25, 2015|access-date=February 4, 2015}}</ref><ref>{{cite web|url=http://www.vegasinsider.com/nfl/superbowl/history/|title=Super Bowl History|publisher=Vegas Insider|access-date=February 4, 2015}}</ref>\n| MVP = [[Peyton Manning]], [[quarterback]]\n| anthem = [[Billy Joel]]\n| halftime = [[Prince (musician)|Prince]] and the [[Marching 100|Florida A&M University Marching 100]]\n| network = [[NFL on CBS|CBS]]\n| announcers = [[Jim Nantz]], [[Phil Simms]], [[Steve Tasker]], [[Solomon Wilcots]], [[Sam Ryan]] and [[Lesley Visser]]\n| coin_toss = [[Dan Marino]] and Norma Hunt (widow of [[Lamar Hunt]])<ref>{{cite web| url=http://sports.espn.go.com/nfl/playoffs06/news/story?id=2752126|title=Goodell wants players held accountable|author=[[ESPN]]|access-date=January 17, 2008}}</ref>\n| referee = [[Tony Corrente]]<ref>{{cite web| url=http://www.superbowl.com/news/story/9966329|title=Corrente to referee first Super Bowl|author=NFL.com|access-date=January 17, 2008 |archive-url = https://web.archive.org/web/20070509040851/http://www.superbowl.com/news/story/9966329|archive-date=May 9, 2007 |author-link= National Football League}}</ref>\n| HOFers = \'\'\'Colts:\'\'\' <!--PLEASE NOTE: "Future" in this case means players who have been\nOFFICIALLY inducted after this Super Bowl has been played. Players who have not yet been\nofficially inducted will be removed. Any addition of "presumed" players will also be\nremoved on grounds of [[WP:No Original Research]] and [[WP:Wikipedia is not a crystal ball]].\nSee also [[Pro Football Hall of Fame#Criticism]] on the criticism of HOF voters, who tend\nto have the habit of turning down "presumed, surefire" players for several years\n(like punter [[Ray Guy]] and QB [[Ken Stabler]]). Thank you for your cooperation.--> [[Bill Polian]] (general manager), [[Tony Dungy]] (head coach), [[Marvin Harrison]], [[Peyton Manning]] <br /> \'\'\'Bears:\'\'\' [[Brian Urlacher]]\n| rating = 42.6 (national)<ref>{{cite web|url=http://publications.mediapost.com/index.cfm?fuseaction=Articles.showArticleHomePage&art_aid=55057|archive-url=https://archive.today/20120724042825/http://publications.mediapost.com/index.cfm?fuseaction=Articles.showArticleHomePage&art_aid=55057|url-status=dead|archive-date=July 24, 2012|publisher=publications.mediapost.com|title=Blockbuster: Best Super Bowl Ratings In 7 Years|access-date=February 6, 2007}}</ref> <br/><small>(est. 93.2 million viewers)</small><ref>[http://www.superbowl.com/news/story/9976864 Colts-Bears draws No. 3 audience of all time] {{webarchive|url=https://web.archive.org/web/20070208000737/http://www.superbowl.com/news/story/9976864 |date=February 8, 2007 }} SuperBowl.com, February 5, 2007.</ref><br/>50.2 (Chicago)<ref>{{cite news | url=http://www.suntimes.com/business/feder/244726,CST-FIN-feder06.article | title=Super Bowl ratings fall short of record | access-date=February 6, 2007 | work=Chicago Sun-Times | first=Robert | last=Feder | date=February 6, 2007 | url-status=dead | archive-url=https://web.archive.org/web/20070207234741/http://www.suntimes.com/business/feder/244726%2CCST-FIN-feder06.article | archive-date=February 7, 2007 | df=mdy-all }}</ref><br/>55.5 ([[Indianapolis]])\n| share = 64 (national)<br/>77 (Chicago)<br/>83 (Indianapolis)\n|commercial=US$2.6 million<ref name=price/>\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Marv Albert]], [[Boomer Esiason]], [[John Dockery]] and [[Bonnie Bernstein]]\n| last = XL\n| next = XLII\n}}\n\'\'\'Super Bowl XLI\'\'\' was an [[American football]] game played between the [[American Football Conference]] (AFC) champion [[2006 Indianapolis Colts season|Indianapolis Colts]] and the [[National Football Conference]] (NFC) champion [[2006 Chicago Bears season|Chicago Bears]] to decide the [[National Football League]] (NFL) champion for the [[2006 NFL season|2006 season]]. The Colts defeated the Bears by the score of 29–17. The game was played on February 4, 2007, at [[Hard Rock Stadium|Dolphin Stadium]] in [[Miami Gardens, Florida]].\n\nThis game featured two teams ending long [[Super Bowl]] appearance [[List of NFL franchise post-season droughts|droughts]]. The Colts, who finished with a 12–4 regular season record, were making their first Super Bowl appearance since winning [[Super Bowl V]] in the [[1970 Baltimore Colts season|1970 season]] during the team\'s tenure in [[Baltimore]]; they had [[Baltimore Colts relocation to Indianapolis|moved]] to [[Indianapolis]] in [[1984 NFL season|1984]]. Meanwhile, the Bears, who posted an NFC-best 13–3 regular season record, were making their first appearance since winning [[Super Bowl XX]] in the [[1985 Chicago Bears season|1985 season]]. In addition, the Bears\' [[Lovie Smith]] and the Colts\' [[Tony Dungy]] both became the first [[African-American]] head coaches to coach in the Super Bowl, with Dungy the first to win. It was also only the second championship game or series in any of the four North American major professional sports leagues to feature two African-American head coaches or managers, the other being the [[1975 NBA Finals]].\n\nIn the first Super Bowl played in rainy conditions, the Colts overcame a 14–6 first-quarter deficit to outscore the Bears 23–3 in the last three quarters. Chicago posted the then-earliest lead in Super Bowl history when returner [[Devin Hester]] ran back the opening kickoff 92 yards for a touchdown after 14 seconds had elapsed (a record later broken in [[Super Bowl XLVIII]] when the Seattle Seahawks scored a [[Safety (gridiron football score)|safety]] 12 seconds into the game). The Colts forced five turnovers, including cornerback [[Kelvin Hayden]]\'s 56-yard interception return for a touchdown. Indianapolis kicker [[Adam Vinatieri]] also scored three field goals. Colts quarterback [[Peyton Manning]] was named the game\'s [[Super Bowl Most Valuable Player Award|Most Valuable Player]] (MVP), completing 25 of 38 passes for 247&nbsp;yards and a touchdown, with one interception for a [[passer rating]] of 81.8.\n\n[[NFL on CBS|CBS]]\'s broadcast of the game was watched by an estimated average of 93.2 million viewers, making it at the time the fifth most watched program in U.S. television history.{{Citation needed|date=February 2021}} The halftime show, headlined by the musician [[Prince (musician)|Prince]], peaked at 140 million viewers, and was widely acclaimed by music critics.<ref name="Hoekstra" />\n\nAs of the end of the 2021 season, this was the last time a 3-seed made or won the Super Bowl; every other seed has made it at least once since [[2010–11 NFL playoffs|2010]] (outside of the 7-seed, which was not created until [[2020–21 NFL playoffs|2020]]).\n\n==Background==\n\n===Host selection process===\n[[File:Dolphin Stadium.jpg|thumb|left|[[Hard Rock Stadium|Dolphin Stadium (now Hard Rock Stadium)]] was selected to be the host site for Super Bowl XLI.]]\n[[Dolphin Stadium]] won the bid to host Super Bowl XLI on September 17, 2003, after a campaign against [[Phoenix, Arizona|Phoenix]], [[Tampa, Florida|Tampa]], [[New York City]], and [[Washington, D.C.]] With this game, the [[South Florida metropolitan area|Miami metropolitan area]] tied [[New Orleans, Louisiana]] as the city to host the most Super Bowls (9).<ref>{{cite web|url=http://www.nfl.com/news/story/6648786|title=Miami awarded 2007 Super Bowl|publisher=[http://www.nfl.com NFL.com]|date=September 17, 2003|access-date=December 10, 2006 |archive-url = https://web.archive.org/web/20060913085310/http://www.nfl.com/news/story/6648786 |archive-date = September 13, 2006}}</ref>\n\nThis was the fourth Super Bowl at Dolphin Stadium, which has also been known as "Joe Robbie Stadium" and "Pro Player Stadium". The venue previously hosted Super Bowls [[Super Bowl XXIII|XXIII]] (broadcast on [[NBC]]), [[Super Bowl XXIX|XXIX]] (on [[American Broadcasting Company|ABC]]), and [[Super Bowl XXXIII|XXXIII]] (on [[Fox Broadcasting Company|FOX]]). Super Bowls [[Super Bowl II|II]], [[Super Bowl III|III]], [[Super Bowl V|V]], [[Super Bowl X|X]], and [[Super Bowl XIII|XIII]] were also in Miami, but held at the [[Miami Orange Bowl]]. This was the first Super Bowl played at the stadium since the city of Miami Gardens where the stadium is located was incorporated on May 13, 2003.\n\nIn February 2006, the NFL and the South Florida Super Bowl XLI Host Committee unveiled the slogan "one game, one dream" for the game, referring to the entire [[South Florida]] region working together to present the event.<ref name="CommitteeUnveilsLogo">{{cite news|first=Sarah|last=Talalay|title=Committee Unveils Logo, Theme|publisher=[[Sun-Sentinel]]|page=7C|date=February 17, 2006}}</ref> The Super Bowl XLI logo was also unveiled, featuring the colors orange (to represent the sun) and blue (for the ocean).<ref name=CommitteeUnveilsLogo/> The "I" in the [[Roman numeral]] "XLI" was drawn to resemble a pylon placed at each corner of an [[end zone]] because "the goal is to get to the game."<ref name=CommitteeUnveilsLogo/> The logo had the same shade of orange as the logo of the host city\'s home team, the [[Miami Dolphins]]. The "XL" part was similar to that of [[Super Bowl XL]]\'s logo.\n\n===Teams===\n====Indianapolis Colts====\n{{main|2006 Indianapolis Colts season}}\n\nThe Colts\' first trip to the Super Bowl in 36 years set a record for longest time between appearances by a team (since broken by the Kansas City Chiefs). Their return was the culmination of a nine-year-long building process. In [[1998 Indianapolis Colts season|1998]], they drafted quarterback [[Peyton Manning]] to lead the team. Over the next four seasons, Manning, along with other stars such as receiver [[Marvin Harrison]] and running back [[Edgerrin James]], turned the Colts into one of the best offensive teams in the NFL, but the team struggled to find consistency on defense and always ended up with either a losing season or elimination from the playoffs in the first round. After the [[2001 Indianapolis Colts season|2001 season]], Indianapolis fired head coach [[Jim E. Mora|Jim Mora]] and replaced him with [[Tony Dungy]]. Dungy had developed one of the best defenses in the NFL while coaching the [[Tampa Bay Buccaneers]], and it was hoped he could solve the Colts\' defensive problems as well.\n\nOver the next four seasons, the Colts won 48 of 64 games, but still could not find much success in the postseason. In [[2002 Indianapolis Colts season|2002]], they were blown out 41–0 in the Wild Card playoffs by the [[2002 New York Jets season|New York Jets]]. In [[2003 Indianapolis Colts season|2003]], they won their first two playoff games behind impressive offensive performances, and reached the AFC Championship Game. There, they lost to the [[2003 New England Patriots season|New England Patriots]] 24–14, with Manning throwing four interceptions. In [[2004 Indianapolis Colts season|2004]], the Colts had one of the most spectacular offensive seasons in NFL history, scoring 522 points and gaining 6,582 yards, while Manning set NFL records for most touchdown passes and highest [[passer rating]]. But again the [[2004 New England Patriots season|New England Patriots]]\' defense (and snowy conditions) proved too formidable, as they lost 20–3 in the Divisional playoffs.\n\nIn [[2005 Indianapolis Colts season|2005]], the Colts\' defense improved, making the team the clear favorites in the NFL. They won the first 13 games of the season and finished with a 14–2 record, while ranking second in the NFL in both points scored and fewest points allowed. But once again they lost in Divisional playoffs, this time to the #6 seeded [[2005 Pittsburgh Steelers season|Pittsburgh Steelers]], 21–18. The Colts\' playoff runs ended to with a loss to the eventual Super Bowl champions in three consecutive years preceding this season. After another disappointing loss, Manning had developed a reputation of being unable to make it to a championship, a reputation that followed him from college after he was unable to win an NCAA title with the [[Tennessee Volunteers]] (who won a title the year after he graduated). The Colts lost some key players after the 2005 season, including James, who departed the Colts for the [[Arizona Cardinals]], and kicker [[Mike Vanderjagt]], the NFL\'s all-time leader in field goal percentage, who left for the [[Dallas Cowboys]]. However, they did gain one key addition: 31-year old special teams returner [[Terrence Wilkins]]. Though he had been out of the NFL for three years, Wilkins finished the season with 52 kickoff returns for 1,272 yards (9th in the NFL).\n\nStill, the Colts remained one of the AFC\'s top teams in the 2006 season. Manning made the Pro Bowl for the 7th time in his career, completing 362 of 555 passes for 4,397 yards and an NFL-best 31 touchdowns, with an additional 4 rushing touchdowns and with only 9 interceptions and 15 sacks. His favorite target was Harrison, who caught 95 passes for 1,366 yards and 12 touchdowns. Receiver [[Reggie Wayne]] was also a major deep threat with 86 receptions for 1,310 yards and 9 touchdowns. Tight ends [[Ben Utecht]] and [[Dallas Clark]] were also reliable targets, each recording over 30 receptions for over 300 yards. On the ground, rookie running back [[Joseph Addai]] led the team with 1,081 yards and a 4.8 yards-per-carry average despite not starting any games in the regular season. He also caught 40 receptions for 325 yards and scored 8 touchdowns. Running back [[Dominic Rhodes]] was also a major contributor, rushing for 641 yards and catching 36 passes for 251 yards. The offensive line was led by Pro Bowlers [[Jeff Saturday]] and [[Tarik Glenn]]. On special teams, the Colts signed kicker [[Adam Vinatieri]] to replace Vanderjagt. While Vinatieri\'s career field goal percentage was lower, the Colts considered him to be an improvement because of his reputation for making "[[clutch (sports)|clutch]]" kicks, a reputation aided by his game winning field goals in [[Super Bowl XXXVI]] and [[Super Bowl XXXVIII]].\n\nIndianapolis\' defense ranked second in the NFL in fewest passing yards allowed. [[Dwight Freeney]] (5.5 sacks and 4 forced fumbles) and [[Robert Mathis]] (9.5 sacks, 4 forced fumbles, 2 fumble recoveries) were widely considered to be among the best pass-rushing [[defensive end]]s in the NFL. Behind them, linebacker [[Cato June]] led the team in tackles (142) and interceptions (3).  Cornerback [[Nick Harper (American football)|Nick Harper]] added 3 interceptions and 75 tackles. The Colts\' run defense, however, was a major problem, giving up 2,768 yards on the ground, an average of 173 per game and last in the NFL. Another major issue for the Colts was their coverage teams, as they ranked 30th out of 32 teams in average kickoff return yardage allowed and 31st in average punt return yardage allowed.  One key issue for the team was the loss of safeties [[Mike Doss]] and [[Bob Sanders]], who had missed most of the seasons with injuries.\n\nIndianapolis started out the season winning their first nine games, but ended up losing four of their next seven and finished with a 12–4 record, giving them the #3 playoff seed. Therefore, they had to play in a wild card playoff game. In the Wild Card playoffs, they defeated the [[2006 Kansas City Chiefs season|Kansas City Chiefs]] 23–8, then defeated the [[2006 Baltimore Ravens season|Baltimore Ravens]] 15–6 in the Divisional playoffs, and advanced to Super Bowl XLI with a 38–34 win over the [[2006 New England Patriots season|New England Patriots]] in the AFC Championship Game after rallying from a 21–3 deficit.\n\n====Chicago Bears====\n{{Main|2006 Chicago Bears season}}\n[[File:Lion Chicago Bears Helmet.jpg|thumb|right|225px|The [[Art Institute of Chicago]]\'s [[Lions (Kemeys)|lion sculptures]] were decorated to show support for the Chicago Bears.]]\n\nChicago finished the season with an NFC best 13–3 record and advanced to the second Super Bowl in franchise history. They defeated the [[2006 Seattle Seahawks season|Seattle Seahawks]] 27–24 in overtime in the divisional round of the playoffs, and the [[2006 New Orleans Saints season|New Orleans Saints]] 39–14 in the NFC Championship game to advance to Super Bowl XLI. The team excelled on defense, ranking third in fewest points allowed (255) and second in fewest points allowed per drive.<ref name="FOdrivestats">{{cite web|url=http://footballoutsiders.com/stats/drivestats2006.php|archive-url=https://archive.today/20070521114359/http://footballoutsiders.com/stats/drivestats2006.php|url-status=dead|archive-date=2007-05-21|title=FOOTBALL OUTSIDERS: Innovative Statistics, Intelligent Analysis - 2006 DRIVE STATS|first=Football|last=Outsiders|website=footballoutsiders.com}}</ref> They also ranked second in scoring (427 points), although only tenth in points per offensive drive<ref name=FOdrivestats/> thanks to a league leading 65 points scored on defensive or special teams plays.\n\nThe Bears offense was led by quarterback [[Rex Grossman]], the team\'s first round draft pick in [[2003 NFL Draft|2003]]. Over the previous three seasons, Grossman had played in just 8 regular season games due to injuries, but he recovered to start in all 16 games in 2006. By the end of the season, he finished with 3,193 yards and 23 touchdowns, the most by a Bears quarterback [[1995 Chicago Bears season|since 1995]]. Grossman had difficulty avoiding [[Turnover (football)|turnovers]], however, and threw 20 interceptions and lost five fumbles during the year. In the last seven games of the season, he turned the ball over 18 times. Many fans and sports writers expected head coach [[Lovie Smith]] to bench him at some point, but Smith insisted that Grossman would be the starter throughout the entire season.\n\nReceivers [[Muhsin Muhammad]] (60 receptions, 863&nbsp;yards, 5 touchdowns) and [[Bernard Berrian]] (51 receptions, 775 yards, 7 touchdowns) provided the main deep threat on the team, along with tight end [[Desmond Clark]], who caught 45 passes for 626&nbsp;yards and 6 touchdowns. Chicago\'s running game was led by running backs [[Thomas Jones (American football)|Thomas Jones]] and [[Cedric Benson]]. Jones rushed for 1,210&nbsp;yards and caught 36 passes, while Benson rushed for 647&nbsp;yards and scored 6 touchdowns.\n\nChicago\'s defense allowed the 5th least total yardage, allowed less than 100&nbsp;yards per game on the ground,<ref>[http://www.nfl.com/stats/teamsort/NFL/DEF-RUSHING/2006/regular?sort_col_1=6 NFL.com rushing defense statistics]{{dead link|date=June 2017 |bot=InternetArchiveBot |fix-attempted=yes }}</ref> and allowed the fewest yards per drive of any NFL team.<ref name="FOdrivestats"/> The line was anchored by [[Adewale Ogunleye]], who had 6.5 sacks, and Pro Bowler [[Tommie Harris]], who recorded 5, along with rookie [[Mark Anderson (American football)|Mark Anderson]], who led the team with 12 sacks. Behind them, two of the three Bears starting linebackers, [[Lance Briggs]], and [[Brian Urlacher]], were selected to the [[2007 Pro Bowl]]. In the secondary, cornerbacks [[Ricky Manning]] and [[Charles Tillman]] each recorded five interceptions. The defense was also able to make up for the offense\'s poor performance earlier in the season, as explained in the [[Monday Night Comeback|win against the Cardinals in Week 6]].\n\nThe loss of Harris to injury after the 12th game of the season coincided with a decline in defensive performance. Before his loss, the Bears allowed only two opponents to score more than 20 points (23 points to the [[2006 Arizona Cardinals season|Arizona Cardinals]] in week six and 31 to the [[2006 Miami Dolphins season|Miami Dolphins]] in week 9).<ref>{{cite web|url=http://www.nfl.com/players/playerpage/492957/gamelogs/2006|title=NFL Players|website=www.nfl.com}}</ref> After his injury, Chicago opponents scored more than 20 points in six of the seven remaining games, including two of three playoff games. Only the [[2006 New Orleans Saints season|New Orleans Saints]] in the [[NFC Championship game]] were held below 21 points.<ref>{{cite web|url=http://www.nfl.com/teams/schedule/CHI/2006|title=Chicago Bears 2006 game results|work=nfl.com|url-status=dead|archive-url=https://web.archive.org/web/20070522183009/http://www.nfl.com/teams/schedule/CHI/2006|archive-date=May 22, 2007}}</ref>\n\nThe Bears\' special teams sent three players to the Pro Bowl, including special teams ace [[Brendon Ayanbadejo]], kicker [[Robbie Gould]] (who led all NFL kickers with 143 points), and rookie return man [[Devin Hester]], who gained 600 punt return yards with a 12.8&nbsp;yards per return average, the second highest in the NFL. He also set a league record with 6 touchdowns on special teams.\n\n====Regular season statistical comparison====\nThe chart below provides a comparison of regular season statistics in key categories (overall rank amongst 32 teams in parentheses).<ref>{{cite web|url=http://www.nfl.com/stats/team?seasonId=2006&seasonType=REG&Submit=Find|title=NFL Team Stats - NFL.com|website=www.nfl.com|access-date=December 28, 2007|archive-date=September 2, 2017|archive-url=https://web.archive.org/web/20170902093656/http://www.nfl.com/stats/team?seasonId=2006&seasonType=REG&Submit=Find|url-status=dead}}</ref>\n\n{| class="wikitable"\n!\'\'\'Statistic\'\'\'\n!\'\'\'Indianapolis Colts\'\'\'\n!\'\'\'Chicago Bears\'\'\'\n|-\n|Points scored per game||26.7 (2nd, tied)||26.7 (2nd, tied)\n|-\n|Points allowed per game||22.5 (23rd)||15.9 (3rd)\n|-\n|Rushing yards gained per game||110.1 (18th)||119.9 (15th)\n|-\n|Rushing yards allowed per game||173.0 (32nd)||99.4 (6th)\n|-\n|Passing yards gained per game||269.2 (2nd)||205.1 (14th)\n|-\n|Passing yards allowed per game||159.2 (2nd)||194.8 (11th)\n|-\n|Yards gained per play||6.0 (2nd)||5.0 (21st)\n|-\n|Yards allowed per play||5.5 (29th)||4.6 (2nd)\n|-\n|Time of possession per game||29:32 (23rd)||30:56 (11th)\n|-\n|Third-down conversion percentage||56 (1st)||37 (22nd)\n|-\n|Third-down conversion percentage allowed||47 (32nd)||31 (2nd)\n|-\n|Fourth-down conversion percentage||0 (32nd)||69 (4th, tied)\n|-\n|Fourth-down conversion percentage allowed||79 (30th, tied)||62 (27th)\n|-\n|Total turnover differential||+7 (7th)||+8 (4th)\n|}\n\n===Playoffs===\n{{main|2006–07 NFL playoffs}}\n\nAlthough the Colts\' rushing defense looked extremely weak during the season, it ended up being a key factor on their road to the Super Bowl. First, Indianapolis defeated the [[2006 Kansas City Chiefs season|Kansas City Chiefs]] 23–8, holding Chiefs running back [[Larry Johnson (running back)|Larry Johnson]] (who rushed for 1,789 yards during the season) to just 32 yards on 13 carries. Then, they defeated the [[2006 Baltimore Ravens season|Baltimore Ravens]] 15–6, winning on a playoff record-tying five field goals by Vinatieri and holding running back [[Jamal Lewis]] (who rushed for 1,132 yards during the season) to just 53 yards.\n\nThen the Colts faced their [[Colts–Patriots rivalry|arch-rival]] [[2006 New England Patriots season|New England Patriots]] in the AFC Championship Game. New England jumped to an early 21–3 lead, but Indianapolis stormed back in the second half, outscoring the Patriots 32–13 with an additional field goal late in the first half. With 2:22 left in the game, the Colts had the ball on their own 20-yard line trailing 34–31. On the first four plays of the drive, Manning completed three passes, moving the ball 69 yards to the Patriots 11-yard line in just 24 seconds. Three plays later, Addai scored a 3-yard touchdown run to put them in the lead, 38–34 with only 60 seconds left in regulation. The Patriots responded with a drive to the Colts 45-yard line, but defensive back [[Marlin Jackson]] ended the drive with an interception to give Indianapolis the win. This happened to be the biggest comeback in AFC-NFC Conference Championship history.\n\nMeanwhile, the Bears started out their post-season with a 27–24 win over the [[2006 Seattle Seahawks season|Seattle Seahawks]] with [[Robbie Gould]]\'s 49-yard field goal in overtime. One week later, they defeated the [[2006 New Orleans Saints season|New Orleans Saints]] 39–14 in the NFC Championship Game. Chicago dominated most of the game, jumping to a 16–0 early lead. Two touchdown passes from Saints quarterback [[Drew Brees]], cut the score to 16–14, but the Bears responded with 23 unanswered points to propel them to their first Super Bowl since 1985. Thomas Jones finished the game with a franchise postseason record 123 rushing yards and two touchdowns.\n\nIt was the first time since the 1996 postseason that the home team won both of the conference championship games. The Colts were the first dome team to win the Super Bowl in an outdoor game (the [[1999 St. Louis Rams season|St. Louis Rams]] were the first dome team to win a Super Bowl, [[Super Bowl XXXIV|XXXIV]] inside the [[Georgia Dome]] in [[Atlanta]]). The [[2006 NFL season|2006]] Indianapolis Colts were the first division champion to win a Super Bowl with four postseason wins and the second division champion ([[2003 NFL season|2003]] [[Carolina Panthers]]) to win a conference title with three postseason wins.\n\nFor the Bears, this marked the first time that a Chicago sports team not owned by [[Jerry Reinsdorf]] had reached the champion game/series in their league since the [[Chicago Blackhawks|Blackhawks]] lost in the [[1992 Stanley Cup Finals]].\n\n===Super Bowl pre-game practices and notes===\n[[File:dolphin-stadium-super-bowl-xli-2007-02-03.jpg|thumb|right|Dolphin Stadium prepares for Super Bowl XLI]]\nThe [[Indianapolis Colts]] held pre-game practices for Super Bowl XLI at the [[Miami Dolphins Training Facility]] on the campus of [[Nova Southeastern University]] in [[Davie, Florida]]. The Colts\' "Team and Family" hotel was the Marriott Harbor Beach.<ref>{{cite web|url=https://www.sun-sentinel.com/business/fl-xpm-2010-03-01-fl-super-bowl-hotels-20100301-story.html|title=Super Bowl helped Fort Lauderdale hotels more than 2007|publisher=South Florida Sun Sentinel|date=March 1, 2010|access-date=May 29, 2020}}</ref> At the [[2007 Indianapolis 500]] pre-race ceremonies on May 27, 2007, eventual Super Bowl XLI [[MVP]] [[Peyton Manning]] stated to [[ABC Sports]] commentator [[Brent Musburger]] the day before the game that two-time [[Indianapolis 500]] winner [[Hélio Castroneves]] gave a pep talk to the Colts team.\n\nThe [[Chicago Bears]] held pre-game practices for Super Bowl XLI at the [[Miami Hurricanes Football Facility]], on the campus of the [[University of Miami]] in [[Coral Gables, Florida]].\n\nChicago defensive tackle [[Tank Johnson]] was required to request a judge\'s order to leave the state of [[Illinois]] due to an arrest for unlicensed ownership of six firearms and two assault rifles. On January 23, 2007, the judge granted him permission to travel out of state to play in the Super Bowl.<ref>{{Cite news|author=Reuters Staff|date=2007-01-23|title=Judge allows Bears\' Johnson to play in Super Bowl|language=en|work=Reuters|url=https://www.reuters.com/article/us-nfl-super-tank-idUSN2327155920070123|access-date=2021-02-28}}</ref>\n\nChicago and Indianapolis are the two closest cities, geographically, to ever play in a Super Bowl; at 182 miles (293&nbsp;km) apart (connected by a route that is mostly [[Interstate 65 in Indiana|Interstate 65]]), they are slightly closer than New York and Baltimore, whose teams played each other at [[Super Bowl III|III]] (207 miles) and [[Super Bowl XXXV|XXXV]] (188 miles). This prompted Colts coach Tony Dungy to joke to Jim Nantz, at the end of the AFC Championship game, that the two teams should split the difference and play the game in [[Fort Wayne, Indiana|Fort Wayne]].<ref>{{cite journal|title=Storming the Fort (Wayne)|url=https://www.si.com/vault/2007/02/05/8399819/storming-the-fort-wayne|author=Rushin, Steve|journal=Sports Illustrated|date=February 5, 2007|access-date=January 10, 2016}}</ref>\n\nThis was the first Super Bowl since [[Super Bowl V]] that all players on both teams wore black shoes. As the [[Super Bowl#Home team designation|designated home team]] in the annual rotation between AFC and NFC teams, the Bears elected to wear their home navy uniforms with white pants, while the Colts wore their road white uniforms with white pants.\n\n==Broadcasting==\n\n===United States===\n[[File:Super Bowl XLI pre-game show in Miami.jpg|thumb|250px|The NFL Today pre-game show with officers of the [[U.S. Air Force Thunderbirds]].]]\n\nThe game was televised in the United States by [[CBS]] in [[High-definition television|high-definition]] with [[play-by-play|play-by-play announcer]] [[Jim Nantz]] and [[color commentator]] [[Phil Simms]]. This was the first Super Bowl announced by Nantz.<ref>{{cite web|url=http://sportsline.com/cbssports/story/9362012|title=CBS Sports announces NFL TV schedule|work=[[CBS Sports]]|date=April 6, 2006|access-date=December 12, 2006|url-status=dead|archive-url=https://web.archive.org/web/20070103034025/http://www.sportsline.com/cbssports/story/9362012|archive-date=January 3, 2007}}</ref> Additionally, the game had [[Steve Tasker]] ([[Chicago Bears|Bears sideline]]) and [[Solomon Wilcots]] ([[Indianapolis Colts|Colts sideline]]) reporting on the sidelines and [[Lesley Visser]] ([[Chicago Bears|Bears sideline]]) and [[Sam Ryan]] ([[Indianapolis Colts|Colts sideline]]) in the stands.\n\nThis was the first Super Bowl aired on CBS since the [[Janet Jackson]] and [[Justin Timberlake]] [[Super Bowl XXXVIII halftime show controversy|incident]] three years earlier, in [[Super Bowl XXXVIII]]; and the first since the [[Viacom (2005–present)|Viacom]]/[[CBS Corporation|CBS]] split at the end of 2005. Extensive pre-game coverage, hosted by \'\'[[The NFL Today]]\'\' team of [[James Brown (sportscaster)|James Brown]], [[Shannon Sharpe]], [[Boomer Esiason]] and [[Dan Marino]], whose name graces the address of the game site (2269 Dan Marino Boulevard), began at noon (US EST) with [[NFL Films]]\' "Road to the Super Bowl" year in review (narrated by [[Tom Selleck]]). This was followed by "The Phil Simms All-Iron Team", and a four-and-a-half-hour \'\'Super Bowl Today\'\' pre-game show followed by game coverage at 6:25 PM.<ref>{{cite web|url=http://sportsline.com/cbssports/schedules/nfl|title=CBS Sports NFL schedule|work=[[CBS Sports]]|date=April 9, 2006|access-date=December 14, 2006|url-status=dead|archive-url=https://web.archive.org/web/20070401053859/http://sportsline.com/cbssports/schedules/nfl|archive-date=April 1, 2007}}</ref> Other contributors to the pre-game show included [[Katie Couric]], anchor of the \'\'[[CBS Evening News]]\'\', [[Armen Keteyian]], CBS News Chief Investigative Correspondent, [[Randy Cross]], who reported from [[Iraq]] where [[United States military|U.S. military]] forces played a touch football game known as "The [[Baghdad]] Bowl" and [[Dick Enberg]], who participated in his 12th Super Bowl telecast as a host, play-by-play announcer, or contributor.\n[[Westwood One (1976–2011)|Westwood One]] provided radio coverage of the event, with [[Marv Albert]] and Boomer Esiason as announcers.<ref>{{cite web|url=http://www.prnewswire.com/cgi-bin/stories.pl?ACCT=SPIENT.story&STORY=/www/story/02-01-2007/0004518089&EDATE=THU+Feb+01+2007,+09:00+AM|title=Westwood One press release|date=February 1, 2007|access-date=February 5, 2007}}</ref>\n\n[[File:BaghdadBowl07.jpg|thumb|right|250px|"The Baghdad Bowl" flag football game played in Iraq.]]\n\nThe [[American Forces Network]] (AFN) provided coverage of the Super Bowl for U.S. forces stationed overseas and to all U.S. Navy ships at sea.<ref>CBS announcer\'s comment during the national anthem</ref>\n\nThe opening title sequence of CBS television coverage featured the composition Lux Aeterna, by artist Clint Mansell, in the background.\n\n[[Nielsen Media Research]] reported 93 million viewers for Super Bowl XLI, making it at the time the fifth most watched program in U.S. television history (trailing only the [[Goodbye, Farewell and Amen|\'\'M*A*S*H\'\' finale]] and Super Bowls [[Super Bowl XLIII|XLIII]], [[Super Bowl XXX|XXX]], and [[Super Bowl XLII|XLII]]).{{Citation needed|date=February 2021}}\n\n===Commercials===\nAdvertising rates were reported as being slightly higher than in the year before, with CBS confirming a price of $2.6 million for some 30-second spots, compared with $2.5 million during [[Super Bowl XL]]. However, CNN reported that after discounts, the average price is likely closer to $1.8 to $2 million. Familiar advertisers in recent years such as [[Anheuser-Busch]], [[CareerBuilder]], [[General Motors]] and [[Coca-Cola]] bought multiple advertising spots, and other popular advertisers like [[Go Daddy]] and [[Diamond Foods, Inc.|Emerald Nuts]] had commercials this year.<ref name="price">{{cite news|url= https://money.cnn.com/2007/01/03/news/funny/superbowl_ads/?postversion=2007010318|title=Super prices for Super Bowl ads|access-date=January 17, 2007|date=January 3, 2007 | work=CNN | first=Paul R. | last=La Monica}}</ref>\n\nThe only major hype related to commercials in the months leading up to Super Bowl XLI involved various campaigns to allow consumers to be involved in the creation of Super Bowl ads, inspired by consumer-generated content sites like YouTube. [[Frito-Lay]] announced a campaign in September 2006 to allow the public to submit ads for their [[Doritos]] brand and vote on the best one, which aired during the Super Bowl. Doritos actually aired two of the ads due to a close voting margin; the winning ad (featuring a chance meeting with a man and a woman that feature the qualities of Doritos) aired in the first quarter, while a second (with a checkout lady overly enamored with the product) aired in the second quarter. The five finalists each received $10,000 in this contest. General Motors announced a similar contest, open only to college students, for their [[Chevrolet]] brand; however, the ad would be produced professionally based on ideas suggested by the public. The winning ad featured men gathering around an HHR model with women in it and stripping off their clothes and giving it a car wash. The NFL itself advertised a similar contest to generate suggestions for a commercial promoting the league, with the winning concept featuring fans\' disappointment that their teams\' season was over.<ref>{{cite web|title=Some Super Bowl ads to be work of \'ordinary people\'|url=http://www.napavalleyregister.com/articles/2007/01/14/business/stories_from_ap/iq_3770297.txt|date=January 14, 2007|access-date=January 17, 2007|url-status=dead|archive-url=https://archive.today/20120905184822/http://www.napavalleyregister.com/articles/2007/01/14/business/stories_from_ap/iq_3770297.txt|archive-date=September 5, 2012}}</ref>\n\nThe annual [[USA Today Super Bowl Ad Meter]] survey chose a [[Budweiser]] ad featuring crabs worshiping an ice chest with that particular beer inside as the top ad of Super Bowl XLI, followed by another Budweiser commercial featuring a stray dog with mud spots climbing onto the brewery\'s trademark Clydesdale-drawn wagon in a parade. In all, Anheuser-Busch took seven of the top ten spots in the annual survey, sweeping the top three spots. A YouTube user survey chose the Doritos "Snack Hard" ad (produced for the incredible price of $12, the cost of three bags of the snack product)<ref>{{cite web|title=Doritos eats up Cary startup\'s ad|url=http://www.newsobserver.com/672/story/539747.html|date=February 5, 2007|access-date=February 15, 2007 |archive-url = https://web.archive.org/web/20070212173826/http://www.newsobserver.com/672/story/539747.html |archive-date = February 12, 2007}}</ref> as their top ad, that ad finished fourth in the USA Today survey. [[ADBOWL]] results were slightly different with only 6 of Anheuser-Busch\'s ads finishing in the top ten. The highest ranked being the [[Bud Light]] "Rock, Paper, Scissors" spot. Also in the top ten were [[Blockbuster Video]]\'s "Mouse", Doritos\' "Car Wreck", GM\'s "Robot" and [[Taco Bell]]\'s "Lion\'s Talk \'Carne\'."\n\nOne ad that drew criticism from the gay community was for the [[Snickers]] candy bar featuring two men accidentally "kissing" each other after sharing the product in question, then proceeded to rip chest hairs as a manly act as [[homophobic]].<ref>{{cite web|title=Snickers ad pulled after gays complain|url=http://www.ajc.com/news/content/sports/stories/2007/02/06/0206snickers.html|date=February 6, 2007|access-date=February 15, 2007 |archive-url = https://web.archive.org/web/20070208003951/http://www.ajc.com/news/content/sports/stories/2007/02/06/0206snickers.html |archive-date = February 8, 2007}}</ref> The ad proved to be controversial, and the ad was cancelled the next day by Masterfoods USA ([[Mars, Incorporated]]\'s snack food division), and three other versions were deleted from the snickers.com web site. The ad was ninth in the USA Today ad survey, and according to a Masterfoods publicist, not intended to harm anyone.\n\nA different ad for [[General Motors]] featured a [[layoff|laid off]] [[assembly line]] [[robot]] contemplating suicide, which drew criticism from the [[American Foundation for Suicide Prevention]]. The group asked for an apology from GM, and that the ad be taken off the air and the company\'s website.<ref>{{cite web|title=AFSP Issues Statement to General Motors Regarding Super Bowl Ad|author=AFSP.org|url=http://www.afsp.org/index.cfm?fuseaction=home.viewpage&page_id=93CA6FF0-7E90-9BD4-C1D8809C1CBC8069|date=February 6, 2007|access-date=February 9, 2007|url-status=dead|archive-url=https://web.archive.org/web/20070220032540/http://www.afsp.org/index.cfm?fuseaction=home.viewpage&page_id=93CA6FF0-7E90-9BD4-C1D8809C1CBC8069|archive-date=February 20, 2007}}</ref> The suicide scene was replaced with a scene of the robot watching a car being crushed at a junkyard when it was shown again during the [[79th Academy Awards]] on February 25.\n\n===International===\nThe Super Bowl was broadcast live in Canada on CBS (which is available in Canada) as well as [[Global TV]] and [[CJON-TV|NTV]] which both took the main CBS commentary, and on the French cable channel [[Réseau des sports|RDS]]. In the United Kingdom the Super Bowl was broadcast on [[ITV1]], [[Sky Sports|Sky Sports 1 & Sky Sports HD1]] with [[Sky Sports]] taking the main CBS commentary and ITV taking the [[National Football League|NFL]] supplied international commentary feed of [[Spero Dedes]] and [[Sterling Sharpe]].\n\nSuper Bowl XLI was broadcast in over 200 countries. Amongst the television networks who broadcast Super Bowl XLI were:\n{{div col|colwidth=48em}}\n*{{ARG}}&nbsp;– [[ESPN|ESPN Argentina]] and [[Fox Sports en Latinoamérica|Fox Sports]]\n*{{flagu|Australia}}&nbsp;– [[Special Broadcasting Service|SBS]], [[Fox Sports (Australia)|Fox Sports]] and [[ESPN]] broadcast the game live (All International Feed).\n*{{AUT}}&nbsp;– [[ORF (broadcaster)|ORF]] 1, [[Deutsches Sportfernsehen|DSF]]\n*{{BLZ}}&nbsp;– [[Great Belize Television|Channel 5]], [[Tropical Vision Limited|Channel 7]] (both CBS feed)\n*{{flagu|Brazil}}&nbsp;– [[Grupo Bandeirantes de Comunicação|BandSports]] and [[ESPN International]]; [[Rede Bandeirantes|Bandeirantes]] also broadcast a condensed version of the game<ref name="SBIntl">{{cite web|url=http://www.nfl.com/international/story/9934713|title=Worldwide media coverage of Super Bowl XLI|access-date=February 4, 2007 |archive-url = https://web.archive.org/web/20070203010749/http://www.nfl.com/international/story/9934713 |archive-date = February 3, 2007}}</ref>\n*{{flagu|Canada}}&nbsp;– [[Global Television Network|Global]], [[CJON-TV|NTV]] (English) and [[Réseau des sports|RDS]] (French)\n*{{flagu|Chile}}&nbsp;– [[ESPN|ESPN Latin-America]] and [[Fox Sports en Latinoamérica|Fox Sports]]\n*{{COL}}&nbsp;– [[ESPN|ESPN Latin-America]] and [[Fox Sports en Latinoamérica|Fox Sports]]\n*{{PRC}}&nbsp;– [[CCTV-5]]\n*{{CRO}}&nbsp;– [[Zagrebačka Televizija|Z1]]\n*{{DEN}}&nbsp;– [[TV2 Zulu]]\n*{{FIN}}&nbsp;– [[MTV3]]\n*{{flagu|France}}&nbsp;– [[France 2]]\n*{{flagu|Germany}}&nbsp;– [[ARD (broadcaster)|ARD]] (international feed), [[NASN]] (international feed), [[Deutsches Sportfernsehen|DSF]] (on [[Broadcast delay|tape delay]])\n*{{HUN}}&nbsp;– [[Sport 1 (Europe)|Sport 1]]\n*{{ISL}}&nbsp;– [[Sýn]]\n*{{IRL}}&nbsp;– [[TV3 (Ireland)|TV3]], [[Sky Sports]]\n*{{flagu|Italy}}&nbsp;– [[Sky Sport (Italy)|Sky Sport Italia]] (in [[High-definition television|HDTV]])\n*{{flagu|India}}&nbsp;– [[ESPN|ESPN Asia]]\n*{{flagu|Japan}}&nbsp;– [[NHK|NHK BS-1]], [[Nippon Television]]\n*{{KOR}}&nbsp;– [[Seoul Broadcasting System]]<ref name=SBIntl/>\n*{{MKD}}&nbsp;– [[Sport 4]]\n*{{flagu|Mexico}}&nbsp;– [[Televisa]], [[TV Azteca]] (in [[HDTV]])\n*{{MNE}}&nbsp;– [[Elmag RTV]]\n*{{flagu|Netherlands}}&nbsp;– [[NASN]] (HDTV)<ref>For the last decade [[SBS6]] used to broadcast the game in The Netherlands. However, this year only NASN will be showing the Super Bowl.</ref>\n*{{flagu|New Zealand}}&nbsp;– [[Sky Sports]], [[ESPN]]\n*{{NOR}}&nbsp;– [[Viasat SportN]]\n*{{PER}}&nbsp;– [[Fox Sports en Latinoamérica|Fox Sports]], [[ESPN]]\n*{{PHI}}&nbsp;– [[Studio 23]], [[Solar Sports]]\n*{{flagu|Poland}}&nbsp;– [[Canal +]]\n*{{POR}}&nbsp;– [[SportTV 1]]\n*{{ROU}}&nbsp;– [[Sport 1 (Europe)|Sport 1]]\n*{{flagu|Russia}}&nbsp;– [[NTV Plus]]\n*{{flagu|Spain}}&nbsp;– [[Canal+ Spain|Canal +]]\n*{{SRB}}&nbsp;– [[SportKlub]]\n*{{flagu|Sweden}}&nbsp;– [[TV6 (Sweden)]]\n*{{THA}}&nbsp;– [[True Vision]] Super Sport ch.61, [[ESPN]]\n*{{TUR}}&nbsp;– [[Fox Sports Turkey]]\n*{{flagu|United Kingdom}}&nbsp;– ITV1, [[Sky Sports]] and [[Sky Sports|Sky Sports HD]] (CBS feed and announcers)\n{{div col end}}\n\n==Entertainment==\n\n===Pre-game ceremonies===\nBefore the game, [[Cirque du Soleil]], [[Romero Britto]] and [[Little Louie Vega]] performed as the pre-game act,<ref>{{cite news|url=http://www.superbowl.com/news/story/9918326|title=Cirque du Soleil to perform in Super Bowl XLI pregame show|publisher=SuperBowl.com|date=January 9, 2007|access-date=January 11, 2006 |archive-url = https://web.archive.org/web/20070206220217/http://www.superbowl.com/news/story/9918326 |archive-date = February 6, 2007}}</ref> and [[Billy Joel]] sang the [[The Star-Spangled Banner|National Anthem]], accompanying himself on piano. He also performed at [[Super Bowl XXIII]], which was played at the same venue. Joel became the second person to sing the National Anthem twice for a Super Bowl; Aaron Neville sang the national anthem before Super Bowl XXIV, in New Orleans, and Super Bowl XL (along with Aretha Franklin), in Detroit. This was also the last time until [[Luke Bryan]] sang at [[Super Bowl LI]] that a male artist had sung the national anthem at a Super Bowl.<ref>{{cite web|url=http://www.nfl.com/news/story/0ap3000000777452/article/luke-bryan-to-sing-national-anthem-at-super-bowl-li|title=Luke Bryan to sing national anthem at Super Bowl LI}}</ref>\n\n2 days before the game, [[2007 Groundhog Day tornado outbreak|4 tornadoes]] devastated [[Central Florida]], causing over 200 million dollars in damage, and causing numerous of injuries and 21 deaths. A [[moment of silence]] was held before kickoff, in honor of the victims of the severe weather. \n\n[[Marlee Matlin]] and Jason Hay-Southwell performed the National Anthem in [[American Sign Language]].<ref>{{cite web|url=http://www.elitestv.com/pub/2007/Feb/EEN45c353af4b7cb.html|title=Marlee Matlin and Jason Hay-Southwell to Sign National Anthem During Billy Joel Performance at Super Bowl XLI on CBS|publisher=Elitestv.com|date=February 2, 2007|access-date=February 2, 2007|url-status=dead|archive-url=https://web.archive.org/web/20080403123335/http://www.elitestv.com/pub/2007/Feb/EEN45c353af4b7cb.html|archive-date=April 3, 2008}}</ref>\n\nBesides participating in the CBS telecast of the pre-game show, Dan Marino also participated in the [[coin toss]] along with Norma Hunt, who was the widow of [[Lamar Hunt]], the former owner of the Kansas City Chiefs and the man who gave the Super Bowl its name.\n\n===Halftime show===\n{{main|Super Bowl XLI halftime show}}\n\n[[File:Prince at Coachella 001.jpg|thumb|American singer and musician [[Prince (musician)|Prince]] headlined the halftime show]]\nAmerican singer and musician [[Prince (musician)|Prince]] performed in the halftime show.<ref>{{cite news|url=http://www.superbowl.com/news/story/9863556|title=Prince to perform at Super Bowl halftime|publisher=SuperBowl.com|date=December 10, 2006|access-date=December 10, 2006 |archive-url = https://web.archive.org/web/20061214065037/http://www.superbowl.com/news/story/9863556 |archive-date = December 14, 2006}}</ref> The setlist for Prince\'s performance was a short rendition of "[[We Will Rock You]]" by [[Queen (band)|Queen]]; Prince\'s hit songs "[[Let\'s Go Crazy]]" and "[[Baby I\'m a Star]]"; covers of [[Creedence Clearwater Revival]]\'s "[[Proud Mary]]", [[Bob Dylan]]\'s "[[All Along the Watchtower]]", and [[Foo Fighters]]\' "[[Best of You]]"; and finally his signature song "[[Purple Rain (song)|Purple Rain]]" in the downpour. The 12-minute performance featured Prince accompanied by two dancers he called "The Twinz" (Maya and Nandy McClean){{citation needed|date=May 2020}} and the [[Florida A&M University]] marching band, the [[Marching 100]].<ref>{{cite news|url=http://www.tallahassee.com/apps/pbcs.dll/article?AID=/20070124/NEWS01/701240335/1010 |title=Marching 100 to play with Prince |publisher=Tallahassee.com |date=January 24, 2007 |access-date=January 25, 2007 |url-status=dead |archive-url=https://web.archive.org/web/20070927220742/http://www.tallahassee.com/apps/pbcs.dll/article?AID=%2F20070124%2FNEWS01%2F701240335%2F1010 |archive-date=September 27, 2007 }}</ref> Prince had rehearsed with the drum line for a week before the performance. The performance was on a large, central stage which was shaped like Prince\'s logo, and was outlined with lights. He played before 74,512 fans at Dolphin Stadium, who had been given flashlights to point at the stage during the performance of "Purple Rain". The event was carried "to the biggest audience of his life" with 140 million television viewers.<ref name="Hoekstra">{{Cite news|title=Purple rain turned super |date=February 5, 2007 |access-date=February 5, 2007 |author=Dave Hoekstra |url=http://www.suntimes.com/sports/football/bears/243107,CST-FTR-super05.article |work=Chicago Sun-Times |archive-url=https://web.archive.org/web/20090412054158/http://www.suntimes.com/sports/football/bears/243107%2CCST-FTR-super05.article |archive-date=April 12, 2009 |url-status=dead }}</ref> Overall, the show was energetic and well received by the rain-soaked audience surrounding the stage.<ref>{{cite news|url=https://www.nytimes.com/2007/02/05/sports/football/05halftime.html|title=A Noncontroversial Prince, Just the Way the N.F.L. Likes Him|work=[[New York Times]]|date=February 5, 2007|access-date=February 5, 2007 | first=Kelefa | last=Sanneh}}</ref> Music critics were extremely enthusiastic about his performance, one calling it "arguably the best halftime show in Super Bowl history",<ref name=Hoekstra/> and others saying it was one of the best ever.<ref>{{cite news|date=February 4, 2007|title=Mini Review: Prince At Super Bowl|author=Brad Kava|publisher=[[Mercury News]]}}</ref><ref name="Associated Press">{{cite news|date=February 5, 2007|title=No Malfunction as Prince Rocks Halftime|agency=Associated Press|work=[[New York Times]]|url=https://www.nytimes.com/aponline/arts/AP-FBN-Super-Bowl-TV-Review.html|access-date=February 5, 2007}}</ref><ref>{{Cite news|url=https://www.miamiherald.com/sports/nfl/super-bowl/article239562813.html|title=Prince\'s Miami Super Bowl show set halftime\'s gold standard|last=Wilson|first=David|date=January 31, 2020|work=The Miami Herald|access-date=1 February 2020}}</ref>\n\nFollowing the game, controversy emerged about a silhouetted camera shot of Prince, projected against a large sheet by a bright light on the other side of him. The controversy centered around his guitar, which detractors claimed seemed [[phallic]], and critics stating that it "looked embarrassingly rude, crude and unfortunately placed". Though the guitar has been considered by some an extension of a male player\'s sexuality (especially highlighted by such artists as [[Jimi Hendrix]], [[Eddie Van Halen]], and even Prince himself), supporters of Prince say that the show did not, in fact, become any more sexually charged than usual, noting that "a guitar at waist level does look like an enormous phallus".<ref>{{cite news|date=February 7, 2007|title=Some complain of phallic imagery in Prince halftime show|author=Jake Coyle|agency=Associated Press|work=[[Houston Chronicle]]|url=https://www.chron.com/disp/story.mpl/headline/entertainment/4533162.html|access-date=February 8, 2007}}</ref> In 2015, \'\'[[Entertainment Tonight]]\'\' listed the silhouetted camera shot, naming it "Prince\'s Demon Phallus", as one of the top seven Super Bowl halftime moments.<ref>{{cite web | url=http://www.etonline.com/tv/158947_the_top_7_super_bowl_halftime_moments | title=The Top 7 Super Bowl Halftime Moments | publisher=Entertainment Tonight  | date=January 30, 2015 | access-date=February 3, 2018}}</ref> In 2016, shortly after Prince\'s death, the halftime show\'s producer [[Don Mischer]] said that having a silhouetted camera shot of Prince was his idea, but denied it was meant to be a phallic statement.<ref>{{cite web | url=https://deadline.com/2016/04/prince-dead-super-bowl-halftime-don-mischer-remembers-watch-1201741735/ | title=Prince Super Bowl Halftime Show: Producer Don Mischer Remembers | publisher=Deadline | last=Lisa | first= de Moraes | date=April 21, 2016 | access-date=February 3, 2018}}</ref>\n\n===Post-game ceremonies===\nFormer Colts and Dolphins head coach [[Don Shula]] presented the [[Vince Lombardi Trophy]] to the Colts after the game, and [[Peyton Manning]] was named MVP.<ref>{{cite news|url=http://www.superbowl.com/news/story/9870848|title=Marino, Shula to be honored at Super Bowl XLI|publisher=SuperBowl.com|date=December 14, 2006|access-date=December 14, 2006 |archive-url = https://web.archive.org/web/20061216064901/http://www.superbowl.com/news/story/9870848 |archive-date = December 16, 2006}}</ref>\n\n==Game summary==\n\n===First quarter===\nThe Bears won the coin toss and elected to receive. For the first time in Super Bowl history, the game was played in the rain, which was continuous throughout the game.\n\nThe rain did not hinder Bears\' return man [[Devin Hester]], who ran back the opening kickoff 92&nbsp;yards for a touchdown to give Chicago the then earliest lead in Super Bowl history, after only 14 seconds.<ref name="USA Today Super Bowl XLI Play by Play">{{cite news|title=Super Bowl XLI play-by-play|url=https://usatoday30.usatoday.com/sports/football/super/superbowl-xli-plays.htm|work=USA Today|date=January 11, 2002|access-date=August 25, 2011|publisher=USATODAY.com}}</ref> The Colts avoided kicking to Hester for the rest of the game, allowing him only one punt return, and choosing to [[squib kick]] whenever Hester was in the deep kickoff return position.<ref>{{Cite web|url=http://beargoggleson.com/2017/01/29/a-chicago-bears-superstar-the-career-of-devin-hester/|title=A Chicago Bears Superstar: The Career of Devin Hester|last1=Jun 30|first1=Bryan Dietzler/FanSided via Bear Goggles On Fansided|last2=ET|first2=2017 at 10:28p|date=2017-01-29|website=FOX Sports|language=en-US|access-date=2019-04-25}}</ref>\n\nOn Indianapolis\' first drive of the game, defensive back [[Chris Harris (safety)|Chris Harris]] intercepted a deep, third-down pass from quarterback [[Peyton Manning]] and returned it 6 yards to the Bears\' 35-yard line. However, Chicago could not gain a first down on their ensuing possession and they were forced to punt. After several short runs and passes, Manning beat the Bears\' defense with a 53-yard touchdown pass to wide receiver [[Reggie Wayne]], cutting the Bears\' lead to 7–6. The touchdown occurred because of a mental error on the Bears\' secondary. Cornerback [[Charles Tillman]] passed Reggie Wayne onto safety [[Danieal Manning]]. However, Manning chose to follow tight end [[Ben Utecht]] over the middle, leaving Wayne all alone. The score remained the same, at 7–6, after punter/holder [[Hunter Smith]] fumbled the snap on the point-after-touchdown (PAT) attempt. On the ensuing kickoff, Chicago tight end [[Gabe Reid]] fumbled [[Adam Vinatieri]]\'s bouncing kickoff while being tackled by [[Robert Mathis]]; Colts linebacker [[Tyjuan Hagler]] recovered the loose ball. However, on the next play, Indianapolis gave the ball back when running back [[Joseph Addai]] fumbled the hand-off and Bears defensive end [[Mark Anderson (American football)|Mark Anderson]] recovered it.\n\nOn the first play after the turnover, [[Thomas Jones (American football)|Thomas Jones]]\'s 52-yard run moved the ball to the Colts\' 5-yard line. Three plays later, [[Rex Grossman]] threw a 4-yard touchdown pass to receiver [[Muhsin Muhammad]], giving the Bears a 14–6 lead. After forcing an Indianapolis punt, Chicago lost another turnover when [[Cedric Benson]]\'s fumble was recovered by Colts defensive end [[Dwight Freeney]] on the Bears\' 43-yard line. Indianapolis subsequently advanced to the 36-yard line, but decided to punt rather than risk a 53-yard field goal attempt.\n\n===Second quarter===\nFollowing a Chicago punt, Indianapolis drove 47&nbsp;yards and scored with Vinatieri\'s 29-yard field goal to make the score 14–9. Chicago was forced to punt again on their next drive, and return man [[Terrence Wilkins]] returned the ball 12&nbsp;yards to his own 42-yard line. Manning started out the drive with a 22-yard completion to [[Marvin Harrison]]. His next pass went to tight end [[Dallas Clark]] for 17&nbsp;yards. Two more completions moved the ball to the Bears\' 11-yard line, and then [[Dominic Rhodes]] took the ball to the end zone with three consecutive carries, the last one a 1-yard touchdown run to give his team a 16–14 lead with 6:09 left in the second quarter.\n\nAfter another Chicago punt, the Colts advanced to the Bears\' 36-yard line before Tillman ended the drive by forcing and recovering a fumble from tight end [[Bryan Fletcher (American football)|Bryan Fletcher]]. But on the next play, Grossman fumbled a snap, and Colts defensive lineman [[Raheem Brock]] recovered the ball. Manning then led the Colts to Chicago\'s 17-yard line. With two seconds left, Vinatieri attempted a 36-yard field goal, but his kick sailed wide left, and the score remained 16–14 at halftime.\n\n===Third quarter===\nWilkins returned the second half kickoff 26&nbsp;yards to the Colts\'s 38-yard line. On the ensuing possession, Addai rushed five times for 25&nbsp;yards and caught four passes for 19&nbsp;yards as the Colts drove 56&nbsp;yards in 13 plays and scored with a 29-yard field goal from Vinatieri, increasing their lead to 19–14. On the Bears\' next drive, Jones started out with a 14-yard run, and then Muhammad caught a 9-yard pass, bringing up second down and one on the Colts\' 45-yard line. But on the next play, Grossman <!--Grossman tripping is considered to create an inaccurate impression of the play and is not a cited fact.-->was sacked for an 11-yard loss by [[Booger McFarland|Anthony McFarland]]. Then, on third down, he fumbled the snap. Grossman recovered the fumble himself, but the Bears were forced to punt, and Wilkins returned the ball 12&nbsp;yards to the Colts\' 36-yard line. Rhodes then gained 52&nbsp;yards on four carries, with a face-mask penalty adding another 10. Chicago managed to halt the drive at their own 2-yard line, but Vinatieri kicked his third field goal to make the score 22–14.\n\nChicago tight end [[John Gilmore (American football)|John Gilmore]] picked up Vinatieri\'s bouncing kickoff and returned it 9-yards to his own 45-yard line, with an unnecessary roughness penalty on Mathis adding another 15&nbsp;yards and giving the Bears a first down on the Colts\' 40-yard line. Chicago could only gain 14 yards on their ensuing possession, but it was enough for [[Robbie Gould]] to make a 44-yard field goal, cutting the score to 22–17.\n\n===Fourth quarter===\nAfter an Indianapolis 7-play drive ended in a punt, Chicago started on their own 20-yard line with 13:38 left in the game. But four plays later, Colts defensive back [[Kelvin Hayden]] intercepted a pass intended for Muhammad and returned it 56&nbsp;yards for a touchdown.\n\nFrom that point on, the Colts took over the game. Four plays after the ensuing kickoff, Colts defensive back [[Bob Sanders]] intercepted Grossman\'s pass and returned it 38 yards to the Bears 41-yard line. Chicago\'s defense eventually forced a punt, but Smith\'s 32-yard kick pinned the Bears back at their own 8-yard line. The Bears drove to their own 47, but on a fourth down conversion attempt, tight end [[Desmond Clark]] dropped a potential first down reception after being leveled by defensive back [[Matt Giordano]]. The Colts subsequently called eight consecutive [[Dominic Rhodes]] runs before turning the ball over on downs themselves, leaving only 1:42 remaining in regulation. Five plays later, the game was over.\n\n===Box score===\n{{Americanfootballbox | bg=#eee\n|titlestyle=text-align:center;background-color:#000;color:#fff\n|title=Super Bowl XLI: Indianapolis Colts 29, Chicago Bears 17\n|Road=\'\'\'Colts (AFC)\'\'\'\n|R1=6\n|R2=10\n|R3=6\n|R4=7\n|Home=Bears (NFC)\n|H1=14\n|H2=0\n|H3=3\n|H4=0\n|stadium=[[Hard Rock Stadium|Dolphin Stadium]], [[Miami Gardens, Florida]]\n|attendance=74,512<ref name="attendance" />\n|date=February 4, 2007\n|time=6:25 p.m. [[Eastern Time Zone|EST]]\n|weather={{convert|67|°F|°C}}, rainy<ref>This was the first Super Bowl to be played in rain.</ref><ref>{{cite news|title=Super Bowl Game-Time Temperatures|url=http://www.profootballhof.com/news/super-bowl-game-time-temperatures/|publisher=[[Pro Football Hall of Fame]]|access-date=March 10, 2018}}</ref>\n|referee=[[Tony Corrente]]\n|TV=[[NFL on CBS|CBS]]\n|TVAnnouncers=[[Jim Nantz]], [[Phil Simms]], [[Steve Tasker]], [[Solomon Wilcots]], [[Sam Ryan]], [[Lesley Visser]]\n|reference=[http://www.nfl.com/gamecenter/2007020400/2006/POST21/colts@bears/recap Recap]\n|state=uncollapsed\n}}\n{{AmFootballScoreSummaryStart |VisitorName=IND |HomeName=CHI |state=expanded}}\n{{AmFootballScoreSummaryEntry |Quarter=1 |Time=14:46 |Team=CHI |DrivePlays=— |DriveLength=— |DriveTime=— |Type=Other |Other=[[Devin Hester]] 92-yard kickoff return for a touchdown, [[Robbie Gould]] kick good |Visitor=0 |Home=7}}\n{{AmFootballScoreSummaryEntry |Quarter=1 |Time=6:50 |Team=IND |DrivePlays=9 |DriveLength=80 |DriveTime=4:30 |Type=RecTD |Receiver=[[Reggie Wayne]] |QB=[[Peyton Manning]] |yards=53 |kickresult=no good |Kicker=[[Adam Vinatieri]] |Visitor=6 |Home=7}}\n{{AmFootballScoreSummaryEntry |Quarter=1 |Time=4:34 |Team=CHI |DrivePlays=4 |DriveLength=57 |DriveTime=2:00 |Type=RecTD |Receiver=[[Muhsin Muhammad]] |QB=[[Rex Grossman]] |yards=4 |kickresult=good |Kicker=Gould |Visitor=6 |Home=14}}\n{{AmFootballScoreSummaryEntry |Quarter=2 |Time=11:17 |Team=IND |DrivePlays=8 |DriveLength=47 |DriveTime=3:52 |Type=FG |yards=29 |Kicker=Vinatieri |Visitor=9 |Home=14}}\n{{AmFootballScoreSummaryEntry |Quarter=2 |Time=6:09 |Team=IND |DrivePlays=7 |DriveLength=58 |DriveTime=3:08 |Type=RushTD |Runner=[[Dominic Rhodes]] |yards=1 |kickresult=good |Kicker=Vinatieri |Visitor=16 |Home=14}}\n{{AmFootballScoreSummaryEntry |Quarter=3 |Time=7:26 |Team=IND |DrivePlays=13 |DriveLength=56 |DriveTime=7:34 |Type=FG |yards=24 |Kicker=Vinatieri |Visitor=19 |Home=14}}\n{{AmFootballScoreSummaryEntry |Quarter=3 |Time=3:16 |Team=IND |DrivePlays=6 |DriveLength=62 |DriveTime=2:07 |Type=FG |yards=20 |Kicker=Vinatieri |Visitor=22 |Home=14}}\n{{AmFootballScoreSummaryEntry |Quarter=3 |Time=1:14 |Team=CHI |DrivePlays=6 |DriveLength=14 |DriveTime=2:02 |Type=FG |yards=44 |Kicker=Gould |Visitor=22 |Home=17}}\n{{AmFootballScoreSummaryEntry |Quarter=4 |Time=11:44 |Team=IND |DrivePlays=— |DriveLength=— |DriveTime=— |Type=IntTD |Def=[[Kelvin Hayden]] |yards=56 |kickresult=good |Kicker=Vinatieri |Visitor=29 |Home=17}}\n{{AmFootballScoreSummaryEnd |Visitor=29 |Home=17}}\n\n[[File:Bush Congratulates 2006 Colts.jpg|thumb|right|225px|[[President of the United States|U.S. President]] [[George W. Bush]] congratulates the Colts on their win.]]\n\n===Statistical overview===\nThis was the third Super Bowl to have two players rush for more than 100 yards as [[Dominic Rhodes]] had 113 for the Colts and [[Thomas Jones (American football)|Thomas Jones]] had 112 for the Bears. Tony Dungy is the third man to win the Super Bowl as a head coach as well as a player, following [[Tom Flores]] and [[Mike Ditka]].\n\nFor the Colts, Rhodes rushed for 113&nbsp;yards and a touchdown, while also catching an 8-yard reception. Addai rushed for 77&nbsp;yards and caught 10 passes for 66&nbsp;yards. Wilkins returned four kickoffs for 89&nbsp;yards and 3 punts for 42&nbsp;yards in what turned out to be his final NFL game. Adam Vinatieri became the first kicker ever to play in five Super Bowls and the first to win four [[Super Bowl ring]]s. Vinatieri\'s three field goals and two extra points gave him 49 points for the entire 2006 post-season, an NFL record. The Colts\' win was the first major professional championship for [[Indiana]] since the [[Indiana Pacers]]\' [[American Basketball Association|ABA]] title in the 1972–73 season.\n\nHester\'s touchdown for the Bears on the opening kickoff was the first one in Super Bowl history, and the ninth kick return for a touchdown in a Super Bowl; only three of the nine teams who did this went on to win the game (the [[Green Bay Packers]] in [[Super Bowl XXXI]], the [[Baltimore Ravens]] in Super Bowls [[Super Bowl XXXV|XXXV]] and [[Super Bowl XLVII|XLVII]], and the [[Seattle Seahawks]] in [[Super Bowl XLVIII]]). Hester\'s return also kept a streak alive for there being a kickoff return for a touchdown in each Super Bowl played at Dolphin Stadium. Hester\'s and Wayne\'s touchdowns in the first quarter marked the first time in Super Bowl history the first two touchdowns were scored by players from the same college ([[University of Miami]]). Jones was the Bears\' top rusher with 112 yards, while also catching four passes for 18 yards. Desmond Clark was the Bears\' top receiver with six receptions for 64 yards. Grossman completed 20 of 28 passes for 165 yards and a touchdown, with two interceptions, leaving him with a QB rating of 68.3 for the game.<ref>{{cite news|url=http://www.rotowire.com/roto_to_gnews.htm?ID=120586&sport=nfl|title=Notebook|publisher=Rotowire.com|date=February 4, 2007|access-date=February 8, 2007}}</ref> [[Muhsin Muhammad]] became the third player ever to score a touchdown in the Super Bowl for two teams, joining [[Ricky Proehl]] and [[Jerry Rice]].\n\n==Final statistics==\nSources:  [http://www.nfl.com/gamecenter/2007020400/2006/POST21/colts@bears#tab=analyze&recap=fullstory NFL.com Super Bowl XLI], [https://www.pro-football-reference.com/play-index/play_finder.cgi?request=1&super_bowl=1&match=summary_all&year_min=1966&year_max=2016&team_id=clt&opp_id=chi&quarter=1&quarter=2&quarter=3&quarter=4&quarter=5&tr_gtlt=lt&minutes=15&seconds=00&down=0&down=1&down=2&down=3&down=4&yg_gtlt=gt&is_first_down=-1&field_pos_min_field=team&field_pos_max_field=team&end_field_pos_min_field=team&end_field_pos_max_field=team&type=PASS&type=RUSH&type=PUNT&type=KOFF&type=ONSD&type=FG&type=XP&type=2PCR&type=2PCP&is_complete=-1&is_turnover=-1&turnover_type=interception&turnover_type=fumble&is_scoring=-1&score_type=touchdown&score_type=field_goal&score_type=safety&is_sack=-1&include_kneels=-1&no_play=0&order_by=yards Super Bowl XLI Play Finder Ind], [https://www.pro-football-reference.com/play-index/play_finder.cgi?request=1&super_bowl=1&match=summary_all&year_min=1966&year_max=2016&team_id=chi&opp_id=clt&quarter=1&quarter=2&quarter=3&quarter=4&quarter=5&tr_gtlt=lt&minutes=15&seconds=00&down=0&down=1&down=2&down=3&down=4&yg_gtlt=gt&is_first_down=-1&field_pos_min_field=team&field_pos_max_field=team&end_field_pos_min_field=team&end_field_pos_max_field=team&type=PASS&type=RUSH&type=PUNT&type=KOFF&type=ONSD&type=FG&type=XP&type=2PCR&type=2PCP&is_complete=-1&is_turnover=-1&turnover_type=interception&turnover_type=fumble&is_scoring=-1&score_type=touchdown&score_type=field_goal&score_type=safety&is_sack=-1&include_kneels=-1&no_play=0&order_by=yards Super Bowl XLI Play Finder Chi]\n\n===Statistical comparison===\n{| class="wikitable"\n!\n!\'\'\'Indianapolis Colts\'\'\'\n!\'\'\'Chicago Bears\'\'\'\n|-\n|First downs||24||11\n|-\n|First downs rushing||12||3\n|-\n|First downs passing||11||8\n|-\n|First downs penalty||1||0\n|-\n|Third down efficiency||8/18||3/10\n|-\n|Fourth down efficiency||0/1||0/1\n|-\n|Net yards rushing||191||111\n|-\n|Rushing attempts||42||19\n|-\n|Yards per rush||4.5||5.8\n|-\n|Passing – Completions-attempts||25/38||20/28\n|-\n|Times sacked-total yards||1–8||1–11\n|-\n|Interceptions thrown||1||2\n|-\n|Net yards passing||239||154\n|-\n|Total net yards||430||265\n|-\n|Punt returns-total yards||3–42||1–3\n|-\n|Kickoff returns-total yards||4–89||6–138\n|-\n|Interceptions-total return yards||2–94||1–6\n|-\n|Punts-average yardage||4–40.5||5–45.2\n|-\n|Fumbles-lost||2–2||4–3\n|-\n|Penalties-yards||6–40||4–35\n|-\n|Time of possession||38:04||21:56\n|-\n|Turnovers||3||5\n|}\n\n===Individual statistics===\n{{col-float|width=48%}}\n{| class="wikitable"\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Indianapolis Colts|year=2006}};"|Colts Passing\n|-\n!\n!C/ATT<sup>1</sup>\n!Yds\n!TD\n!INT\n!Rating\n|-\n|[[Peyton Manning]]\n|25/38\n|247\n|1\n|1\n|81.8\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Indianapolis Colts|year=2006}};"|Colts Rushing\n|-\n!\n!Car<sup>2</sup>\n!Yds\n!TD\n!LG<sup>3</sup>\n!Yds/Car\n|-\n|[[Dominic Rhodes]]\n|21\n|113\n|1\n|36\n|5.38\n|-\n|[[Joseph Addai]]\n|19\n|77\n|0\n|14\n|4.05\n|-\n|[[Dallas Clark]]\n|1\n|1\n|0\n|1\n|1.00\n|-\n|Peyton Manning\n|1\n|0\n|0\n|0\n|0.00\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Indianapolis Colts|year=2006}};"|Colts Receiving\n|-\n!\n!Rec<sup>4</sup>\n!Yds\n!TD\n!LG<sup>3</sup>\n!Target<sup>5</sup>\n|-\n|Joseph Addai\n|10\n|66\n|0\n|12\n|10\n|-\n|[[Marvin Harrison]]\n|5\n|59\n|0\n|22\n|11\n|-\n|Dallas Clark\n|4\n|36\n|0\n|17\n|6\n|-\n|[[Reggie Wayne]]\n|2\n|61\n|1\n|53\n|4\n|-\n|[[Bryan Fletcher (American football)|Bryan Fletcher]]\n|2\n|9\n|0\n|6\n|2\n|-\n|Dominic Rhodes\n|1\n|8\n|0\n|8\n|2\n|-\n|[[Ben Utecht]]\n|1\n|8\n|0\n|8\n|2\n|-\n|[[Aaron Moorehead]]\n|0\n|0\n|0\n|0\n|1\n|}\n{{col-float-break|width=48%}}\n{| class="wikitable"\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Chicago Bears}};"|Bears Passing\n|-\n!\n!C/ATT<sup>1</sup>\n!Yds\n!TD\n!INT\n!Rating\n|-\n|[[Rex Grossman]]\n|20/28\n|165\n|1\n|2\n|68.3\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Chicago Bears}};"|Bears Rushing\n|-\n!\n!Car<sup>2</sup>\n!Yds\n!TD\n!LG<sup>3</sup>\n!Yds/Car\n|-\n|[[Thomas Jones (American football)|Thomas Jones]]\n|15\n|112\n|0\n|52\n|7.47\n|-\n|Rex Grossman\n|2\n|0\n|0\n|0\n|0.00\n|-\n|[[Cedric Benson]]\n|2\n|–1\n|0\n|4\n|–0.50\n|-\n! colspan="6" style="{{NFLPrimaryStyle|Chicago Bears}};"|Bears Receiving\n|-\n!\n!Rec<sup>4</sup>\n!Yds\n!TD\n!LG<sup>3</sup>\n!Target<sup>5</sup>\n|-\n|[[Desmond Clark]]\n|6\n|64\n|0\n|18\n|9\n|-\n|[[Bernard Berrian]]\n|4\n|38\n|0\n|14\n|7\n|-\n|[[Thomas Jones (American football)|Thomas Jones]]\n|4\n|18\n|0\n|14\n|4\n|-\n|[[Muhsin Muhammad]]\n|3\n|35\n|1\n|22\n|4\n|-\n|[[Jason McKie]]\n|2\n|8\n|0\n|4\n|2\n|-\n|[[Rashied Davis]]\n|1\n|2\n|0\n|2\n|2\n|}\n{{col-float-end}}\n<sup>1</sup>Completions/attempts\n<sup>2</sup>Carries\n<sup>3</sup>Long gain\n<sup>4</sup>Receptions\n<sup>5</sup>Times targeted\n\n==Starting lineups==\n\n:Source:<ref>{{cite web|title=Super Bowl XLI–National Football League Game Summary|url=http://www.nfl.com/liveupdate/gamecenter/29192/CHI_Gamebook.pdf|publisher=National Football League|date=February 4, 2007|access-date=March 6, 2016}}</ref>\n\n{{hilite|Hall of Fame‡|#FFCC00}}\n{| class="wikitable"\n! width="140px" style="{{NFLPrimaryStyle|Indianapolis Colts|year=2006}};" | Indianapolis !! Position !! Position !! width="140px" style="{{NFLPrimaryStyle|Chicago Bears}};" | Chicago\n|-\n| colspan=4 align=center | \'\'\'Offense\'\'\'\n|-\n| [[Reggie Wayne]] || colspan="2" style="text-align:center;" | WR || [[Muhsin Muhammad]]\n|-\n| [[Tarik Glenn]] || colspan="2" style="text-align:center;" | LT || [[John Tait (American football)|John Tait]]\n|-\n| [[Ryan Lilja]] || colspan="2" style="text-align:center;" | LG || [[Ruben Brown]]\n|-\n| [[Jeff Saturday]] || colspan="2" style="text-align:center;" | C || [[Olin Kreutz]]\n|-\n| [[Jake Scott (guard)|Jake Scott]] || colspan="2" style="text-align:center;" | RG || [[Roberto Garza]]\n|-\n| [[Ryan Diem]] || colspan="2" style="text-align:center;" | RT || [[Fred Miller (American football, born 1973)|Fred Miller]]\n|-\n| [[Dallas Clark]] || colspan="2" style="text-align:center;" | TE || [[Desmond Clark]]\n|-\n| bgcolor="#FFCC00" | [[Marvin Harrison]]‡ || colspan="2" style="text-align:center;" | WR || [[Bernard Berrian]]\n|-\n| bgcolor="#FFCC00" | [[Peyton Manning]]‡ || colspan="2" style="text-align:center;" | QB || [[Rex Grossman]]\n|-\n|[[Dominic Rhodes]]|| colspan="2" style="text-align:center;" | RB || [[Thomas Jones (American football)|Thomas Jones]]\n|-\n| [[Ben Utecht]] || style="text-align:center;" | H-B || style="text-align:center;" | FB || [[Jason McKie]]\n|-\n| colspan=4 align=center | \'\'\'Defense\'\'\'\n|-\n| [[Robert Mathis]] || colspan="2" style="text-align:center;" | LE || [[Adewale Ogunleye]]\n|-\n| [[Booger McFarland]] || colspan="2" style="text-align:center;" | LDT || [[Tank Johnson]]\n|-\n| [[Raheem Brock]] || colspan="2" style="text-align:center;" | RDT || [[Ian Scott (American football)|Ian Scott]]\n|-\n| [[Dwight Freeney]] || colspan="2" style="text-align:center;" | RE || [[Alex Brown (defensive end)|Alex Brown]]\n|-\n| [[Cato June]] || colspan="2" style="text-align:center;" | WLB || [[Lance Briggs]]\n|-\n| [[Gary Brackett]] || colspan="2" style="text-align:center;" | MLB || bgcolor="#FFCC00" | [[Brian Urlacher]]‡\n|-\n| [[Rob Morris (American football)|Rob Morris]] || colspan="2" style="text-align:center;" | SLB || [[Hunter Hillenmeyer]]\n|-\n| [[Nick Harper (American football)|Nick Harper]] || colspan="2" style="text-align:center;" | LCB || [[Charles Tillman]]\n|-\n| [[Jason David]] || colspan="2" style="text-align:center;" | RCB || [[Nathan Vasher]]\n|-\n| [[Antoine Bethea]] || colspan="2" style="text-align:center;" | SS || [[Chris Harris (safety)|Chris Harris]]\n|-\n| [[Bob Sanders]] || colspan="2" style="text-align:center;" | FS || [[Danieal Manning]]\n|-\n| colspan=4 align=center | \'\'\'Special Teams\'\'\'\n|-\n| [[Adam Vinatieri]] || colspan="2" style="text-align:center;" | K || [[Robbie Gould]]\n|-\n| [[Hunter Smith]] || colspan="2" style="text-align:center;" | P || [[Brad Maynard]]\n|}\n\n==Officials==\n*\'\'\'Referee:\'\'\' [[Tony Corrente]] #99 (first Super Bowl)\n*\'\'\'Umpire:\'\'\' [[Carl Paganelli]] #124 (second Super Bowl, also XXXIX, XLVI and XLVIII)\n*\'\'\'Head Linesman:\'\'\' [[George Hayward (linesman)|George Hayward]] #54 (first Super Bowl)\n*\'\'\'Line Judge:\'\'\' [[Ron Marinucci]] #107 (first Super Bowl)\n*\'\'\'Field Judge:\'\'\' [[Jim Saracino]] #58 (first Super Bowl)\n*\'\'\'Side Judge:\'\'\' [[John Parry (American football official)|John Parry]] #132 (first Super Bowl, also XLVI and LIII as referee)\n*\'\'\'Back Judge:\'\'\' [[Perry Paganelli]] #46 (first Super Bowl, also LII)\n*\'\'\'Alternate Referee:\'\'\' [[Jeff Triplette]] #42 \n*\'\'\'Alternate Umpire:\'\'\' [[Butch Hannah]] #40\n*\'\'\'Alternate Line Judge:\'\'\' [[Carl Johnson (referee)|Carl Johnson]] #101\n*\'\'\'Alternate Field Judge:\'\'\' [[Buddy Horton]] #82\n*\'\'\'Alternate Back Judge:\'\'\' [[Richard Reels]] #83\n\n==Controversies prohibiting fan parties and presentations==\n\n===Prohibiting tailgating===\nThe NFL upset many fans by banning the traditional practice of [[tailgate party|tailgating]] at Super Bowl XLI. Originally, spokesmen for Dolphin Stadium announced that tailgating would be permitted as usual. However, the NFL quickly contradicted this statement, announcing an NFL owner-imposed ban on all tailgating and prohibiting non-ticketed fans within a two-block-radius of the stadium.<ref>{{cite web|url=http://www.wsvn.com/news/articles/super_bowl_xli/MI38526/|title=NFL Officials Ban Tailgating at Super Bowl XLI|publisher=WSVN.com|date=January 29, 2007|access-date=January 30, 2007 |archive-url = https://web.archive.org/web/20070202053004/http://www.wsvn.com/news/articles/super_bowl_xli/MI38526/ |archive-date = February 2, 2007}}</ref>\n\n===Prohibiting church display===\nThe NFL upset a large number of fans by threatening churches with lawsuits if they had Super Bowl parties. National Football League assistant [[Lawyer|counsel]] Rachel L. Margolies sent a letter to the [[Fall Creek Place, Indianapolis|Fall Creek]] [[Baptists|Baptist Church]] in [[Indianapolis]], ordering the church to cancel its party and remove the trademarked Super Bowl name from its website.<ref>{{cite web|url=http://www.theindychannel.com/sports/10931886/detail.html|title="church": Indy Church Ignores NFL, Shows Game Despite Warning|publisher=[[WRTV]]|date=February 1, 2007|access-date=December 28, 2007|archive-date=January 31, 2008|archive-url=https://web.archive.org/web/20080131201249/http://www.theindychannel.com/sports/10931886/detail.html|url-status=dead}}</ref> She said that the church could not use the words "Super Bowl" as it violates trademark law, could not charge admission as that violates [[copyright]] law, could not use its projection screen (only one television could be used and it could not be over 55&nbsp;inches), and could not "promote a message" in connection with the game. Regarding the last point, the Fall Creek Baptist church planned to also show an extra video to highlight the Christian testimonies of Colts coach Tony Dungy and Chicago Bears coach Lovie Smith. "While this may be a noble message", Rachel L. Margolies wrote, "we are consistent in refusing the use of our game broadcasts in connection with events that promote a message, no matter the content."<ref>{{cite web|url=http://www.theindychannel.com/sports/10902206/detail.html|title="NFLchurch": NFL Defends Stance Against Church\'s Super Bowl Plan|publisher=[[WRTV]]|date=February 1, 2007|access-date=December 28, 2007|archive-date=February 6, 2007|archive-url=https://web.archive.org/web/20070206065500/http://www.theindychannel.com/sports/10902206/detail.html|url-status=dead}}</ref> [[Sports bar]]s nationwide were allowed to show the game, as were businesses that televised sports as part of their everyday operations because they didn\'t charge admission and they left the message the NFL intended intact.\n\n\'\'[[The Indianapolis Star]]\'\' picked up the story, and soon pastors across the U.S. were working to cancel their parties. Immediately the NFL received a backlash of bad publicity, with indignant football fans in constant sarcasm (akin to rules imposed about on-field behavior i.e. taunting opposition teams) referring to the NFL as the "No Fun League."<ref>{{cite web|url=http://football.about.com/b/2007/02/01/nfl-strikes-down-church-party.htm|title="NoFun": NFL Strikes Down Church Party|publisher=[[About.com]]|date=February 1, 2007|access-date=December 28, 2007|archive-url=https://web.archive.org/web/20110611000502/http://football.about.com/b/2007/02/01/nfl-strikes-down-church-party.htm|archive-date=June 11, 2011|url-status=dead}}</ref> For example, the enforcement of this policy earned the [[National Football League|NFL]] a "Worst Person in the World" silver on the edition of February 2, 2007 of \'\'[[Countdown with Keith Olbermann]]\'\'.<ref>{{cite web|url=https://www.nbcnews.com/id/16989424|title="Worst": Less casualties or fuzzy math|publisher=[[NBC News]]|date=February 5, 2007|access-date=February 10, 2007|first=Keith|last=Olbermann|author-link=Keith Olbermann}}</ref>\n\nAfter a long series of bad press, the NFL issued a written statement clarifying their policy, saying that they did not object to churches hosting Super Bowl parties so long as they did not charge admission and showed the game on "a television of the type commonly used at home". This statement did not attempt to forbid coordination of any other message with the game, something typically done by churches, nor did it attempt to forbid the use of the term "Super Bowl."<ref>{{cite news|title=No sanctuary for Super Bowl flock: Churches scramble to cancel parties after an NFL warning|work=[[Los Angeles Times]]|date=February 3, 2007|author=Stephanie Simon}}</ref><ref>{{cite web|url=http://cbs.sportsline.com/nfl/story/9971155|title=NFL warns churches to abide by Super Bowl party guidelines|access-date=February 6, 2007|url-status=dead|archive-url=https://web.archive.org/web/20070205084710/http://www.cbs.sportsline.com/nfl/story/9971155|archive-date=February 5, 2007}}</ref>\n\n==References==\n{{Reflist|30em}}\n\n==External links==\n{{Commons category|Super Bowl XLI}}\n* {{YouTube|id=pAsarBgakuc|title=Super Bowl XLI: NFL Full Game}}\n*[http://www.superbowl.com Official Super Bowl website]\n*[https://web.archive.org/web/20090213155803/http://www.southfloridasuperbowl.com/ Host committee website]\n*[http://www.nfl.com/superbowl/history/recap/sbxli Game summary] at [[NFL.com]]\n\n{{Super Bowl}}\n{{Super Bowl XLI}}\n{{Indianapolis Colts}}\n{{Chicago Bears}}\n{{2006 NFL season by team}}\n{{NFLC-SuperBowl}}\n{{NFL on CBS}}\n\n{{Good article}}\n\n{{DEFAULTSORT:Super Bowl 041}}\n[[Category:2006 National Football League season]]\n[[Category:2007 in American football]]\n[[Category:2007 in American television]]\n[[Category:2007 in sports in Florida]]\n[[Category:Chicago Bears postseason]]\n[[Category:Indianapolis Colts postseason]]\n[[Category:American football in Florida]]\n[[Category:Super Bowl|Super Bowl 041]]\n[[Category:February 2007 sports events in the United States]]\n[[Category:Sports competitions in Miami Gardens, Florida]]\n[[Category:Television controversies in the United States]]'




```python
# example of section of page we will be scraping data from
parsed = wtp.parse(ex)
parsed.templates[3]
```




    Template('{{Infobox Super Bowl\n| type = sb\n| name = XLI\n| image = Super Bowl XLI logo.svg\n| visitor = [[2006 Indianapolis Colts season|Indianapolis Colts]]\n| visitor_abbr = IND\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Tony Dungy]]\n| visitor_record = 12–4\n| visitor_place = 3\n| home = [[2006 Chicago Bears season|Chicago Bears]]\n| home_abbr = CHI\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Lovie Smith]]\n| home_record = 13–3\n| home_place = 1\n| visitor_total = 29\n| home_total = 17\n| visitor_qtr1 = 6\n| visitor_qtr2 = 10\n| visitor_qtr3 = 6\n| visitor_qtr4 = 7\n| home_qtr1 = 14\n| home_qtr2 = 0\n| home_qtr3 = 3\n| home_qtr4 = 0\n| date = {{start-date|February 4, 2007}}\n| stadium = [[Hard Rock Stadium|Dolphin Stadium]]\n| city = [[Miami Gardens, Florida]]\n| attendance = 74,512<ref name="attendance">{{cite web|title=Super Bowl Summaries|url=https://nflcommunications.com/media/Publications/2019%20Postseason%20Media%20Guide.pdf#page=113|publisher=NFL Enterprises, LLC|work=2019 NFL Postseason Media Guide|date=January 2, 2020|access-date=May 23, 2020}}</ref>\n| weather = Rain\n| odds = Colts by 7<ref>{{cite news|last=DiNitto|first=Marcus|title=Super Bowl Betting History – Underdogs on Recent Roll|url=http://linemakers.sportingnews.com/nfl/2015-01-25/super-bowl-betting-history-patriots-vs-seahawks-point-spread-line-vegas-odds|magazine=[[Sporting News]]|date=January 25, 2015|access-date=February 4, 2015}}</ref><ref>{{cite web|url=http://www.vegasinsider.com/nfl/superbowl/history/|title=Super Bowl History|publisher=Vegas Insider|access-date=February 4, 2015}}</ref>\n| MVP = [[Peyton Manning]], [[quarterback]]\n| anthem = [[Billy Joel]]\n| halftime = [[Prince (musician)|Prince]] and the [[Marching 100|Florida A&M University Marching 100]]\n| network = [[NFL on CBS|CBS]]\n| announcers = [[Jim Nantz]], [[Phil Simms]], [[Steve Tasker]], [[Solomon Wilcots]], [[Sam Ryan]] and [[Lesley Visser]]\n| coin_toss = [[Dan Marino]] and Norma Hunt (widow of [[Lamar Hunt]])<ref>{{cite web| url=http://sports.espn.go.com/nfl/playoffs06/news/story?id=2752126|title=Goodell wants players held accountable|author=[[ESPN]]|access-date=January 17, 2008}}</ref>\n| referee = [[Tony Corrente]]<ref>{{cite web| url=http://www.superbowl.com/news/story/9966329|title=Corrente to referee first Super Bowl|author=NFL.com|access-date=January 17, 2008 |archive-url = https://web.archive.org/web/20070509040851/http://www.superbowl.com/news/story/9966329|archive-date=May 9, 2007 |author-link= National Football League}}</ref>\n| HOFers = \'\'\'Colts:\'\'\' <!--PLEASE NOTE: "Future" in this case means players who have been\nOFFICIALLY inducted after this Super Bowl has been played. Players who have not yet been\nofficially inducted will be removed. Any addition of "presumed" players will also be\nremoved on grounds of [[WP:No Original Research]] and [[WP:Wikipedia is not a crystal ball]].\nSee also [[Pro Football Hall of Fame#Criticism]] on the criticism of HOF voters, who tend\nto have the habit of turning down "presumed, surefire" players for several years\n(like punter [[Ray Guy]] and QB [[Ken Stabler]]). Thank you for your cooperation.--> [[Bill Polian]] (general manager), [[Tony Dungy]] (head coach), [[Marvin Harrison]], [[Peyton Manning]] <br /> \'\'\'Bears:\'\'\' [[Brian Urlacher]]\n| rating = 42.6 (national)<ref>{{cite web|url=http://publications.mediapost.com/index.cfm?fuseaction=Articles.showArticleHomePage&art_aid=55057|archive-url=https://archive.today/20120724042825/http://publications.mediapost.com/index.cfm?fuseaction=Articles.showArticleHomePage&art_aid=55057|url-status=dead|archive-date=July 24, 2012|publisher=publications.mediapost.com|title=Blockbuster: Best Super Bowl Ratings In 7 Years|access-date=February 6, 2007}}</ref> <br/><small>(est. 93.2 million viewers)</small><ref>[http://www.superbowl.com/news/story/9976864 Colts-Bears draws No. 3 audience of all time] {{webarchive|url=https://web.archive.org/web/20070208000737/http://www.superbowl.com/news/story/9976864 |date=February 8, 2007 }} SuperBowl.com, February 5, 2007.</ref><br/>50.2 (Chicago)<ref>{{cite news | url=http://www.suntimes.com/business/feder/244726,CST-FIN-feder06.article | title=Super Bowl ratings fall short of record | access-date=February 6, 2007 | work=Chicago Sun-Times | first=Robert | last=Feder | date=February 6, 2007 | url-status=dead | archive-url=https://web.archive.org/web/20070207234741/http://www.suntimes.com/business/feder/244726%2CCST-FIN-feder06.article | archive-date=February 7, 2007 | df=mdy-all }}</ref><br/>55.5 ([[Indianapolis]])\n| share = 64 (national)<br/>77 (Chicago)<br/>83 (Indianapolis)\n|commercial=US$2.6 million<ref name=price/>\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Marv Albert]], [[Boomer Esiason]], [[John Dockery]] and [[Bonnie Bernstein]]\n| last = XL\n| next = XLII\n}}')



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
wtp.parse(DATA["query"]["pages"]["53119758"]["revisions"][0]["slots"]["main"]["*"]).templates[3]
```




    [Template('{{Short description|2022 National Football League championship game}}'),
     Template('{{Redirect|2022 Super Bowl|the Super Bowl at the completion of the 2022 season|Super Bowl LVII}}'),
     Template('{{Use mdy dates|date=February 2022}}'),
     Template('{{Infobox NFL game\n<!--Please do not add anything else to this Infobox until it has been officially announced by the National Football League or NBC. Thank you.-->\n| type = sb\n| name = LVI\n| full name = <!--ONLY if name is not a simple year, roman numeral, etc.-->\n| image = Super Bowl LVI logo.png\n| image_size = 250px \n| caption = \n| visitor = [[2021 Los Angeles Rams season|Los Angeles Rams]]\n| visitor_abbr = LAR\n| visitor_conf = [[National Football Conference|NFC]]\n| visitor_coach = [[Sean McVay]]\n| visitor_record = 12–5\n| visitor_place = 4\n| home = [[2021 Cincinnati Bengals season|Cincinnati Bengals]]\n| home_abbr = CIN\n| home_conf = [[American Football Conference|AFC]]\n| home_coach = [[Zac Taylor]]\n| home_record = 10–7\n| home_place = 4\n| visitor_qtr1 = 7\n| visitor_qtr2 = 6\n| visitor_qtr3 = 3\n| visitor_qtr4 = 7\n| home_qtr1 = 3\n| home_qtr2 = 7\n| home_qtr3 = 10\n| home_qtr4 = 0\n| date = February 13, 2022\n| stadium = [[SoFi Stadium]]\n| city = [[Inglewood, California]]\n| attendance = 70,048\n| odds = Rams by 4<ref>{{cite web|title=NFL Odds And Lines - SportsLine.com|url=https://www.sportsline.com/nfl/odds/|access-date=February 11, 2022|website=SportsLine|archive-date=January 15, 2022|archive-url=https://web.archive.org/web/20220115015951/https://www.sportsline.com/nfl/odds/|url-status=live}}</ref>\n| MVP = [[Cooper Kupp]], [[wide receiver]]\n| anthem = [[Mickey Guyton]]<ref>{{cite news|last=McCarriston|first=Shanna|title=Super Bowl 2022: Mickey Guyton to sing national anthem, other pregame performers at SoFi Stadium in L.A.|url=https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|website=CBSsports.com|date=February 12, 2022|access-date=February 12, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031742/https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|url-status=live}}</ref>\n| coin_toss = [[Billie Jean King]]\n| referee = [[Ronald Torbert]]<ref name="FootballZebras">{{cite web |last1=Filipe |first1=Cameron |title=Ron Torbert is the referee for Super Bowl LVI |url=https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |website=footballzebras.com |date=January 25, 2022 |access-date=January 25, 2022 |archive-date=January 25, 2022 |archive-url=https://web.archive.org/web/20220125175824/https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |url-status=live }}</ref><ref>{{cite web|title=NFL announces Super Bowl LVI officiating crew; four of eight officials have prior SB experience|url=https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|website=NFL.com|date=January 25, 2022|access-date=January 25, 2022|archive-date=January 25, 2022|archive-url=https://web.archive.org/web/20220125210756/https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|url-status=live}}</ref>\n| HOFers =\n| halftime = [[Dr. Dre]], [[Snoop Dogg]], [[Eminem]], [[Mary J. Blige]] and [[Kendrick Lamar]] featuring [[50 Cent]] and [[Anderson .Paak]]<ref name="Halftime-Show"/><ref name="Halftime-ASL"/>\n| network = <!-- DO NOT list streaming services. -->[[NBC Sunday Night Football|NBC]]<br/>[[Telemundo]] (Spanish)<ref name=mundo>{{cite web |url=https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |title=Who is broadcasting the 2022 Super Bowl? TV channel, announcers, live stream for Bengals vs. Rams |date=February 6, 2022 |website=[[NBC Sports]] |access-date=February 8, 2022 |archive-date=February 6, 2022 |archive-url=https://web.archive.org/web/20220206175757/https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |url-status=live }}</ref><!-- Do not change this without a source. -->\n| announcers = [[Al Michaels]] (play-by-play)<br/>[[Cris Collinsworth]] (analyst)<br/>[[Michele Tafoya]] and [[Kathryn Tappen]] (sideline reporters)<br/>[[Terry McAulay]] (rules analyst)\n| rating = 40.1 (national)<br>36.7 (Los Angeles)<br>46.1 (Cincinnati)<br>U.S. TV viewership: 101.1&nbsp;million<ref name = viewers>{{cite news|url = https://variety.com/2022/tv/news/super-bowl-lvi-ratings-nbc-1235181334/|title = TV Ratings: NBC\'s Super Bowl LVI Telecast Draws Over 100 Million Viewers|work = [[Variety (magazine)|Variety]]|date = February 15, 2022|accessdate = February 15, 2022|last = Zorrilla|first = Mónica Marie}}</ref>\n| share =\n| commercial = $6.5–7&nbsp;million<ref>{{cite web|title=NBC Sells Out Super Bowl LVI, Hitting $7M for 30-Second Ads|url=https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|website=HollywoodReporter.com|last=Weprin|first= Alex|date=February 3, 2022|access-date=February 5, 2022|archive-date=February 5, 2022|archive-url=https://web.archive.org/web/20220205024403/https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|url-status=live}}</ref>\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Kurt Warner]] (analyst)<br>[[Laura Okmin]] and [[Mike Golic]] (sideline reporters)<br/>[[Gene Steratore]] (rules analyst)\n| last = LV\n| next = LVII\n}}'),
     Template('{{cite web|title=NFL Odds And Lines - SportsLine.com|url=https://www.sportsline.com/nfl/odds/|access-date=February 11, 2022|website=SportsLine|archive-date=January 15, 2022|archive-url=https://web.archive.org/web/20220115015951/https://www.sportsline.com/nfl/odds/|url-status=live}}'),
     Template('{{cite news|last=McCarriston|first=Shanna|title=Super Bowl 2022: Mickey Guyton to sing national anthem, other pregame performers at SoFi Stadium in L.A.|url=https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|website=CBSsports.com|date=February 12, 2022|access-date=February 12, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031742/https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|url-status=live}}'),
     Template('{{cite web |last1=Filipe |first1=Cameron |title=Ron Torbert is the referee for Super Bowl LVI |url=https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |website=footballzebras.com |date=January 25, 2022 |access-date=January 25, 2022 |archive-date=January 25, 2022 |archive-url=https://web.archive.org/web/20220125175824/https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |url-status=live }}'),
     Template('{{cite web|title=NFL announces Super Bowl LVI officiating crew; four of eight officials have prior SB experience|url=https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|website=NFL.com|date=January 25, 2022|access-date=January 25, 2022|archive-date=January 25, 2022|archive-url=https://web.archive.org/web/20220125210756/https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|url-status=live}}'),
     Template('{{cite web |url=https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |title=Who is broadcasting the 2022 Super Bowl? TV channel, announcers, live stream for Bengals vs. Rams |date=February 6, 2022 |website=[[NBC Sports]] |access-date=February 8, 2022 |archive-date=February 6, 2022 |archive-url=https://web.archive.org/web/20220206175757/https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |url-status=live }}'),
     Template("{{cite news|url = https://variety.com/2022/tv/news/super-bowl-lvi-ratings-nbc-1235181334/|title = TV Ratings: NBC's Super Bowl LVI Telecast Draws Over 100 Million Viewers|work = [[Variety (magazine)|Variety]]|date = February 15, 2022|accessdate = February 15, 2022|last = Zorrilla|first = Mónica Marie}}"),
     Template('{{cite web|title=NBC Sells Out Super Bowl LVI, Hitting $7M for 30-Second Ads|url=https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|website=HollywoodReporter.com|last=Weprin|first= Alex|date=February 3, 2022|access-date=February 5, 2022|archive-date=February 5, 2022|archive-url=https://web.archive.org/web/20220205024403/https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|url-status=live}}'),
     Template('{{cite web |title=17th Game Press Release |url=https://nflcommunications.com/Documents/2021%20Football%20Information/03%2030%2021%20-%20Enhanced%20Season%20Structure.pdf |website=NFLCommunications.com |access-date=March 30, 2021 |archive-date=March 30, 2021 |archive-url=https://web.archive.org/web/20210330184240/https://nflcommunications.com/Documents/2021%20Football%20Information/03%2030%2021%20-%20Enhanced%20Season%20Structure.pdf |url-status=live }}'),
     Template('{{cite news|last=Patra|first=Kevin|title=Bengals, not Rams, are official home team for Super Bowl LVI at SoFi Stadium|url=https://www.nfl.com/news/bengals-not-rams-are-official-home-team-for-super-bowl-lvi-at-sofi-stadium|website=NFL.com|date=January 31, 2022|access-date=February 1, 2022|url-status=live|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131234719/https://www.nfl.com/news/bengals-not-rams-are-official-home-team-for-super-bowl-lvi-at-sofi-stadium}}'),
     Template('{{cite news |last1=McCarriston |first1=Shanna |title=Super Bowl 2022: Super Bowl LVI logo unveiled in video featuring Snoop Dogg |url=https://www.cbssports.com/nfl/news/super-bowl-2022-super-bowl-lvi-logo-unveiled-in-video-featuring-snoop-dogg/ |work=CBS Sports |date=February 9, 2021 |access-date=February 17, 2021 |archive-date=February 10, 2021 |archive-url=https://web.archive.org/web/20210210221511/https://www.cbssports.com/nfl/news/super-bowl-2022-super-bowl-lvi-logo-unveiled-in-video-featuring-snoop-dogg/ |url-status=live }}'),
     Template('{{cite web |last1=Sykes |first1=Mike D. |title=What is the date of the Super Bowl in 2022? |url=https://ftw.usatoday.com/2021/02/what-is-the-date-of-the-super-bowl-in-2022 |work=For the Win |date=February 7, 2021 |access-date=February 17, 2021 |archive-date=February 7, 2021 |archive-url=https://web.archive.org/web/20210207175611/https://ftw.usatoday.com/2021/02/what-is-the-date-of-the-super-bowl-in-2022 |url-status=live }}'),
     Template('{{nfly|1975}}'),
     Template('{{cite web |last1=Florio |first1=Mike |title=Two No. 4 seeds meet in Super Bowl |url=https://profootballtalk.nbcsports.com/2022/01/30/two-no-4-seeds-meet-in-super-bowl/ |website=[[Profootballtalk.com|Pro Football Talk]] |access-date=January 31, 2022 |date=January 30, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131044612/https://profootballtalk.nbcsports.com/2022/01/30/two-no-4-seeds-meet-in-super-bowl/ |url-status=live }}'),
     Template('{{cite magazine |last1=Chan |first1=Melissa |title=NFL Announces 3 New Super Bowl Host Cities |url=https://time.com/4346925/super-bowl-cities-nfl-football-2019-2021/ |magazine=[[Time (magazine)|Time]] |access-date=January 31, 2022 |date=May 24, 2016 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131040435/https://time.com/4346925/super-bowl-cities-nfl-football-2019-2021/ |url-status=live }}'),
     Template('{{Cite news|title=Super Bowl LV relocated to Tampa; L.A. will host SB LVI|url=http://www.nfl.com/news/story/0ap3000000810644/article/super-bowl-lv-relocated-to-tampa-la-to-host-sb-lvi?campaign=fb-nf-sf81243817-sf81243817|work=NFL.com|access-date=May 23, 2017|archive-date=June 3, 2017|archive-url=https://web.archive.org/web/20170603010752/http://www.nfl.com/news/story/0ap3000000810644/article/super-bowl-lv-relocated-to-tampa-la-to-host-sb-lvi?campaign=fb-nf-sf81243817-sf81243817|url-status=live}}'),
     Template('{{cite news|last1=McCarriston|first1=Shanna|date=February 9, 2021|title=Super Bowl 2022: Super Bowl LVI logo unveiled in video featuring Snoop Dogg|work=CBS Sports|url=https://www.cbssports.com/nfl/news/super-bowl-2022-super-bowl-lvi-loJango-unveiled-in-video-featuring-snoop-dogg/}}'),
     Template('{{see also|Impact of the COVID-19 pandemic on sports|COVID-19 pandemic in California}}'),
     Template('{{cite web|title=NFL confident about L.A. Super Bowl amid pandemic, but still has alternate sites|url=https://www.latimes.com/sports/story/2022-01-05/att-stadium-in-dallas-selected-as-super-bowl-lvi-alternate-site|access-date=January 6, 2022|work=Los Angeles Times|date=January 6, 2022|archive-date=January 6, 2022|archive-url=https://web.archive.org/web/20220106013428/https://www.latimes.com/sports/story/2022-01-05/att-stadium-in-dallas-selected-as-super-bowl-lvi-alternate-site|url-status=live}}'),
     Template('{{cite web|date=January 13, 2022|title=NFL, organizers say Super Bowl not moving from SoFi Stadium amid COVID-19 surge in California|url=https://www.espn.com/nfl/story/_/id/33059444/organizers-say-super-bowl-not-moving-sofi-stadium-amid-covid-19-surge-california|access-date=January 13, 2022|website=ESPN|archive-date=January 13, 2022|archive-url=https://web.archive.org/web/20220113224920/https://www.espn.com/nfl/story/_/id/33059444/organizers-say-super-bowl-not-moving-sofi-stadium-amid-covid-19-surge-california|url-status=live}}'),
     Template("{{cite web|url=https://www.reuters.com/lifestyle/sports/la-super-bowl-red-carpet-ready-full-stadium-expected-2022-01-13/|title=LA Super Bowl 'red carpet ready', full stadium expected|last=Carroll|first=Rory|publisher=Reuters|date=January 14, 2022|access-date=February 7, 2022|archive-date=February 7, 2022|archive-url=https://web.archive.org/web/20220207155033/https://www.reuters.com/lifestyle/sports/la-super-bowl-red-carpet-ready-full-stadium-expected-2022-01-13/|url-status=live}}"),
     Template('{{cite news|date=January 22, 2021|title=Super Bowl to Host 22,000 Fans|url=https://www.nytimes.com/2021/01/22/sports/football/super-bowl-attendance-fans.html|access-date=February 7, 2022|work=The New York Times|archive-date=February 7, 2022|archive-url=https://web.archive.org/web/20220207155033/https://www.nytimes.com/2021/01/22/sports/football/super-bowl-attendance-fans.html|url-status=live}}'),
     Template('{{cite web|url=https://www.nbclosangeles.com/news/local/inglewood-super-bowl-lvi-sofi-stadium-kn95-mask/2806448/|title=Every Fan at Super Bowl LVI at SoFi Stadium Gets a KN95 Mask, County Says|website=NBCLosAngeles.com|date=January 25, 2022|access-date=February 7, 2022|archive-date=February 7, 2022|archive-url=https://web.archive.org/web/20220207153835/https://www.nbclosangeles.com/news/local/inglewood-super-bowl-lvi-sofi-stadium-kn95-mask/2806448/|url-status=live}}'),
     Template('{{cite web|url=https://kutv.com/sports/nfl/super-bowl-lvi-super-bowl-56-mask-requirement-vaccine-mandate-covid-19-pcr-test-antigen-test-sofi-stadium-inglewood-california|title=Fans attending the Super Bowl will be required to wear masks, show proof of vaccination|last=Jacobson|first=Matt|website=kutv.com|date=January 29, 2022|access-date=February 7, 2022|archive-date=February 7, 2022|archive-url=https://web.archive.org/web/20220207153833/https://kutv.com/sports/nfl/super-bowl-lvi-super-bowl-56-mask-requirement-vaccine-mandate-covid-19-pcr-test-antigen-test-sofi-stadium-inglewood-california|url-status=live}}'),
     Template('{{cite web|url=https://www.latimes.com/california/story/2022-02-03/super-bowl-fans-will-need-to-wear-masks-nfl-and-sofi-stadium-officials-say|title=Officials stress COVID safety measures at Super Bowl, including, yes, masks|last=Money|first=Luke|work=Los Angeles Times|date=February 3, 2022|access-date=February 7, 2022|archive-date=February 6, 2022|archive-url=https://web.archive.org/web/20220206165014/https://www.latimes.com/california/story/2022-02-03/super-bowl-fans-will-need-to-wear-masks-nfl-and-sofi-stadium-officials-say|url-status=live}}'),
     Template('{{main|2021 Los Angeles Rams season}}'),
     Template('{{win-loss record|w=12|l=5}}'),
     Template('{{cite web|url=https://www.pro-football-reference.com/teams/ram/2021.htm|title=2021 Los Angeles Rams Statistics & Players|date=February 1, 2022|work=[[Pro-Football-Reference.com]]|accessdate=February 1, 2022|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131025310/https://www.pro-football-reference.com/teams/ram/2021.htm|url-status=live}}'),
     Template('{{cite web |last=Iannaconi |first=Emily |url=https://sports.nbcsports.com/2022/01/30/rams-super-bowl-history-when-is-the-last-time-los-angeles-made-it-to-won-the-super-bowl/ |title=Rams Super Bowl history: When is the last time Los Angeles made it to, won the Super Bowl? |publisher=Sports.nbcsports.com |date=January 30, 2022 |accessdate=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131040130/https://sports.nbcsports.com/2022/01/30/rams-super-bowl-history-when-is-the-last-time-los-angeles-made-it-to-won-the-super-bowl/ |url-status=live }}'),
     Template('{{cite web|url=https://www.ocregister.com/2022/02/04/alexander-first-l-a-rams-title-team-followed-similar-template|title=Alexander: First L.A. Rams title team followed similar template|date=February 5, 2022|access-date=February 14, 2022|archive-date=February 6, 2022|archive-url=https://web.archive.org/web/20220206105551/https://www.ocregister.com/2022/02/04/alexander-first-l-a-rams-title-team-followed-similar-template/|url-status=live}}'),
     Template('{{cite web |last1=Thiry |first1=Lindsey |title=Why the Sean McVay-Jared Goff partnership fell apart for Los Angeles Rams |url=https://www.espn.com/nfl/story/_/id/31123122/why-sean-mcvay-jared-goff-partnership-fell-apart-los-angeles-rams |work=[[ESPN]] |access-date=January 31, 2022 |date=October 19, 2021 |archive-date=November 16, 2021 |archive-url=https://web.archive.org/web/20211116041429/https://www.espn.com/nfl/story/_/id/31123122/why-sean-mcvay-jared-goff-partnership-fell-apart-los-angeles-rams |url-status=live }}'),
     Template('{{cite web |last=Jackson |first=Stu |date=March 18, 2021 |title=Rams trade Jared Goff, future draft picks to Lions for Matthew Stafford |url=https://www.therams.com/news/rams-trade-jared-goff-future-draft-picks-to-lions-for-matthew-stafford |access-date=September 15, 2021 |website=Los Angeles Rams |archive-date=September 5, 2021 |archive-url=https://web.archive.org/web/20210905232029/https://www.therams.com/news/rams-trade-jared-goff-future-draft-picks-to-lions-for-matthew-stafford |url-status=live }}'),
     Template('{{cite web |last1=Dubin and Kerr |first1=Jared and Jeff |title=Matthew Stafford trade: Rams send two first-round picks, a third-round pick and Jared Goff to Lions for QB |url=https://www.cbssports.com/nfl/news/matthew-stafford-trade-rams-send-two-first-round-picks-a-third-round-pick-and-jared-goff-to-lions-for-qb/ |work=[[CBS Sports]] |access-date=January 31, 2022 |date=January 31, 2021 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214031747/https://www.cbssports.com/nfl/news/2022-super-bowl-rams-odell-beckham-jr-ruled-out-after-suffering-non-contact-knee-injury-vs-bengals/ |url-status=live }}'),
     Template('{{Cite news |last=Shook, Nick |date=November 1, 2021 |title=Broncos trade star LB Von Miller to Rams for two 2022 NFL Draft picks |work=NFL.com |url=https://www.nfl.com/news/broncos-trade-star-lb-von-miller-to-rams |access-date=November 1, 2021 |archive-date=November 1, 2021 |archive-url=https://web.archive.org/web/20211101164027/https://www.nfl.com/news/broncos-trade-star-lb-von-miller-to-rams |url-status=live }}'),
     Template('{{cite web|title=Rams agree to terms with receiver Odell Beckham Jr.|url=https://www.therams.com/news/rams-agree-to-terms-with-receiver-odell-beckham-jr|website=TheRams.com|date=November 11, 2021|access-date=January 31, 2022|archive-date=January 23, 2022|archive-url=https://web.archive.org/web/20220123081703/https://www.therams.com/news/rams-agree-to-terms-with-receiver-odell-beckham-jr|url-status=live}}'),
     Template('{{cite web |title=NFL Team Total Offense Regular Season Stats 2021 |url=https://www.espn.com/nfl/stats/team/_/season/2021/seasontype/2 |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201223226/https://www.espn.com/nfl/stats/team/_/season/2021/seasontype/2 |url-status=live }}'),
     Template('{{cite web |title=Matthew Stafford 2021 Game Log |url=https://www.pro-football-reference.com/players/S/StafMa00/gamelog/2021/ |access-date=January 17, 2022 |website=[[Pro-Football-Reference.com]] |archive-date=January 18, 2022 |archive-url=https://web.archive.org/web/20220118183132/https://www.pro-football-reference.com/players/S/StafMa00/gamelog/2021/ |url-status=live }}'),
     Template('{{cite web |title=Cleveland/LA/St. Louis Rams Single-Season Passing Leaders |url=https://www.pro-football-reference.com/teams/ram/single-season-passing.htm |access-date=January 17, 2022 |website=Pro-Football-Reference.com |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214031743/https://www.pro-football-reference.com/teams/ram/single-season-passing.htm |url-status=live }}'),
     Template('{{cite web |last1=Gordon |first1=Grant |title=Rams WR Cooper Kupp captures receiving triple crown |url=https://www.nfl.com/news/rams-wr-cooper-kupp-captures-receiving-triple-crown |website=[[NFL.com]] |access-date=January 31, 2022 |date=January 9, 2022 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201081409/https://www.nfl.com/news/rams-wr-cooper-kupp-captures-receiving-triple-crown |url-status=live }}'),
     Template('{{cite web |title=NFL Receiving Yards Single-Season Leaders |url=https://www.pro-football-reference.com/leaders/rec_yds_single_season.htm |access-date=January 17, 2022 |website=Pro-Football-Reference.com |archive-date=January 17, 2022 |archive-url=https://web.archive.org/web/20220117202436/https://www.pro-football-reference.com/leaders/rec_yds_single_season.htm |url-status=live }}'),
     Template('{{cite web |last1=Breech |first1=John |title=NFL Honors 2022: Aaron Rodgers earns second-straight MVP, Super Bowl teams come up big, Hall of Fame entrants |url=https://www.cbssports.com/nfl/news/nfl-honors-2022-aaron-rodgers-earns-second-straight-mvp-super-bowl-teams-come-up-big-hall-of-fame-entrants/live/ |website=[[CBS Sports]] |access-date=February 11, 2022 |date=February 10, 2022 |archive-date=February 11, 2022 |archive-url=https://web.archive.org/web/20220211043431/https://www.cbssports.com/nfl/news/nfl-honors-2022-aaron-rodgers-earns-second-straight-mvp-super-bowl-teams-come-up-big-hall-of-fame-entrants/live/ |url-status=live }}'),
     Template('{{cite web |title=Odell Beckham Jr. 2021 Game Log |url=https://www.pro-football-reference.com/players/B/BeckOd00/gamelog/2021 |website=[[Pro-Football-Reference.com]] |access-date=February 3, 2022 |archive-date=February 3, 2022 |archive-url=https://web.archive.org/web/20220203150730/https://www.pro-football-reference.com/players/B/BeckOd00/gamelog/2021 |url-status=live }}'),
     Template('{{cite web|title=Patriots Trade RB Sony Michel to LA Rams|url=https://www.patriots.com/news/patriots-trade-rb-sony-michel-to-la-rams|access-date=August 26, 2021|website=New England Patriots|date=August 25, 2021|archive-date=August 26, 2021|archive-url=https://web.archive.org/web/20210826021426/https://www.patriots.com/news/patriots-trade-rb-sony-michel-to-la-rams|url-status=live}}'),
     Template('{{cite web |title=Los Angeles Rams Player Stats 2021 |url=https://www.espn.com/nfl/team/stats/_/name/lar/season/2021/seasontype/2 |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160252/https://www.espn.com/nfl/team/stats/_/name/lar/season/2021/seasontype/2 |url-status=live }}'),
     Template('{{cite web|author=The Athletic Staff|title=Rams designate Cam Akers for return from IR, 5 months after Achilles injury|url=https://theathletic.com/news/rams-designate-cam-akers-for-return-from-ir-5-months-after-achilles-injury/APkfslgHtUhb/|access-date=January 10, 2022|work=The Athletic|archive-date=January 10, 2022|archive-url=https://web.archive.org/web/20220110203936/https://theathletic.com/news/rams-designate-cam-akers-for-return-from-ir-5-months-after-achilles-injury/APkfslgHtUhb/|url-status=live}}'),
     Template('{{cite web |title=Darrell Henderson 2021 Game Log |url=https://www.pro-football-reference.com/players/H/HendDa00/gamelog/2021/ |website=[[Pro-Football-Reference.com]] |access-date=February 3, 2022 |archive-date=February 3, 2022 |archive-url=https://web.archive.org/web/20220203150732/https://www.pro-football-reference.com/players/H/HendDa00/gamelog/2021/ |url-status=live }}'),
     Template("{{cite web|url=https://theramswire.usatoday.com/2021/10/26/rams-offensive-line-stats-best-nfl/|title=Stats show Rams' offensive line has been one of NFL's best this season|work=USA Today|date=October 26, 2021|last=Carlin|first=Skyler|access-date=February 1, 2022|archive-date=February 1, 2022|archive-url=https://web.archive.org/web/20220201052252/https://theramswire.usatoday.com/2021/10/26/rams-offensive-line-stats-best-nfl/|url-status=live}}"),
     Template('{{cite web |title=NFL Team Total Defense Regular Season Stats 2021 |url=https://www.espn.com/nfl/stats/team/_/view/defense/season/2021/seasontype/2/ |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160254/https://www.espn.com/nfl/stats/team/_/view/defense/season/2021/seasontype/2/ |url-status=live }}'),
     Template('{{cite web |last1=Patra |first1=Kevin |title=Rams signing former All-Pro safety Eric Weddle out of retirement |url=https://www.nfl.com/news/rams-signing-former-all-pro-safety-eric-weddle-out-of-retirement |website=NFL.com |access-date=January 12, 2022 |archive-date=January 12, 2022 |archive-url=https://web.archive.org/web/20220112162428/https://www.nfl.com/news/rams-signing-former-all-pro-safety-eric-weddle-out-of-retirement |url-status=live }}'),
     Template('{{cite web|website=TheRams.com|url=https://www.therams.com/news/rams-sign-eric-weddle-to-practice-squad|title=Rams sign Eric Weddle to practice squad|date=January 12, 2022|accessdate=January 13, 2022|first=Stu|last=Jackson|archive-date=January 12, 2022|archive-url=https://web.archive.org/web/20220112205432/https://www.therams.com/news/rams-sign-eric-weddle-to-practice-squad|url-status=live}}'),
     Template('{{cite web |last1=Jackson |first1=Stu |title=Matt Gay on first Pro Bowl selection: "It\'s just an honor to receive this" |url=https://www.therams.com/news/matt-gay-on-first-pro-bowl-selection-just-an-honor-to-receive-this |work=[[Los Angeles Rams]] |access-date=January 31, 2022 |date=December 23, 2021 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160252/https://www.therams.com/news/matt-gay-on-first-pro-bowl-selection-just-an-honor-to-receive-this |url-status=live }}'),
     Template('{{main|2021 Cincinnati Bengals season}}'),
     Template('{{win-loss record|w=10|l=7}}'),
     Template('{{cite web |title=2021 Cincinnati Bengals Statistics & Players |url=https://www.pro-football-reference.com/teams/cin/2021.htm |website=[[Pro-Football-Reference.com]] |access-date=February 2, 2022 |archive-date=January 29, 2022 |archive-url=https://web.archive.org/web/20220129201001/https://www.pro-football-reference.com/teams/cin/2021.htm |url-status=live }}'),
     Template('{{cite web |last1=Joyce |first1=Rob |title=Longest Playoff Droughts in Sports |url=https://www.audacy.com/wtic/sports/longest-playoff-droughts-in-sports |work=[[WTIC (AM)|WTIC]] |publisher=[[Audacy, Inc.]] |access-date=February 1, 2022 |date=January 12, 2021 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201040409/https://www.audacy.com/wtic/sports/longest-playoff-droughts-in-sports |url-status=live }}'),
     Template("{{cite web |last1=DeArdo |first1=Bryan |title=Ranking NFL's most surprising Super Bowl teams of all-time: Joe Burrow-led Bengals at the top |url=https://www.cbssports.com/nfl/news/ranking-nfls-most-surprising-super-bowl-teams-of-all-time-joe-burrow-led-bengals-at-the-top/ |work=[[CBS Sports]] |access-date=January 31, 2022 |date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131042204/https://www.cbssports.com/nfl/news/ranking-nfls-most-surprising-super-bowl-teams-of-all-time-joe-burrow-led-bengals-at-the-top/ |url-status=live }}"),
     Template('{{cite web |date=April 9, 2020 |title=58 prospects to virtually participate in NFL draft |url=http://www.nfl.com/news/story/0ap3000001108866/article/58-prospects-to-virtually-participate-in-nfl-draft |access-date=April 10, 2020 |website=National Football League |archive-date=April 10, 2020 |archive-url=https://web.archive.org/web/20200410064948/http://www.nfl.com/news/story/0ap3000001108866/article/58-prospects-to-virtually-participate-in-nfl-draft |url-status=live }}'),
     Template('{{cite web |last=Baby |first=Ben |date=April 23, 2020 |title=Cincinnati Bengals take Joe Burrow with No. 1 pick in NFL draft |url=https://www.espn.com/nfl/story/_/id/29089822/cincinnati-bengals-take-joe-burrow-no-1-pick-nfl-draft |access-date=April 24, 2020 |website=ESPN |archive-date=April 24, 2020 |archive-url=https://web.archive.org/web/20200424031129/https://www.espn.com/nfl/story/_/id/29089822/cincinnati-bengals-take-joe-burrow-no-1-pick-nfl-draft |url-status=live }}'),
     Template("{{cite web |last=Patra |first=Kevin |date=April 29, 2021 |title=Bengals select LSU WR Ja'Marr Chase No. 5 overall |url=https://www.nfl.com/news/bengals-select-lsu-wr-ja-marr-chase-no-5-overall |access-date=April 30, 2021 |website=NFL.com |archive-date=April 30, 2021 |archive-url=https://web.archive.org/web/20210430005958/https://www.nfl.com/news/bengals-select-lsu-wr-ja-marr-chase-no-5-overall |url-status=live }}"),
     Template("{{cite web |last1=Patra |first1=Kevin |title=Joe Burrow-Ja'Marr Chase connection 'right back to where it was' at LSU |url=https://www.nfl.com/news/joe-burrow-ja-marr-chase-connection-right-back-to-where-it-was-at-lsu |website=[[NFL.com]] |access-date=February 1, 2022 |date=June 25, 2021 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201131749/https://www.nfl.com/news/joe-burrow-ja-marr-chase-connection-right-back-to-where-it-was-at-lsu |url-status=live }}"),
     Template('{{cite web|title=In Round 5, Bengals Shore Up Kicking Game With Evan McPherson|url=https://www.bengals.com/news/in-round-5-bengals-shore-up-kicking-game-with-evan-mcpherson|website=Bengals.com|date=May 1, 2021|access-date=January 31, 2022|archive-date=December 6, 2021|archive-url=https://web.archive.org/web/20211206055429/https://www.bengals.com/news/in-round-5-bengals-shore-up-kicking-game-with-evan-mcpherson|url-status=live}}'),
     Template('{{cite web|title=Former Saints DE Trey Hendrickson signing with Bengals for four years, $60 million|url=https://www.nfl.com/news/former-saints-de-trey-hendrickson-signing-with-bengals-for-four-years-60-million|last=Gordon|first=Grant|website=NFL.com|date=March 15, 2021|access-date=January 31, 2022|archive-date=February 1, 2022|archive-url=https://web.archive.org/web/20220201083331/https://www.nfl.com/news/former-saints-de-trey-hendrickson-signing-with-bengals-for-four-years-60-million|url-status=live}}'),
     Template('{{cite web|title=Bengals Sign Five Free Agents|url=https://www.bengals.com/news/bengals-sign-five-free-agents|website=Bengals.com|date=March 19, 2021|access-date=January 31, 2022|archive-date=December 9, 2021|archive-url=https://web.archive.org/web/20211209200632/https://www.bengals.com/news/bengals-sign-five-free-agents|url-status=live}}'),
     Template('{{cite web|work=Giants.com|url=https://www.giants.com/news/giants-to-trade-dl-b-j-hill-details-not-finalized-joe-judge|title=Giants to trade DL B.J. Hill; details not finalized|date=August 30, 2021|accessdate=August 30, 2021|first=Dan|last=Salomone|archive-date=August 30, 2021|archive-url=https://web.archive.org/web/20210830182916/https://www.giants.com/news/giants-to-trade-dl-b-j-hill-details-not-finalized-joe-judge|url-status=live}}'),
     Template('{{cite web|url=https://www.bengals.com/news/price-for-trade-as-bengals-pick-up-d-tackle-from-giants|title=Price For Trade As Bengals Pick Up D-Tackle From Giants|date=August 30, 2021|accessdate=August 30, 2021|first=Geoff|last=Hobson|work=Bengals.com|archive-date=August 30, 2021|archive-url=https://web.archive.org/web/20210830185414/https://www.bengals.com/news/price-for-trade-as-bengals-pick-up-d-tackle-from-giants|url-status=live}}'),
     Template('{{cite web |title=NFL Team Total Offense Regular Season Stats 2020 |url=https://www.espn.com/nfl/stats/team/_/season/2020/seasontype/2 |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160252/https://www.espn.com/nfl/stats/team/_/season/2020/seasontype/2 |url-status=live }}'),
     Template('{{cite web|url=https://www.pro-football-reference.com/leaders/|title=NFL Leaders, Football Records, NFL Leaderboards|website=[[Pro-Football-Reference.com]]|access-date=January 10, 2022|archive-date=January 8, 2022|archive-url=https://web.archive.org/web/20220108234032/https://www.pro-football-reference.com/leaders/|url-status=live}}'),
     Template('{{cite web|title=2021 NFL Passing|url=https://www.pro-football-reference.com/years/2021/passing.htm|access-date=February 2, 2022|website=[[Pro-Football-Reference.com]]|archive-date=February 1, 2022|archive-url=https://web.archive.org/web/20220201214050/https://www.pro-football-reference.com/years/2021/passing.htm|url-status=live}}'),
     Template("{{cite web |last=Rapien |first=James |date=January 2, 2022 |title=Joe Burrow Breaks Two Bengals' Single-Season Records |url=https://www.si.com/nfl/bengals/news/joe-burrow-breaks-two-cincinnati-bengals-single-season-records |url-status=live |archive-url=https://web.archive.org/web/20220110200856/https://www.si.com/nfl/bengals/news/joe-burrow-breaks-two-cincinnati-bengals-single-season-records |archive-date=January 10, 2022 |access-date=January 10, 2022 |website=Si.com}}"),
     Template('{{cite web |title=NFL Player Receiving Stats 2021 |url=https://www.espn.com/nfl/stats/player/_/stat/receiving/season/2021/seasontype/2/table/receiving/sort/receivingYards/dir/desc |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160254/https://www.espn.com/nfl/stats/player/_/stat/receiving/season/2021/seasontype/2/table/receiving/sort/receivingYards/dir/desc |url-status=live }}'),
     Template('{{cite web |last=Hobson |first=Geoff |date=January 2, 2022 |title=Burrow, Bengals Chase Down AFC North Title With Classic 34–31 Winner Over Chiefs |url=https://www.bengals.com/news/burrow-bengals-chase-down-afc-north-title-with-classic-34-31-winner-over-chiefs |access-date=January 2, 2022 |website=www.bengals.com |archive-date=January 2, 2022 |archive-url=https://web.archive.org/web/20220102213410/https://www.bengals.com/news/burrow-bengals-chase-down-afc-north-title-with-classic-34-31-winner-over-chiefs |url-status=live }}'),
     Template("{{cite web |last1=Kerr |first1=Jeff |title=NFL Honors: Ja'Marr Chase named 2021 AP Offensive Rookie of the Year; third Bengals player to win honor |url=https://www.cbssports.com/nfl/news/nfl-honors-jamarr-chase-named-2021-ap-offensive-rookie-of-the-year-third-bengals-player-to-win-honor/ |website=[[CBS Sports]] |access-date=February 11, 2022 |date=February 10, 2022 |archive-date=February 11, 2022 |archive-url=https://web.archive.org/web/20220211023726/https://www.cbssports.com/nfl/news/nfl-honors-jamarr-chase-named-2021-ap-offensive-rookie-of-the-year-third-bengals-player-to-win-honor/ |url-status=live }}"),
     Template('{{cite web |title=Cincinnati Bengals Player Stats 2021 |url=https://www.espn.com/nfl/team/stats/_/name/cin/season/2021/seasontype/2 |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160252/https://www.espn.com/nfl/team/stats/_/name/cin/season/2021/seasontype/2 |url-status=live }}'),
     Template('{{cite web |title=Tyler Boyd 2021 Game Log |url=https://www.pro-football-reference.com/players/B/BoydTy00/gamelog/2021/ |access-date=January 28, 2022 |website=[[Pro-Football-Reference.com]] |archive-date=January 28, 2022 |archive-url=https://web.archive.org/web/20220128193944/https://www.pro-football-reference.com/players/B/BoydTy00/gamelog/2021/ |url-status=live }}'),
     Template('{{cite web |title=C.J. Uzomah 2021 Game Log |url=https://www.pro-football-reference.com/players/U/UzomC.00/gamelog/2021/ |access-date=January 28, 2022 |website=[[Pro-Football-Reference.com]] |archive-date=January 28, 2022 |archive-url=https://web.archive.org/web/20220128193116/https://www.pro-football-reference.com/players/U/UzomC.00/gamelog/2021/ |url-status=live }}'),
     Template('{{cite web |title=NFL Player Rushing Stats 2021 |url=https://www.espn.com/nfl/stats/player/_/stat/rushing/season/2021/seasontype/2 |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160255/https://www.espn.com/nfl/stats/player/_/stat/rushing/season/2021/seasontype/2 |url-status=live }}'),
     Template('{{cite web |title=NFL Player Defense Stats 2021 |url=https://www.espn.com/nfl/stats/player/_/view/defense/season/2021/seasontype/2/table/defensive/sort/sacks/dir/desc |website=[[ESPN]] |access-date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131160257/https://www.espn.com/nfl/stats/player/_/view/defense/season/2021/seasontype/2/table/defensive/sort/sacks/dir/desc |url-status=live }}'),
     Template("{{cite web|url=https://sports.yahoo.com/evan-mcpherson-ties-bengals-franchise-222313360.html|title=Evan McPherson breaks Bengals' franchise record for field goals of 50+ in one season|work=Cincinnati Enquirer|last=Clark|first=Dave|date=November 21, 2021|via=Yahoo! Sports|access-date=January 31, 2022|archive-date=November 29, 2021|archive-url=https://web.archive.org/web/20211129001019/https://sports.yahoo.com/evan-mcpherson-ties-bengals-franchise-222313360.html|url-status=live}}"),
     Template('{{main|2021–22 NFL playoffs}}'),
     Template("{{cite web |date=January 18, 2022 |title=NFL playoffs: Rams' Matthew Stafford secures first postseason win with beatdown of Cardinals |url=https://theathletic.com/news/nfl-playoffs-rams-matthew-stafford-secures-first-postseason-win-with-beatdown-of-cardinals/PrRvItGUMzpA/ |access-date=January 18, 2022 |work=The Athletic |archive-date=January 18, 2022 |archive-url=https://web.archive.org/web/20220118133610/https://theathletic.com/news/nfl-playoffs-rams-matthew-stafford-secures-first-postseason-win-with-beatdown-of-cardinals/PrRvItGUMzpA/ |url-status=live }}"),
     Template("{{cite web |last=Henderson |first=Brady |date=January 23, 2022 |title=Matthew Stafford, Los Angeles Rams outlast Tampa Bay Buccaneers in memorable playoff game that was 'a whole lot of fun' |url=https://www.espn.com/nfl/story/_/id/33133867/los-angeles-rams-outlast-tampa-bay-buccaneers-earn-opportunity-host-nfc-championship-game |access-date=January 24, 2022 |website=ESPN |archive-date=January 24, 2022 |archive-url=https://web.archive.org/web/20220124001557/https://www.espn.com/nfl/story/_/id/33133867/los-angeles-rams-outlast-tampa-bay-buccaneers-earn-opportunity-host-nfc-championship-game |url-status=live }}"),
     Template("{{cite web |last1=Dunleavy |first1=Ryan |title=Rams can put 49ers' dominance behind them with NFC title win |url=https://nypost.com/2022/01/30/rams-can-put-49ers-dominance-behind-them-with-nfc-title-win/ |work=[[New York Post]] |access-date=January 31, 2022 |date=January 30, 2022 |archive-date=January 30, 2022 |archive-url=https://web.archive.org/web/20220130080238/https://nypost.com/2022/01/30/rams-can-put-49ers-dominance-behind-them-with-nfc-title-win/ |url-status=live }}"),
     Template('{{cite web |last1=DiGiovanna |first1=Mike |title=Jaquiski Tartt drops opportunity against Rams to send 49ers to Super Bowl LVI |url=https://www.latimes.com/sports/rams/story/2022-01-31/jaquiski-tartt-drops-interception-rams-49ers-super-bowl |website=[[Los Angeles Times]] |access-date=February 8, 2022 |date=January 31, 2022 |archive-date=February 8, 2022 |archive-url=https://web.archive.org/web/20220208020948/https://www.latimes.com/sports/rams/story/2022-01-31/jaquiski-tartt-drops-interception-rams-49ers-super-bowl |url-status=live }}'),
     Template("{{cite web |title=Niners' blown lead ends magical playoff run and possibly the Jimmy Garoppolo era |url=https://www.espn.com/blog/san-francisco-49ers/post/_/id/40538/niners-blown-lead-ends-magical-playoff-run-and-possibly-the-jimmy-garoppolo-era |website=ESPN.com |access-date=January 31, 2022 |date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131030355/https://www.espn.com/blog/san-francisco-49ers/post/_/id/40538/niners-blown-lead-ends-magical-playoff-run-and-possibly-the-jimmy-garoppolo-era |url-status=live|last=Wagoner|first=Nick }}"),
     Template('{{cite web |last1=Henderson |first1=Brady |title=Los Angeles Rams TE Tyler Higbee, backup lineman Joe Noteboom out of Super Bowl LVI with injuries |url=https://www.espn.com/nfl/story/_/id/33269110/los-angeles-rams-tight-end-tyler-higbee-backup-lineman-joe-noteboom-super-bowl-lvi-injuries |website=[[ESPN]] |access-date=February 12, 2022 |date=February 11, 2022 |archive-date=February 12, 2022 |archive-url=https://web.archive.org/web/20220212004344/https://www.espn.com/nfl/story/_/id/33269110/los-angeles-rams-tight-end-tyler-higbee-backup-lineman-joe-noteboom-super-bowl-lvi-injuries |url-status=live }}'),
     Template('{{cite news|last=Baby|first=Ben|title=Bengals win first playoff game in 31 years, set the table for a run at AFC title|url=https://www.espn.com/blog/cincinnati-bengals/post/_/id/33667/bengals-win-first-playoff-game-in-31-years-set-the-table-for-a-run-at-afc-title|website=[[ESPN.com]]|publisher=[[ESPN Inc.]]|agency=[[Associated Press]]|date=January 15, 2022|access-date=January 17, 2022|archive-date=January 24, 2022|archive-url=https://web.archive.org/web/20220124124357/https://www.espn.com/blog/cincinnati-bengals/post/_/id/33667/bengals-win-first-playoff-game-in-31-years-set-the-table-for-a-run-at-afc-title|url-status=live}}'),
     Template('{{cite web |last1=Baby |first1=Ben |title=Cincinnati Bengals lose starting DT Larry Ogunjobi for rest of playoffs |url=https://www.espn.com/nfl/story/_/id/33085900/cincinnati-bengals-lose-starting-dt-larry-ogunjobi-rest-season |website=[[ESPN]] |access-date=February 2, 2022 |date=January 17, 2022 |archive-date=February 2, 2022 |archive-url=https://web.archive.org/web/20220202220927/https://www.espn.com/nfl/story/_/id/33085900/cincinnati-bengals-lose-starting-dt-larry-ogunjobi-rest-season |url-status=live }}'),
     Template('{{cite news|last=Sullivan|first=Tyler|title=Raiders-Bengals controversial touchdown: Cincinnati awarded TD despite inadvertent whistle by referees|url=https://www.cbssports.com/nfl/news/bengals-raiders-controversial-touchdown-cincinnati-awarded-td-despite-inadvertent-whistle-by-referees/|work=[[CBS Sports]]|date=January 15, 2022|access-date=January 17, 2022|archive-date=January 25, 2022|archive-url=https://web.archive.org/web/20220125090002/https://www.cbssports.com/nfl/news/bengals-raiders-controversial-touchdown-cincinnati-awarded-td-despite-inadvertent-whistle-by-referees/|url-status=live}}'),
     Template('{{cite web |last=Salvador |first=Joseph |date=January 22, 2022 |title=Bengals Knock Off Top-Seeded Titans on Last-Second Field Goal for First Road Playoff Win |url=https://www.si.com/nfl/2022/01/23/bengals-knock-off-titans-walk-off-field-goal-first-road-playoff-win |access-date=January 24, 2022 |website=Sports Illustrated |archive-date=January 24, 2022 |archive-url=https://web.archive.org/web/20220124062308/https://www.si.com/nfl/2022/01/23/bengals-knock-off-titans-walk-off-field-goal-first-road-playoff-win |url-status=live }}'),
     Template('{{cite web |last=Kerr |first=Jeff |date=January 22, 2022 |title=Joe Burrow ties NFL record for most times sacked in playoff game, and rewrites history book with Bengals win |url=https://www.cbssports.com/nfl/news/joe-burrow-ties-nfl-record-for-most-times-sacked-in-playoff-game-and-rewrites-history-book-with-bengals-win/ |access-date=January 23, 2022 |website=CBSSports.com |archive-date=January 23, 2022 |archive-url=https://web.archive.org/web/20220123005344/https://www.cbssports.com/nfl/news/joe-burrow-ties-nfl-record-for-most-times-sacked-in-playoff-game-and-rewrites-history-book-with-bengals-win/ |url-status=live }}'),
     Template("{{cite web |last1=Patra |first1=Kevin |title=2021 NFL playoffs: What we learned from Bengals' win over Titans in Divisional Round |url=https://www.nfl.com/news/2021-nfl-playoffs-what-we-learned-from-bengals-win-over-titans-in-divisional-rou |website=National Football league |access-date=January 31, 2022 |date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131205726/https://www.nfl.com/news/2021-nfl-playoffs-what-we-learned-from-bengals-win-over-titans-in-divisional-rou |url-status=live }}"),
     Template('{{cite web |last1=Zucker |first1=Joseph |title=Joe Burrow, Bengals Stun Patrick Mahomes, Chiefs in OT, Advance to 2022 Super Bowl |url=https://bleacherreport.com/articles/10025361-joe-burrow-bengals-stun-patrick-mahomes-chiefs-in-ot-advance-to-2022-super-bowl |website=Bleacher Report |access-date=January 30, 2022 |archive-date=January 30, 2022 |archive-url=https://web.archive.org/web/20220130233438/https://bleacherreport.com/articles/10025361-joe-burrow-bengals-stun-patrick-mahomes-chiefs-in-ot-advance-to-2022-super-bowl |url-status=live }}'),
     Template('{{cite web |title=Bengals roar back from 18 points down to stun Chiefs and reach Super Bowl |url=https://www.theguardian.com/sport/2022/jan/30/afc-championship-game-chiefs-bengals-nfl-football-joe-burrow |website=The Guardian |date=January 30, 2022 |access-date=January 31, 2022 |archive-date=January 30, 2022 |archive-url=https://web.archive.org/web/20220130234828/https://www.theguardian.com/sport/2022/jan/30/afc-championship-game-chiefs-bengals-nfl-football-joe-burrow |url-status=live }}'),
     Template('{{cite web |title=Cincinnati Bengals take elongated path back to Super Bowl |url=https://www.foxsports.com/stories/nfl/cincinnati-bengals-take-elongated-journey-back-to-super-bowl |work=[[Fox Sports]] |access-date=January 31, 2022 |date=January 30, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131021306/https://www.foxsports.com/stories/nfl/cincinnati-bengals-take-elongated-journey-back-to-super-bowl |url-status=live }}'),
     Template('{{cite web|last=Wilner|first=Barry|date=March 30, 2021|title=NFL moves to 17-game regular season, 2022 Super Bowl pushed back a week|url=https://www.wgrz.com/article/news/nation-world/nfl-moves-to-17-game-regular-schedule/507-7b5c6e42-e013-402d-9e39-e1eb2822c2b8|url-status=live|access-date=January 17, 2022|website=[[WGRZ]]|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031749/https://www.wgrz.com/article/news/nation-world/nfl-moves-to-17-game-regular-schedule/507-7b5c6e42-e013-402d-9e39-e1eb2822c2b8}}'),
     Template('{{cite web|last=Kerr|first=Jeff|date=January 30, 2022|title=Super Bowl 2022: Rams become first team to host conference championship game and Super Bowl in same season|url=https://www.cbssports.com/nfl/news/super-bowl-2022-rams-become-first-team-to-host-conference-championship-game-and-super-bowl-in-same-season/|access-date=January 31, 2022|website=CBSSports.com|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131075330/https://www.cbssports.com/nfl/news/super-bowl-2022-rams-become-first-team-to-host-conference-championship-game-and-super-bowl-in-same-season/|url-status=live}}'),
     Template("{{cite news|last=Williams|first=Charean|title=Bengals are home team for Super Bowl LVI in Rams' stadium|url=https://profootballtalk.nbcsports.com/2022/01/31/bengals-are-home-team-for-super-bowl-lvi-in-rams-stadium/|website=[[Profootballtalk.com|Pro Football Talk]]|date=January 31, 2022|access-date=February 3, 2022|url-status=live|archive-date=February 3, 2022|archive-url=https://web.archive.org/web/20220203160013/https://profootballtalk.nbcsports.com/2022/01/31/bengals-are-home-team-for-super-bowl-lvi-in-rams-stadium/}}"),
     Template('{{cite web|url=https://www.ocregister.com/2022/02/10/1979-rams-look-back-on-super-bowl-xiv-at-the-rose-bowl|title=1979 Rams look back on Super Bowl XIV at the Rose Bowl|date=February 10, 2022|access-date=February 14, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214030817/https://www.ocregister.com/2022/02/10/1979-rams-look-back-on-super-bowl-xiv-at-the-rose-bowl/|url-status=live}}'),
     Template('{{cite news|last=Shook|first=Nick|title=Bengals to wear black home uniforms in Super Bowl LVI vs. Rams|url=https://www.nfl.com/news/bengals-to-wear-black-home-uniforms-in-super-bowl-lvi-vs-rams|publisher=NFL Enterprises, LLC|website=NFL.com|date=February 2, 2022|access-date=February 3, 2022|url-status=live|archive-date=February 3, 2022|archive-url=https://web.archive.org/web/20220203041905/https://www.nfl.com/news/bengals-to-wear-black-home-uniforms-in-super-bowl-lvi-vs-rams}}'),
     Template('{{cite web |last=Smith|first=Michael |date=January 31, 2022 |title=Sean McVay vs. Zac Taylor is the youngest matchup of Super Bowl head coaches ever |work=NBC Sports |url=https://profootballtalk.nbcsports.com/2022/01/31/sean-mcvay-vs-zac-taylor-is-the-youngest-matchup-of-super-bowl-head-coaches-ever/ |accessdate=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131173420/https://profootballtalk.nbcsports.com/2022/01/31/sean-mcvay-vs-zac-taylor-is-the-youngest-matchup-of-super-bowl-head-coaches-ever/ |url-status=live }}'),
     Template("{{cite web |last1=Patra |first1=Kevin |title=Bengals coach Zac Taylor: Working for Rams' Sean McVay was 'best two years of my life' |url=https://www.nfl.com/news/bengals-coach-zac-taylor-working-for-rams-sean-mcvay-was-best-two-years-of-my-li |website=[[National Football League]] |access-date=January 31, 2022 |date=January 31, 2022 |archive-date=January 31, 2022 |archive-url=https://web.archive.org/web/20220131193456/https://www.nfl.com/news/bengals-coach-zac-taylor-working-for-rams-sean-mcvay-was-best-two-years-of-my-li |url-status=live }}"),
     Template('{{cite web |last1=Albert |first1=Darryn |title=Rams lineman Andrew Whitworth older than both Super Bowl coaches |url=https://www.yardbarker.com/nfl/articles/rams_lineman_andrew_whitworth_older_than_both_super_bowl_coaches/s1_127_37143950 |website=[[YardBarker]] |access-date=February 3, 2022 |date=February 1, 2022 |archive-date=February 2, 2022 |archive-url=https://web.archive.org/web/20220202095336/https://www.yardbarker.com/nfl/articles/rams_lineman_andrew_whitworth_older_than_both_super_bowl_coaches/s1_127_37143950 |url-status=live }}'),
     Template("{{cite web |last1=Rogust |first1=Scott |title=Super Bowl 56 features unprecedented matchup between QB's with losing records |url=https://fansided.com/2022/02/13/super-bowl-56-quarterbacks-losing-records-stafford-burrow/ |website=[[FanSided]] |access-date=February 15, 2022 |date=February 13, 2022}}"),
     Template("{{cite web |last1=DeArdo |first1=Bryan |title=Bengals' Joe Burrow becomes first QB to reach Super Bowl as a No. 1 overall pick in first two years |url=https://www.cbssports.com/nfl/news/bengals-joe-burrow-becomes-first-qb-to-reach-super-bowl-as-a-no-1-overall-pick-in-first-two-years/ |website=[[CBS Sports]] |access-date=February 1, 2022 |date=January 30, 2022 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201043716/https://www.cbssports.com/nfl/news/bengals-joe-burrow-becomes-first-qb-to-reach-super-bowl-as-a-no-1-overall-pick-in-first-two-years/ |url-status=live }}"),
     Template("{{cite web |last1=Jeffrey |first1=Joyann |title=Who are the deaf performers at the Super Bowl? Meet Warren 'Wawa' Snipe and Sean Forbes |url=https://diverseabilitymagazine.com/2022/02/deaf-performers-super-bowl-meet-warren-wawa-snipe-sean-forbes/ |website=DIVERSEability Magazine |access-date=February 16, 2022 |date=February 15, 2022}}"),
     Template('{{cite web |title=Mickey Guyton, Jhené Aiko, Mary Mary to sing at Super Bowl |url=https://apnews.com/article/sports-entertainment-arts-and-entertainment-california-music-2a9d566f6176eee4af772cf0a71bbb5a |website=Associated Press |date=February 2022 |access-date=February 1, 2022 |archive-date=February 1, 2022 |archive-url=https://web.archive.org/web/20220201171328/https://apnews.com/article/sports-entertainment-arts-and-entertainment-california-music-2a9d566f6176eee4af772cf0a71bbb5a |url-status=live }}'),
     Template('{{cite press release|title= Air Force Heritage Flight flyover scheduled to support Super Bowl LVI in Los Angeles|url= https://www.af.mil/News/Article-Display/Article/2909003/air-force-heritage-flight-flyover-scheduled-to-support-super-bowl-lvi-in-los-an/|publisher= United States Air Force|date= January 24, 2022|access-date= February 13, 2022|archive-date= February 13, 2022|archive-url= https://web.archive.org/web/20220213193531/https://www.af.mil/News/Article-Display/Article/2909003/air-force-heritage-flight-flyover-scheduled-to-support-super-bowl-lvi-in-los-an/|url-status= live}}'),
     Template('{{Main|Super Bowl LVI halftime show}}'),
     Template("{{cite web|url=https://deadline.com/2022/02/super-bowl-viewership-up-rams-win-bengals-nbc-olympics-1234932994/|title=Super Bowl Total Viewership Tops 112 Million For NBC & NFL With L.A. Rams' Hometown Win – Update|last=Patten|first=Dominic|publisher=Deadline|date=February 15, 2022|access-date=February 15, 2022}}"),
     Template('{{cite web|title=Five Epic Hitmakers Unite for PEPSI Super Bowl LVI Halftime Show|url=https://www.nfl.com/news/five-epic-hitmakers-unite-for-pepsi-super-bowl-lvi-halftime-show|access-date=September 30, 2021|date=September 30, 2021|website=NFL.com|archive-date=October 14, 2021|archive-url=https://web.archive.org/web/20211014214534/https://www.nfl.com/news/five-epic-hitmakers-unite-for-pepsi-super-bowl-lvi-halftime-show|url-status=live}}'),
     Template("{{cite magazine|last1=Lipshutz|first1=Jason|date=February 14, 2022|title=50 Cent Makes Surprise 'In Da Club' Performance During 2022 Super Bowl Halftime Show|url=https://www.billboard.com/music/music-news/50-cent-in-da-club-2022-super-bowl-halftime-show-1235031017/|access-date=February 14, 2022|magazine=Billboard|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031748/https://www.billboard.com/music/music-news/50-cent-in-da-club-2022-super-bowl-halftime-show-1235031017/|url-status=live}}"),
     Template("{{cite web|title=Rappers Sean Forbes, Warren 'WaWa' Snipe to Perform ASL Interpretations During Super Bowl Halftime Show|url=https://www.billboard.com/music/rb-hip-hop/2022-super-bowl-deaf-rappers-sign-language-interpretation-halftime-show-1235028006/|access-date=February 13, 2022|date=February 7, 2022|website=Billboard.com|last=Kaufman|first=Gil|archive-date=February 11, 2022|archive-url=https://web.archive.org/web/20220211230753/https://www.billboard.com/music/rb-hip-hop/2022-super-bowl-deaf-rappers-sign-language-interpretation-halftime-show-1235028006/|url-status=live}}"),
     Template('{{Cite news|last=Caramanica|first=Jon|date=2022-02-14|title=Rap Takes Over Super Bowl Halftime, Balancing Celebration and Protest|work=The New York Times|url=https://www.nytimes.com/2022/02/13/arts/music/super-bowl-halftime-show-review.html|access-date=2022-02-15|issn=0362-4331}}'),
     Template("{{Cite news|last=Belson|first=Ken|date=2022-02-14|title=N.F.L. was 'aware' Eminem planned to kneel during halftime performance.|work=The New York Times|url=https://www.nytimes.com/2022/02/13/sports/football/nfl-eminem-kneel-super-bowl-halftime.html|access-date=2022-02-15|issn=0362-4331}}"),
     Template('{{Cite web|date=2022-02-14|title=The NFL Was "Aware" Eminem Would Take a Knee at Super Bowl 2022 Halftime Show|url=https://pitchfork.com/news/eminem-takes-a-knee-at-super-bowl-2022-halftime-show-nfl-was-aware-that-he-would/|access-date=2022-02-15|website=Pitchfork}}'),
     Template('{{cite magazine|last1=Sheffield|first1=Rob|date=February 14, 2022|title=Every Super Bowl Halftime Show, Ranked From Worst to Best|magazine=Rolling Stone|url=https://www.rollingstone.com/culture/culture-lists/super-bowl-halftime-shows-ranked-from-worst-to-best-14201/prince-2007-228422/|access-date=February 14, 2022}}'),
     Template('{{cite web|last=Levy|first=Joe|date=March 14, 2019|title=CBS agrees to Super Bowl swap to give NBC Winter Olympics boost|url=http://www.sportspromedia.com/news/cbs-super-bowl-nbc-winter-olympics|access-date=March 14, 2019|work=SportsPro|archive-date=February 7, 2021|archive-url=https://web.archive.org/web/20210207024835/https://www.sportspromedia.com/news/cbs-super-bowl-nbc-winter-olympics|url-status=live}}'),
     Template("{{cite web|last=Crupi|first=Anthony|date=March 13, 2019|title=CBS, NBC in 'Freaky Friday' Super Bowl swap|url=https://adage.com/article/media/cbs-nbc-swap-super-bowls/316974/|access-date=March 13, 2019|website=adage.com|archive-date=March 20, 2019|archive-url=https://web.archive.org/web/20190320090601/https://adage.com/article/media/cbs-nbc-swap-super-bowls/316974/|url-status=live}}"),
     Template('{{cite web|last=Steinberg|first=Brian|date=March 13, 2019|title=CBS, NBC to Swap Super Bowl Broadcasts|url=https://variety.com/2019/tv/news/cbs-nbc-swap-super-bowl-1203162667/|access-date=March 13, 2019|work=Variety|archive-date=May 27, 2019|archive-url=https://web.archive.org/web/20190527010753/https://variety.com/2019/tv/news/cbs-nbc-swap-super-bowl-1203162667/|url-status=live}}'),
     Template('{{cite web|last=Goldsmith|first=Jill|date=May 18, 2021|title=Telemundo Deportes Nabs Exclusive Spanish-Language Rights To Super Bowl LVI Live From Los Angeles|url=https://deadline.com/2021/05/telemundo-deportes-super-bowl-lvi-live-telemundo-los-angeles-spanish-language-rights-1234759107/|url-status=live|access-date=May 18, 2021|work=Deadline|archive-date=May 18, 2021|archive-url=https://web.archive.org/web/20210518140248/https://deadline.com/2021/05/telemundo-deportes-super-bowl-lvi-live-telemundo-los-angeles-spanish-language-rights-1234759107/}}'),
     Template("{{cite web|last=Dachman|first=Jason|date=January 24, 2022|title=Super Bowl LVI: NBC Sports' Fred Gaudelli Previews Production Plans for 'Super Gold Sunday'|url=https://www.sportsvideo.org/2022/01/24/super-bowl-lvi-nbc-sports-fred-gaudelli-previews-production-plans-for-super-gold-sunday/|access-date=January 31, 2022|work=Sports Video Group|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131042416/https://www.sportsvideo.org/2022/01/24/super-bowl-lvi-nbc-sports-fred-gaudelli-previews-production-plans-for-super-gold-sunday/|url-status=live}}"),
     Template("{{cite news|date=February 6, 2011|title=Goal of spectacle colors NFL's thinking about Super Bowl halftime show|newspaper=Chicago Tribune|url=http://articles.chicagotribune.com/2011-02-06/business/ct-biz-0206-rosenthal--20110206_1_super-halftime-party-doritos-zaptime-halftime-show|access-date=January 30, 2013|archive-date=March 12, 2017|archive-url=https://web.archive.org/web/20170312153733/http://articles.chicagotribune.com/2011-02-06/business/ct-biz-0206-rosenthal--20110206_1_super-halftime-party-doritos-zaptime-halftime-show|url-status=live}}"),
     Template('{{cite web|last=White|first=Peter|date=November 12, 2021|title=NBC To Air Olympics Coverage After Super Bowl LVI|url=https://deadline.com/2021/11/nbc-olympics-coverage-super-bowl-lvi-1234873167/|url-status=live|access-date=November 12, 2021|work=Deadline|archive-date=November 12, 2021|archive-url=https://web.archive.org/web/20211112193405/https://deadline.com/2021/11/nbc-olympics-coverage-super-bowl-lvi-1234873167/}}'),
     Template("{{cite web|last=Bucholtz|first=Andrew|date=November 16, 2021|title=Winter Olympics coverage post-Super Bowl shows NBC's live sports focus, but is also about unprecedented Olympic-SB overlap|url=https://awfulannouncing.com/nfl/winter-olympics-super-bowl-overlap.html|access-date=November 16, 2021|work=Awful Announcing|archive-date=November 16, 2021|archive-url=https://web.archive.org/web/20211116033732/https://awfulannouncing.com/nfl/winter-olympics-super-bowl-overlap.html|url-status=live}}"),
     Template("{{cite web|last=Johnson|first=Ted|date=February 7, 2022|title=NBC Sports' Mike Tirico Returning To U.S. From Beijing Earlier Than Planned; Will Cover Super Bowl From L.A. And Remainder Of Olympics From Stamford|url=https://deadline.com/2022/02/mike-tirico-nbc-sports-winter-olympics-china-1234927724/|access-date=February 7, 2022|work=Deadline|archive-date=February 7, 2022|archive-url=https://web.archive.org/web/20220207174354/https://deadline.com/2022/02/mike-tirico-nbc-sports-winter-olympics-china-1234927724/|url-status=live}}"),
     Template('{{cite web|date=January 14, 2022|last=Bucholtz|first=Andrew|title=Mike Tirico will host Olympics from Beijing, then fly to LA for remote hosting and Super Bowl|url=https://awfulannouncing.com/nbc/mike-tirico-beijing-olympics-la-super-bowl.html|access-date=January 31, 2022|work=Awful Announcing|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131040838/https://awfulannouncing.com/nbc/mike-tirico-beijing-olympics-la-super-bowl.html|url-status=live}}'),
     Template('{{cite web|date=January 27, 2022|title=NBC moving EPL show to SoFi Stadium during Super Bowl week|url=https://www.latimes.com/sports/soccer/story/2022-01-27/nbc-moving-epl-show-to-sofi-stadium-during-super-bowl-week|access-date=February 13, 2022|website=Los Angeles Times|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213191046/https://www.latimes.com/sports/soccer/story/2022-01-27/nbc-moving-epl-show-to-sofi-stadium-during-super-bowl-week|url-status=live}}'),
     Template("{{cite web|last=Gardner|first=Steve|title=NBC's Mike Tirico to anchor network's Olympic, Super Bowl coverage simultaneously|url=https://www.usatoday.com/story/sports/media/2022/01/13/nbc-mike-tirico-set-unprecedented-olympics-super-bowl-double/6520311001/|access-date=January 31, 2022|work=USA Today|archive-date=January 31, 2022|archive-url=https://web.archive.org/web/20220131040838/https://www.usatoday.com/story/sports/media/2022/01/13/nbc-mike-tirico-set-unprecedented-olympics-super-bowl-double/6520311001/|url-status=live}}"),
     Template('{{cite web|first=Kristian|last=Hernández|date=February 11, 2022|title=Live From Super Bowl LVI: Telemundo Deportes Brings Latin Flavor to Its First Over-the-Air Broadcast of the Big Game|url=https://www.sportsvideo.org/2022/02/11/live-from-super-bowl-lvi-telemundo-deportes-brings-latin-flavor-to-its-first-over-the-air-broadcast-of-the-big-game/|access-date=February 13, 2022|work=Sports Video Group|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213211359/https://www.sportsvideo.org/2022/02/11/live-from-super-bowl-lvi-telemundo-deportes-brings-latin-flavor-to-its-first-over-the-air-broadcast-of-the-big-game/|url-status=live}}'),
     Template("{{cite web|last=Young|first=Ryan|date=January 11, 2022|title=Michele Tafoya leaving NBC's 'Sunday Night Football' after Super Bowl LVI|url=https://sports.yahoo.com/michele-tafoya-leaving-nbc-sunday-night-football-after-super-bowl-012054810.html|url-status=live|access-date=January 12, 2022|website=Yahoo! Sports|archive-date=January 12, 2022|archive-url=https://web.archive.org/web/20220112020522/https://sports.yahoo.com/michele-tafoya-leaving-nbc-sunday-night-football-after-super-bowl-012054810.html}}"),
     Template('{{cite web|url=https://www.silive.com/nation/2022/01/how-to-watch-super-bowl-2022-time-tv-channel-livestream-information.html|title=How to watch Super Bowl 2022: Time, TV channel, livestream information|date=January 31, 2022|last=Knudson|first=Annalise|website=silive.com|access-date=February 8, 2022|archive-date=February 6, 2022|archive-url=https://web.archive.org/web/20220206090820/https://www.silive.com/nation/2022/01/how-to-watch-super-bowl-2022-time-tv-channel-livestream-information.html|url-status=live}}'),
     Template('{{cite web|last=Hernández|first=Kristian|date=February 11, 2022|title=Live From Super Bowl LVI: Verizon Powers 5G Multi-View for In-Venue Fans, Halftime-Show Option for At-Home Viewers|url=https://www.sportsvideo.org/2022/02/11/live-from-super-bowl-lvi-verizon-powers-5g-multi-view-for-in-venue-fans-halftime-show-option-for-at-home-viewers/|access-date=February 13, 2022|website=Sports Video Group|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213192129/https://www.sportsvideo.org/2022/02/11/live-from-super-bowl-lvi-verizon-powers-5g-multi-view-for-in-venue-fans-halftime-show-option-for-at-home-viewers/|url-status=live}}'),
     Template('{{cite web |title=Super Bowl LVI Ad Tracker: Updates on All the 2022 Commercials |url=https://www.adweek.com/brand-marketing/super-bowl-lvi-ad-tracker-updates-on-all-the-2022-commercials/ |website=Adweek |access-date=February 3, 2022 |date=December 21, 2021 |archive-date=February 3, 2022 |archive-url=https://web.archive.org/web/20220203113156/https://www.adweek.com/brand-marketing/super-bowl-lvi-ad-tracker-updates-on-all-the-2022-commercials/ |url-status=live }}'),
     Template('{{cite web |title=Budweiser, Clydesdales are back in Super Bowl LVI commercial lineup after skipping 2021 |url=https://www.jacksonville.com/story/business/2022/01/26/budweiser-clydesdales-back-super-bowl-lvi-2022/9225951002/ |website=The Florida Times-Union |access-date=February 3, 2022 |date=December 21, 2021 |archive-date=February 3, 2022 |archive-url=https://web.archive.org/web/20220203113158/https://www.jacksonville.com/story/business/2022/01/26/budweiser-clydesdales-back-super-bowl-lvi-2022/9225951002/ |url-status=live }}'),
     Template("{{cite news|url=https://www.vulture.com/2022/02/2022-super-bowl-trailers.html|title=''Jurassic World Dominion'' Kicks Off 2022 Super Bowl Trailers|last=Alter|first=Rebecca|work=Vulture|date=February 11, 2022|access-date=February 11, 2022|archive-date=February 10, 2022|archive-url=https://web.archive.org/web/20220210194947/https://www.vulture.com/2022/02/2022-super-bowl-trailers.html|url-status=live}}"),
     Template('{{cite news|url=https://qz.com/2126685/hollywoods-return-to-normal-starts-with-super-bowl-trailers/|title=Hollywood is using Super Bowl trailer ads to lure fans back to the movies|last=Strange|first=Adario|publisher=Quartz|date=February 12, 2022|access-date=February 13, 2022|archive-date=February 12, 2022|archive-url=https://web.archive.org/web/20220212162314/https://qz.com/2126685/hollywoods-return-to-normal-starts-with-super-bowl-trailers/|url-status=live}}'),
     Template('{{cite news|url=https://www.digitalspy.com/tv/ustv/a39034282/lord-of-the-rings-rings-of-power-first-images-amazon-prime-video/|title=First full look at Lord of the Rings TV show finally revealed by Amazon Prime|last=West|first=Amy|publisher=Digital Spy|access-date=February 13, 2022|archive-date=February 11, 2022|archive-url=https://web.archive.org/web/20220211181253/https://www.digitalspy.com/tv/ustv/a39034282/lord-of-the-rings-rings-of-power-first-images-amazon-prime-video/|url-status=live}}'),
     Template("{{cite web|last=Lacques|first=Gabe|title=What was that? Coinbase's QR code Super Bowl commercial confuses viewers|url=https://www.usatoday.com/story/sports/Ad-Meter/2022/02/13/coinbase-qr-code-super-bowl-ad-crypto-commercial-confuses-viewers/6778949001/|access-date=February 14, 2022|website=USA TODAY|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031750/https://www.usatoday.com/story/sports/Ad-Meter/2022/02/13/coinbase-qr-code-super-bowl-ad-crypto-commercial-confuses-viewers/6778949001/|url-status=live}}"),
     Template("{{cite web|last1=Steinberg|first1=Brian|date=February 14, 2022|title=Larry David Doesn't Like Much, But He Loved This Super Bowl Cryptocurrency Ad|url=https://variety.com/2022/tv/news/larry-david-super-bowl-commercial-ftx-cryptocurrency-jeff-schaffer-1235180358/|access-date=February 14, 2022|website=Variety|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031748/https://variety.com/2022/tv/news/larry-david-super-bowl-commercial-ftx-cryptocurrency-jeff-schaffer-1235180358/|url-status=live}}"),
     Template('{{Cite magazine|last=Watercutter|first=Angela|title=These Crypto Super Bowl Ads Feel Like Pets.com All Over Again|magazine=Wired|url=https://www.wired.com/story/crypto-super-bowl/|access-date=February 14, 2022|issn=1059-1028|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213165134/https://www.wired.com/story/crypto-super-bowl/|url-status=live}}'),
     Template('{{cite web|title=Super Bowl 2022: The most notable commercials released so far|url=https://www.nbcnews.com/pop-culture/pop-culture-news/super-bowl-2022-notable-commercials-released-far-rcna15179|access-date=February 14, 2022|website=NBC News|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213152152/https://www.nbcnews.com/pop-culture/pop-culture-news/super-bowl-2022-notable-commercials-released-far-rcna15179|url-status=live}}'),
     Template('{{cite web |url=https://www.npr.org/2022/02/14/1080237873/superbowl-ads-crypto-bitcoin |title=Crypto ads are a Super Bowl talker, with floating QR codes and Larry David |last=Tellez|first=Anthony |work=NPR |date=February 14, 2022 |access-date=February 14, 2022 |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011519/https://www.npr.org/2022/02/14/1080237873/superbowl-ads-crypto-bitcoin |url-status=live }}'),
     Template('{{cite web |url=https://www.latimes.com/entertainment-arts/business/story/2022-02-13/why-larry-david-and-lebron-james-super-bowl-ads-are-the-tip-of-hollywoods-crypto-iceberg |title=Et tu, Larry? Why so many celebrities are shilling for crypto |last=Faughnder|first=Ryan |work=Los Angeles Times |date=February 14, 2022 |access-date=February 14, 2022 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214043545/https://www.latimes.com/entertainment-arts/business/story/2022-02-13/why-larry-david-and-lebron-james-super-bowl-ads-are-the-tip-of-hollywoods-crypto-iceberg |url-status=live }}'),
     Template("{{cite web |url=https://www.nytimes.com/2022/02/11/business/media/super-bowl-commercials.html |title=Prepare Yourself for This Weekend's 'Crypto Bowl' |last=Hsu|first=Tiffany |work=The New York Times |date=February 11, 2022 |access-date=February 14, 2022 |archive-date=February 13, 2022 |archive-url=https://web.archive.org/web/20220213223956/https://www.nytimes.com/2022/02/11/business/media/super-bowl-commercials.html |url-status=live }}"),
     Template('{{cite web |url=https://fortune.com/2022/02/14/crypto-companies-super-bowl-ads-coinbase-ftx-bitcoin-ether/ |title=Crypto companies spent millions on Super Bowl ads. So did Pets.com |last=Mellor|first=Sophie |work=Fortune |date=February 14, 2022 |access-date=February 14, 2022 |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011516/https://fortune.com/2022/02/14/crypto-companies-super-bowl-ads-coinbase-ftx-bitcoin-ether/ |url-status=live }}'),
     Template("{{cite web |url=https://www.nytimes.com/2022/02/14/arts/television/super-bowl-nostalgia.html |title=At the Super Bowl, Nostalgia's the Only Game |last=Poniewozik|first=James |work=The New York Times |date=February 14, 2022 |access-date=February 14, 2022 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214222029/https://www.nytimes.com/2022/02/14/arts/television/super-bowl-nostalgia.html |url-status=live }}"),
     Template("{{cite web |url=https://www.cnbc.com/2022/02/11/2022-super-bowl-ads-gm-meta-and-frito-lay-are-leaning-into-nostalgic-themes-.html |title=Celebrities, humor and a huge dose of nostalgia will dominate Sunday's Super Bowl ads |last=Wayland|first=Michael |work=CNBC |date=February 11, 2022 |access-date=February 14, 2022 |archive-date=February 13, 2022 |archive-url=https://web.archive.org/web/20220213040826/https://www.cnbc.com/2022/02/11/2022-super-bowl-ads-gm-meta-and-frito-lay-are-leaning-into-nostalgic-themes-.html |url-status=live }}"),
     Template('{{cite web |last1=Anderson |first1=Mae |title=Super Bowl ads go heavy on nostalgia and star power |url=https://apnews.com/article/super-bowl-ads-4d2e05ac2b5fbce8a9cb5b06bba1d68a |website=Associated Press |access-date=February 14, 2022 |date=February 13, 2022 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214011004/https://apnews.com/article/super-bowl-ads-4d2e05ac2b5fbce8a9cb5b06bba1d68a |url-status=live }}'),
     Template("{{cite web|last=Lacques|first=Gabe|title=Anna Kendrick leads Rocket Mortgage to another Super Bowl victory in USA TODAY's Ad Meter|url=https://www.usatoday.com/story/sports/Ad-Meter/2022/02/14/rocket-mortgage-voted-top-super-bowl-commercial-usa-today-ad-meter/6781228001/|website=USA Today|date=February 14, 2022|access-date=February 14, 2022}}"),
     Template('{{cite web |url=https://sports.yahoo.com/nbc-draws-second-largest-super-183542623.html |title=NBC Draws Second-Largest Super Bowl Audience to Cap NFL Bonanza |author=Crupi, Anthony |work=Yahoo Sports |date=February 15, 2022 |access-date=February 15, 2022}}'),
     Template('{{Cite web|last=Porter|first=Rick|date=2022-02-15|title=TV Ratings: Super Bowl Rebounds, Tops 100M Viewers|url=https://www.hollywoodreporter.com/tv/tv-news/tv-ratings-super-bowl-lvi-feb-13-2022-1235093067/|access-date=2022-02-16|website=The Hollywood Reporter}}'),
     Template('{{Cite web|title=How many people watched Super Bowl 56? Full 2022 ratings for Rams vs. Bengals show Detroit outperforms LA|url=https://www.sportingnews.com/us/nfl/news/super-bowl-2022-ratings-rams-bengals/uwundtq1y7dgmajpswn3wlsr|access-date=2022-02-15|website=Sporting News}}'),
     Template("{{Cite web|date=2022-02-15|title=Rams' late comeback drives Super Bowl 2022 audience to 112 million viewers|url=https://www.latimes.com/entertainment-arts/business/story/2022-02-15/super-bowl-ratings|access-date=2022-02-15|website=Los Angeles Times}}"),
     Template('{{cite web |title=Ways to Watch Super Bowl LVI |url=https://www.nfl.com/super-bowl/ways-to-watch/countries-and-languages |website=NFL.com |access-date=February 10, 2022 |archive-date=February 11, 2022 |archive-url=https://web.archive.org/web/20220211005115/https://www.nfl.com/super-bowl/ways-to-watch/countries-and-languages |url-status=live }}'),
     Template("{{cite web |title=NFL Lives Here: TSN and CTV's Comprehensive Coverage of the 2021 NFL Season Kicks Off September 9 |url=https://www.bellmedia.ca/the-lede/press/nfl-lives-here-tsn-and-ctvs-comprehensive-coverage-of-the-2021-nfl-season-kicks-off-september-9/ |website=Bellmedia.ca |access-date=December 26, 2021 |date=September 7, 2021 |archive-date=December 26, 2021 |archive-url=https://web.archive.org/web/20211226195812/https://www.bellmedia.ca/the-lede/press/nfl-lives-here-tsn-and-ctvs-comprehensive-coverage-of-the-2021-nfl-season-kicks-off-september-9/ |url-status=live }}"),
     Template('{{Cite news|url=https://business.financialpost.com/telecom/media/nfl-broadcaster-notch-canadian-court-victory-on-super-bowl-ads|url-access=limited|title=Super Bowl ad victory for Bell, NFL in Canada|first=Theophilos|last=Argitis|newspaper=Financial Post|agency=[[Bloomberg News]]|date=December 19, 2019|access-date=December 19, 2019|archive-date=December 19, 2019|archive-url=https://web.archive.org/web/20191219210835/https://business.financialpost.com/telecom/media/nfl-broadcaster-notch-canadian-court-victory-on-super-bowl-ads|url-status=live}}'),
     Template('{{Cite web |title=CTV, TSN, and RDS Announce Broadcast Details for Comprehensive Live Coverage of Super Bowl LVI, February 13 |publisher=CTV |via=newswire.ca |date=9 February 2022 |access-date=14 February 2022 |url=https://www.newswire.ca/news-releases/ctv-tsn-and-rds-announce-broadcast-details-for-comprehensive-live-coverage-of-super-bowl-lvi-february-13-847038932.html |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011522/https://www.newswire.ca/news-releases/ctv-tsn-and-rds-announce-broadcast-details-for-comprehensive-live-coverage-of-super-bowl-lvi-february-13-847038932.html |url-status=live }}'),
     Template("{{cite web|title=NFL and Sky Sports unveil 'Sky Sports NFL' as part of five-year partnership|url=https://www.skysports.com/nfl/news/12118/12047354/nfl-and-sky-sports-unveil-sky-sports-nfl-as-part-of-five-year-partnership|access-date=August 12, 2020|work=Sky Sports|archive-date=August 21, 2020|archive-url=https://web.archive.org/web/20200821000208/https://www.skysports.com/nfl/news/12118/12047354/nfl-and-sky-sports-unveil-sky-sports-nfl-as-part-of-five-year-partnership|url-status=live}}"),
     Template('{{cite web |title=Seven strikes NFL rights deal until 2024 |url=https://sevenwestmedia.com.au/assets/pdfs/Seven-strikes-NFL-rights-deal-until-2024.pdf |work=Seven West Media |access-date=January 31, 2022 |date=January 17, 2021 |archive-date=January 27, 2022 |archive-url=https://web.archive.org/web/20220127024052/https://www.sevenwestmedia.com.au/assets/pdfs/Seven-strikes-NFL-rights-deal-until-2024.pdf |url-status=live }}'),
     Template('{{cite web |title=SEN 1116 on Twitter |url=https://twitter.com/1116sen/status/1490853730807287808?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1490853730807287808%7Ctwgr%5E%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fwww.sen.com.au%2Fnews%2F2022%2F02%2F08%2Fwho-wins-the-super-bowl-our-preview-of-rams-versus-bengals |website=Twitter |access-date=February 12, 2022 |date=February 8, 2022 |archive-date=February 12, 2022 |archive-url=https://web.archive.org/web/20220212024749/https://twitter.com/1116sen/status/1490853730807287808?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1490853730807287808%7Ctwgr%5E%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fwww.sen.com.au%2Fnews%2F2022%2F02%2F08%2Fwho-wins-the-super-bowl-our-preview-of-rams-versus-bengals |url-status=live }}'),
     Template('{{cite web|url=https://www.eurosport.fr/football-us/nfl/2021-2022/super-bowl-2022-date-diffusion-chaine-tv-concert-de-mi-temps.-voici-le-programme_sto8772344/story.shtml|title=Super Bowl 2022 : Date, diffusion, chaine TV, concert de mi-temps... Voici le programme|date=February 9, 2022|language=fr|access-date=February 10, 2022|archive-date=February 10, 2022|archive-url=https://web.archive.org/web/20220210110342/https://www.eurosport.fr/football-us/nfl/2021-2022/super-bowl-2022-date-diffusion-chaine-tv-concert-de-mi-temps.-voici-le-programme_sto8772344/story.shtml|url-status=live}}'),
     Template('{{cite web|author1=Aatif Sulleyman Contributions from Adam Marshall|date=February 14, 2022|title=Who won Super Bowl 2022? MVP, score and Bengals vs Rams recap|url=https://www.techradar.com/news/how-to-watch-super-bowl-2022-lvi-date-channels-free-live-streams-online|access-date=February 14, 2022|website=TechRadar|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214013649/https://www.techradar.com/news/how-to-watch-super-bowl-2022-lvi-date-channels-free-live-streams-online|url-status=live}}'),
     Template('{{cite web|last=Redaktion|date=February 10, 2022|title=SUPER BOWL LVI live auf PULS 4 & PULS 24|url=https://football-austria.com/der-grosse-showdown-um-die-vince-lombardi-trophy-am-13-februar-um-2245-uhr-live-auf-puls-4-puls-24/|access-date=February 14, 2022|website=Österreichs Football Portal|language=de-DE|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214051103/https://football-austria.com/der-grosse-showdown-um-die-vince-lombardi-trophy-am-13-februar-um-2245-uhr-live-auf-puls-4-puls-24/|url-status=live}}'),
     Template('{{cite web|url=https://www.espn.com.br/nfl/artigo/_/id/9859370/super-bowl-lvi-onde-assistir-palco-show-do-intervalo-tudo-voce-precisa-saber-final-nfl|title=Super Bowl LVI: onde assistir, o palco, o show do intervalo... Tudo que você precisa saber sobre a final da NFL|website=[[ESPN (Brazil)|ESPN]]|date=January 31, 2022|access-date=February 6, 2022|language=pt|archive-date=February 1, 2022|archive-url=https://web.archive.org/web/20220201211828/https://www.espn.com.br/nfl/artigo/_/id/9859370/super-bowl-lvi-onde-assistir-palco-show-do-intervalo-tudo-voce-precisa-saber-final-nfl|url-status=live}}'),
     Template('{{cite web|url=https://moskva.mts.ru/personal/novosti/2021-12-27/premernye-pokazy-v-yanvare-na-kanalah-vip|title=Премьерные показы в январе на каналах ViP! {{!}} МТС – Москва|website=moskva.mts.ru|access-date=February 2, 2022|archive-date=January 18, 2022|archive-url=https://web.archive.org/web/20220118182754/https://moskva.mts.ru/personal/novosti/2021-12-27/premernye-pokazy-v-yanvare-na-kanalah-vip|url-status=live}}'),
     Template('{{!}}'),
     Template('{{cite web |language=fr |title=Suivez le Super Bowl en direct vidéo sur Les Sports +, Eleven Sports et au Hard Rock Café de la Grand Place ! |url=https://www.dhnet.be/sports/omnisports/sports-us/le-super-bowl-sera-a-suivre-en-direct-video-sur-les-sports-eleven-sports-et-au-hard-rock-cafe-de-la-grand-place-5e3309bb9978e23487ff13b5 |website=dhnet.be |date=January 30, 2020 |publisher=La Dernière Heure – Les Sports |access-date=February 10, 2022 |archive-date=February 10, 2022 |archive-url=https://web.archive.org/web/20220210201801/https://www.dhnet.be/sports/omnisports/sports-us/le-super-bowl-sera-a-suivre-en-direct-video-sur-les-sports-eleven-sports-et-au-hard-rock-cafe-de-la-grand-place-5e3309bb9978e23487ff13b5 |url-status=live }}'),
     Template('{{Cite web |title=Pozdrav iz Los Anđelesa sa Superboula LVI (VIDEO) – Sportklub |last=Trajković |first=Jelena |work=Sportklub |date=8 February 2022 |access-date=14 February 2022 |url=https://sportklub.rs/video/ostalo-video/pozdrav-iz-los-andjelesa-sa-superboula-lvi-video/ |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011527/https://sportklub.rs/video/ostalo-video/pozdrav-iz-los-andjelesa-sa-superboula-lvi-video/ |url-status=live }}'),
     Template('{{cite web|date=February 11, 2022|title=SUPER BOWL LVI 2022 – Los Angeles Rams vs Cincinnati Bengals|url=https://mola.tv/watch?v=vd43957326&utm_source=molatv&utm_medium=banner|url-status=live|work=Mola}}'),
     Template('{{Cite web |title=Legal black market inflates Super Bowl prices unheard of |publisher=TV3 Sport |author=Ritzau |date=11 February 2022 |access-date=14 February 2022 |url=https://www.tv3sport.dk/sport/nfl/nyheder/super-bowl-vinder-blev-far-under-triumf-kone-hastet-pa-hospitalet-midt-under-braget |language=da |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011525/https://www.tv3sport.dk/sport/nfl/nyheder/super-bowl-vinder-blev-far-under-triumf-kone-hastet-pa-hospitalet-midt-under-braget |url-status=live }}'),
     Template('{{cite web|date=February 14, 2022|title=Super Bowl 2022. Cincinnati Bengals – Los Angeles Rams. Transmisja meczu na żywo online w TVP Sport|url=https://sport.tvp.pl/58463384/super-bowl-2022-na-zywo-mecz-cincinnati-bengals-los-angeles-rams-transmisja-online-live-stream-14022022-gdzie-ogladac-mecz|url-status=live|access-date=February 13, 2022|archive-date=February 13, 2022|archive-url=https://web.archive.org/web/20220213230529/https://sport.tvp.pl/58463384/super-bowl-2022-na-zywo-mecz-cincinnati-bengals-los-angeles-rams-transmisja-online-live-stream-14022022-gdzie-ogladac-mecz}}'),
     Template('{{cite web|title=¿Dónde y a qué hora puedes ver el Super Bowl LVI?|url=https://www.metro.pr/deportes/2022/02/13/donde-y-a-que-hora-puedes-ver-el-super-bowl-lvi/|access-date=February 14, 2022|website=Metro Puerto Rico|language=es|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214033107/https://www.metro.pr/deportes/2022/02/13/donde-y-a-que-hora-puedes-ver-el-super-bowl-lvi/|url-status=live}}'),
     Template("{{cite news|title=Kupp's late TD lifts Rams over Bengals 23-20 in Super Bowl|date=February 13, 2022|agency=AP|website=ESPN.com|url=https://www.espn.com/nfl/recap?gameId=401326638|access-date=February 15, 2022}}"),
     Template('{{cite web |title=Rams vs. Bengals – Play-By-Play |url=https://www.espn.com/nfl/playbyplay/_/gameId/401326638 |website=ESPN |access-date=February 13, 2022 |date=February 13, 2022 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214031750/https://www.espn.com/nfl/playbyplay/_/gameId/401326638 |url-status=live }}'),
     Template('{{cite web |title=Stafford rallies Rams after Beckham (knee) exits |url=https://www.espn.com/nfl/story/_/id/33284663/los-angeles-rams-wide-receiver-odell-beckham-jr-questionable-return-suffering-leg-injury |website=ESPN |access-date=February 14, 2022 |date=February 14, 2022 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214015513/https://www.espn.com/nfl/story/_/id/33284663/los-angeles-rams-wide-receiver-odell-beckham-jr-questionable-return-suffering-leg-injury |url-status=live }}'),
     Template("{{cite web|last=Kerr|first=Jeff|title=Super Bowl 2022: Jalen Ramsey burned by non-call on Tee Higgins' 75-yard TD to start second half|url=https://www.cbssports.com/nfl/news/super-bowl-2022-jalen-ramsey-burned-by-non-call-on-tee-higgins-75-yard-td-to-start-second-half/|work=[[CBS Sports]]|date=February 13, 2022|access-date=February 14, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214022141/https://www.cbssports.com/nfl/news/super-bowl-2022-jalen-ramsey-burned-by-non-call-on-tee-higgins-75-yard-td-to-start-second-half/|url-status=live}}"),
     Template('{{cite news|last=Shook|first=Nick|title=Rams wide receiver Cooper Kupp named Super Bowl LVI MVP|url=https://www.nfl.com/news/rams-wide-receiver-cooper-kupp-named-super-bowl-lvi-mvp|publisher=NFL Enterprises, LLC|website=NFL.com|date=February 13, 2022|access-date=February 14, 2022|url-status=live}}'),
     Template("{{cite web|last=DeArdo|first=Bryan|title=Super Bowl 2022 MVP: Rams' Cooper Kupp becomes eighth wide receiver to be named MVP|url=https://www.cbssports.com/nfl/news/super-bowl-2022-mvp-rams-cooper-kupp-becomes-eighth-wide-receiver-to-be-named-mvp/|work=[[CBS Sports]]|date=February 13, 2022|access-date=February 14, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214044542/https://www.cbssports.com/nfl/news/super-bowl-2022-mvp-rams-cooper-kupp-becomes-eighth-wide-receiver-to-be-named-mvp/|url-status=live}}"),
     Template("{{Americanfootballbox\n|bg=#eee\n|titlestyle={{NFLAltPrimaryStyle|Los Angeles Chargers|year=2021|border=2}};text-align:center;\n|title=<!--DO NOT include seeds here --> Los Angeles Rams vs. Cincinnati Bengals — Game summary\n|date=February 13, 2022\n|time=6:30&nbsp;p.m. [[Eastern Time Zone|EST]]/3:30&nbsp;p.m. [[Pacific Time Zone|PST]]\n|road='''Rams (NFC)'''\n|R1=7|R2=6|R3=3|R4=7\n|home=Bengals (AFC)\n|H1=3|H2=7|H3=10|H4=0\n|stadium=[[SoFi Stadium]], [[Inglewood, California]]\n|attendance=70,048\n|weather=Clear, {{convert|82|F|C}}\n|referee=[[Ronald Torbert]]\n|TV=[[NFL on NBC|NBC]]\n|TVAnnouncers=[[Al Michaels]], [[Cris Collinsworth]], [[Michele Tafoya]] and [[Kathryn Tappen]]\n|reference=[https://www.nfl.com/games/rams-at-bengals-2021-post-4 Recap], [https://nflcdns.nfl.com/liveupdate/gamecenter/58836/CIN_Gamebook.pdf Game Book]\n}}"),
     Template('{{NFLAltPrimaryStyle|Los Angeles Chargers|year=2021|border=2}}'),
     Template('{{convert|82|F|C}}'),
     Template('{{AmFootballScoreSummaryStart |VisitorName=LAR |HomeName=CIN |state=expanded}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=1\n| Time=6:22\n| Team=LAR\n| DrivePlays=6\n| DriveLength=50\n| DriveTime=3:35\n| Type=RecTD\n| Receiver=[[Odell Beckham Jr.]]\n| QB=[[Matthew Stafford]]\n| yards=17\n| Kicker=[[Matt Gay]]\n| kickresult=good\n| Visitor=7\n| Home=0\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=1\n| Time=0:28\n| Team=CIN\n| DrivePlays=6\n| DriveLength=59\n| DriveTime=1:41\n| Type=FG\n| yards=29\n| Kicker=[[Evan McPherson]]\n| Visitor=7\n| Home=3\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=2\n| Time=12:51\n| Team=LAR\n| DrivePlays=6\n| DriveLength=75\n| DriveTime=2:37\n| Type=RecTD\n| Receiver=[[Cooper Kupp]]\n| QB=Stafford\n| yards=11\n| 2pt type=extra pt pass\n| 2pt result=failed\n| Visitor=13\n| Home=3\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=2\n| Time=5:47\n| Team=CIN\n| DrivePlays=12\n| DriveLength=75\n| DriveTime=7:04\n| Type=RecTD\n| Receiver=[[Tee Higgins]]\n| QB=[[Joe Mixon]]\n| yards=6\n| Kicker=McPherson\n| kickresult=good\n| Visitor=13\n| Home=10\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=3\n| Time=14:48\n| Team=CIN\n| DrivePlays=1\n| DriveLength=75\n| DriveTime=0:12\n| Type=RecTD\n| Receiver=Higgins\n| QB=[[Joe Burrow]]\n| yards=75\n| Kicker=McPherson\n| kickresult=good\n| Visitor=13\n| Home=17\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=3\n| Time=10:15\n| Team=CIN\n| DrivePlays=8\n| DriveLength=11\n| DriveTime=4:23\n| Type=FG\n| yards=38\n| Kicker=McPherson\n| Visitor=13\n| Home=20\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=3\n| Time=5:58\n| Team=LAR\n| DrivePlays=10\n| DriveLength=52\n| DriveTime=4:17\n| Type=FG\n| yards=41\n| Kicker=Gay\n| Visitor=16\n| Home=20\n}}'),
     Template('{{AmFootballScoreSummaryEntry\n| Quarter=4\n| Time=1:25\n| Team=LAR\n| DrivePlays=15\n| DriveLength=79\n| DriveTime=4:48\n| Type=RecTD\n| Receiver=Kupp\n| QB=Stafford\n| yards=1\n| Kicker=Gay\n| kickresult=good\n| Visitor=23\n| Home=20\n}}'),
     Template('{{AmFootballScoreSummaryEnd|Visitor=23|Home=20}}'),
     Template('{{cite web|title=Super Bowl LVI–National Football League Game Summary|url=https://nflcdns.nfl.com/liveupdate/gamecenter/58836/CIN_Gamebook.pdf|website=NFL.com|date=February 13, 2022|access-date=February 13, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214060553/https://nflcdns.nfl.com/liveupdate/gamecenter/58836/CIN_Gamebook.pdf|url-status=live}}'),
     Template("{{Cite web|title=Rams vs. Bengals by the numbers: 5 key stats from Los Angeles' Super Bowl 56 win|url=https://www.sportingnews.com/ca/nfl/news/rams-vs-bengals-numbers-5-key-stats-super-bowl-56-win/ydytzbvmgntxcqnhwkkxulxk|access-date=2022-02-18|website=www.sportingnews.com|language=en}}"),
     Template('{{cite web|last=DeArdo|first=Bryan|title=Records set in Super Bowl 2022: Aaron Donald, Rams apply record-tying pressure on Joe Burrow|url=https://www.cbssports.com/nfl/news/records-set-in-super-bowl-2022-aaron-donald-rams-apply-record-tying-pressure-on-joe-burrow/|work=[[CBS Sports]]|date=February 13, 2022|access-date=February 14, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214042828/https://www.cbssports.com/nfl/news/records-set-in-super-bowl-2022-aaron-donald-rams-apply-record-tying-pressure-on-joe-burrow/|url-status=live}}'),
     Template("{{cite web|date=February 14, 2022|title=Cincinnati Bengals' Super Bowl LVI loss a reminder rebuild not complete|url=https://www.espn.com/nfl/story/_/id/33281899/cincinnati-bengals-super-bowl-lvi-loss-los-angeles-rams-reminder-rebuild-not-complete|access-date=February 14, 2022|website=ESPN.com|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214050430/https://www.espn.com/nfl/story/_/id/33281899/cincinnati-bengals-super-bowl-lvi-loss-los-angeles-rams-reminder-rebuild-not-complete|url-status=live|author=Baby, Ben}}"),
     Template('{{small|(Unless noted as "NFL Championships", "Single Postseason" or "Pro Football History", all records refer only to Super Bowls)}}'),
     Template('{{cite web |last=Smith|first=Michael |date=February 14, 2022 |title=Von Miller has 4.5 sacks in Super Bowls, tying Charles Haley for the most ever |work=Yahoo Sports |url=https://sports.yahoo.com/von-miller-4-5-sacks-155925473.html |accessdate=February 14, 2021 |archive-date=February 15, 2022 |archive-url=https://web.archive.org/web/20220215011550/https://sports.yahoo.com/von-miller-4-5-sacks-155925473.html |url-status=live }}'),
     Template('{{Clear}}'),
     Template('{{NFLPrimaryStyle|Los Angeles Rams}}'),
     Template('{{sup|1}}'),
     Template('{{NFLPrimaryStyle|Los Angeles Rams}}'),
     Template('{{sup|2}}'),
     Template('{{sup|3}}'),
     Template('{{NFLPrimaryStyle|Los Angeles Rams}}'),
     Template('{{sup|4}}'),
     Template('{{sup|3}}'),
     Template('{{sup|5}}'),
     Template('{{NFLPrimaryStyle|Cincinnati Bengals}}'),
     Template('{{sup|1}}'),
     Template('{{NFLPrimaryStyle|Cincinnati Bengals}}'),
     Template('{{sup|2}}'),
     Template('{{sup|3}}'),
     Template('{{NFLPrimaryStyle|Cincinnati Bengals}}'),
     Template('{{sup|4}}'),
     Template('{{sup|3}}'),
     Template('{{sup|5}}'),
     Template('{{Clear}}'),
     Template('{{sup|1}}'),
     Template('{{sup|2}}'),
     Template('{{sup|3}}'),
     Template('{{sup|4}}'),
     Template('{{sup|5}}'),
     Template('{{NFLPrimaryStyle|Los Angeles Rams}}'),
     Template('{{NFLPrimaryStyle|Cincinnati Bengals}}'),
     Template('{{abbr|TE|Tight end}}'),
     Template('{{abbr|LT|Left tackle}}'),
     Template('{{abbr|LG|Left guard}}'),
     Template('{{abbr|C|Center}}'),
     Template('{{abbr|RG|Right guard}}'),
     Template('{{abbr|RT|Right tackle}}'),
     Template('{{abbr|WR|Wide receiver}}'),
     Template('{{abbr|QB|Quarterback}}'),
     Template('{{abbr|RB|Running back}}'),
     Template('{{abbr|DE|Defensive end}}'),
     Template('{{abbr|LDE|Left defensive end}}'),
     Template('{{abbr|NT|Nose tackle}}'),
     Template('{{abbr|DT|Defensive tackle}}'),
     Template('{{abbr|OLB|Outside linebacker}}'),
     Template('{{abbr|RDE|Right defensive end}}'),
     Template('{{abbr|LB|Linebacker}}'),
     Template('{{abbr|ILB|Inside linebacker}}'),
     Template('{{abbr|LCB|Left cornerback}}'),
     Template('{{abbr|NCB|Nickel cornerback}}'),
     Template('{{abbr|RCB|Right cornerback}}'),
     Template('{{abbr|CB|Cornerback}}'),
     Template('{{abbr|FS|Free safety}}'),
     Template('{{abbr|S|Safety}}'),
     Template('{{abbr|SS|Strong safety}}'),
     Template('{{abbr|K|Placekicker}}'),
     Template('{{abbr|P|Punter}}'),
     Template('{{cite web |last=Calabrese |first=Joe |url=https://www.foxla.com/news/rams-disneyland-super-bowl-2022 |title=LA Rams are going to Disneyland Monday after Super Bowl win |work=KTTV |publication-place=Los Angeles |date=2022-02-14 |accessdate=2022-02-14 |archive-date=February 14, 2022 |archive-url=https://web.archive.org/web/20220214080950/https://www.foxla.com/news/rams-disneyland-super-bowl-2022 |url-status=live }}'),
     Template('{{cite news|last1=Habeshian|first1=Sareen|last2=Yost|first2=Chip|title=Rams players celebrate Super Bowl victory at Disneyland|url=https://ktla.com/news/local-news/rams-players-celebrate-super-bowl-victory-at-disneyland/|work=[[KTLA]]|publisher=Nexstar Media Group|date=February 14, 2022}}'),
     Template('{{cite news|last1=Smith|first1=Hayley|last2=Newberry|first2=Laura|title=Super Bowl champion Rams will parade through L.A. on Wednesday|url=https://www.latimes.com/california/story/2022-02-14/when-is-l-a-rams-parade-2022-celebrating-super-bowl-win|work=Los Angeles Times|date=February 14, 2022|access-date=February 14, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214184402/https://www.latimes.com/california/story/2022-02-14/when-is-l-a-rams-parade-2022-celebrating-super-bowl-win|url-status=live}}'),
     Template('{{Reflist|30em}}'),
     Template('{{URL|https://www.nfl.com/super-bowl|Official website}}'),
     Template('{{URL|https://www.lasuperbowlhc.com|Official host committee website}}'),
     Template('{{Super Bowl}}'),
     Template('{{Super Bowl LVI}}'),
     Template('{{Los Angeles Rams}}'),
     Template('{{Cincinnati Bengals}}'),
     Template('{{2021 NFL season by team}}'),
     Template('{{NFLC-SuperBowl}}'),
     Template('{{NFL on NBC}}')]




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
wiki_templates[-10:]
```




    [Template("{{Infobox Super Bowl\n| type = sb\n| name = XXXVII\n| image = Super Bowl XXXVII Logo.svg\n| image_size = 200px\n| visitor = [[2002 Oakland Raiders season|Oakland Raiders]]\n| home = [[2002 Tampa Bay Buccaneers season|Tampa Bay Buccaneers]]\n| home_coach = [[Jon Gruden]]\n| home_record = 12–4\n| home_place = 2\n| visitor_coach = [[Bill Callahan (American football)|Bill Callahan]]\n| visitor_record = 11–5\n| visitor_place = 1\n| visitor_abbr = OAK\n| home_abbr = TB\n| visitor_conf = [[American Football Conference|AFC]]\n| home_conf = [[National Football Conference|NFC]]\n| visitor_total = 21\n| home_total = 48\n| visitor_qtr1 = 3\n| visitor_qtr2 = 0\n| visitor_qtr3 = 6\n| visitor_qtr4 = 12\n| home_qtr1 = 3\n| home_qtr2 = 17\n| home_qtr3 = 14\n| home_qtr4 = 14\n| date = {{start-date|January 26, 2003}}\n| stadium = [[SDCCU Stadium|Qualcomm Stadium]]\n| city = [[San Diego|San Diego, California]]\n| attendance = 67,603<ref>{{cite web|title=Super Bowl XXXVII Box Score: Tampa Bay 48, Oakland 21|url=http://www.nfl.com/superbowl/history/boxscore/sbxxxvii|publisher=National Football League|website=NFL.com|date=January 27, 2003|access-date=March 10, 2018}}</ref>\n| odds = Raiders by 4<ref>{{cite news|last=DiNitto|first=Marcus|title=Super Bowl Betting History – Underdogs on Recent Roll|url=http://linemakers.sportingnews.com/nfl/2015-01-25/super-bowl-betting-history-patriots-vs-seahawks-point-spread-line-vegas-odds|magazine=[[Sporting News]]|date=January 25, 2015|access-date=February 4, 2015}}</ref><ref>{{cite web|url=http://www.vegasinsider.com/nfl/superbowl/history/|title=Super Bowl History|publisher=Vegas Insider|access-date=February 4, 2015}}</ref>\n| MVP = [[Dexter Jackson (safety)|Dexter Jackson]], [[Defensive back|safety]]\n| anthem = [[Dixie Chicks]]\n| coin_toss = [[1972 Miami Dolphins season|1972 Miami Dolphins]]: [[Don Shula]], [[Bob Griese]], [[Larry Csonka]], [[Larry Little]], [[Jim Langer]], [[Nick Buoniconti]], [[Paul Warfield]]\n| referee = [[Bill Carollo]]\n| HOFers = '''Raiders:''' [[Al Davis]] (owner/administrator), [[Tim Brown (American football)|Tim Brown]], [[Jerry Rice]], [[Charles Woodson]], [[Rod Woodson]]<br />'''Buccaneers:''' [[Derrick Brooks]], [[John Lynch (American football)|John Lynch]], [[Warren Sapp]]\n| halftime = [[Shania Twain]], [[No Doubt]], and [[Sting (musician)|Sting]]\n| network = [[Monday Night Football|ABC]]\n| announcers = [[Al Michaels]], [[John Madden]], [[Melissa Stark]] and [[Lynn Swann]]\n| rating = 40.7 <br /><small>(est. 88.6 million viewers)</small><ref>{{cite web|url=http://tvbythenumbers.com/2009/01/18/historical-super-bowl-tv-ratings/11044|title=Historical Super Bowl Nielsen TV Ratings, 1967–2009 – Ratings|publisher=TVbytheNumbers|access-date=October 9, 2012}}</ref>\n| share = 61\n| commercial = $2.1 million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Marv Albert]], [[Boomer Esiason]], [[John Dockery]] and [[Warren Moon]]\n| last = XXXVI\n| next = XXXVIII\n}}"),
     Template("{{Infobox Super Bowl\n| type = sb\n| name = XXXVIII\n| image = Super Bowl XXXVIII.svg\n| image_size = 200px\n| visitor = [[2003 Carolina Panthers season|Carolina Panthers]]\n| home = [[2003 New England Patriots season|New England Patriots]]\n| home_coach = [[Bill Belichick]]\n| home_record = 14–2\n| home_place = 1\n| visitor_coach = [[John Fox (American football)|John Fox]]\n| visitor_record = 11–5\n| visitor_place = 3\n| visitor_abbr = CAR\n| home_abbr = NE\n| visitor_conf = [[National Football Conference|NFC]]\n| home_conf = [[American Football Conference|AFC]]\n| visitor_total = 29\n| home_total = 32\n| visitor_qtr1 = 0\n| visitor_qtr2 = 10\n| visitor_qtr3 = 0\n| visitor_qtr4 = 19\n| home_qtr1 = 0\n| home_qtr2 = 14\n| home_qtr3 = 0\n| home_qtr4 = 18\n| date = {{start-date|February 1, 2004}}\n| stadium = [[NRG Stadium|<!--The name was Reliant Stadium back in 2004-->Reliant Stadium]]\n| city = [[Houston|Houston, Texas]]\n| attendance = 71,525<ref>{{cite web|title=Super Bowl XXXVIII Box Score: New England 32, Carolina 29|url=http://www.nfl.com/superbowl/history/boxscore/sbxxxviii|publisher=National Football League|website=NFL.com|date=February 1, 2004|access-date=March 10, 2018|archive-date=March 14, 2018|archive-url=https://web.archive.org/web/20180314222010/http://www.nfl.com/superbowl/history/boxscore/sbxxxviii|url-status=live}}</ref>\n| HOFers = '''Panthers:''' Sam Mills <br />'''Patriots:''' [[Ty Law]], [[Richard Seymour]]\n| odds = Patriots by 7<ref>{{cite news|last=DiNitto|first=Marcus|title=Super Bowl Betting History – Underdogs on Recent Roll|url=http://linemakers.sportingnews.com/nfl/2015-01-25/super-bowl-betting-history-patriots-vs-seahawks-point-spread-line-vegas-odds|magazine=[[Sporting News]]|date=January 25, 2015|access-date=February 4, 2015|archive-date=February 4, 2015|archive-url=https://web.archive.org/web/20150204100054/http://linemakers.sportingnews.com/nfl/2015-01-25/super-bowl-betting-history-patriots-vs-seahawks-point-spread-line-vegas-odds|url-status=live}}</ref><ref>{{cite web|url=http://www.vegasinsider.com/nfl/superbowl/history/|title=Super Bowl History|publisher=Vegas Insider|access-date=February 4, 2015|archive-date=February 4, 2015|archive-url=https://web.archive.org/web/20150204101556/http://www.vegasinsider.com/nfl/superbowl/history/|url-status=live}}</ref>\n| MVP = [[Tom Brady]], [[quarterback]]\n| anthem = [[Beyoncé]]\n| coin_toss = [[Earl Campbell]], [[Ollie Matson]], [[Don Maynard]], [[Y. A. Tittle]], [[Mike Singletary]], [[Gene Upshaw]]\n| referee = [[Ed Hochuli]]\n| halftime = [[Jessica Simpson]], [[Ocean of Soul]], [[Spirit of Houston]], [[Janet Jackson]], [[Justin Timberlake]], [[Sean Combs|P. Diddy]], [[Kid Rock]], and [[Nelly]]\n| network = [[NFL on CBS|CBS]]\n| announcers = [[Greg Gumbel]], [[Phil Simms]], [[Armen Keteyian]] and [[Bonnie Bernstein]]\n| rating = 41.3 <br /><small>(est. 89.8 million viewers)</small><ref>{{cite web|url=http://tvbythenumbers.com/2009/01/18/historical-super-bowl-tv-ratings/11044|title=TV By The Numbers by zap2it.com|website=TV By The Numbers by zap2it.com|access-date=January 25, 2010|archive-date=February 8, 2010|archive-url=https://web.archive.org/web/20100208213536/http://tvbythenumbers.com/2009/01/18/historical-super-bowl-tv-ratings/11044|url-status=dead}}</ref>\n| share = 63\n| commercial = $2.2 million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Marv Albert]], [[Boomer Esiason]], [[John Dockery]] and [[John Riggins]] \n| last = XXXVII\n| next = XXXIX\n}}"),
     Template('{{Infobox Super Bowl\n| type = sb\n| name = 50\n| image = Super Bowl 50 Logo.svg\n| visitor = [[2015 Carolina Panthers season|Carolina Panthers]]\n| visitor_abbr = CAR\n| visitor_conf = [[National Football Conference|NFC]]\n| visitor_coach = [[Ron Rivera]]\n| visitor_record = 15–1\n| visitor_place = 1\n| home = [[2015 Denver Broncos season|Denver Broncos]]\n| home_abbr = DEN\n| home_conf = [[American Football Conference|AFC]]\n| home_coach = [[Gary Kubiak]]\n| home_record = 12–4\n| home_place = 1\n| visitor_qtr1 = 0\n| visitor_qtr2 = 7\n| visitor_qtr3 = 0\n| visitor_qtr4 = 3\n| home_qtr1 = 10\n| home_qtr2 = 3\n| home_qtr3 = 3\n| home_qtr4 = 8\n| date = {{start date|2016|2|7}}\n| stadium = [[Levi\'s Stadium]]\n| city = [[Santa Clara, California]]\n| attendance = 71,088<ref name="Attendance">{{cite news|last1=Breech|first1=John|title=Fans at Super Bowl 50 spent nearly $11 million, bought 8K glasses of wine|url=http://www.cbssports.com/nfl/eye-on-football/25481285/fans-at-super-bowl-50-spent-nearly-11m-bought-8000-glasses-of-wine|access-date=February 14, 2016|publisher=CBSSports.com|date=February 11, 2016|url-status=live|archive-url=https://web.archive.org/web/20160302012242/http://www.cbssports.com/nfl/eye-on-football/25481285/fans-at-super-bowl-50-spent-nearly-11m-bought-8000-glasses-of-wine|archive-date=March 2, 2016}}</ref>\n| odds = Panthers by 5.5<ref name="odds">{{cite news|last=Perdum|first=David|title=Panthers open as clear favorites over Broncos to win Super Bowl|url=http://espn.go.com/chalk/story/_/id/14641020/carolina-panthers-installed-clear-super-bowl-favorites-denver-broncos|publisher=ESPN|date=January 25, 2016|access-date=January 25, 2016|url-status=live|archive-url=https://web.archive.org/web/20160126093738/http://espn.go.com/chalk/story/_/id/14641020/carolina-panthers-installed-clear-super-bowl-favorites-denver-broncos|archive-date=January 26, 2016}}</ref>\n| MVP = [[Von Miller]], [[linebacker]]\n| anthem = [[Lady Gaga]]\n| coin_toss = [[Fred Biletnikoff]], [[Marcus Allen]], [[Joe Montana]], [[Jim Plunkett]], [[Jerry Rice]], [[Steve Young]]\n| referee = [[Clete Blakeman]]\n| HOFers = \'\'\'Panthers:\'\'\' none <br />\'\'\'Broncos:\'\'\' [[Pat Bowlen]] (owner), [[Peyton Manning]]\n| halftime = [[Coldplay]] featuring [[Beyoncé]] and [[Bruno Mars]] with [[Mark Ronson]]\n| network = [[NFL on CBS|CBS]]\n| announcers = [[Jim Nantz]] (play-by-play)<br>[[Phil Simms]] (color analyst)<br>[[Tracy Wolfson]] and [[Evan Washburn]] (sideline reporters)<br>[[Mike Carey (American football)|Mike Carey]] (rules expert)\n| rating = 46.6 (national)<br> 53.9 (Denver)<br> 55.9 (Charlotte)<br>U.S. viewership: 111.9&nbsp;million est. avg.,<ref name="Nielsen ratings"/> 167.0&nbsp;million est. total<ref name="NFL.com ratings"/>\n| share = 72 (national)\n| commercial = $5 million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Boomer Esiason]] and [[Dan Fouts]] (analysts)<br />[[James Lofton]] and [[Mark Malone]] (sideline reporters)\n| last = XLIX\n| next = LI\n}}'),
     Template('{{Infobox NFL game\n| type = sb\n| name = LI\n| image = Super Bowl LI logo.svg\n| image_size = 200px\n| caption =\n| visitor = [[2016 New England Patriots season|New England Patriots]]\n| visitor_abbr = NE\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Bill Belichick]]\n| visitor_record = 14–2\n| visitor_place = 1\n| home = [[2016 Atlanta Falcons season|Atlanta Falcons]]\n| home_abbr = ATL\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Dan Quinn (American football)|Dan Quinn]]\n| home_record = 11–5\n| home_place = 2\n| visitor_qtr1 = 0\n| visitor_qtr2 = 3\n| visitor_qtr3 = 6\n| visitor_qtr4 = 19\n| visitor_qtr5 = 6\n| home_qtr1 = 0\n| home_qtr2 = 21\n| home_qtr3 = 7\n| home_qtr4 = 0\n| home_qtr5 = 0\n| date = February 5, 2017\n| stadium = [[NRG Stadium]]\n| city = [[Houston|Houston, Texas]]\n| attendance = 70,807<ref>{{cite web|title=Super Bowl LI Game Summary|url=https://nflcdns.nfl.com/liveupdate/gamecenter/57167/ATL_Gamebook.pdf|publisher=NFL Enterprises|website=NFL.com|date=February 5, 2017|access-date=February 6, 2017}}</ref>\n| odds = Patriots by 3<ref>{{cite news|last=Williams|first=Cody|title=Super Bowl 51: Patriots Open As Favorites Over Falcons|url=http://www.foxsports.com/nfl/story/super-bowl-51-patriots-open-as-favorites-over-falcons-012217|work=[[Fox Sports (United States)|Fox Sports]]|date=January 22, 2017|access-date=January 23, 2017}}</ref>\n| MVP = [[Tom Brady]], [[quarterback]]\n| anthem = [[Luke Bryan]]<ref>{{cite web|title=Luke Bryan to sing the National Anthem at Super Bowl LI on Fox|url=https://nflcommunications.com/Pages/LUKE-BRYAN-TO-SING-THE-NATIONAL-ANTHEM--AT-SUPER-BOWL-LI-ON-FOX.aspx|publisher=NFL Enterprises|website=NFLCommunications.com|date=February 3, 2017|access-date=September 25, 2019}}</ref>\n| coin_toss = Former [[President of the United States|U.S. President]] [[George H. W. Bush]],<br />Former [[First Lady of the United States|U.S. First Lady]] [[Barbara Bush]]<ref>{{cite news|last1=Chasmar|first1=Jessica|title=George H.W. Bush to flip coin at Super Bowl LI|url=http://www.washingtontimes.com/news/2017/feb/1/george-hw-bush-to-flip-coin-at-super-bowl-li/|newspaper=[[The Washington Times]]|date=February 3, 2017|access-date=September 25, 2019}}</ref>\n| referee = [[Carl Cheffers]]<ref name="Super Bowl 51 officials"/>\n| HOFers =\n| halftime = [[Lady Gaga]]<ref>{{cite news|title=Lady Gaga headlines Pepsi Zero Sugar Super Bowl LI Halftime Show|url=http://www.nfl.com/news/story/0ap3000000711980/article/lady-gaga-headlines-pepsi-zero-sugar-super-bowl-li-halftime-show|publisher=NFL Enterprises|website=NFL.com|date=September 29, 2016|access-date=September 29, 2016}}</ref>\n| network = [[NFL on Fox|Fox]]\n| announcers = [[Joe Buck]] (play-by-play)<br />[[Troy Aikman]] (analyst)<br />[[Erin Andrews]] and [[Chris Myers]] (sideline reporters)<br>[[Mike Pereira]] (rules analyst)\n| rating = 48.8 (national)<br /> 57.0 (Atlanta)<br /> 54.3 (Boston)<br />U.S. viewership: 111.3&nbsp;million est. avg.<ref>{{cite news|title=Ratings: Super Bowl LI Posts Huge Numbers, Just Shy of Record (Updated)|url=https://www.yahoo.com/tv/ratings-super-bowl-li-posts-huge-numbers-just-154243304.html|publisher=[[Yahoo!]]|date=February 6, 2017|access-date=February 7, 2017}}</ref>\n| share =\n| commercial = $5.02&nbsp;million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Boomer Esiason]] (analyst)<br />[[James Lofton]] and [[Tony Boselli]] (sideline reporters)\n| last = 50\n| next = LII\n}}'),
     Template("{{Infobox NFL game\n| type = sb \n| name = LII\n| image = Super Bowl LII logo.svg\n| caption =\n| visitor = [[2017 Philadelphia Eagles season|Philadelphia Eagles]]\n| visitor_abbr = PHI\n| visitor_coach = [[Doug Pederson]]\n| visitor_record = 13–3\n| visitor_place = 1\n| visitor_conf = [[National Football Conference|NFC]]\n| home = [[2017 New England Patriots season|New England Patriots]]\n| home_abbr = NE\n| home_conf = [[American Football Conference|AFC]]\n| home_coach = [[Bill Belichick]]\n| home_record = 13–3\n| home_place = 1\n| visitor_qtr1 = 9\n| visitor_qtr2 = 13\n| visitor_qtr3 = 7\n| visitor_qtr4 = 12\n| home_qtr1 = 3\n| home_qtr2 = 9\n| home_qtr3 = 14\n| home_qtr4 = 7\n| date = February 4, 2018\n| stadium = [[U.S. Bank Stadium]]\n| city = [[Minneapolis|Minneapolis, Minnesota]]\n| attendance = 67,612\n| odds = Patriots by 5\n| MVP = [[Nick Foles]], [[quarterback]]\n| anthem = [[Pink (singer)|Pink]]\n| coin_toss = [[Hershel W. Williams]], representing [[Medal of Honor]] recipients\n| referee = [[Gene Steratore]]\n| HOFers =\n| halftime = [[Justin Timberlake]]\n| network = [[NBC Sunday Night Football|NBC]]<br />[[Universo (TV network)|Universo]] (Spanish language)\n| announcers = [[Al Michaels]] (play-by-play)<br />[[Cris Collinsworth]] (analyst)<br />[[Michele Tafoya]] (sideline reporter)<br />Edgar López (play-by-play- Universo)<br />René Giraldo and [[Rolando Cantú]] (analysts- Universo)<br />Verónica Contreras (sidelines- Universo)\n| rating = 43.1 (national)<br />56.2 (Philadelphia)<br />55.9 (Boston)<br />U.S. viewership: 103.4&nbsp;million est. avg.<ref>{{cite web|last1=Porter|first1=Rick|title=TV Ratings Sunday: Super Bowl LII smallest since 2009, still massive; 'This Is Us' scores big [Updated]|url=http://tvbythenumbers.zap2it.com/daily-ratings/tv-ratings-sunday-feb-4-2018-super-bowl/|website=[[TV by the Numbers]]|access-date=February 5, 2018|url-status=dead|archive-url=https://web.archive.org/web/20180205222211/http://tvbythenumbers.zap2it.com/daily-ratings/tv-ratings-sunday-feb-4-2018-super-bowl/|archive-date=February 5, 2018|date=February 5, 2018}}</ref>\n| share = 68 (national)\n| commercial = $5&nbsp;million<ref>{{cite news|last1=Chiari|first1=Mike|title=Super Bowl Commercials 2018: Expectations, Rumors and Most-Hyped Movie Trailers|url=http://bleacherreport.com/articles/2755529-super-bowl-commercials-2018-expectations-rumors-and-most-hyped-movie-trailers|access-date=January 25, 2018|work=Bleacher Report|date=January 24, 2018|url-status=live|archive-url=https://web.archive.org/web/20180125125713/http://bleacherreport.com/articles/2755529-super-bowl-commercials-2018-expectations-rumors-and-most-hyped-movie-trailers|archive-date=January 25, 2018}}</ref>\n| radio = [[NFL on Westwood One Sports|Westwood One]]<br />[[ESPN Deportes Radio]] (Spanish language)\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Boomer Esiason]] and [[Mike Holmgren]] (analysts)<br />[[Ed Werder]] and [[Tony Boselli]] (sideline reporters)<br />[[Álvaro Martín (sports announcer)|Álvaro Martín]] (play-by-play- ESPN Deportes Radio)<br />[[Raúl Allegre]] (analyst- ESPN Deportes Radio)<br />John Sutcliffe (sideline- ESPN Deportes Radio)\n| last = LI\n| next = LIII\n}}"),
     Template('{{Infobox NFL game\n| type = sb\n| name = LIII\n| full_name = <!-- ONLY needed if name is not a simple year, roman numeral, etc-->\n| image = Super Bowl LIII logo.png\n| visitor = [[2018 New England Patriots season|New England Patriots]]\n| visitor_abbr = NE\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Bill Belichick]]\n| visitor_record = 11–5\n| visitor_place = 2\n| home = [[2018 Los Angeles Rams season|Los Angeles Rams]]\n| home_abbr = LAR\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Sean McVay]]\n| home_record = 13–3\n| home_place = 2\n| visitor_qtr1 = 0\n| visitor_qtr2 = 3\n| visitor_qtr3 = 0\n| visitor_qtr4 = 10\n| home_qtr1 = 0\n| home_qtr2 = 0\n| home_qtr3 = 3\n| home_qtr4 = 0\n| date = February 3, 2019\n| stadium = [[Mercedes-Benz Stadium]]\n| city = [[Atlanta|Atlanta, Georgia]]\n| attendance = 70,081\n| odds = Patriots by 2.5\n| over-under = Under: 56\n| MVP = [[Julian Edelman]], [[Wide Receiver]]\n| anthem = [[Gladys Knight]]\n| coin_toss = [[Bernice King]]\n| referee = [[John Parry (American football official)|John Parry]]\n| HOFers =\n| halftime = [[Maroon 5]] featuring [[Travis Scott]] and [[Big Boi]]\n| network = [[NFL on CBS|CBS]]<br>[[ESPN Deportes]] (Spanish language)\n| announcers = [[Jim Nantz]] (play-by-play)<br>[[Tony Romo]] (analyst)<br>[[Tracy Wolfson]] and [[Evan Washburn]] (sideline reporters)<br>[[Jay Feely]] (special teams analyst)<br>[[Gene Steratore]] (rules analyst)\n| rating = 41.1 (national)<br> 57.4 (Boston) <br> 44.6 (Los Angeles) <br>U.S. viewership: 98.2&nbsp;million est. avg.\n| share = \n| commercial = $5.25&nbsp;million\n| radio = [[NFL on Westwood One Sports|Westwood One]]<br />[[ESPN Deportes Radio]] (Spanish language)\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br>[[Kurt Warner]] and [[Mike Holmgren]] (analysts)<br>[[Ed Werder]] and [[Tony Boselli]] (sideline reporters)<br>[[Kenneth Garay]] (play-by-play- ESPN Deportes Radio)<br>Sebastian Martínez Christensen (analyst- ESPN Deportes Radio)\n| last = LII\n| next = LIV\n}}'),
     Template('{{Infobox NFL game \n| type = sb\n| name = LIV\n| image = Super Bowl LIV.svg\n| caption = \n| visitor = [[2019 San Francisco 49ers season|San Francisco 49ers]]\n| visitor_abbr = SF\n| visitor_conf = [[National Football Conference|NFC]]\n| visitor_coach = [[Kyle Shanahan]]\n| visitor_record = 13–3\n| visitor_place = 1\n| home = [[2019 Kansas City Chiefs season|Kansas City Chiefs]]\n| home_abbr = KC\n| home_conf = [[American Football Conference|AFC]]\n| home_coach = [[Andy Reid]]\n| home_record = 12–4\n| home_place = 2\n| visitor_qtr1 = 3\n| visitor_qtr2 = 7\n| visitor_qtr3 = 10\n| visitor_qtr4 = 0\n| home_qtr1 = 7\n| home_qtr2 = 3\n| home_qtr3 = 0\n| home_qtr4 = 21\n| date = February 2, 2020\n| stadium = [[Hard Rock Stadium]]\n| city = [[Miami Gardens, Florida]]\n| attendance = 62,417\n| odds = Chiefs by 1.5\n| MVP = [[Patrick Mahomes]], [[quarterback]]\n| anthem = [[Demi Lovato]]\n| coin_toss = [[Colonel (United States)|Colonel]]<!--McGee was still a Colonel when the game was held, he was not promoted until Feb. 4--> [[Charles McGee (Tuskegee Airman)|Charles E. McGee]] (Ret.)\n| referee = [[Bill Vinovich]]\n| HOFers = \n| halftime = [[Jennifer Lopez]] and [[Shakira]] featuring [[Bad Bunny]] and [[J Balvin]]\n| network = [[NFL on Fox|Fox]]<br>[[Fox Deportes]] (Spanish language)\n| announcers = [[Joe Buck]] (play-by-play)<br />[[Troy Aikman]] (color commentator)<br />[[Erin Andrews]] and [[Chris Myers]] (sideline reporters)<br />[[Mike Pereira]] (rules analyst)\n| rating = 41.6 (national)<br> 55.7 (Kansas City) <br> 48.6 (San Francisco) <br>U.S. viewership: 102.8&nbsp;million est. avg.\n| share = \n| commercial = $5.6&nbsp;million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Kurt Warner]] (color commentator)<br>Laura Okmin and [[Tony Boselli]] (sideline reporters)<br />[[Gene Steratore]] (rules analyst)\n| last = LIII\n| next = LV\n}}'),
     Template('{{Infobox NFL game\n<!--Please do not add anything else to this Infobox until it has been officially announced by the National Football League or CBS. Thank you.-->\n| type = sb\n| name = LV\n| full_name = <!--ONLY if name is not a simple year, roman numeral, etc.-->\n| image = Super Bowl LV.png\n| image_size = 250px\n| caption =\n| visitor = [[2020 Kansas City Chiefs season|Kansas City Chiefs]]\n| visitor_abbr = KC\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Andy Reid]]\n| visitor_record = 14–2\n| visitor_place = 1\n| home = [[2020 Tampa Bay Buccaneers season|Tampa Bay Buccaneers]]\n| home_abbr = TB\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Bruce Arians]]\n| home_record = 11–5\n| home_place = 5\n| visitor_qtr1 = 3\n| visitor_qtr2 = 3\n| visitor_qtr3 = 3\n| visitor_qtr4 = 0\n| home_qtr1 = 7\n| home_qtr2 = 14\n| home_qtr3 = 10\n| home_qtr4 = 0\n| date = February 7, 2021\n| stadium = [[Raymond James Stadium]]\n| city = [[Tampa, Florida]]\n<!--Please do not add anything else to this Infobox table until it has been officially announced by the National Football League or CBS. Thank you.-->\n| attendance = 24,835{{efn|Only 37% of the seats were filled due to the [[COVID-19 pandemic in Florida|COVID-19 pandemic]].}}\n| odds = Chiefs by 3<ref>{{cite news |last=Purdum |first=David |title=Kansas City Chiefs (−3) open as favorites over Tampa Bay Buccaneers in Super Bowl LV |url=https://www.espn.com/nfl/story/_/id/30773421/kansas-city-chiefs-3-open-favorites-tampa-bay-buccaneers-super-bowl-lv |publisher=ESPN |date=January 24, 2020 |access-date=January 24, 2020}}</ref>\n| MVP = [[Tom Brady]], [[quarterback]]\n| anthem = [[Eric Church]] and [[Jazmine Sullivan]]<ref name="anthem">{{cite press release|title=Eric Church, Jazmine Sullivan to sing national anthem at Super Bowl LV; H.E.R. to sing America the Beautiful|url=https://www.nfl.com/news/eric-church-jazmine-sullivan-to-sing-national-anthem-at-super-bowl-lv-h-e-r-to-s|website=NFL.com|date=January 19, 2021|access-date=January 23, 2021}}</ref>\n| halftime = [[The Weeknd]]<ref name="TheWeekndNFL">{{cite press release|title=The Weeknd to headline Pepsi Super Bowl LV Halftime Show at Raymond James Stadium|url=https://www.nfl.com/news/the-weeknd-to-headline-pepsi-super-bowl-lv-halftime-show-at-raymond-james-stadiu|website=NFL.com|date=November 12, 2020|access-date=November 13, 2020}}</ref>\n| coin_toss = Suzie Dorner, [[Intensive care unit|ICU]] nurse representing medical personnel during the [[COVID-19 pandemic in the United States|COVID-19 pandemic]] \n| HOFers =\n| referee = [[Carl Cheffers]]<ref name="SBLVOfficiatingCrew">{{cite news|last=Bergman|first=Jeremy|title=Super Bowl LV officiating crew: Carl Cheffers named referee, Sarah Thomas to make history|url=https://www.nfl.com/news/super-bowl-lv-officiating-crew-carl-cheffers-referee-sarah-thomas-history|website=NFL.com|date=January 19, 2021|access-date=February 2, 2021}}</ref><ref name="FootballZebras">{{cite web|last=Filipe|first=Cameron|title=Carl Cheffers is the referee for Super Bowl LV. Sarah Thomas becomes the first woman to officiate a Super Bowl|url=http://www.footballzebras.com/2021/01/carl-cheffers-is-the-referee-for-super-bowl-lv-sarah-thomas-becomes-the-first-woman-to-officiate-a-super-bowl/|website=Football Zebras|date=January 19, 2021|access-date=January 19, 2021}}</ref>\n| network = [[NFL on CBS|CBS]]<br>[[ESPN Deportes]] (Spanish language)\n| announcers = [[Jim Nantz]] (play-by-play)<br>[[Tony Romo]] (analyst)<br>[[Tracy Wolfson]] and [[Evan Washburn]] (sideline reporters)<br>[[Jay Feely]] (special teams analyst)<br>[[Gene Steratore]] (rules analyst)\n| rating = 38.2 (national)<br>59.9 (Kansas City)<br/>52.3 (Tampa Bay)<br/>U.S. viewership: 96.4 million<ref>{{cite web |last1=Young |first1=Jabari |title=Super Bowl posts worst viewer ratings since 2007 |url=https://www.cnbc.com/2021/02/09/super-bowl-ratings-game-fails-to-attract-more-than-100-million-viewers.html |website=CNBC |access-date=February 14, 2022 |date=February 9, 2021}}</ref>\n| share =\n| commercial = $5.6&nbsp;million\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Kurt Warner]] (analyst)<br>[[Laura Okmin]] and [[Tony Boselli]] (sideline reporters)\n| last = LIV\n| next = LVI\n|Cost of 30-second commercial=$5.6 million}}'),
     Template('{{Infobox NFL game\n<!--Please do not add anything else to this Infobox until it has been officially announced by the National Football League or NBC. Thank you.-->\n| type = sb\n| name = LVI\n| full name = <!--ONLY if name is not a simple year, roman numeral, etc.-->\n| image = Super Bowl LVI logo.png\n| image_size = 250px \n| caption = \n| visitor = [[2021 Los Angeles Rams season|Los Angeles Rams]]\n| visitor_abbr = LAR\n| visitor_conf = [[National Football Conference|NFC]]\n| visitor_coach = [[Sean McVay]]\n| visitor_record = 12–5\n| visitor_place = 4\n| home = [[2021 Cincinnati Bengals season|Cincinnati Bengals]]\n| home_abbr = CIN\n| home_conf = [[American Football Conference|AFC]]\n| home_coach = [[Zac Taylor]]\n| home_record = 10–7\n| home_place = 4\n| visitor_qtr1 = 7\n| visitor_qtr2 = 6\n| visitor_qtr3 = 3\n| visitor_qtr4 = 7\n| home_qtr1 = 3\n| home_qtr2 = 7\n| home_qtr3 = 10\n| home_qtr4 = 0\n| date = February 13, 2022\n| stadium = [[SoFi Stadium]]\n| city = [[Inglewood, California]]\n| attendance = 70,048\n| odds = Rams by 4<ref>{{cite web|title=NFL Odds And Lines - SportsLine.com|url=https://www.sportsline.com/nfl/odds/|access-date=February 11, 2022|website=SportsLine|archive-date=January 15, 2022|archive-url=https://web.archive.org/web/20220115015951/https://www.sportsline.com/nfl/odds/|url-status=live}}</ref>\n| MVP = [[Cooper Kupp]], [[wide receiver]]\n| anthem = [[Mickey Guyton]]<ref>{{cite news|last=McCarriston|first=Shanna|title=Super Bowl 2022: Mickey Guyton to sing national anthem, other pregame performers at SoFi Stadium in L.A.|url=https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|website=CBSsports.com|date=February 12, 2022|access-date=February 12, 2022|archive-date=February 14, 2022|archive-url=https://web.archive.org/web/20220214031742/https://www.cbssports.com/nfl/news/super-bowl-2022-mickey-guyton-to-sing-national-anthem-other-pregame-performers-at-sofi-stadium-in-l-a/|url-status=live}}</ref>\n| coin_toss = [[Billie Jean King]]\n| referee = [[Ronald Torbert]]<ref name="FootballZebras">{{cite web |last1=Filipe |first1=Cameron |title=Ron Torbert is the referee for Super Bowl LVI |url=https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |website=footballzebras.com |date=January 25, 2022 |access-date=January 25, 2022 |archive-date=January 25, 2022 |archive-url=https://web.archive.org/web/20220125175824/https://www.footballzebras.com/2022/01/ron-torbert-is-the-referee-for-super-bowl-lvi/ |url-status=live }}</ref><ref>{{cite web|title=NFL announces Super Bowl LVI officiating crew; four of eight officials have prior SB experience|url=https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|website=NFL.com|date=January 25, 2022|access-date=January 25, 2022|archive-date=January 25, 2022|archive-url=https://web.archive.org/web/20220125210756/https://www.nfl.com/news/nfl-announces-super-bowl-lvi-officiating-crew-four-of-eight-officials-have-prior|url-status=live}}</ref>\n| HOFers =\n| halftime = [[Dr. Dre]], [[Snoop Dogg]], [[Eminem]], [[Mary J. Blige]] and [[Kendrick Lamar]] featuring [[50 Cent]] and [[Anderson .Paak]]<ref name="Halftime-Show"/><ref name="Halftime-ASL"/>\n| network = <!-- DO NOT list streaming services. -->[[NBC Sunday Night Football|NBC]]<br/>[[Telemundo]] (Spanish)<ref name=mundo>{{cite web |url=https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |title=Who is broadcasting the 2022 Super Bowl? TV channel, announcers, live stream for Bengals vs. Rams |date=February 6, 2022 |website=[[NBC Sports]] |access-date=February 8, 2022 |archive-date=February 6, 2022 |archive-url=https://web.archive.org/web/20220206175757/https://sports.nbcsports.com/2022/02/05/who-is-broadcasting-the-2022-super-bowl-tv-channel-announcers-live-stream-for-super-bowl-lvi-this-year/ |url-status=live }}</ref><!-- Do not change this without a source. -->\n| announcers = [[Al Michaels]] (play-by-play)<br/>[[Cris Collinsworth]] (analyst)<br/>[[Michele Tafoya]] and [[Kathryn Tappen]] (sideline reporters)<br/>[[Terry McAulay]] (rules analyst)\n| rating = 40.1 (national)<br>36.7 (Los Angeles)<br>46.1 (Cincinnati)<br>U.S. TV viewership: 101.1&nbsp;million<ref name = viewers>{{cite news|url = https://variety.com/2022/tv/news/super-bowl-lvi-ratings-nbc-1235181334/|title = TV Ratings: NBC\'s Super Bowl LVI Telecast Draws Over 100 Million Viewers|work = [[Variety (magazine)|Variety]]|date = February 15, 2022|accessdate = February 15, 2022|last = Zorrilla|first = Mónica Marie}}</ref>\n| share =\n| commercial = $6.5–7&nbsp;million<ref>{{cite web|title=NBC Sells Out Super Bowl LVI, Hitting $7M for 30-Second Ads|url=https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|website=HollywoodReporter.com|last=Weprin|first= Alex|date=February 3, 2022|access-date=February 5, 2022|archive-date=February 5, 2022|archive-url=https://web.archive.org/web/20220205024403/https://www.hollywoodreporter.com/business/business-news/nbc-sells-out-super-bowl-7m-1235086449/|url-status=live}}</ref>\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Kurt Warner]] (analyst)<br>[[Laura Okmin]] and [[Mike Golic]] (sideline reporters)<br/>[[Gene Steratore]] (rules analyst)\n| last = LV\n| next = LVII\n}}'),
     Template('{{Infobox Super Bowl\n| type = sb\n| name = XLIX\n| image = SuperBowlXLIXLogo.png\n| image_size = x220px\n| visitor = [[2014 New England Patriots season|New England Patriots]]\n| visitor_abbr = NE\n| visitor_conf = [[American Football Conference|AFC]]\n| visitor_coach = [[Bill Belichick]]\n| visitor_record = 12–4\n| visitor_place = 1\n| home = [[2014 Seattle Seahawks season|Seattle Seahawks]]\n| home_abbr = SEA\n| home_conf = [[National Football Conference|NFC]]\n| home_coach = [[Pete Carroll]]\n| home_record = 12–4\n| home_place = 1\n| visitor_qtr1 = 0\n| visitor_qtr2 = 14\n| visitor_qtr3 = 0\n| visitor_qtr4 = 14\n| home_qtr1 = 0\n| home_qtr2 = 14\n| home_qtr3 = 10\n| home_qtr4 = 0\n| date = February 1, 2015\n| kickoff time = 6:30 PM EST\n| stadium = [[State Farm Stadium|University of Phoenix Stadium]]\n| city = [[Glendale, Arizona]]\n| attendance = 70,288<ref>{{cite web |title=Total attendance for Super Bowl XLIX at 70,288 |website=breakingnews.com |date=February 2, 2015 |url=http://www.breakingnews.com/item/2015/02/02/total-attendance-for-super-bowl-xlix-at-70288-of |access-date=February 3, 2015 }}</ref>\n| odds = Pick \'em (even/toss-up)<ref name="odds-espn-final">{{cite web |last=Purdum |first=David |title=Vegas books move lines to pick \'em |url=http://espn.go.com/chalk/story/_/id/12261273/late-seattle-seahawks-bets-move-betting-lines-pick-em |publisher=[[ESPN]] |date=January 31, 2015 |access-date=February 1, 2015 }}</ref>\n| MVP = [[Tom Brady]], [[quarterback]]\n| anthem = [[Idina Menzel]]<ref name="Idina Menzel">{{cite news |title=Idina Menzel to sing National Anthem at Super Bowl |url=http://www.nfl.com/superbowl/story/0ap3000000459514/article/idina-menzel-to-sing-national-anthem-at-super-bowl |publisher=National Football League |date=January 16, 2015 |access-date=January 17, 2015 }}</ref>\n| coin_toss = [[Tedy Bruschi]], [[Kenny Easley]]\n| referee = [[Bill Vinovich]]<ref name="Super Bowl XLIX officials">{{cite news |title=Super Bowl XLIX officials named; Vinovich to be head referee |url=http://www.nfl.com/news/story/0ap3000000461220/article/super-bowl-xlix-officials-named-vinovich-to-be-head-referee |publisher=[[National Football League]] |date=January 20, 2015 |access-date=January 20, 2015 }}</ref>\n| halftime = [[Katy Perry]]<ref name="Katy Perry to headline Pepsi Super Bowl XLIX Halftime Show" /> featuring [[Lenny Kravitz]],<ref name="Lenny Kravitz joins Katy Perry">{{cite news |title=Lenny Kravitz joins Katy Perry for Super Bowl Halftime Show |url=http://www.nfl.com/news/story/0ap3000000456026/article/lenny-kravitz-joins-katy-perry-for-super-bowl-halftime-show |publisher=National Football League |date=January 10, 2015 |access-date=January 10, 2015 }}</ref> [[Missy Elliott]]<ref name="rollingstone">{{cite web |last=Reed |first=Ryan |title=Missy Elliott and Katy Perry Will Team Up for Super Bowl Halftime Show |url=https://www.rollingstone.com/music/news/missy-elliott-will-join-katy-perry-super-bowl-halftime-performance-20150130 |work=[[Rolling Stone (magazine)|Rolling Stone]] |date=January 30, 2015 |access-date=January 31, 2015 }}</ref> and the [[Sun Devil Marching Band|Arizona State University Sun Devil Marching Band]]<ref name="eastvalleytribune">{{cite web |title=ASU marching band practices for Super Bowl pre-game and halftime shows |url=http://www.eastvalleytribune.com/local/article_138f8b60-aa3b-11e4-9c69-0fd4d96cae24.html |work=[[East Valley Tribune]] |date=February 1, 2015 |access-date=February 1, 2015 }}</ref>\n| network = [[NBC Sunday Night Football|NBC]]\n| announcers = [[Al Michaels]] (play-by-play)<br /> [[Cris Collinsworth]] (analyst)<br /> [[Michele Tafoya]] (sideline reporter)\n| rating = 47.5 (national)<ref name="national">{{cite web |title=Super Bowl 49 viewership sets US television record|url=https://sports.yahoo.com/news/super-bowl-49-viewership-sets-us-television-record-225749457--nfl.html/|website=Yahoo |date=February 2, 2015 |access-date=February 1, 2016 }}</ref><br /> 61.0 (Boston)<ref name="ratings">{{cite web |title=Boston Top Market For Super Bowl 49; Seattle Down |url=http://www.sportsmediawatch.com/2015/02/local-super-bowl-ratings-boston-second-highest-ever-seattle-down-patriots-seahawks-nbc/|website=Sports Media Watch |date=February 2, 2015 |access-date=February 3, 2015 }}</ref><br /> 55.6 (Phoenix)<ref name="ratings"/><br />52.1 (Seattle)<ref name="ratings"/><br />U.S. viewership: 114.4&nbsp;million est. avg.<ref>{{cite news |last=Pallotta |first=Frank |title=Super Bowl XLIX posts the largest audience in TV history |url=https://money.cnn.com/2015/02/02/media/super-bowl-ratings/index.html |publisher=[[CNNMoney]] |date=February 2, 2015 |access-date=February 3, 2015 }}</ref>\n| share = 72 (national)\n| commercial = $4.5&nbsp;million<ref name=MCastillo>{{cite web |last=Castillo |first=Michelle |title=NBC Has Sold 95% of Super Bowl Ads and Says $4.5 Million Per :30 \'Is a Steal\' |url=http://www.adweek.com/news/television/nbc-has-sold-95-super-bowl-ads-and-says-45-million-30-steal-162210 |website=[[Adweek]] |access-date=January 27, 2015 |date=January 7, 2015 }}</ref>\n| radio = [[NFL on Westwood One Sports|Westwood One]]\n| radioannouncers = [[Kevin Harlan]] (play-by-play)<br />[[Boomer Esiason]] (analyst)<br />[[James Lofton]] and [[Mark Malone]] (sideline reporters)\n| last = XLVIII\n| next = 50\n}}')]




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






  <div id="df-cb5767c1-de5a-44f6-ace2-03c084ba5081">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-cb5767c1-de5a-44f6-ace2-03c084ba5081')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-cb5767c1-de5a-44f6-ace2-03c084ba5081 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-cb5767c1-de5a-44f6-ace2-03c084ba5081');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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






  <div id="df-a246b266-ae75-4bd7-b8a0-68cc26cab339">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-a246b266-ae75-4bd7-b8a0-68cc26cab339')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-a246b266-ae75-4bd7-b8a0-68cc26cab339 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-a246b266-ae75-4bd7-b8a0-68cc26cab339');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-c6c076c0-e320-4e9b-9af1-5cdc2e076338">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-c6c076c0-e320-4e9b-9af1-5cdc2e076338')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-c6c076c0-e320-4e9b-9af1-5cdc2e076338 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-c6c076c0-e320-4e9b-9af1-5cdc2e076338');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-2bf38590-a560-4f5b-a774-77c0e9da9ed1">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-2bf38590-a560-4f5b-a774-77c0e9da9ed1')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-2bf38590-a560-4f5b-a774-77c0e9da9ed1 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-2bf38590-a560-4f5b-a774-77c0e9da9ed1');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-cc77f515-370f-46ba-ba08-3ba80b126921">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-cc77f515-370f-46ba-ba08-3ba80b126921')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-cc77f515-370f-46ba-ba08-3ba80b126921 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-cc77f515-370f-46ba-ba08-3ba80b126921');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-d7f97162-77e0-4514-9e13-783675560876">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-d7f97162-77e0-4514-9e13-783675560876')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-d7f97162-77e0-4514-9e13-783675560876 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-d7f97162-77e0-4514-9e13-783675560876');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-cad97e1f-303d-4f00-8f3a-15080bf39a62">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-cad97e1f-303d-4f00-8f3a-15080bf39a62')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-cad97e1f-303d-4f00-8f3a-15080bf39a62 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-cad97e1f-303d-4f00-8f3a-15080bf39a62');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-77ea9826-fd5e-4d19-8df7-9502ac175ed7">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-77ea9826-fd5e-4d19-8df7-9502ac175ed7')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-77ea9826-fd5e-4d19-8df7-9502ac175ed7 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-77ea9826-fd5e-4d19-8df7-9502ac175ed7');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>





```python
# perform a merge to create an even larger dataset
final_data = prev.merge(superbowl_history_df, how="inner", on="Game")
```


```python
# as we can see we have more data for each game
final_data.head()
```





  <div id="df-05ea807b-6a8e-4608-952f-d5bb00521676">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-05ea807b-6a8e-4608-952f-d5bb00521676')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-05ea807b-6a8e-4608-952f-d5bb00521676 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-05ea807b-6a8e-4608-952f-d5bb00521676');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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





  <div id="df-dc3f96c3-96d4-463f-bb6b-f37da2620b44">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-dc3f96c3-96d4-463f-bb6b-f37da2620b44')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-dc3f96c3-96d4-463f-bb6b-f37da2620b44 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-dc3f96c3-96d4-463f-bb6b-f37da2620b44');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
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




    0     NaN
    1     NaN
    2     NaN
    3     NaN
    4     NaN
    5     NaN
    6     NaN
    7     NaN
    8     NaN
    9     NaN
    10    NaN
    11    NaN
    12    NaN
    13    NaN
    14    NaN
    15    NaN
    16    NaN
    17    NaN
    18    NaN
    19    NaN
    20    NaN
    21    NaN
    22    NaN
    23    NaN
    24    NaN
    25    NaN
    26    NaN
    27    NaN
    28    NaN
    29    NaN
    30    NaN
    31    NaN
    32    NaN
    33    NaN
    34    NaN
    35    NaN
    36    NaN
    37    NaN
    38    NaN
    39    NaN
    40    NaN
    41    NaN
    42    NaN
    43    NaN
    44    NaN
    45    NaN
    46    NaN
    47    NaN
    48    NaN
    49    NaN
    50    6.0
    51    NaN
    52    NaN
    53    NaN
    54    NaN
    55    NaN
    Name: Visitor OT, dtype: float64




```python
# succesful data update
final_data.tail(10)
```





  <div id="df-d034f413-8d69-4756-8c2b-b36287b8c700">
    <div class="colab-df-container">
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
      <button class="colab-df-convert" onclick="convertToInteractive('df-d034f413-8d69-4756-8c2b-b36287b8c700')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-d034f413-8d69-4756-8c2b-b36287b8c700 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-d034f413-8d69-4756-8c2b-b36287b8c700');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>





```python
# now we can download the data - uncomment as necessary
#from google.colab import files
#final_data.to_csv("superbowl-final.csv", index=False)
#files.download("superbowl-final.csv")
```


    <IPython.core.display.Javascript object>



    <IPython.core.display.Javascript object>

