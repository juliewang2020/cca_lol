#Step 2 - Screenshotting the Webpages

Remember those champion-skin specific URL snippets we scraped earlier? Those are a key part of this next section: capturing each skin's model. 
For this portion of the project, I used the selenium package with the chromedriver. Selenium opens up a "new Chrome Window" that it controls for its own personal use (our code). 

##Step 2.1 Taking the Screeshots
That pattern that we noticed in the URL before comes up handy now, as we can quickly loop through the champion+skin snippets to generate the corresponding link
 > full_url = 'https://teemo.gg/model-viewer?skinid=' + url + '&model-type=champions'
 
But, it's not as simple as going to the URL. We need to scroll and capture the correct portion of the webpage. I tried several things here:
* Screenshotting by element id --> canvas returned blacked out
* Screenshotting, recording where element is,then cropping --> y is offset by scrolling
* Model could not be zoomed into, element needed a physical scroll bar

So because I could not accomplish screenshotting the element specifically, I went a roundabout way of doing it where I focused on screenshotting the whole visible window. 
After navigating to the page, I had the window scroll all the way down, and then take a screenshot, as the model viewer was located at the bottom of the page. 
Since the model viewer also took time to load, I had it wait 30 seconds for the model to fully load.

In order to keep my code cleaner, I wrote a function to manage the page manipulation and screen capturing.
```python
def screen_capture(url, name):
    driver = webdriver.Chrome(ChromeDriverManager().install())
    full_url = 'https://teemo.gg/model-viewer?skinid=' + url + '&model-type=champions'
    try:
        driver.get(full_url)
        driver.execute_script('window.scrollTo(0, document.body.scrollHeight);') # get the whole model viewer in view
        time.sleep(30) # wait in case the model needs a little bit of time to load
        screenshot = driver.save_screenshot(url + '-' + name + '.png')
        driver.quit()
    except:
        print('Could not get: ' + url + ' ' + name )
```
Grabbing every single scrrenshot took about 21.3 hours!
But now you're wondering, how did I use the webpage captue, when I only needed the model viewer section? I cropped it down to the model viewer!

# Step 2.2 Cropping the Screen Capture
I simply used the image package from PIL. And while there are better ways to indicate where you want to crop an image, I just experimented till I got it just about right on the model viewer box.

```python
# Crop all the images to just the model viewer
from PIL import Image 

directory = cwd
for filename in os.listdir(directory):
    path = os.path.join(directory, filename)

    im = Image.open(path) 

    # Setting the points for cropped image 
    left = 200
    top = 200
    right = 1260
    bottom = 728

    # Cropped image
    im1 = im.crop((left, top, right, bottom)) 

    # Save the cropped image
    im1.save(filename) 
```




