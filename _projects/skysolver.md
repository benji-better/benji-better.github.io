---
layout: page
title: Skysolver Overview
description: Airline Scheduling Optimization Project
img: assets/img/skysolver_prof_pic1.jpg
importance: 1
category: work
---

During the busy holiday season of 2022, winter storms engulfed the Denver International Airport making departures from the airport impossible for many flights on the 21st of December. Nearly 1,000 flights were either delayed or cancelled from Denver that day. Additionally, large numbers of Denver airport ground staff had already called in sick before the winter weather. This created a situation which only worsened with the increasing holiday flight volume in the subsequent days.

Over the course of a week, starting December 22nd, airlines cancelled over 18,000 flights nationwide. Surprisingly, more than half of these cancellations were Southwest Airlines flights. These cancellations were caused by Southwest Airlines relying on an outdated scheduling system that was only designed to handle 300 scheduling changes per day. This system relies on a linear algebra system that looks at every possible solution* and this leads to slow solution generation. With this project my aim is to use modern techniques used in data science to create a system that could quickly solve the resource and crew scheduling problem in an event similar to this one. Real world Southwest cancellation and flight data from the 2022 crisis with simulated crew roster data will be used to test the ability of my system.

[comment]: add project roadmap https{:// benji-better. github. io/ portfolio/news/skysolver_roadmap/}

Approach:

The main problem with Southwest’s current approach is that the system takes too long to respond to cancellations. Therefore, my system arranges relevant data to respond to new delays and cancellations hastily.

When thinking about the airline crew scheduling problem it is important to know about two distinct types of aircrew: reserve crew, and line crew.
Line aircrew have scheduled flights, and some degree of union protection that allows better work-life balance, i.e., they’re not on call; therefore, line crew can only be expected to work flights for which they’re scheduled. 

Reserve aircrew are on-call crew members that are available for situations where line crew are sick or delayed for a scheduled flight

[comment]: Crew Data IMG
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/crew_data_2022.png" title="Crew Data IMG 2022" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Planes and reserve aircrew are assignable to any flight (insofar as they will be able and ready at a given airport at a given time to board passengers and get those passengers to their chosen destination). Thus, reserve aircrew and planes are accounted for in an almost identical fashion in the system that accounts for resources at airports at a given time. 

Reserve captains, first officers, and flight attendants constitute three different types of object in the system that accounts for assignable resources at an airport at a given time. Additionally,  Southwest utilizes two different sizes of aircraft to get customers to their destinations. Both of these aircraft have similar flight and safety characteristics and can be operated interchangeably by any captain, first officer, or flight attendant working for Southwest. 

[comment]: Plane Data IMG
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/plane_data_2022.png" title="Plane Data IMG" class="img-fluid rounded z-depth-1" %}
    </div>
</div>



**Assignable Resources Object:**

The first part of my approach is to divide every flight into a given timeblock. To enable my application to allocate resources under constraints, the system needs to know
what resources are available at an airport within a certain timeblock. The system within my application
that records the quantities of these assignable resources is a three dimensional array, where
each layer or slice of the array represents each different type of assignable resource. The rows
of this array represent airports, while the columns represent the timeblocks that are being
solved.

[comment]: Assignable Resource Object Image
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/assignable_resource_object2.png" title="Assignable resources object" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Roster and Roster Helper array:

The next part of my system comprises a pair of 3-dimensional arrays, with one main roster array holding a record of which line crew will be available for a given flight, while the other helper array has the purpose of holding the position of the linecrew for their next flight within the main roster array. The helper array can be thought of as a reference number to quickly go to that crew member’s roster position in their next flight in the main roster array to record whether or not they will be able to make it for that flight. In the event of delays or cancellations this allows my application to quickly record that certain line crew members will not be able to make their next flight. 

[comment]: Flight-Roster Roster-Helper Image
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/roster_roster_helper.png" title="roster_roster_helper" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

At all slices of these roster arrays, the rows represent the airport that a flight is departing from while columns represent a flight’s destination airport; therefore, every flight has a specific location within all slices of these roster arrays.

For instance if you wanted to locate the next roster position for the attendant in the third attendant roster position for the flight leaving airport two in timeblock one and landing at airport three, within the flight-roster array, then you would use the following steps:


1)

Working a Southwest flight, at most, there will be one captain, one first officer, and four flight attendants. Therefore, within the roster helper array, we will skip past the slices of the array containing the captain and first officers and go to the fifth slices of the row, column and roster position slice sets, this specific set of slices corresponds specifically with attendants in the third attendant roster position for any given flight within the flight-roster and roster-helper arrays

2)

