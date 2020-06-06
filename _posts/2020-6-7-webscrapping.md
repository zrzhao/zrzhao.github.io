---
layout: post
title: test
---
In recent weeks, just finished my first `Python` webscrapping project. It was to capture information about thousands of projects from an online governmental database. In a series of posts, I am writing down some key steps in developing the codes, in particular, some obstacles I encountered, and the solutions or get-arounds. Notes and references for myself, and, a [PoW](https://en.wikipedia.org/wiki/Proof_of_work).

# Open the webpage 

It is tedious to install the webdriver mannually. You need to download the right version of webdriver for your browser, move the file to a global path on your system, or alternativley, set up the `PATH` to be executable in terminal through `sudo nano`. Lots of tutorial on line, like [this](https://www.swtestacademy.com/install-chrome-driver-on-mac/). 

An easier way to do it is through `webdriver_manager` by Sergey Pirogov. The package downloads binaries/executables in an automated way, and helps you to manage driver related settings automatically for python. Nice examples are provided [here](https://github.com/SergeyPirogov/webdriver_manager/blob/master/README.md).

I used the codes below to open up the database webpage: 

```python
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(ChromeDriverManager().install()) 
driver.get('https://www.cpppc.org:8082/inforpublic/homepage.html#/searchresult')
```

Never mind the Chinese language in this webpage. Basically, it is a database portal that links to about 10,000 governmental development projects. This webpage has multiple selections to different categories of projects, links to the first five projects, and some buttons at the bottom to advance to other pages for additional projects. 

A great context for me to practice simulating mouse click with `Selenium`. 

# Click to a project page

Select the first project name, right click to `inspect`, and observe the html codes. The five project links in this webpage were defined with a class name `projectName`. With `driver.find_elements_by_class_name()`, I got a list of length five. 

I simulate a click with the first project link, switch to the new tab that is just opened, and check its url address.   
```
just test 
elem = 'projectName'
test test test
```
```python
elem = 'projectName'
linkElem = driver.find_elements_by_class_name(elem)

linkElem[0].click()
driver.switch_to.window(driver.window_handles[-1])
print(driver.current_url)
```

Before checking with other projects, I need to close the current tab and switch back to the previous webpage, the database portal. 

```python
driver.close() 
driver.switch_to.window(driver.window_handles[0])
```
# Get url links for multiple projects

Now that it is working, I wrap around these steps and define a functoin `Multi_pj()` to get url links for all projects shown in this page. 

```python
# Define a function
def Multi_pj():
    '''
    Get url for all projets listed in a page
    '''
    elem = 'projectName'
    linkElem = driver.find_elements_by_class_name(elem)
    
    for i in range(len(linkElem)):    
        linkElem[i].click()
        driver.switch_to.window(driver.window_handles[-1])
        urls.append(driver.current_url)
        driver.close() 
        driver.switch_to.window(driver.window_handles[0])
```

# Advance to additional pages

There are multiple web elements for advancing to (or retreating from) other pages to see additional projects. To adance a page, use the element with a class name `ant-pagination-next`. To move back a page, use `ant-pagination-prev`. 

The source file also has some faster ways to move forward or back -- although I don't see any button for those clicks. To jump forward five pages, use `ant-pagination-jump-next`; to jump back five pages, use `ant-pagination-jump-prev`.    

The database portal shows 1900 pages at the moment. I define a function for fast forwarding to any specific page. The floor division `//` and modulus `%` come really handy for doing this. (More about Python operators, see [here](https://www.w3schools.com/python/python_operators.asp).)

```python
def get_to_page(p):
    '''
    Get to specific page.
    '''
    for i in range((p-1)//5):
        elem = 'ant-pagination-jump-next'
        linkElem = driver.find_elements_by_class_name(elem)
        linkElem[0].click()   
    for i in range((p-1)%5):
        elem = 'ant-pagination-next'
        linkElem = driver.find_elements_by_class_name(elem)
        linkElem[0].click()  
```
And you can easily get back from any page to the first page of database portal:

```python
def return_from_page(p):
    '''
    Return from specific page to page 1.
    '''
    for i in range((p-1)//5):
        elem = 'ant-pagination-jump-prev'
        linkElem = driver.find_elements_by_class_name(elem)
        linkElem[0].click()  
    for i in range((p-1)%5):
        elem = 'ant-pagination-prev'
        linkElem = driver.find_elements_by_class_name(elem)
        linkElem[0].click()  
```
# Get project urls from multiple pages

Now I can combine all the pieces and define a function to get project information -- for example, their url links -- from any specific page (`p_start`) in the database portal to another specific page (`p_end`).  

```python
# Define a function
def Multi_page(p_start, p_end):
    '''
    Starting from page 'p_start',
    and stop at 'p_end'.
    '''
    # get to page p_start
    get_to_page(p_start)
    
    # Operate on (p_end - p_start) pages
    for i in range(p_end-p_start):
        Multi_pj()
        print('Page ' + str(i+p_start) + ' is done.') 
        elem = 'ant-pagination-next'
        linkElem = driver.find_elements_by_class_name(elem)
        linkElem[0].click()   
        
    # Get back to the first page
    return_from_page(p_end)
        
# Test run the function, from page 3 to 5. 
urls = []
Multi_page(3,6)
len(urls)
```


