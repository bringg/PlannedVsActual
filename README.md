# PlannedVsActual
This repository defines and compute three new KPIs at the product level for measuring the gap between the Plan that the JSprit Engine creates and the Actual route that is executed by the driver.
Planned Vs Actual - KPIs

The underlying assumption is that the quality of our product increases as the gap between the Planned and the Actual decreases. In the next phase, the purpose of these measures is to be the cornerstones for of our Data products, thus before we use them we need to remove bugs and to understand their behavior. 

Currently we are measuring our success ratio only against the customer requirements (SLA), that is - do we meet the Task’s Time-Window or not. 
However, the customer requirements are less strict and less detailed than the plan that is created by the Optimization Engine (which includes also the tasks sequence, and the exact anticipated times for the driver to CheckIn and CheckOut for each task). 
Thus, we believe that adding the comparison between the optimization results (plan) and what happens in the field (actual) as a monitored KPI will generate information that will help us in improving our product.

We suggest the following three KPIs:
TBS - Time Between Sites
TOS - Time On Site
Order (aka priority) - whether the order of the tasks in the route is preserved as planned

The reason for choosing these KPIs is that our business is to deliver. So, the basic measurable item is a delivery. Delivery is fulfilled by visiting Way Points and performing tasks on Site. 
For example, we didn't use the drive time for each road segment (that reflects the traffic) as those measures are not directly correlated to our core business and can be supplied by third parties. 

To make a concrete and measurable KPIs several requirements need to be met:
The KPIs needs to be well defined. E.g. is parking time part of TOS or TBS?  in general, when the TOS ends and TBS starts? 
The measures from the different apps (Web, IOS, Android) needs to be consistent and aligned with the definition.
Special cases needs to be detected and marked. E.g.:
When the driver press the check-in and check-out one after the other.
When the computed route is modified by the dispatcher or by the driver
 
Measuring the KPIs
TOS:
This is easiest to assess as it is already measured and stored at the WayPoints table in the PG database:
TOS=CheckIn(Current)-CheckOut(Current) 
Note that the underlying assumption is that no additional time (such as break time) was included. 

TBS:
Computed by looking at the difference between subsequent Waypoints of the same route, called Current and Previous.
That is: 
TBS=CheckIn(Current) - CheckOut(Previous)
TBS includes the Parking time.

TBS can be compared only when the Order is preserved.

Order:
The actual is computed by sequencing the order of the WayPoint.Start_time.
The optimization order is given at the priority column in the tasks table  

Again, the order of the task is As-Defined if it comes in reality after the task that was defined in the plan. (QUESTION - should we count at the route level or at the task level?)

When Order is not preserved, the change can come either from the dispatcher or from the Driver. If the Dispatcher is the one which made the change, the Driver is actually preserving the route and the Planned Vs Actual can be measured.

Debugging:
As we would like to use the check-in and check-out times in new ways we need to understand and test the way that they are recorded.

Here are some cases that doesn't make sense.

Usages and next steps:
We started by working on the Boulanger Merchant. We need to generalize the KPI’s to other use cases such as restaurants (Batcher mode) and multiple pickups.

Well defined and accurate measures will allow:
Harvesting the data to create data products:
Give merchants more visibility into their operations such as driver and fleet performance.
Smaller time windows  
Build a feedback loop in to the optimization engine to improve the optimization accuracy and in the long run higher efficiency of the delivery operation:
More accurate TOS estimation 
Better visibility into the Traffic effects based on the TBS 

Future ideas for Visibility improvements:
Break down the TBS into 1) Drive, 2) Park, 3) walk and 3) Idle. 
For Idle, it is not clear whether it is a planned break (to give the driver some rest time or due to time windows constraint) or it is due to overestimation of TOS/TBS
When the Order is not preserved we need to know the reason. Who made the change is it the Dispatcher or the Driver?



Issues:


