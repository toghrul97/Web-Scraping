# Web-Scraping

## Some useful Web Scraping tools

### Download images from google.com

```

def get_photos(n, inp, folder, parent_dir):
  
    """
    n: amount of images You want to extract. Recommended limit is 400.
    inp: Your request
    folder: name of folder You want to store images
    parent_dir: directory where You want to store the folder
    
    May not work for some requests.
    Personally, use this tool to get data for image classification problems.
    """
    #import packages
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.common.keys import Keys
    import urllib
    import urllib.request
    import time
    import os
    
    #  Create a path to store images
    path = os.path.join(parent_dir, folder)
    os.mkdir(path)
    
    # Open webdriver and open google.com
    driver = webdriver.Chrome()  
    driver.get('https://google.com')
    
    box = driver.find_element(By.XPATH, '/html/body/div[1]/div[3]/form/div[1]/div[1]/div[1]/div/div[2]/input')  #XPATH of google.com's search bar
    box.send_keys(inp)  # Placing the input
    box.send_keys(Keys.ENTER)   # Entering it
    driver.find_element(By.XPATH, '//*[@id="hdtb-msb"]/div[1]/div/div[2]/a').click()  # Finding images bar and clicking on it
    i = 1
    j = 3274  #Google.com's page height. Further it will scroll down by this amount, so he can see new images for the request.
    
    while i<n+n//25 + 1:  # selenium can't see 25th elements as photos, so I tell it to pass it. For the images to have ordered names I use this line.
        try:
            src = driver.find_element(By.XPATH, f'//*[@id="islrg"]/div[1]/div[{i}]/a[1]/div[1]/img').get_attribute('src')
            urllib.request.urlretrieve(src, fr"{path}\{inp.replace(' ', '_')}_{i-i//25}.png")
            if (i+1)%25!=0:
                i += 1
            else:   # If it can't see the image it is because it needs to scroll further down and he reached 25th element in XPATH (not an image).
                try:
                    driver.execute_script(f'window.scrollTo(0, {j})') # It first scroll down the page
                    time.sleep(3) # Waits for the page to load
                    j += 1800 # Assign new height to scroll down in next attempt
                    i += 2  # Passes 25th element in the XPATH
                except: # You can scroll down up to 414th image, so these lines are for clicking see more images. But, again it is not recommended to request more than 400 images.
                    driver.find_element(By.XPATH, '//*[@id="islmp"]/div/div/div/div/div[1]/div[2]/div[2]/input').click()
                    time.sleep(3)
                    driver.execute_script(f'window.scrollTo(0, {j})')
                    time.sleep(3)
                    j += 1800
                    i += 2
        except TypeError: # Sometimes there may be some errors in getting specific images. These lines are for passing them
            if (i+1)%25!=0:
                i += 1
            else:
                i += 2
```
