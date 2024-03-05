---
title: TinyURL Shortener with Python
date: 2023-02-20
categories: [Projects]
tags: [Python]
toc: false
math: true
comments: true
mermaid: true

<author_id>:
    name: exploot
---
# URL Shortener - 

Creating this URL shortener was a smooth ride, thanks to Python's simplicity and the pyshorteners library. With just a few lines of code, I tapped into the TinyURL API, transforming long URLs into compact links effortlessly. It's a practical tool, showcasing Python's knack for simplifying everyday coding tasks.

```
import pyshorteners

long_url = input("Enter the URL you wish to shorten: ") #The input for the long URL

type_tiny = pyshorteners.Shortener() #this class object from the imported 'pyshorteners' library is now initialized

short_url = type_tiny.tinyurl.short(long_url) #TinyURL is the default API so this converts the long URL to a TinyURL link 

print("Your new URL is: "+ short_url) #should be obvious what this does :)

#It's as simple as that!
```

