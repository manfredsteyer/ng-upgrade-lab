# Migrate to Angular 2 with ngUpgrade

In this part of the lab, you will migrate AngularJS 1.x code to Angular 2 using ngUpgrade.

## Migrating FlightSearchComponent

1. Migrate the FlightSearchComponent to Angular. For this, create a file ``migrated-flight-search.component`` in the folder ``flight-search`` and add the following code:

	```
	import { Component, Inject } from '@angular/core';
	import { FlightService } from '../services/flight.service';
	import { Flight } from "../shared/flight";
	
	@Component({
	    selector: 'migrated-flight-search',
	    templateUrl: './migrated-flight-search.component.html'
	})
	export class MigratedFlightSearchComponent {
	
	    public from: string = "Hamburg";
	    public to: string = "Graz";
	
	    public selectedFlight: Flight;
	
	    constructor(@Inject('flightService') private flightService: FlightService) {
	    }
	
	    public get flights() {
	        return this.flightService.flights;
	    }
	
	    public search() {
	        this.flightService.find(this.from, this.to);
	    }
	
	    public select(flight: Flight) {
	        this.selectedFlight = flight;
	    }
	}
	```

3. Note the usage of ``@Inject('flightService')`` in the listing above. ``flightService`` is the name of a service that has been written with AngularJS 1.x. It is reused together with the Angular 2 component in question.
4. Add a template for this component with the name ``migrated-flight-search.component.html`` to the folder ``flight-search``:

	```
	<h1>Flight Search (Migrated)</h1>
	
	<div class="form-group">
	    <label>From</label>
	    <input class="form-control" [(ngModel)]="from" />
	</div>
	
	<div class="form-group">
	    <label>To</label>
	    <input class="form-control" [(ngModel)]="to" />
	</div>
	
	<div class="form-group">
	    <button class="btn btn-default" (click)="search()">Search</button>
	</div>
	
	<div *ngFor="let f of flights" class="col-sm-6 col-md-4">
	
	        <flight-card
	            [item]="f"
	            [selectedItem]="selectedFlight"
	            (selectedItemChange)="selectedFlight = $event"></flight-card>
	
	</div>
	
	<pre>Shopping Basket
	------------------------
	{{ selectedFlight | json }}
	</pre>
	```

5. Note that ``flight-card`` is the name of a pre-existing AngularJS 1.x component which is reused here.
6. Switch to the file ``app.module.ts`` and add the following import statements at the top:

	```
	// Upgrade-Adapter
	import { UpgradeAdapter } from '@angular/upgrade';
	
	// Angular
	import { forwardRef, NgModule } from '@angular/core';
	import { BrowserModule } from '@angular/platform-browser';
	import { HttpModule } from '@angular/http';
	import { FormsModule } from '@angular/forms';
	
	// Migrated Component
	import {MigratedFlightSearchComponent} from "./flight-search/migrated-flight-search.component";
	```

7. Create an ``UpgradeAdapter`` at the end of ``app.module.ts`` and let it reference an ``AppModule`` that contains the migrated components and services. Also make sure to export it.

	```
	export const upgradeAdapter = new UpgradeAdapter(forwardRef(() => AppModule));
	
	@NgModule({
	    imports: [
	        BrowserModule,
	        HttpModule,
	        FormsModule
	    ],
	    declarations: [
	        MigratedFlightSearchComponent,
	        upgradeAdapter.upgradeNg1Component('flightCard')
	    ]
	})
	class AppModule {
	}
	```

8. Please note that the AppModule declares the ``MigratedFlightSearchComponent`` as well as an upgraded version of the ``FlightCardComponent`` which has been written with AngularJS 1.x.

8. Below the ``AppModule`` add code to upgrade the ``FlightService`` which has been written with AngularJS 1.x and should be used with Angular 2. Additionally, downgrade the ``MigratedFlightSearchComponent`` in order to be able to use it within the current AngularJS 1.x application.

	```
	upgradeAdapter.upgradeNg1Provider('flightService');
	
	app.directive('migratedFlightSearch', <any>upgradeAdapter.downgradeNg2Component(MigratedFlightSearchComponent))
	```

