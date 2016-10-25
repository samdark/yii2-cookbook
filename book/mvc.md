MVC
===

Since you're working with web development, most probably you've heard "MVC" and know that it refers to "Model", "View" and "Controller".
The "MVC" thing was introduced to explain the principles of building desktop applications: how a user interface is interacting with program itself and the data. Then it was adapted for web development but since web, and especially PHP, is a bit different it didn't fit perfectly.

The problem with MVC is that, despite its simplicity, it's not so easy to tell what "Model", "View" and "Controller" really mean. Let's find out starting with the simplest one.


## Controller

The role of the controller is to accept input and convert it to commands for the model or view. So basically controller works with external data and environment such as:

- GET, POST and other requests in web
- user input in console

## View

A view layer is getting prepared data from controller and formats it before sending it to the user. It can create HTML or JSON or whatever format is needed.

> Note: It is strictly forbidden to work with any environment, database or user input directly in the view. It should be in controller.

## Model

A model is the most interesting part of the MVC and the most misunderstood one. MVC model is often confused with Yii Model class and even Active Record. These are not the same thing. MVC model is not a single class, it's the whole domain layer (also called problem layer or application logic). Given data from controller it's doing actual job of the application and passing results back to controller.
