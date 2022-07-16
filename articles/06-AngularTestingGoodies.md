# Tips and tricks for Angular Testing

Sometimes when writing Unit Tests I encountered the following error:

```
console.error
      NG0304: 'mat-card-content' is not a known element:
      1. If 'mat-card-content' is an Angular component, then verify that it is part of this module.
      2. If 'mat-card-content' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the '@NgModule.schemas' of this component to suppress this message.

```

This error stems from the fact that in my test class I did not include the `MatCardModule`, but the question is: 'is this really needed?' As always the answer: 'well, it depedens'.
As I was writing a simple unit test where I was not excercising the DOM or anything UI-related I did not need to import the `MatCardModule` in my imports and I could simply get rid of
the issuse by adding:

```
schemas: [NO_ERRORS_SCHEMA]
```

in my test class. There are of course cases where it is not wise to use this way of supporessing errors and of course it is not a good idea to abuse it.
