# Refactoring to components

In this lab, you will start with an Angular 1.x project that has been partly refactored to use components. Most of it has also been ported to TypeScript and webpack has been introduced as a bundling solution. 

Now, your task is refactoring the rest of this solution.

## Getting started

1. To prevent annoying caching problems, install the Chrome plugin ``Clear Cache``.
2. If you don't manage to install ``Clear Cache`` make sure to disable caching in the ``network`` tab within the dev tools (F12) in Chrome.
2. Open the root folder of the project in Visual Studio Code. The root folder is the folder that contains the file ``tsconfig.json``.
3. Compile the project with CTRL+SHIFT+b (*b* like build) to make sure that there are no type script errors.
	- When Visual Studio asks you to create a build task, choose the option TypeScript.
	- When, and only when, Visual Studio Code creates a task.json after that, update it with the following snippet:
	 
		```	
		{
		    // See https://go.microsoft.com/fwlink/?LinkId=733558
		    // for the documentation about the tasks.json format
		    "version": "0.1.0",
		    "command": "npm",
		    "isShellCommand": true,
		    "args": ["run", "tsc"],
		    "showOutput": "silent",
		    "problemMatcher": "$tsc"
		}
		```
		
4. Switch to the console (CTRL+SHIFT+c) and start the project:

	```
	npm start
	```

5. After the web server started, navigate to ``http://localhost:8080`` and use the application to search for flights from ``Hamburg`` (in Germany) to ``Graz`` (in Austria). Select one of the found flights.
   

## Inspecting the project

1.  Open the project in Visual Studio Code and have a look to the file ``tsconfig.json``. Assure yourself, that it configures the TypeScript compiler to compile TypeScript code down to EcmaScript 5 and CommonJS modules. 
2.  Have a look to the configured entry points within the file  ``webpack.config.js``.
3.  Open the file ``app.module.ts`` and find out, that those files contain the main module of the app.
3.  Open the files ``flight-search.js`` and ``flight-search.html`` in the folder ``app/flight-search``. Assure yourself, that this files contain a classic Angular 1.x controller as well as a corresponding template. 
4.  Have a look at the file ``app.routes.ts`` and examine that it defines the routes for the application. Also contemplate the route for the ``FlightSearchController`` and find out that it uses ``controllerAs``.
5.  The application has to define the ``FlightSearchController`` before it is used within the routing configuration. In all other cases, webpack assures this by following the references between the TypeScript files. Those references are defined by ES6 import statement. But as the ``FlightSearchController`` is written in a traditional way, the application has to assure this manually. For this purpose, the ``app.ts`` which is the entry point of the app bundle references the files ``app.module.ts``, ``flight-search.ts`` and ``app.routes.ts`` in the right order. Assure yourself of this fact. 

## Refactoring flight-search.js to TypeScript

1. Rename the file ``flight-search.js`` to ``flight-search.ts``
2. Remove the IIFE expression at the begin and at the end of the file. This expression isn't needed anymore, cause ES6/ TypeScript creates a module with an own namespace for each file. 
3. Add an ``export`` statement in front of the declaration of the function to make the function visible in other modules. 
4. Remove the registration of the controller. Later, you will reintroduce it within the file ``app.module.ts``.
5. Make sure that your file looks like in the following listening: 

	```
	// Remove IIFE expression:
	// (function() {
	
	    // Add export statement:
	    export function FlightSearchController(flightService, bookingEventService) {
	
			[...]
	    }
	
	    // Remove the registration for the controller:
	    // angular.module('flight-app')
	    //       .controller('FlightSearchController', FlightSearchController);
	
	// Remove IIFE expression:
	// })();
	```

6. Open the file ``app.module.ts`` and import the ``FlightSearchController``. Register this controller with the module: 

	```
	import { FlightSearchController } from './flight-search/flight-search';
	
	[...]
	
	var app = angular.module('flight-app', ['ngMessages', 'ui.router', tabs]);
	
	[...]
	
	app.controller('FlightSearchController', FlightSearchController);
	```

