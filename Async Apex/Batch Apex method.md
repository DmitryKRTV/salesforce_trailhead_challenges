# Use Batch Method

## Create an Apex class that implements the Database.Batchable interface to update all Lead records in the org with a specific LeadSource.
* Create an Apex class:
  * Name: LeadProcessor
  * Interface: Database.Batchable
  * Use a QueryLocator in the start method to collect all Lead records in the org
  * The execute method must update all Lead records in the org with the LeadSource value of Dreamforce
* Create an Apex test class:
  * Name: LeadProcessorTest
  * In the test class, insert 200 Lead records, execute the LeadProcessor Batch class and test that all Lead records were updated correctly
  * The unit tests must cover all lines of code included in the LeadProcessor class, resulting in 100% code coverage
* Before verifying this challenge, run your test class at least once using the Developer Console Run All feature

### Apex Class

```
```

### Apex Test Class

```
``` 

> Source: https://developer.salesforce.com/forums/?id=906F0000000D8hwIAC
