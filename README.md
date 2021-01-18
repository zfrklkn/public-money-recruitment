## How we'd like to recieve the soluion?

1. Fork this repository
2. Create a pull request to our original repository
3. Share link to the PR with us 

# Backend C# developer project

## Background

We as a vacation rental software provide a solution for the vacation rental hosts to create their rentals and assign received bookings to them. We have to control the current rental availability to avoid overbookings. Term `rental` refers to anything that could be rented: house, flat, room, bed in room etc. We allow to define the number of the same rentals, it is called `unit`. So for example we can have rental with 2 units. Let's assume that this rental represents a room. It means that the host rents 2 the same/similar rooms.
Our customers want to retrieve the calendar which shows their current rental occupation. To provide this functionality we deliver the API.

## The API

This project includes an API that can be consumed in order to update the aforementioned information about the host vacation rentals. It contains:
- `RentalsController` which needs to be used first for the rental creation. It provides the following endpoints:
`POST api/v1/rentals` - to create the host rental
`GET api/v1/rentals/{id}` - to retrieve the rental information for the given rental id
- `BookingsController` which needs to be used to create a booking for the existing rental. It provides the following endpoints:
`POST api/v1/bookings` - to create new booking for the existing rental
`GET api/v1/bookings/{id}` - to retrieve the booking information for the given booking id
- `CalendarController` which needs to be used to retrieve the availability of an existing rental. It provides the following endpoints:
`GET api/v1/calendar` - to retrieve the booking information for the given query parameters (rental id, start date, number of nights)
- Additionally we provide the endpoint
- `GET /swagger` - it's a swagger documentation for the API (will open on process run)

As it is presented the information for the contacts is only stored in memory and therefore we lose all information when the service restarts.

## The tasks

1. Our customers requested a new feature related to the coronavirus restriction. They have to clean each rental unit after their guest occupies it. To do it we should block additional X days after each booking. Block means to not allow to have any booking for the same unit during this time. The number of days will be defined by the host.

E.g.: 1 day preparation time:
For any booking without other booking before nor after, a day is blocked after it for the unit where the booking occurs.

One booking always occupies only one unit. The rental can have many units. So a rental with 3 units is fully occupated for the selected night when it has (3 bookings) OR (2 bookings and 1 preparation time period) OR (1 booking and 2 preparation time periods) OR (3 preparation time periods).

The contracts with API consumers are already defined. You have to extend existing endpoints to fulfill new definitions and provide the requested functionality.

A new `Unit` property is added to the `Booking` calendar model and to the new `PreparationTime` calendar model. This property is a number that references one of the available units in the Rental. For example, if the Rental has a total of 3 units, the Unit property can accept the values 1, 2,  or 3. It is irrelevant whether the Booking occupies unit 1, 2 or 3, as long as its associated PreparationTime occupies the same Unit number as the Booking.

a) extend `POST api/v1/vacationrental/rentals` endpoint request body by adding `PreparationTimeInDays` property:

POST api/v1/vacationrental/rentals
{
   Units: 3,
   PreparationTimeInDays: 2
}

b) extend `GET api/v1/vacationrental/calendar` response by adding number of occupied unit to `Bookings` collection (property `Unit`) and by adding collection of preparation time objects (property `PreparationTimes`) - each object should have `Unit` number property:

{
  Dates: [
  {
    Date: “2020-01-01”,
    Bookings: [
       {
		   Id: 2, 
           Unit: 1
       }
   ],
   PreparationTimes: []
  },
  {
    Date: “2020-01-02”,
    Bookings: [
       {
		   Id: 2, 
           Unit: 1
       }
   ],
   PreparationTimes: []
  },
  {
     Date: “2020-01-03”,
     Bookings: [],
     PreparationTimes: [
       {
           Unit: 1
       }
    ]
  }
 ]
}

2. Please consider existing code as legacy. Improve it. Please focus on readability, maintainability and self-explanatory. Unit tests are more than welcome.

3. **Optional** (nice to have). Our customers requested the possibility of updating their existing rentals. They would like to be able to change the number of units and the length of preparation time. 
Please provide a new endpoint which allows them to do it:

PUT api/v1/vacationrental/rentals/{id} 
{
   Units: 3,
   PreparationTimeInDays: 2
}

If the length of preparation time is changed then it should be updated for all existing bookings. The request should fail if decreasing the number of units or increasing the length of preparation time will produce overlapping between existing bookings and/or their preparation times.

### What we are looking for

- How you deal with the existing code and its refactoring
- How you implement new features
- How you test the correctness of your solution

### Restrictions

- Please do not modify the API contract
  - Do not rename the exposed model's properties
  - Do not modify the API routes
- Besides it changing/adding everything is allowed, feel free to add new projects, libraries etc. - everything what you consider as useful
- If there is something that you want us to notice in your approach please add a text file describing it


