![[Screenshot 2023-02-15 at 19.01.39.png]]

![[Screenshot 2023-02-15 at 19.06.55.png]]
![[Screenshot 2023-02-15 at 19.12.25.png]]

![[Screenshot 2023-02-15 at 19.15.17.png]]

![[Screenshot 2023-02-16 at 9.20.52.png]]

![[Screenshot 2023-02-16 at 10.16.04.png]]
![[Screenshot 2023-02-16 at 10.30.04.png]]

![Screenshot 2023-02-17 at 8.34.28.png](file:///Users/ram/Desktop/Screenshot%202023-02-17%20at%208.34.28.png)

![[Screenshot 2023-02-17 at 8.41.12.png]]

![[Screenshot 2023-02-18 at 9.37.02.png]]

The asynchronous tasks run in the web APIs then when they are finished are passed to the callback queue and the event loop decides when each callback is executed: orchestration.
The asynchronous tasks are not part of the JavaScript language itself because JavaScript is a sinlge thread language so it cannot run multiple tasks at the same time.
When we use promises there is antoher queue (**Micrio tests queue**) that is used only for this scenario, in contrast with the callback queue which is shared with the web APIs(Event handlers...).