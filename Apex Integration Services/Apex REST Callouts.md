# Apex REST Callouts

## Create an Apex class that calls a REST endpoint and write a test class.

  Create an Apex class that calls a REST endpoint to return the name of an animal, write unit tests that achieve 100% code coverage for the class using a mock response, and run your Apex tests.
* Create an Apex class:
  * Name: AnimalLocator
  * Method name: getAnimalNameById
  * The method must accept an Integer and return a String.
  * The method must call https://th-apex-http-callout.herokuapp.com/animals/<id>, replacing <id> with the ID passed into the method 
  * The method returns the value of the name property (i.e., the animal name)
* Create a test class:
  * Name: AnimalLocatorTest
  * The test class uses a mock class called AnimalLocatorMock to mock the callout response
* Create unit tests:
  * Unit tests must cover all lines of code included in the AnimalLocator class, resulting in 100% code coverage
* Run your test class at least once (via Run All tests the Developer Console) before attempting to verify this challenge
  
  ### Apex Class

```
  public with sharing class AnimalLocator {
    public static String getAnimalNameById(Integer id) {
        Http http = new Http();
        HttpRequest request = new HttpRequest();
        request.setEndpoint('https://th-apex-http-callout.herokuapp.com/animals/' + id);
        request.setMethod('GET');
        HttpResponse response = http.send(request);
        String animalName;
        // If the request is successful, parse the JSON response.
        if(response.getStatusCode() == 200) {
            // Deserializes the JSON string into collections of primitive data types.
            System.debug(response.getBody());
            Map<String, Object> results = (Map<String, Object>) JSON.deserializeUntyped(response.getBody());
            System.debug(results);
            // Cast the values in the 'animals' key as a list
            Map<String, Object> animalObj = (Map<String, Object>) results.get('animal');
            animalName = (String) animalObj.get('name');
            // animalName = string.valueof(animalObj.get('name'));
        }
        return animalName;
    }
}  
```
  
  ### Apex Test Class

```
  @isTest
public with sharing class AnimalLocatorTest {
    @isTest static void AnimalLocatorTest() {
        Test.setMock(HttpCalloutMock.class, new AnimalLocatorMock());
        String actualValue = AnimalLocator.getAnimalNameById(1);
        String expectedValue = 'chicken';
        System.assertEquals(expectedValue, actualValue);
    }
}
```
  
  ### Apex Mock Class

```
@isTest
global with sharing class AnimalLocatorMock implements HttpCalloutMock {
    global HTTPResponse respond(HTTPRequest request) {
        HttpResponse response = new HttpResponse();
        response.setHeader('Content-Type', 'application/json');
        response.setBody('{"animal":{"id":1,"name":"chicken","eats":"chicken food","says":"cluck cluck"}}');
        response.setStatusCode(200);
        return response; 
    }
}
```
