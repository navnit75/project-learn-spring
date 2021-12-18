README

This examples shows how to destroy prototype scoped beans. A couple of changes are required

1. Create a custom bean processor. This bean processor will keep track of prototype scoped beans. During shutdown it will call the destroy() method on the prototype scoped beans.

2. The prototype scoped beans MUST implement the DisposableBean interface. This interface defines a "destory()" method. This method should be used instead of the @PostDestroy annotation.

3. In this app, BeanProcessorDemoApp.java is the main program.  TennisCoach.java is the prototype scoped bean. TennisCoach implements the DisposableBean interface and provides the destroy() method. The custom bean processing is handled in the MyCustomBeanProcessor class.