9. Open the file ``app.routes.ts`` and replace the template in the route for ``/flight`` with a template, that points to the new ``MigratedFlightSearchComponent``:

	```
	[...]
	.state('flightBooking.flightSearch', {
	    url: '/flight',
	    //template: '<flight-search></flight-search>'
	    template: '<migrated-flight-search></migrated-flight-search>'
	})
	[...]
	```

10. Switch to the file ``app.ts`` and import the UpgradeAdapter from ``app.module``. Use it at the end to bootstrap the Angular app:


	```
	import { upgradeAdapter } from './app.module';
	import './app.routes';
	
	// Use upgradeAdapter to manual bootstrap Angular1+2
	upgradeAdapter.bootstrap(document.body, ['flight-app']);
	```

11. In the ``index.html`` remove the ``ng-app`` within the ``body`` element. This is necessary because an Angular 1+2 application has to be bootstrapped manually.
12. Compile the project to make sure that there are no TypeScript errors.
13. Refresh the project in your browser and ensure that the app uses the ``MigratedFlightSearchComponent`` now. Make sure that a selected flight is displayed with orange background and within the shopping basket at the end of the page.

## Using Two Way Binding in Angular 2

1. Navigate to the file ``migrated-flight-search.component.html`` and assure yourself that the element ``flight-search`` contains both, property bindings and event bindings:

	```
	<flight-card
	    [item]="f"
	    [selectedItem]="selectedFlight"
	    (selectedItemChange)="selectedFlight = $event"></flight-card>
	```

2. Update it to use the less verbose two way binding syntax:

	```
	<flight-card [item]="f"
	             [(selectedItem)]="selectedFlight"></flight-card>
	```

3. Assure yourself that the application still works as expected.



## Migrating PassengerService

1. Have a look at the files ``passenger-search.component.ts`` and ``passenger-search.component.html`` in the folder ``passenger-search``. 

2. Assure yourself that the ``PassengerSearchComponent`` uses the ``PassengerCardComponent`` that can be found in the same folder as well as the ``PassengerService`` that is located in the folder ``services``. Examine this implementations too.

3. Copy the file ``passenger.service.ts`` and name the copy ``migrated-passenger.service.ts``. 

4. Open the file ``migrated-passenger.service.ts`` and add the following imports for Angular 2:
	```
	import {Http, URLSearchParams, Headers} from '@angular/http';
	import {Injectable} from '@angular/core';
	```

5. Alter the class name to ``MigratedPassengerService`` and decorate it with ``Injectable``:

	```
	@Injectable()
	export class MigratedPassengerService {
		[...]
	}
	``` 

6. Update the constructor so that it takes an argument of ``Http`` instead of the service ``$http``:

	```
    constructor(private http: Http) {
    }
	```

7. Add a debug message at the top of the method ``find``:

	```
	    find(name): Promise<Passenger[]> {
	        // Add debug-info
	        console.debug('calling MigratedPassengerService.find', name);
			[...]
		}
	```

8. Update the creation of the variable ``urlParams`` because the new ``Http`` service uses an instance of ``URLSearchParams`` to represent parameters. Also create an instance of ``Headers``:

	```
	let urlParams = new URLSearchParams()
	urlParams.set('name', name);
	
	let headers = new Headers();
	headers.set('Accept', 'text/json');
	```

9. Update the call to the ``Http`` service:

	```
	return this
	        .http
	        .get(url, {search: urlParams, headers: headers})
	        .map(resp => resp.json())
	        .toPromise();
	```

10. Open the file ``app.module.ts`` and replace the ``import`` statement for the ``PassengerService`` with an ``import`` statement for the newly created ``MigratedPassengerService``:

	```
	// Remove import for PassengerService
	// import {PassengerService} from "./services/passenger.service";
	
	// Add import for MigratedPassengerService
	import {MigratedPassengerService} from "./services/migrated-passenger.service";
	```

11. Remove the registration for the ``PassengerService``:

	```
	// Remove the registration for passengerService
	//app.service('passengerService', PassengerService);
	```

13. As the ``MigratedPassengerService`` is an Angular 2 service, it needs to be registered with the Angular 2 module:

	```
	@NgModule({
	    [...]
	    providers: [
	        MigratedPassengerService
	    ]
	})
	class AppModule {
	}
	```

