### Step 1: Scraping the Images
In order to pull off this ML project, I need images of each champion in a somewhat similar angle/position. However, I cannot use official splash art or icons as those are all different sizes, angles, resolution, and styles (artists). 

To gather data that I believe would be good for this project, I've decided to scrape 3D Models of the charaters from the website  www.teemo.gg/. Here's what the model viewer looks like: 
 ![Image of Model Viewer](https://github.com/juliewang2020/cca_lol/blob/master/images/model_viewer.png)

The model viewer allows you to select the desired champion and skin, rotate and zoom in, and view animations. For the purpose of this project, I will be using the forward-facing pose, non-animated, as seen in the above image. 

The scraper should be able to screenshot each skin within the viewer and remove the background with the website logo. The end result should look something like this: 
![Image of Model-No Background](https://github.com/juliewang2020/cca_lol/blob/master/images/model_no_background.png)

The background here was removed using the remove background function on Microsoft Word. The AI there seems to able to find the outline of the champion easily, despite similar colors of purple in this model and the background. However, it does struggle with removing the background between the champion's body parts and weapons. Hopefully I can improve on this using Python.

The scraper should be able to work like this:
![Example](https://github.com/juliewang2020/cca_lol/blob/master/images/example_progress.PNG)

### Reasoning
* url can be easily modified by champ name + skin number
* need to somehow come up with a combination of this to generate each url
* this will allow me to vist every single model page, and title the screenshots appropriately

### Challenges
* need to examine the html better (almost scrapped champ names and made it harder for myself), there was a js script that the website already had compiled with the matching url and name of skin
  * luckily in the script, there is a dictionary variable with every single skin and it's url identifier
*  issues with punctuation in names. Originally split by ",", but turns out there was a skin name with a comma
