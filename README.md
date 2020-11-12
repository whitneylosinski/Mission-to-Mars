# Mission-to-Mars

## Purpose
The purpose of this project was to create a Flask web application that scrapes several different websites and returns the latest news, images and articles relating to Mars in a new HTML page.  The specific desired outputs for the new page listed below.

1. Latest News 
2. Latest Featured Image
3. Latest Mars Facts
4. Latest Mars Hemisphere Featured Images

## Resources
***Software:***
- Python 3.8
- Jupyter Notebook (anaconda3)
- VSCode 1.50
- Flask 1.1 
- Chromedriver 86.0.4240.22
- MongoDB 4.4 
- Pandas
- Splinter
- Beautiful Soup
- Bootstrap

***Websites:***
- https://mars.nasa.gov/news/</br>
- https://www.jpl.nasa.gov/spaceimages/?search=&category=Mars</br>
- https://space-facts.com/mars/</br>
- https://astrogeology.usgs.gov/search/results?q=hemisphere+enhanced&k1=target&v1=Mars</br>

## Summary
This project was completed in five major steps as outlined below.  

1. ***Scraping the Data***: The first step was to write a script using Splinter and BeautifulSoup to visit various websites, convert the html script to a soup object, parse the soup html and then collect the desired data.  The script was originally written in Jupyter Notebook to help with debugging and then exported to python and refactored.  The data collected was as follows.
   
    1. The title and short snippet of the latest news article was scraped from http://mars.nasa.gov/news/
    2. The latest featured image was scraped from https://www.jpl.nasa.gov/spaceimages/?search=&category=Mars
    3. The latest Mars facts table was scraped from https://space-facts.com/mars/
    4. The latest Mars weather data was scraped from https://mars.nasa.gov/insight/weather/</br> 
       <span style="font-size:8px;">*(This script was written in the jupyter notebook file but later omitted from the python file since the decision was made not to include the information in the web app.)*</span>
    5. The latest Mars hemispheres featured images were scraped from https://astrogeology.usgs.gov/search/results?q=hemisphere+enhanced&k1=target&v1=Mars

2. ***Storing the Data***:  The second step of the project was to build a place to store the results from the scrape where they can be easily accessed and retreived as needed.  This was done using a MongoDB non-relational database which can store both tabular and non-tabular data.  After getting started with Mongo by starting a new Mongod instance, and opening a Mongo workstation, a new MongoDB database was created called mars_app.

3. ***Create the Web App***:  The third step was to build the framework for the web app using Flask and Mongo together.  After importing the dependencies and setting up a flask page, flask_PyMongo was then used to establish the flask and Mongo database connection.  Next, two routes were developed for the app; one as a main page which displays the information in the Mongo database formatted with HTML and Bootstrap, and one to actually scrape the data using the scraping script from step 1 and update the Mongo database with the new infomration collected.  See the flask app script below.

   ```py
   # Import dependenciesfrom flask import Flask, render_template
   from flask import Flask, render_template
   from flask_pymongo import PyMongo
   import scraping

   # Set up a Flask
   app = Flask(__name__)

   # Use flask_pymongo to set up mongo connection
   app.config["MONGO_URI"] = "mongodb://localhost:27017/mars_app"
   mongo = PyMongo(app)

   @app.route("/")
   def index():
      mars = mongo.db.mars.find_one()
      return render_template("index.html", mars=mars)

   @app.route("/scrape")
   def scrape():
      mars = mongo.db.mars
      mars_data = scraping.scrape_all()
      mars.update({}, mars_data, upsert=True)
      return "Scraping Successful!"

   if __name__ == "__main__":
      app.run()
   ```

4. ***Customize the Appearance***: The fourth of the project was to customize the web application and make it easy to read and compatible with any size device.  This was done by writing the index.html file that contains the html and Bootstrap formatting mentioned in step 3.  For this particular project, the BootStrap template 3.3.7 was used and customized to create a professional, fun, and easy to read appearance.

5.***Launch***: The final step of the project was to actually launch the web app by running the code.  With the Mongo connection still running using the mars_app database, the flask application was then run in the terminal by typing `$ python app.py` and the web app was opened by navigating to localhost:500 in the web browser.  This displayed the web app with the customized appearance but since it was the first time it was run it doesn't have any data stored.  To retrieve the data, the "Scrape New Data" button was pushed, the data was scaped and a message appeared saying "Scraping Successful!"  After returning back to the localhost:500 page, the web app was then seen with all the updated data collected from scraping.  See screenshot below.
   
![screenshot](screenshot.png)
