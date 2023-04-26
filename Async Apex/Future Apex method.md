# [Use Future Methods](https://trailhead.salesforce.com/modules/asynchronous_apex/units/async_apex_future_methods)

## Create an Apex class that uses the @future annotation to update Account records.

Create an Apex class with a method using the @future annotation that accepts a List of Account IDs and updates a custom field on the Account object with the number of contacts associated to the Account. Write unit tests that achieve 100% code coverage for the class.

* Create a field on the Account object called 'Number_of_Contacts__c' of type Number. This field will hold the total number of Contacts for the Account.
* Create an Apex class called 'AccountProcessor' that contains a 'countContacts' method that accepts a List of Account IDs. This method must use the @future annotation.
* For each Account ID passed to the method, count the number of Contact records associated to it and update the 'Number_of_Contacts__c' field with this value.
* Create an Apex test class called 'AccountProcessorTest'.
* The unit tests must cover all lines of code included in the AccountProcessor class, resulting in 100% code coverage.
* Run your test class at least once (via 'Run All' tests the Developer Console) before attempting to verify this challenge.

### Apex Class

```
public with sharing class AccountProcessor {
    @future
    public static void countContacts(List<Id> accountIds) {
        List<Account> relatedAccounts = [SELECT Id, Number_Of_Contacts__c,(Select Id FROM Contacts) FROM Account WHERE Id IN :accountIds];
        for(Account a :relatedAccounts) {
            List<Contact> relatedContacts = a.contacts;
            a.Number_Of_Contacts__c = relatedContacts.size();
        }
        update relatedAccounts;
    }
}
```

### Apex Test Class

```
@isTest
public with sharing class AccountProcessorTest {
    @isTest
    public static void countContactsTest(){
        Integer accToContactRelationAmount = 0;
        List<Account> accs = [SELECT Id, (Select Id FROM Contacts) FROM Account LIMIT 1];
        List<Id> mockId = new List<Id>();
        if(accs.size() > 0) {
            if(accs[0].contacts.size() != 0) {
                accToContactRelationAmount = accs[0].contacts.size();
            }
            
            mockId.add(accs[0].id);
        }
        Test.startTest();
            AccountProcessor.countContacts(mockId);
        Test.stopTest(); 
        if(mockId.size() > 0) {
        List<Account> accsResult = [SELECT Id, Number_of_Contacts__c FROM Account WHERE Id = :mockId[0]];
    
        if(accsResult.size() > 0) {
            System.assertEquals(Integer.valueOf(accsResult[0].Number_of_Contacts__c), accToContactRelationAmount);
        }
    }
    }

    public static testmethod void TestAccountProcessorTest() 
    {
        Account a = new Account();
        a.Name = 'Test Account';
        Insert a;

        Contact cont = New Contact();
        cont.FirstName ='Bob';
        cont.LastName ='Masters';
        cont.AccountId = a.Id;
        Insert cont;
        
        List<Id> setAccId = new List<ID>();
        setAccId.add(a.id);
 
        Test.startTest();
            AccountProcessor.countContacts(setAccId);
        Test.stopTest();
        
        Account ACC = [select Number_of_Contacts__c from Account where id = :a.id LIMIT 1];
        System.assertEquals ( Integer.valueOf(ACC.Number_of_Contacts__c) ,1);
  }
}
``` 

> Source: https://developer.salesforce.com/forums/?id=906F0000000D8hwIAC