14. As the ``MigratedPassengerService`` needs to be used with the pre-existing ``PassengerSearchComponent``, which has been written with AngularJS 1.x, it has to be downgraded as well:

	```
	// Downgrade MigratedPassengerService and register it as passengerService
	app.factory('passengerService', upgradeAdapter.downgradeNg2Provider(MigratedPassengerService));
	```

12. Compile the project to make sure that there are no TypeScript errors.

13. Refresh the project in your browser and ensure that the app uses the ``MigratedPassengerService`` now. If this is the case you should see a corresponding debug message in the browser console when searching for passengers.



## Migrating FlightEditComponent and passing a routing parameter

1. Navigate to the folder ``flight-edit`` and explore the files ``flight-edit.component.ts`` as well as ``flight-edit.component.html``.
2. Copy those files and name them ``migrated-flight-edit.component.ts`` and ``migrated-flight-edit.component.html``.
3. Open the file ``migrated-flight-edit.component.ts`` and add the following ``import``:
	```
	import { Component, OnInit, Input, Inject } from '@angular/core';
	```

5. Add a ``Component`` decorator to the class, rename it to ``MigratedFlightEditComponent`` and implement ``OnInit``: 

	```
	@Component({
	    selector: 'flight-edit',
	    templateUrl: './migrated-flight-edit.component.html'
	})
	export class MigratedFlightEditComponent implements OnInit {
	    [...]
	}
	```

6. Prefix the property ``id`` with ``@Input()``. This allows to pass the ``id`` into the component by means of data binding later. Also assign an empty object to the property ``flight`` to prevent navigating over ``undefined``: 

	```
	@Input() id: string;
	
	flight: Flight = <any>{};
	```


7. As the migrated component doesn't have access to the used version of UI-Router, remove the parameter $stateParams. Also add ``@Inject('flightService')`` to the remaining parameter. This is necessary because the ``FlightService`` is an upgraded AngularJS 1.x service registered under the name ``flightService``:

	```
	//old: constructor($stateParams: any, private flightService: FlightService)
	constructor(@Inject('flightService') private flightService: FlightService) {
	   [...]
	}
	```

8. Move the content of the constructor to a newly created method ``ngOnInit``. This method is executed after properties like ``id`` have been bound. Remove the usage of ``$stateParams``:

	```
	ngOnInit() {
	    // remove usage of $stateParams
	    //this.id = $stateParams.id;
	
	    this.flightService
	        .getById(this.id)
	        .then((flight: Flight) => {
	            this.flight = flight;
	            this.message = "";
	        })
	        .catch(resp => {
	            console.error(resp);
	            this.message = resp.data;
	        })
	}
	```
