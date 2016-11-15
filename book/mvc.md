MVC
===

If you've been working in web development you've most probably heard of the term "MVC" and know that it refers to "Model", "View", and "Controller". The "MVC" software pattern was introduced in the nineties to explain the principles of building desktop applications. In particular to describe how a user interface interacts with a program and its underlying data. Later it was adjusted and adapted by web developers and has become a core foundation of many popular web frameworks including PHP frameworks. The name of a new pattern is still "MVC" and is often causing confusion.

A key issue for beginners with MVC is that despite its simplicity, it's not always easy to identify what a "Model", "View", and "Controller" really mean. Let's find out by starting with the simplest one.


## Controller

The role of the controller is to accept input and convert it to commands for the model or view. Essentially the controller works with external data and environment such as:

- GET, POST and other requests in web
- user input in console

## View

The view layer processes and formats data from the controller before sending it to the user. It generates HTML, JSON, or whatever format is needed.

> Note: It is strictly forbidden to work with any environment, database or user input directly in the view. It should be in controller.

## Model

The model is the most interesting part of the MVC pattern and the most misunderstood one. A MVC model is often confused with the Yii Model class and even Active Record. These are not the same thing. The MVC model is not a single class, it's the whole domain layer (also called the problem layer or application logic). Given data from the controller it's doing actual work of the application and passing results back to controller.

> Note: Its possible the model can be totally disconnected from the database structure.

ActiveRecord classes should not contain any significant business logic. It deserves to be in separate classes which are built according to [SOLID](solid.md) and [Dependency Inversion](dependencies.md). Don't be afraid to create your own classes which are not inherited from anything from the framework.

> Note: The Model should never deal with formatting i.e. it should not produce any HTML. This is the job of the view layer. Also, same as in the view, it is strictly forbidden to work with any environment, database or user input directly in the view. It should be in controller.
