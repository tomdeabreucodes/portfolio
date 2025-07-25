+++
title = 'General Purpose DIY Stock Checker'
date = 2023-10-25
tags = ['Python', 'Bash', 'Linux', 'Automation']
draft = false
+++

## The Problem
Most good e-commerce websites will have a built-in feature to remind you when a product is back in stock. This is the ideal scenario when there's a popular product you want to get next time it becomes available. Just plug in your email or mobile number, and get a ping when the stock is replenished. While this is the standard, it isn't always an option, which is where I found myself when trying to get my hands on a Kensington trackball last year. 

I was frustrated, as there was no clear information about when this product would be coming to my region. With this type of product I didn't expect a huge release or a large amount of stock, so I wanted to try and get one as soon as it was available. Conflicting with this goal was the fact that I did not want to sink my time into checking and remembering to look at their website, especially because no estimated timeline was provided.

Naturally, I wondered if there was a straightforward solution to this with code.

## The Solution
To address this problem, I decided to create a stock checker which would regularly check the website for me in the background and automatically email me once the page had gone live.
{{< github repo="tomdeabreucodes/kensington-stock-checker" >}}

### Python Script
To achieve this, it mainly relied on two pillars. The most important of which is **Selenium**, a browser automation module available in Python and several other languages. Via Selenium I would automate the process of accessing the product page and checking for the presence of a "Where to buy" button. From researching other product pages, this seemed to be the consistent signal of a live product which was in-stock.

*Import the necessary modules*
```python
from selenium import webdriver
from selenium.webdriver.firefox.options import Options
import os
from dotenv import load_dotenv
import smtplib
import ssl
import logging
```

*Load in your `.env` file, this should contain your `SMTP_EMAIL` and `SMTP_PASSWORD`*
```python
load_dotenv()
```
*Setup logging*
```python
FORMAT = '%(levelname)s:%(asctime)s - %(message)s'
logging.basicConfig(filename='/home/artfvl/Documents/code/kensington-stock-checker/process.log',
                    encoding='utf-8', level=logging.INFO, format=FORMAT)
```
*Now we can run the automation of loading the product page*
```python
product_name = 'Kensington Slimblade Pro'
product_url = 'https://www.kensington.com/en-gb/p/products/control/trackballs/slimblade-pro-trackball-1/'  # Target product
# product_url = 'https://www.kensington.com/en-gb/p/products/control/trackballs/slimblade-trackball/' # Known in-stock product for testing

options = Options()
options.add_argument("-headless")
driver = webdriver.Firefox(options=options, service_log_path=os.devnull)
driver.get(product_url)
```

The `-headless` argument, enables Selenium to perform this in the background, so when the script is automated you will not be interrupted by random browser windows popping up.

As long as the product is out of stock, only this step will run, so you can set it and forget it.

Only once the "Where to buy" button is detected, the programme will continue to notify you by email.

```python
port = 587
# SMTP setup (option 2) https://support.google.com/a/answer/176600?hl=en
smtp_server = 'smtp.gmail.com'
smtp_email = os.getenv('SMTP_EMAIL')
# Create app password https://support.google.com/accounts/answer/185833?hl=en#zippy=%2Cwhy-you-may-need-an-app-password
smtp_password = os.getenv('SMTP_PASSWORD')

# For different retailers, the wording can be adjusted to match their "in stock" identifier
if "Where to Buy" in driver.page_source:
    try:
        context = ssl.create_default_context()
        server = smtplib.SMTP(smtp_server, port)
        server.starttls(context=context)
        server.login(smtp_email, smtp_password)
        message = 'Subject: {} in Stock!\n\n{}'.format(
            product_name, product_url)

        server.sendmail(
            smtp_email,
            smtp_email,
            message
        )
        logging.info("Product is available.")
    except Exception:
        logging.exception("Item in stock, email failed.")
else:
    logging.info("Item not in stock, no email sent.")
driver.close()
```
### Shell Script and CRON Job
We now need a Bash script which executes the Python script.
```bash
#!/bin/bash
# Adjust for your own paths (must be absolute)
source /home/artfvl/Documents/code/kensington-stock-checker/venv/bin/activate
python3 /home/artfvl/Documents/code/kensington-stock-checker/main.py
```
The Bash script can now be scheduled via a [CRON job](https://crontab.guru/). Craft your preferred schedule using the link, the example below runs once per hour.

```bash
0 * * * * bash /home/yourusername/Documents/code/kensington-stock-checker/main.sh
```

### Logging
Logging is included, so if some time has passed and you want to double check everything is still running correctly, you can consult `process.log` which should confirm it is either running as expected or needs some attention.

## Conclusion
I hope I don't need to use this script much going forward as the convenience of a built-in stock reminder is unmatched, but if you find yourself not wanting to keep manually checking on a product with inadequate release information - perhaps this script will serve as a reasonable compromise. Each website may have a slightly different indicator of whether it is in stock, so I would recommend checking against a couple of similar products which are in stock to help identify a reliable signal.

Thanks for reading!