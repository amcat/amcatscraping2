AmCAT-Scraping
==============

A seperate repository for scraping to AmCAT.

To install:
```
export INSTALLDIR=$HOME #edit this line if you want to install in a different directory

git clone https://github.com/amcat/amcat-scraping.git $INSTALLDIR/amcatscraping
git clone https://github.com/vanatteveldt/amcatclient.git $INSTALLDIR/amcatclient

# Install dependencies
sudo pip install -r $INSTALLDIR/amcatscraping/requirements.txt

# To run scripts in amcatscraping, PYTHONPATH needs to be set to the directory it's in:
echo >> ~/.bashrc
echo 'export PYTHONPATH=$PYTHONPATH':$INSTALLDIR >> ~/.bashrc

# To run scrapers at their scheduled time, we use a script that should run every minute. Add it to cron:
(crontab -l ; echo "* * * * * python "$INSTALLDIR/amcatscraping/maintenance/timed_actions.py)| crontab -
```

Different types of scrapers

We have 2 different types of scrapers: periodic and daterange. The former is a scraper without date options. It takes any article that is currently available on the website. This is useful for media that don't come with an archive, such as RSS feeds. The latter takes min datetime and max datetime arguments, and is supposed to scrape only those articles that fall within this range. This is useful for media that do keep an archive, so we can scrape articles from any given date at any time.

Registering a scraper

```
$ python maintenance/db.py add
usage: db.py add [-h] [--cron CRON] [--username USERNAME]
                 [--password PASSWORD] [--label LABEL]
                 active articleset project {periodic,daterange} classpath
```

Example for a daterange scraper:
```
$ python maintenance/db.py add t 100 10 daterange amcatscraping.scrapers.newspapers.volkskrant.VolksKrantScraper --username user1 --password pass1
```
Daterange scrapers do not need a cron argument, for they are updated all together at 2 AM every night.

Example for a periodic scraper:
```
$ python maintenance/db.py add t 100 10 periodic amcatscraping.scrapers.tv.teletekst.TeletekstScraper --cron "30 * * * *"
```
