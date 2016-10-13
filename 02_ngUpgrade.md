# Migrate to Angular 2 with ngUpgrade

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

3. Note the usage of ``@Inject('flightService')`` in the last listing. ``flightService`` is the name of a service that has been written with AngularJS 1.x. It is reused together with the Angular 2 component in question.
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

5. Note that ``flight-card`` is the name of a preexisting AngularJS 1.x component, that is reused here.
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

8. Please note, that the AppModule declares the ``MigratedFlightSearchComponent`` as well as an upgraded version of the ``FlightCardComponent`` that has been written with AngularJS 1.x.

8. Below the ``AppModule``, add code to upgrade the ``FlightService`` which has been written with AngularJS 1.x and should be used with Angular 2. Also, downgrade the ``MigratedFlightSearchComponent`` to make it possible to use it within the current AngularJS 1.x application.

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

11. In the ``index.html``, remove the ``ng-app`` within the ``body`` element. This is necessary, because an Angular 1+2 application has to be bootstrapped manually.
12. Compile the project to make sure that there are no TypeScript errors.
13. Refresh the project in your browser and ensure that the the app uses the ``MigratedFlightSearchComponent`` now. Make sure, that a selected flight is displayed at an orange background and displayed within the shopping basked at the end of the page.

## Using Two Way Binding in Angular 2

1. Navigate to the file ``migrated-flight-search.component.html`` and assure yourself, that the element ``flight-search`` contains both, property bindings and event bindings:

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

3. Assure yourself, that the application still works as expected.



## Migrating PassengerCardComponent

...

## Migrating PassengerService

...

## Migrating Tabs-Component

...