7. Please note, that the ``import`` statement introduced in the ``app.module.ts`` also makes sure, that webpack bundles up the files in the right order. Therefore, it is not necessary to reference it within the file ``app.ts``:

	```
	require("./app.module");
	// Remove reference to flight-search.ts
	// require("./flight-search/flight-search");
	require("./app.routes");
	``` 
8. Compile the project to make sure that there are no TypeScript errors.
9. Refresh the project in your browser and ensure that the refactored project still works.

## Refactoring FlightSearchController to a class

1. Refactor the constructor function ``FlightSearchController`` to a class. Try to do this without the following snippet and just use it, when you get stuck.

	```
	import {FlightService} from "../services/flight.service";
	import {BookingEventService} from "../services/booking-event.service";
	import {Flight} from "../shared/flight";
	
	export class FlightSearchController {
	
	    public from: string = 'Hamburg';
	    public to: string = 'Graz';
	    public selectedFlight: Flight = null;
	    private flightService: FlightService;
	    private bookingEventService: BookingEventService;
	
	    constructor(flightService: FlightService, bookingEventService: BookingEventService) {
	        this.flightService = flightService;
	        this.bookingEventService = bookingEventService;
	    }
	
	    getFlights() {
	        return this.flightService.flights;
	    }
	
	    search() {
	
	        return this
	            .flightService
	            .find(this.from, this.to)
	            .catch(function (resp) {
	                console.debug(resp);
	            });
	    }
	
	    select(f: Flight) {
	        this.selectedFlight = f;
	        this.bookingEventService.publish(f);
	    }
	}
	```
2. Compile the project to make sure that there are no TypeScript errors.
3. Refresh the project in your browser and ensure that the refactored project still works.

## Refactoring FlightSearchController to a component

1. Add an exported Constant ``FlightSearchComponent`` at the end of the file ``flight-search.ts``, that provides the metadata for a component using the ``FlightSearchController`` as well as its template. In addition to that, remove the ``export`` statement from the declaration of the ``FlightSearchController``, as this controller isn't needed outside of the current file anymore:

	```
	import {FlightService} from "../services/flight.service";
	import {BookingEventService} from "../services/booking-event.service";
	import {Flight} from "../shared/flight";
	
	// Remove the export statement
	// export class FlightSearchController {
	class FlightSearchController {
	
	    [...]
	
	}
	
	export const FlightSearchComponent: angular.IComponentOptions = {
	    controller: FlightSearchController,
	    templateUrl: './flight-search.html'
	}
	```

2. Switch to the file ``app.module.ts`` and import the ``FlightSearchComponent`` instead of the ``FlightSearchController``. Also replace the registration for the ``FlightSearchController`` with a registration for the ``FlightSearchComponent``. Make sure to use the name ``flightSearch`` for this component and note that the corresponding tag name is ``flight-search``:

	```
	// Remove import for FlightSearchController
	// import { FlightSearchController } from './flight-search/flight-search';
	
	// Add import for FlightSearchComponent
	import { FlightSearchComponent } from './flight-search/flight-search';
	
	[...]
	
	var app = angular.module('flight-app', ['ngMessages', 'ui.router', tabs]);
	
	[...]
	
	// Remove registration for FlightSearchController
	// app.controller('FlightSearchController', FlightSearchController);
	
	// Add registration for FlightSearchComponent
	app.component('flightSearch', FlightSearchComponent);
	```

3. Open the file ``app.routes.ts`` and update the route configuration for the ``FlightSearchComponent``:

	```
	[...]
	.state('flightBooking.flightSearch', {
	    url: '/flight',
	    template: '<flight-search></flight-search>'
	})
	[...]
	```
4. Compile the project to make sure that there are no TypeScript errors.
5. Refresh the project in your browser and ensure that the refactored project still works.

## Rename files to align with Angular 2 conventions

1. Rename the file ``flight-search.ts`` to ``flight-search.component.ts`` to align with typical conventions in Angular 2.
2. For the same reason, rename the file ``flight-search.html`` to ``flight-search.component.html`` for the same reason.
3. Update references to this files within the files ``flight-search.component.ts`` and ``app.module.ts``.
4. Compile the project to make sure that there are no TypeScript errors.
5. Refresh the project in your browser and ensure that the refactored project still works.

