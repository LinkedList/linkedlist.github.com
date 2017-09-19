---
layout: post
title: "@RequestParam in every Spring Controller"
description: "How to not duplicate the same @RequestParam in every Spring controller"
category: 
tags: [spring, controller, RequestParam]
---
There is often a need to have the same `@RequestParam` in multiple methods and even in multiple controllers, e.g.
a required filter param that is processed in a hibernate filter for security checking. 
To avoid repeating the same parameter in every request the only thing needed is to register a new `@ControllerAdvice` bean.

To show with an example:

~~~java
import org.springframework.web.bind.annotation.ControllerAdvice
import org.springframework.web.bind.annotation.ModelAttribute
import org.springframework.web.bind.annotation.RequestParam

@ControllerAdvice("my.package.with.controllers")
class RequiredParameterControllerAdvice {

    @ModelAttribute
    public void requiredParam(@RequestParam Long requiredLongParameter) {
        // empty on purpose - @RequestParam is by default required so this
        // will throw validation error on request without requiredLongParameter
    }
}
~~~

The key things in this example are:
* `@ControllerAdvice("my.package.with.controllers")` which tells spring to apply this `@ControllerAdvice` only to controllers in listed packages (You could of course omit this so that this advice is applied for every controller).
* `@ModelAttribute` that binds `requiredParam` method parameter as a named model attribute
* And of course `@RequestParam` (which is required by default) that defines our parameter
