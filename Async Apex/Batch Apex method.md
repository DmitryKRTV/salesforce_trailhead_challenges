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
public with sharing class LeadProcessor implements Database.Batchable<SObject> {
    public Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator(
            'SELECT LeadSource FROM Lead'
        );
    }
    public void execute(Database.BatchableContext bc, List<Lead> scope){
        // process each batch of records
        List<Lead> leads = new List<Lead>();
        for (Lead lead : scope) {
                lead.LeadSource = 'Dreamforce';
                leads.add(lead);
        }
        update leads;
    }
    public void finish(Database.BatchableContext bc){
        AsyncApexJob job = [SELECT Id, Status, NumberOfErrors,
            JobItemsProcessed,
            TotalJobItems, CreatedBy.Email
            FROM AsyncApexJob
            WHERE Id = :bc.getJobId()];
    }
}
```

### Apex Test Class

```
@isTest
public with sharing class LeadProcessorTest {
    @testSetup
    static void setup() {
        List<Lead> leads = new List<Lead>();
        for (Integer i = 0; i < 200; i++) {
            leads.add(new Lead(lastName='lead' + i, company='net', MobilePhone='123456789'));
        }
        insert leads;
    }
    @isTest static void LeadProcessorTest() {
        Test.startTest();
            LeadProcessor lp = new LeadProcessor();
            Id batchId = Database.executeBatch(lp);
        Test.stopTest();
        System.assertEquals(200, [select count() from lead where LeadSource='Dreamforce']);
    }
}
``` 
