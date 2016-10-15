# Further Exercises

If you want, you can use the knowledge you've developed during this tutorial to solve the following exercises.

**Important**: Save your current solution before you start with the following more difficult exercises.

1. Filters are named pipes in Angular 2. Look up the [section ``Custom Pipes``](https://angular.io/docs/ts/latest/guide/pipes.html#!#custom-pipes) in the documentation of Angular and write a pipe for transforming the property ``passengerStatus`` of the loaded passengers:
	- Transform ``A`` to ``Senator`` if a parameter ``long`` is passed to the pipe, otherwise transform it to ``SEN``
	- Transform ``B`` to Frequent Traveler or ``FTL``
	- Transform ``C`` to Passenger or ``P``
2. Migrate further components like ``FlightSearchComponent``, ``PassengerSearchComponent`` or ``PassengerCardComponent``.
3. Migrate the ``FlightService``.
4. Write a resolver for the ``flight-edit`` route that loads the flight in question for the ``FlightEditComponent``.
5. Write a ``PassengerEditComponent``.
6. Lookup how to validate input in the [Angular 2 documentation](http://www.angular.io) and validate the data passed into ``PassengerEditComponent`` and/or ``FlightEditComponent``.
7. Lookup how to create custom validation rules in the [Angular 2 documentation](http://www.angular.io) and create one for the ``PassengerEditComponent`` and/or ``FlightEditComponent``.




