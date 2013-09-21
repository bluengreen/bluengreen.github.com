---
layout: post
title: "Cleanup Rails controllers with base controllers"
date: 2013-09-21 04:44
comments: true
categories: rails, Activecontroller
---

Controllers are just basic ruby classes that inherit from Activecontroller. Which means you can manage your controllers just as you would manage a ruby class. You can cleanup your controllers by moving supporting methods into a base controller and creating a namspace. 


<!-- more -->