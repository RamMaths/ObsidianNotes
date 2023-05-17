[[Theory]] section must be reviewd after this topic.

A function has access to the variable environment (VE) of the execution context in which it was created.
**Closure** : VE attached to the function, exactly as it was at the time and place the function was created

![[Screen Shot 2023-01-19 at 11.33.27.png]]
![[Screen Shot 2023-01-19 at 11.34.28.png]]

- A closer is the closed-over variable environment of the execution context in which a function was created, even after that execution is gone;
- (less formal way) A closure gives a function access to all the variables of its parent function, even after that parent function has returned. The function keeps a reference to its outer scope, which preserves the scope chain throughout time.

We do not have to manually create closures, this a javaScript feature that happens automatically. We can't even access closed-over variables explicity. A closure is NOT a tangible  JavaScript object.