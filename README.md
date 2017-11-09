# ClinicalTrial
from bs4 import BeautifulSoup
import re
from selenium import webdriver
#from nltk.tokenize import sent_tokenize
#from nltk.tokenize import word_tokenize
import time


web = webdriver.Chrome()
#for i in range(1, 136046, 10):
file_object = open("D:\Web\clinicaltrial.txt", "w", encoding = 'utf-8')
for p in range(1, 136046, 10):
    url = "https://clinicaltrials.gov/ct2/results?recrs=e&draw=&rank=%d"%p
    web.get(url)
    time.sleep(4)
    html = web.page_source
    soup = BeautifulSoup(html.encode("utf-8"), "html.parser")
#web.find_element_by_id('theDataTable_next').click()
    article = re.findall(r'href="/ct2/show/(.*?)">', html)
    artlen = len(article)
    for i in range(artlen):
        nart = "%s"%(str(article[i]))
        link = "https://clinicaltrials.gov/ct2/show/%s"%nart
        web.get(link)
        html = web.page_source.encode('utf-8')
        soup = BeautifulSoup(html,'html.parser')
        title = soup.find_all("h1", class_ = "solo_record")[0].get_text().strip()
        try:    
            publication = re.split(r'Publications:[\n]', html.decode("utf-8"))[1]
        except (IndexError, TypeError): 
                print("error")
        else:
            #print(re.findall(r'href="/ct2/bye/(.*?)"', publication)
            length = len(re.findall(r'href="/ct2/bye/(.*?)"', publication))
            newpage = re.findall(r'href="/ct2/bye/(.*?)"', publication)
            for ii in range(length):
                pages = "%s"%(str(newpage[ii]))
                pubN = re.sub(r'"', '', pages)
                link = "https://clinicaltrials.gov/ct2/bye/%s"%pubN
                web.get(link)
                #time.sleep(4)
                html = web.page_source.encode('utf-8')
                soup = BeautifulSoup(html,'html.parser')
                try:
                    abstract = soup.find_all("div", class_ = "abstr")[0].get_text().strip()
                except(IndexError):
                    print("error")
                else:
                    ptitle = soup.find_all("h1")[1].get_text().strip()
                    information = "%s\n %s\n %s\n"%(str(title), str(ptitle), str(abstract))
                    file_object.write(information)
file_object.close()

#url = 'https://clinicaltrials.gov/ct2/show/NCT03237598?recrs=e&draw=2&rank=861'







