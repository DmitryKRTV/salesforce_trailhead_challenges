# Use Schedule   Method
## Create an Apex class that uses Scheduled Apex to update Lead records.
Create an Apex class that implements the Schedulable interface to update Lead records with a specific LeadSource. (This is very similar to what you did for Batch Apex.)
* Create an Apex class:
  * Name: DailyLeadProcessor
  * Interface: Schedulable
  * The execute method must find the first 200 Lead records with a blank LeadSource field and update them with the LeadSource value of Dreamforce
* Create an Apex test class:
  * Name: DailyLeadProcessorTest
  * In the test class, insert 200 Lead records, schedule the DailyLeadProcessor class to run and test that all Lead records were updated correctly
  * The unit tests must cover all lines of code included in the DailyLeadProcessor class, resulting in 100% code coverage.
* Before verifying this challenge, run your test class at least once using the Developer Console Run All feature

### Apex Class

```
public with sharing class DailyLeadProcessor implements Schedulable {
    public void execute(SchedulableContext ctx) {
        List<Lead> leads = [SELECT Id, LeadSource 
            FROM Lead
            WHERE LeadSource = ''];
        // Create a task for each opportunity in the list
        if(leads.size() > 0) {
            List<Lead> updatedLeads = new List<Lead>();
            for (Lead lead : leads) {
                lead.LeadSource = 'Dreamforce';
                updatedLeads.add(lead);
            }
            update updatedLeads;
        }
    }
}
```

### Apex Test Class

```
@isTest
public with sharing class DailyLeadProcessorTest {
    //Seconds Minutes Hours Day_of_month Month Day_of_week optional_year
    //Note: in any corn expression Day and Day_of_the_week can not be used simultaneously.
    //denotes * all and ? denotes none.
    public static String cronExp = '0 0 0 27 4 ? 2024';
    @TestSetup
    static void makeData(){
        List<Lead> leads = new List<Lead>();

        for (Integer i = 0; i < 200; i++) {
            leads.add(new Lead(lastName='lead' + i, LeadSource = '', company='net', MobilePhone='123456789'));
        }

        insert leads;
    }
    @isTest static void DailyLeadProcessorTest() {
        DailyLeadProcessor dlp = new DailyLeadProcessor();
        Test.startTest();
        String jodId = System.schedule('Udate LeadSourse Field to Dreamforce', cronExp, dlp);
        Test.stopTest();
        System.assertEquals(200, [select count() from lead where LeadSource='Dreamforce'], 'Leads were not updated');
    }
}
``` 
