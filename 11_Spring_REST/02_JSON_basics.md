## What is JSON?
- JavaScript Object Notation
- Lightweight data format for storing and exchanging data … plain text
- Language independent … not just for JavaScript
- Can use with any programming language: Java, C#, Python etc…
- Ex
```json
{
    "id": 14,
    "firstName": "Mario",
    "lastName": "Rossi",
    "active": true
}
```

## JSON values
- JSON Values
- Numbers: no quotes
- String: in double quotes
- Boolean: **true**, **false**
- Nested JSON object
- Array
- **null**

## Nested JSON Objects
```json
{
    "id": 14,
    "firstName": "Mario",
    "lastName": "Rossi",
    "active": true,
    "address" : {
        "street" : "100 Main St",
        "city" : "Philadelphia",
        "state" : "Pennsylvania",
        "zip" : "19103",
        "country" : "USA"
    }
}
```

## JSON Array 
```json
{
    "id": 14,
    "firstName": "Mario",
    "lastName": "Rossi",
    "active": true,
    "languages" : ["Java", "C#", "Python", "Javascript"]
}
```

## JSON Data Binding
- Data binding is the process of converting JSON data to a Java POJO
- Also known as **Mapping**, **Serialization / Deserialization** and **Marshalling / Unmarshalling**. 
- JSON Data Binding with Jackson
- Spring uses the `Jackson Project` behind the scenes
- Jackson handles data binding between JSON and Java POJO
- Jackson Project [github link](https://github.com/FasterXML/jackson-databind)

## Jackson Data Binding
- Jackson Data Binding API
- Package: com.fasterxml.jackson.databind
- Maven Dependency
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```
- By default, Jackson will call appropriate getter/setter method

## Convert JSON to JAVA pojo
- Convert JSON to Java POJO … call setter methods on POJO
- ![](./images/setter_method_json_call.jpg)
- Jackson calls the setXXX methods 
- It does NOT access internal private fields directly

## Code Example 
```Java
import java.io.File;
import com.fasterxml.jackson.databind.ObjectMapper;
public class Driver {
    public static void main(String[] args) throws Exception {
        // create object mapper
        ObjectMapper mapper = new ObjectMapper();
        
        // read JSON from file and map/convert to Java POJO
        Student myStudent = mapper.readValue(new File("data/sample.json"), Student.class);
        
        // also print individual items
        System.out.println("First name = " + myStudent.getFirstName());
        System.out.println("Last name = " + myStudent.getLastName());
    }
}
```
## Output 
```
First name = Mario
Last name = Rossi
```

## Java POJO to JSON
- Convert Java POJO to JSON … call getter methods on POJO
```Java
// create object mapper
ObjectMapper mapper = new ObjectMapper();

// read JSON from file and map/convert to Java POJO
Student myStudent = mapper.readValue(new File("data/sample.json"), Student.class);
...
// now write JSON to output file

/// Indent the JSON output for "pretty printing"
mapper.enable(SerializationFeature.INDENT_OUTPUT);

/// Jackson calls the getter methods on Student POJO to create JSON output file
mapper.writeValue(new File("data/output.json"), myStudent);
```

## Output
```json
{
    "id": 14,
    "firstName": "Mario",
    "lastName": "Rossi",
    "active": true
}
```

## Spring and Jackson Support
- When building Spring REST applications
- Spring will automatically handle Jackson Integration
- JSON data being passed to REST controller is converted to POJO
- Java object being returned from REST controller is converted to JSON
- Happens automatically behind the scenes
