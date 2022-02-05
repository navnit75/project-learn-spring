## Interaction
```
            TargetObject
@Before -------------------+
                           |
                           v
                public void doSomeStuff(){
                    ....
                }   |
                    |
@After<-------------+
(Runs after a method is completed. Regardless of the outcome / exceptions)
```
- Works just like `finally` block