### This script should monitor Pastebin for a series of key words; if the keywords come up, email me ###

import pprint
import time
import smtplib
import requests
import csv
import json
import urllib2

from email.MIMEMultipart import MIMEMultipart
from email.MIMEText import MIMEText

print "[*] Booting up Pastebin_Monitor"

# File for retrieving list of search terms
with open('PUT A CSV OF YOUR SEARCH TERMS HERE', 'rb') as f:
    search_terms_reader = csv.reader(f)
    search_terms_list = list(search_terms_reader)

base_url = "http://pastebin.com/api_scraping.php"

# Empty lists to store requested Pastebin URLs and successful hits
paste_url_list = []
results_list   = []

#
# Code for the loop (download feed, parse, go through feed, search for keywords)
#
def the_loop():

    # Sleep so there is a delay between scrapes
    print "[*] Sleeping..."
    time.sleep(90)
    
    # Request the user page
    response = requests.get(base_url)

    if response.status_code == 200:
    
        # Print that it was successful
        print "[*] Connected to Pastebin"
    
        # Pull the JSON content
        result = json.loads(response.content)
    
        # Print how many results are in the JSON
        print "[*] Found %d entries" % len(result)
    
        # Request the paste pages
        for entry in result:
            paste_url      = entry["scrape_url"]
            full_paste_url = entry ["full_url"]
    
            # Check whether the URL has already been seen
            if paste_url not in paste_url_list:
                paste_url_list.append(paste_url)
        
                # If not, request the page    
                response = requests.get(paste_url)
                if response.status_code == 200:
                    print "[*] Connected to %s" % paste_url
        
                    # Pull the HTML content of the individual press release page
                    page = urllib2.urlopen(paste_url).read()
            
                    # Convert the search terms list into strings
                    for search_term in search_terms_list:
                        search_term_string = search_term[0].encode("utf-8")                    
            
                        # Check to see if the search terms are in there
                        if search_term_string in page:
                    
                            # If it's a new result, add it to the list
                            if paste_url not in results_list:
                                results_list.append(paste_url)
                        
                                print "[!] Found '%s' in %s" % (search_term_string,full_paste_url)
                        
                                # And then send out an email alert
                                from_address = "ADD YOUR FROM ADDRESS"
                                to_address   = "ADD YOUR TO ADDRESS"
                                password     = "ADD YOUR FROM ADDRESS PASSWORD"
                                
                                subject = 'Pastebin_Monitor: "%s" in %s' % (search_term_string,full_paste_url)
                                
                                msg = MIMEMultipart()
                                msg['Subject'] = subject
                                msg['From']    = from_address
                                msg['To']      = to_address
                                
                                # Put in the message 
                                body = "%s" % (full_paste_url)
                                msg.attach(MIMEText(body, 'plain'))
                                
                                server = smtplib.SMTP('smtp.gmail.com', 587)
                                server.starttls()    
                                server.login(from_address, password)
                                text = msg.as_string()
                                server.sendmail(from_address,to_address, msg.as_string())
                                server.quit()        
                                
                                print "[!] Sent email notification"

	      print "[*] Scanned all 50 results"
                        
# Start the loop
while True:
    the_loop()        