Then for all of these slices we look at row 2, and column 3, corresponding to the flight leaving airport two for airport three. In the example given, we can see that the third flight attendant for this flight has their next flight in timeblock three, given that row nine corresponds with flights leaving airport three in timeblock three, and this flight will be leaving for airport one, with this flight attendant in the second flight attendant roster position for their next flight. 



[comment]: Roster-Helper Example Revised Image
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/roster_helper_example_rev.png" title="Roster-Helper Example Revised Image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This system of indexing positions within the flight-roster array allows for the system to be quickly updated given cancellations and delays. Also, the system can be quickly updated while attempting to find optimal solutions to scheduling problems during adverse events.

The next fundamental part of my system is assigning a point value to every flight, for instance a crew with all line crew will always have a higher value than the same flight with one reserve crew member or the same flight with all reserve crew, because keeping line crew on their assigned schedule keeps small events from compounding into bigger issues for the airline.

The values assigned to linecrew within my system for any given scenario being solved will be kept static, but these static values could be values that have already been found to be optimal for previous scenarios similar to the one being solved for. 

What will not stay static is the values assigned to ‘assignable resources’. Different values for assignable resources will be tested multiple times to quickly find a solution that is close to optimal. The values for assignable resources that will be available certain timeblocks will 
be contained in an array similar to the Assignable Resources object. Where for every given airport at a given timeblock for the current instance or iteration of the solver the value assigned to any given type of resource (pilots, attendants and planes) will be easily searchable. Similarly every airport will be given a deficit for each type of resource for a given type of resource, airport and timeblock. This resource deficit number represents the number of a given resource that an airport needs available but is lacking for a given timeblock in order to make all the flights in that timeblock possible. For instance, if an airport has three flights that need plane type 1, but only one plane to fulfill those flights, then the resource deficit for plane type 1, for the given time and place will be two. 

My application also can redefine this resource deficit number to look one and two blocks ahead to have resources allocated before they are absolutely needed. This approach is done to smooth things out for larger airports receiving flights, or in the case of smaller airports ensure that shortages caused by low flight volume can be dealt with by looking at future needs.

This deficit system forms the basis of how deficit values are assigned to the different types of assignable resources. In order to rank flights and allocate resources, given a proposed solution to current delays and cancellation, the resource deficits for airports at given timeblocks can be used to find the value associated with each given assignable resource to be used in these flight rankings.

Let me illustrate this with an example. For simplicity, in this example, I won’t be including the value of planes. Let’s say that Airport 1 in Timeblock 3 has a shortage for all crew types, while Airport 2 has no shortage of crew in Timeblock 3. Airport 3 has two flights leaving in the first Timeblock for both of these other airports, where both crews of these flights would be ready to work on another flight in Timeblock 3. The following images illustrate how these departing flights would be ranked.

[comment]: Example asignable_recources and crew roster
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/assignable_resource_values_rev.png" title="example_asignable_recources_rev" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/example_flight_roster.png" title="example_crew_roster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

In short, the system ranks flights with values assigned to assignable resources for airports at given times. The system starts by ranking flights in timeblock 1. Using these rankings and available resources, flights with higher rankings are greenlit, then the rosters are updated for the future flights of linecrew assigned to those greenlit flights in timeblock 1 using the flight-roster array and helper array discussed previously. This process continues for each timeblock until the final solved timeblock is reached. Updating the next flight rosters of linecrew is important to accurately rank those flights needing fewer reserve-crew as higher priority flights. When the final timeblock is reached the final resource deficit numbers for all timeblocks and airports will be used to create new deficit values to feed into another round for testing these values and ranking flights. Below is an example of this process.

[comment]: basic iteration process
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/basic_iteration_process.png" title="basic_iteration_process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

To keep the system from getting stuck in one place or stuck switching between extreme values for these deficit values, my approach is to use different types of iterations for this process. One type of iteration would look forward two timeblocks to reserve resources for more valuable flights in the future, while another would be the type described above where the system just goes timeblock by timeblock. 

The main idea of my approach is that not every possibly viable solution needs to be tested as with the current systems that use linear algebra. In a crisis, airports are not lacking any given specific flight, but instead airports are lacking the assignable resources that they need to keep resources flowing through the system. Therefore, my system places its emphasis on finding how valuable assignable resources are to an airport at a specific time

Running this application on a single core, the aim would be to calibrate the system’s values before running any iterations to give a good starting point so that the system can more quickly converge on a solution. Another strength of the approach is that multiple computers or multiple cores of a single computer could all be set up differently to run the algorithm. A time limit or acceptable number of new cancelled flights could be given to all of these different computers and these computers could continue to try to find more optimal solutions until stopping due to the time limit or the acceptable number given to them.

###
###

**This project is still a work in progress, come back for more updates soon!!!**

###
###
