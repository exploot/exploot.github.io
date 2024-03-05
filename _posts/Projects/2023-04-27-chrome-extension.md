---
title: Chrome Darkmode Web Extension
date: 2023-04-27
categories: [Projects]
tags: [JavaScript, Utility]
toc: false
math: true
comments: false
mermaid: true
image: https://i.ibb.co/jHFQRhz/2023-03-24-15-59-29.gif

<author_id>:
    name: exploot
---
# Darkmode Extension - 

Introducing the Dark Mode Converter: my go-to solution for transforming webpage colors to suit my browsing needs. With this handy extension, I can effortlessly switch bright backgrounds to soothing dark tones, making late-night browsing sessions a breeze. Not only does it reduce eye strain, but it also enhances readability and adds a touch of sophistication to my browsing experience. Whether I'm burning the midnight oil or simply prefer a sleeker aesthetic, the Dark Mode Converter has become an essential tool for optimizing my online activities.

Firstly here is the GUI for the app:

```    
    if(document.querySelector(".popup")){
    const button = document.querySelector(".button");
    let buttonOn = false;
    
    button.addEventListener("click", () => {
        if (!buttonOn) {
            buttonOn = true;
            chrome.tabs.executeScript({
                file: "appOn.js"
            })
        }
        else {
            buttonOn = false;
            chrome.tabs.executeScript({
                file: 'appOff.js'
            })
        }
    })
} 

```

Next is the behavior when the app is turned "On":

```
(function () {
    document.querySelector("html").style.filter = "invert(1) hue-rotate(180deg)";
    let media = document.querySelectorAll("img, picture, video");
    media.forEach((mediaItem) => {
        mediaItem.style.filter = "invert(1) hue-rotate(180deg)"
    })

})();
```

And finally, the behavior when the "Off" button is selected:
```
(function () {
    document.querySelector("html").style.filter = "invert(0) hue-rotate(0deg)";
    let media = document.querySelectorAll("img, picture, video");
    media.forEach((mediaItem) => {
        mediaItem.style.filter = "invert(0) hue-rotate(0deg)"
    })
})();
```

In a digital world filled with endless browsing possibilities, the Dark Mode Converter stands out as a game-changer, offering a simple yet powerful solution for enhancing comfort and style. Its ability to seamlessly adjust webpage colors to match my preferences has transformed my online experience, making late-night sessions more enjoyable and reducing eye strain along the way. With its sleek design and practical functionality, this extension has earned its place as a must-have tool in my browsing arsenal.