9. Replace the template of the ``FlightEditComponent``:
	- Rename ``ng-model`` to ``[(ngModel)]``
	- Rename ``ng-if`` to ``*ngIf``
	- Remove the prefix ``$ctrl.`` from data binding expressions (e.g. use ``{{id}}`` instead of ``{{$ctrl.id}}`` 

10. Remove the ``tabs`` element and all ``tab`` elements within the template. We will migrate them in another exercise.

11. Move to the file ``app.module`` and register the ``FlightEditComponent`` within the ``AppModule``, as it is an Anguler 2 Component:

	```
	@NgModule({
		[...]
	    declarations: [
			[...]
	        MigratedFlightEditComponent
			[...]
	    ],
		[...]
	})
	class AppModule {
	}
	
	```
12. Remove the ``import`` and declaration for the ``FlightEditComponent``, as it is replaced with its migrated counterpart now:
 
	```
	// Remove FlightEdit
	//import {FlightEditComponent} from "./flight-edit/flight-edit.component";

	[...]

	// Remove registration for flightEdit
	// app.component('flightEdit', FlightEditComponent);
	```

13. Import the ``MigratedFlightEditComponent``. Downgrade it to an AngularJS 1.x directive and register it:
	
	```
	import {MigratedFlightEditComponent} from "./flight-edit/migrated-flight-edit.component";
	[...]
	
	app.directive('flightEdit', <any>upgradeAdapter.downgradeNg2Component(MigratedFlightEditComponent));
	```
14. Switch to the file ``app.routes.ts`` and modify the route for ``flight-edit``. To pass the parameter ``id``, you need a controller that provides it to the template. In the template you can bind it to the property ``id`` of the ``flight-edit`` element:

	```
		[...]
	    .state('flightBooking.flightEdit', {
	        url: '/flight/:id',
	        template: '<flight-edit [id]="$ctrl.id"></flight-edit>',
	        resolve: {
	            id: $stateParams => $stateParams.id
	            // same as:
	            // id: function($stateParams) { return $stateParams.id; }
	        },
	        controllerAs: '$ctrl',
	        controller: function(id) { this.id = id; }
	    });
	```

15. Compile the project to make sure that there are no TypeScript errors.

16. Refresh the project in your browser and ensure that the app uses the ``MigratedFlightEditComponent`` now. 


## Migrating TabComponent **

1. Move to the the folder ``tabs`` and explore the implementation of the ``tabs`` control that can contain several ``tab`` elements. Try to find out how these components work and how they interact with each other.

2. Copy the file ``tabs.component.ts`` and name the copy ``migrated-tabs.component.ts``.

3. Migrate the two components within the file ``migrated-tabs.component.ts`` to Angular 2.

4. Apply what you have learned during this tutorial for this migration and consider the following points:
	1. You can use the ``ngAfterContentInit`` as a replacement for ``$postLink``. You can implement ``AfterContentInit`` from ``@angular/core`` which defines this method.
	2. You can use the ``ngOnInit`` as a replacement for ``$onInit``. You can implement ``OnInit`` from ``@angular/core`` which defines this method.
	3. You can get a parent component by means of dependency injection: 
		```
		export class MigratedTabComponent implements OnInit {
			[...]
		    constructor(private tabs: MigratedTabsComponent) {
            }
		}
		```
	4. Prefix properties, which you want to get via data binding, with ``@Input()`` from ``@angular/core``.
	5. The counterpart of ``ng-transclude`` in Angular 2 is ``ng-content``.
	6. Take care of the syntax for ``*ngFor`` which differs from the one of ``ng-repeat``:
		```
			AngularJS 1.x: ng-repeat="tab in $ctrl.tabs"
			Angular 2: *ngFor="let tab of tabs"
		```
5. Move to the file ``tabs.module.ts`` and remove the registration for the old ``tabs`` and ``tab`` components:
	```
	// Remove registrations
	// tabs.component('tab', tabComponentDesc);
	// tabs.component('tabs', tabsComponentDesc);
	```
6. In the folder ``tabs`` create a file ``migrated-tabs.module.ts`` with a module for the migrated components:

	```
	import { NgModule } from '@angular/core';
	import { CommonModule } from '@angular/common';
	import {MigratedTabComponent, MigratedTabsComponent} from "./m-tabs.component";
	
	@NgModule({
	    imports: [
	        CommonModule
	    ],
	    declarations: [
	        MigratedTabsComponent,
	        MigratedTabComponent
	    ],
	    exports: [
	        MigratedTabsComponent,
	        MigratedTabComponent
	    ]
	})
	export class MigratedTabsModule {
	}

	```
7. Move to the file ``app.module.ts`` and import the ``MigratedTabsModule``:

	```
	import {MigratedTabsModule} from "./tabs/migrated-tabs.module";
	```

8. Reference this ``MigratedTabsModule`` within the ``imports`` of the ``AppModule``:

	```
	@NgModule({
	    imports: [
	        BrowserModule,
	        HttpModule,
	        FormsModule,
	        // Add import for MigratedTabsModule
	        MigratedTabsModule
	    ],
	    [...]
	})
	class AppModule {
	}
	
	```

9. Switch to the file ``migrated-flight-edit.component.html`` within the folder ``flight-edit`` and reintroduce the ``tab`` and ``tabs`` elements you have removed during the last exercise:

	```
	<tabs>
		<tab title="Flight">
			[...]
		</tab>
		<tab title="Details">
		</tab>
	</tabs>
	```
	
12. Compile the project to make sure that there are no TypeScript errors.

13. Refresh the project in your browser and ensure yourself that the ``tabs`` control works correctly within the ``flight-edit`` route.
