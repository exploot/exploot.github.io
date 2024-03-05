---
title: Tip Calculator
date: 2023-01-18
categories: [Projects, Python]
tags: [Python]
toc: false
math: true
comments: false
mermaid: true

<author_id>:
    name: Iapetus J. D.
---
# Simple code to calculate tips! - 

Embarking on the journey of coding this tip calculator project was an enlightening experience filled with both challenges and triumphs. As a novice coder, every line of code presented an opportunity to learn and grow. Initially, the task seemed straightforward: create a program that calculates the total bill including a user-defined tip percentage. Armed with basic Python knowledge and a dash of enthusiasm, I dove headfirst into the project.

The first hurdle arose when implementing the verification function. The goal was to prompt the user to confirm their order total and give them the option to recalculate if needed. However, figuring out how to loop the verification process proved to be more challenging than anticipated. After several attempts, I opted for a workaround, providing users with one last chance to recalculate before proceeding. Though not ideal, it served as a testament to my perseverance and problem-solving skills at the time.

Despite the initial setback, the rest of the project flowed relatively smoothly. Implementing the tip calculation logic was straightforward, thanks to Python's simplicity and readability. With each line of code, I gained a deeper understanding of programming concepts and principles.

Finally, as I ran the program and witnessed it in action, a sense of accomplishment washed over me. Though simple in nature, the tip calculator represented a milestone in my coding journey. It was a tangible reminder of how far I had come since my first foray into programming.

Looking back on coding this tip calculator project, it's clear it was about more than just typing out code. It was about sticking with it, facing challenges head-on, and staying curious to learn more. Sure, the program isn't flawless, but it's a reflection of the steps I've taken and the road ahead in my coding journey.

```
def tip_calc():
    price = int(input('Enter total price:'))
    tip = int(input('Enter desired tip %:'))


    total = (price * (tip / 100)) + price
    print("Your total is $", total)

def verify():
    verify = input ('''
    Are you sure you want to spend this amount?
    Please type Y for YES or N to recalculate total order.
    ''')

    while True:
        if verify == 'N':
            print("You only have one more chance to figure it out.")
#At this point in time, the issue I was having was that I couldn't figure out how to make the verify function repeat itself until it gave the output Y, 
#so I just made it give you one more chance before it shut itself down lol. I could fix it now, but this is a momument to me being a novice coder.
            tip_calc()
            break
        
        elif verify == 'Y':
            break         
        
tip_calc()
verify()
```

