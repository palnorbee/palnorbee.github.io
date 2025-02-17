---
title: Data scraping with Python
layout: page
---
<h1>Data scraping with Python(BeautifulSoup library)</h1>
<h2 style="text-align:center;">Hungarian site</h2>
<h3>For analysis, firstly I needed some date, which turned out to be the price of tobacco both in Hungary and Belgium.I kept looking until i found some 
table-like information with names and prices.</h3>
<br><br>
<h4>First attempt</h4>
<details>
    <summary>Filtering matching string</summary>
    <pre>
    <samp>
import re
from bs4 import BeautifulSoup
import requests
        <br><br>
html_text = requests.get('https://szamoldki.hu/hu/hirek/cigaretta-arak-2023-mennyibe-kerul-egy-doboz-cigi-2023-ban').text
soup = BeautifulSoup(html_text,"lxml")
string_match = soup.find_all(string = re.compile("Ft"))
for x in string_match :
    print((x.text).rsplit(" ",2))
    </samp>
    </pre>
</details>
<h4>This would have been all fine, but i noticed that some lines don't have the currency next to them,so my list was a bit slimmer.</h4>
<h4>Final attempt</h4>
<details>
    <summary>Filtering for lenght</summary>
    <pre>
    <samp>
import re
from bs4 import BeautifulSoup
import requests
        <br><br>
html_text = requests.get('https://szamoldki.hu/hu/hirek/cigaretta-arak-2023-mennyibe-kerul-egy-doboz-cigi-2023-ban').text
soup = BeautifulSoup(html_text,"lxml")
def length_test(tag):
    return tag.name == 'p' and len(tag.text) < 40
length_modif = soup.find_all(length_test)
for x in length_modif:
    print((x.text).rsplit(" ",2))
    </samp>
    </pre>
</details>
<h4>Now all i had to do was to save it in a txt file.I prefer using the txt and later convert it into csv rather than using the csv module of python.</h4>
<details>
    <summary>Creating the text file</summary>
    <pre>
    <samp>
f = open("tobaccoprices_2023_hu.txt", "w")
for x in length_modif:
    f.writelines(str(x.rsplit(" ",2))+"\n")
    </samp>
    </pre>
</details>
<h2 style="text-align:center;">Belgian site</h2>
<h4>First attempt</h4>
<details>
    <summary>Filtering for tags</summary>
    <pre>
    <samp>
import re
from bs4 import BeautifulSoup
import requests
        <br><br>
html_text = requests.get('https://www.tabakshoekje.be/index.php?p=product&subcat=sigaretten').text
soup = BeautifulSoup(html_text,'lxml')
names = soup.find_all('tr')
    </samp>
    </pre>
</details>
<details>
    <summary>Creating the text file</summary>
    <pre>
    <samp>
f= open("tobaccoprices_2023_be.txt","w")
for x in names:
    f.writelines(str(x.text.rsplit(maxsplit=3)).strip()+"\n")
    </samp>
    </pre>
</details>