## Refactor the FlightCardDirective to a component

1. Open the file ``flight-card.directive.ts`` in the folder ``app/flight-search`` and assure yourself that it defines the factory function ``createFlightCardDirective`` that creates a directive description object (DDO) for the FlightCardDirective. 
2. Try to find out how the directive in question works. 
3. Also, examine that the directive uses both, ``controllerAs`` as well as ``bindToController``.
3. Also have a look to the corresponding template in the file ``flight-card.directive.html``.
4. Replace the function ``createFlightCardDirective`` in the file ``flight-card.directive.ts`` with an exported metadata object for a ``FlightCardComponent``:
	
	```
	export const FlightCardComponent: angular.IComponentOptions = {
	    controller: FlightCardController,
	    templateUrl: './flight-card.directive.html',
	    transclude: true,
	    bindings: {
	        item: '=',
	        selectedItem: '='
	    }
	}
	```

5. Open the file ``app.module.ts`` and replace the registration of the ``FlightCardDirective`` with a registration of ``FlightCardComponent``:


	```
	[...]
	
	// Remove registration for the directive flightCard
	// app.directive('flightCard', createFlightCardDirective);
	
	// Add registration for the FlightCardComponent
	app.component('flightCard', FlightCardComponent);
	
	
	[...]
	
	var app = angular.module('flight-app', ['ngMessages', 'ui.router', tabs]);
	
	[...]
	
	// Remove registration for the directive flightCard
	// app.directive('flightCard', createFlightCardDirective);
	
	// Add registration for the FlightCardComponent
	app.component('flightCard', FlightCardComponent);
	
	[...]
	```
		
6. Compile the project to make sure that there are no TypeScript errors.
7. Refresh the project in your browser and ensure that the refactored project still works.

## Rename files to align with Angular 2 conventions

1. Rename the file ``flight-card.directive.ts`` to ``flight-card.component.ts`` to align with typical conventions in Angular 2.
2. For the same reason, rename the file ``flight-card.directive.html`` to ``flight-card.component.html`` for the same reason.
3. Update references to this files within the files ``flight-card.component.ts`` and ``app.module.ts``.
4. Compile the project to make sure that there are no TypeScript errors.
5. Refresh the project in your browser and ensure that the refactored project still works.

## Switch to unidirectional data flow

1. Update the FlightCardController in the file ``flight-card.component.ts`` with a property selectedItemChange that can point to an event handler. Call this event handler at the end of ``select``:

	```
	class FlightCardController {
	    item: Flight;
	    selectedItem: Flight;
	    selectedItemChange: Function;
	
	    select() {
	        this.selectedItem = this.item;
	        if (this.selectedItemChange) {
	            this.selectedItemChange(this.selectedItem);
	        }
	    }
	}
	```
2. Update the ``bindings`` within the ``FlightCardComponent`` to use unidirectional property bindings and an event handler:

	```
	export const FlightCardComponent: angular.IComponentOptions = {
	    controller: FlightCardController,
	    templateUrl: './flight-card.component.html',
	    transclude: true,
	    bindings: {
	        item: '<',
	        selectedItem: '<',
	        selectedItemChange: '&'
	    }
	}
	```
4. Switch to the file ``flight-search.component.html`` and update the usage of ``flight-card`` by setting up an event-handler.
	```
    <flight-card
            item="f"
            selected-item="$ctrl.selectedFlight"
            selected-item-change="$ctrl.selectedFlight = $locals">
	```
3. Compile the project to make sure that there are no TypeScript errors.
4. Refresh the project in your browser and ensure that the refactored project still works.

## Life Cycle Hooks

1. Look up the presented slides regarding life cycle hooks.
2. Find out how to create a life cycle hook for $onInit and $onChange.
3. Create those live cycle hooks within the ``FlightCardComponent`` and ensure yourself that AngularJS 1.x calls them.