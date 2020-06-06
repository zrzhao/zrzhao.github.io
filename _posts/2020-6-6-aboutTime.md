---
layout: post
title: It's all about time.
---

In the [web scraping project](https://zrzhao.github.io/webscraping/), the codes seem to work well with several test pages, but I soon noticed some issues when I tried to scale it up with all projects, which was about 10,000 at that time. 

One issue is the lack of robustness. Sometimes I got lucky and the codes ran smoothly with hundreds of pages. Sometimes they just crashed. Another issue turned up when I examined the results that have been collected. Some results are duplicates of others. Some are missing of critical information. 

The problem is with time. The database was written with dynamic HTML codes. Most elements were not directly written into a static html body of a project page. They were built as requests to get information from other scrips before the information can be shown. Hence the elements within a page may load at different time intervals. 

To make sure that I get the information I want from all project pages, I have to allow sufficient time for each webpage to load. 

## Implicit Wait

An implicit wait tells WebDriver to poll Document Object Model (`DOM`) for a certain amount of time when trying to find any element (or elements) not immediately available. The default setting is 0. 

I set an implicit wait of 10 seconds right before I use `driver.get()` to load the database portal. Once there, the implicit wait is set for the life of the WebDriver object.

```python
driver = webdriver.Chrome(ChromeDriverManager().install()) 
driver.implicitly_wait(10) # Implicit wait
driver.get('https://www.cpppc.org:8082/inforpublic/homepage.html#/searchresult')
```

## Explicit Wait

Selenium also has a way to set explicit waits, which were defined to wait for a certain condition to occur before proceeding further in the code. The extreme case of this is `time.sleep()`, which sets the condition to an exact time period to wait.

There are some convenience methods to have the following codes wait only as long as required. One way is to use `WebDriverWait` in combination with `ExpectedCondition` through [a `try-except' statement](https://selenium-python.readthedocs.io/waits.html). 

I use the easier one, `time.sleep()`, and set it to wait for four seconds after the database portal is advanced to a new page, and eight seconds for each individual project to load, before I capture its url and other information.   

```python
elem = 'ant-pagination-next'
linkElem = driver.find_elements_by_class_name(elem)
linkElem[0].click() # Open the next page  
time.sleep(4) # Allow time for webpage to load
```

```python
linkElem[i].click()
driver.switch_to.window(driver.window_handles[-1])
time.sleep(8) # Allow time for webpage to load
urls.append(driver.current_url)
...
```

## Running time

Adding implicit and explicit waits, it takes a long time to run the whole script. But the results are much cleaner. It is a trade-off before I may figure out other solutions. 

To keep track of the running time, I use the [time](https://docs.python.org/3/library/time.html) module.  

```python
import time
start_time = time.time()
... run the actual codes here ...
print('-- %s seconds --' % round(time.time() - start_time, 2))
```


