---
title: Prime Number Generator
date: 2023-07-07
categories: [Projects]
tags: [Python, Math]
toc: false
math: true
comments: true
mermaid: true
image: https://i.ibb.co/x8zKNWR/2024-03-0516-50-36-ezgif-com-video-to-gif-converter.gif

<author_id>:
    name: exploot
---
# Generate a list of all the prime numbers, starting from the number *you* choose! - 

This code serves as a prime number list generator, offering a convenient way to explore the realm of prime numbers starting from a number of your choice. Once you enter a starting number, the generator will tirelessly churn out prime numbers, providing you with a comprehensive list to marvel at.

But why would you need such a tool? Well, prime numbers hold a special place in mathematics and computer science. They're not just numbers; they're the building blocks of many algorithms and encryption schemes. From cryptography to optimization problems, prime numbers play a crucial role.

So, whether you're a math enthusiast exploring number theory or a programmer diving into the depths of algorithms, this prime number generator has something for you. It's a welcoming companion on your journey through the fascinating world of prime numbers, ready to help you discover and explore their intriguing patterns and properties. Simply enter a starting number, press Enter, and let the exploration begin!

```
import math, sys

def main():
    while True:
        print("Enter a number to start searching for primes from: ")
        response = input ('> ')
        num = int(response)
        break

    input('Press Ctrl-C at any time to quit. Press Enter to begin...')
    
    while True:
        if isPrime(num): 
            print(str(num) + ', ', end= '', flush=True)
        num = num + 1

def isPrime(number):
    if number < 2:
        return False
    elif number == 2:
        return True

    for i in range(2, int(math.sqrt(number)) + 1):
        if number % i == 0:
            return False
    return True

if __name__ == '__main__': 
    try:
        main()
    except KeyboardInterrupt:
        sys.exit()
    
```    