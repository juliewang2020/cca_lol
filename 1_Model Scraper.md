# Step 1: Scraping the Images
In order to pull off this ML project, I need images of each champion in a somewhat similar angle/position. However, I cannot use official splash art or icons as those are all different sizes, angles, resolution, and styles (artists). 

To gather data that I believe would be good for this project, I've decided to scrape 3D Models of the charaters from the website  teemo.gg. Here's what the model viewer looks like: 
 ![Image of Model Viewer](https://github.com/juliewang2020/cca_lol/blob/master/images/model_viewer.png)

The model viewer allows you to select the desired champion and skin, rotate and zoom in, and view animations. For the purpose of this project, I will be using the forward-facing pose, non-animated, as seen in the above image. Luckily, it is also the default position the viewer displays the model in.

The scraper should be able to screenshot each skin within the viewer and remove the background with the website logo. The end result should look something like this: 


![Image of Model-No Background](https://github.com/juliewang2020/cca_lol/blob/master/images/model_no_background.png)

The background here was removed using the remove background function on Microsoft Word. The AI there seems to able to find the outline of the champion easily, despite similar colors of purple in this model and the background. However, it does struggle with removing the background between the champion's body parts and weapons. Hopefully I can improve on this using Python.

The scraper screenshot only the model viewer area (to make removing the background easier). The process looks like this:
![Example](https://github.com/juliewang2020/cca_lol/blob/master/images/example_progress.PNG)

## Possible Difficulties
There's a lot of scary things I will need to figure out here.
* How to Name the Images
  * Ideally, the files should be descriptive of the champion and the specific skin. Will I need to scrape these from somewhere else and match it the website?
* Controlling Mouse Inputs
  * In order to increase the image quality, I would prefer to zoom in using the model viewer a certain amount before taking the screenshot. In the above example, the model takes up a little over 50% of the model viewer's height.
  * Will I need to change the inputs on the website form in order to scrape all the skins? And can I use these buttons to help scrape for the names of the skins?
  * Can I control the area of the screenshot? Or will I need to crop it later?
* How to remove the backgrounds ( I don't know anything here at all!) 
  * No clue here on how to remove patterned backgrounds. 

## Step 1.1 - How Do I Generate the URLs?
I need to figure out how to iterate through all the champion models on this website. Looking at basic webscraping tutorials, they all suggest trying to find a pattern in the URL that can be manipulated. So, I started fiddling around, trying to find a pattern in the url that indicates what skin is being pulled up. 

> https://teemo.gg/model-viewer?skinid=jinx-0&model-type=champions

> https://teemo.gg/model-viewer?skinid=jinx-1&model-type=champions

> https://teemo.gg/model-viewer?skinid=jhin-0&model-type=champions

See a pattern?

> skinid=jinx-0

> skinid=jinx-1

> skinid=jhin-0

It seems the "skinid" is modified by the champion name and the # of the skin, with 0 used to indicate the default skin.
While this pattern is easy to replicate, some difficulties come up. How does the website deal with champion names that contain punctuation or white space or multiple words (examples: Kai'Sa, Nunu & Willump, Miss Fortune)? And how can I know the # of skins that a champion has? 

**Here's where I made a mistake. I got caught up too fast in thinking I could do something with the inputs on the model-viewer to scrape all this information.** 

![Model Viewer Inputs](https://github.com/juliewang2020/cca_lol/blob/master/images/inputs.PNG)

I thought I could first go to each champions default skin, guaranteed to be 'champion_name-0', and then scrape the skin input for the number of skins and the name of the skins. This seemed like a solid plan, as I could easily land on the default skin, and look at the button choices for skins. I jumped in, scraping the names of the champions from the champion input. However, I did have to manually figure out how champions with problematic names were dealt with, and change that in my list of champions. 

**Instead, if I had just taken the time to examine the source code more clearly, I would have seen that the website developers coded in a dictionary with all skins and their corresponding skin ids!** And I could have not taken that wasted detour method above!
![Source Code](https://github.com/juliewang2020/cca_lol/blob/master/images/source_code.PNG)

Now, how to access this beautiful source code?

## Step 1.2 - How will I get a dictionary of URLs and the Skin Names?

By using the popular HTML scraper, 'BeautifulSoup', I can get this information in a parseable, cleaned form! First I scrape the ENTIRE source code as a BeautifulSoup Object. 

```python
import bs4  #(known as "BeautifulSoup

# Create beautiful soup object to examine the source code
res = requests.get('https://teemo.gg/model-viewer')
res.raise_for_status()
ModelSoup = bs4.BeautifulSoup(res.text)
```
Then, the tag used to identify this champion dictionary is <script>. I created a list of objects of all the script tags. From iterating through, using getText() to textify the objects, I see that the script object we want is the one at index 9. In addition, when I examined the text, I saw that the dictionary of skins for EACH champ could be split using ';'. 

```python
# Get List of Skins/Chromas and the corresponding URL
skins_html = ModelSoup.findAll('script')
skins_champs = skins_html[9].getText() # contains the dictionary of champions and skins
skins_champs = skins_champs.split(';')
skins_champs.pop(0) # remove the first index which does not contain champ/skin info
```
This is what the list looks like currently. It is a list of "dictionaries", with each index being one champ.
![List Example](https://github.com/juliewang2020/cca_lol/blob/master/images/champ_skindicts.JPG)
 
So for each index, I will be able to pull out a champion and its skins. Within that index, is a dictionary, with entries seperated in key, value pairs of URL/skinID, skin name. Because this is text and not an actual dictionary recognizeable by python, I will need to parse and create my own dictionary of URL/skinID and skin names. There were 2 ways I thought about doing this:
  1. Split the text by comma. The even numbers will contain the URLs/skinIDs and the odd numbers will contain the skin names. Then use a regex matcher to search for a start and end match, and grab the string in between
   Example Splits:
   > index 0 = {'id': 'aatrox-0', 
  
  > index 1 = 'name': "Default Aatrox"} 
   
   Example Regex Matcher
  > 
  
  >
  2.

### Challenges
*  issues with punctuation in names. Originally split by ",", but turns out there was a skin name with a comma



### Lessons Learned
* Examine Source Code more carefully!!!
