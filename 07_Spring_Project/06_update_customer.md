## Process Steps 
1. Update list-customers.jsp
    - New Update link 
2. Create customer-form.jsp 
    - Prepopulate the form 
3. Process form data
    - Controller -> Service -> DAO

## Things to remember 
- When using forms in jsp with the model parameters
- When form is loaded the SpringMVC automatically calls the `Getters` for the model object. 
- When form is submitted the SpringMVC automatically calls the `Setters` for the model object. 

## Now in our implementation\
- We are trying to use the same page for update as well as saving the new customer. 
- Which means , SUBMIT button has two functionality 
    - Update the data  OR 
    - Save the data
- Hibernate provides various functionalities 
    - save(...): INSERT new record
    - update(...): UPDATE existing record
    - **saveOrUpdate(...)**
        - if (primary key is empty) INSERT new CUSTOMER 
        - else UPDATE existing CUSTOMER