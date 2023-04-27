# Use Batch Method

## Create a Queueable Apex class that inserts Contacts for Accounts.
Create a Queueable Apex class that inserts the same Contact for each Account for a specific state.
* Create an Apex class:
  * Name: AddPrimaryContact
  * Interface: Queueable
  * Create a constructor for the class that accepts as its first argument a Contact sObject and a second argument as a string for the State abbreviation
  * The execute method must query for a maximum of 200 Accounts with the BillingState specified by the State abbreviation passed into the constructor and insert the Contact sObject record associated to each Account. Look at the sObject clone() method.
* Create an Apex test class:
  * Name: AddPrimaryContactTest
  * In the test class, insert 50 Account records for BillingState NY and 50 Account records for BillingState CA
  * Create an instance of the AddPrimaryContact class, enqueue the job, and assert that a Contact record was inserted for each of the 50 Accounts with the BillingState of CA
  * The unit tests must cover all lines of code included in the AddPrimaryContact class, resulting in 100% code coverage
* Before verifying this challenge, run your test class at least once using the Developer Console Run All feature

### Apex Class

```
public with sharing class AddPrimaryContact implements Queueable {
    private Contact curContact;
    private String curState;
    public AddPrimaryContact (Contact contact, String state) {
        this.curContact = contact;
        this.curState = state;
    }
    public void execute(QueueableContext context) {
        List<Account> accounts = [SELECT id FROM Account WHERE BillingState = :curState LIMIT 200];
        List<Contact> toUpdate = new List<Contact>();
        for (Account acc: accounts) {
            Contact tempContact = curContact.clone();
            tempContact.AccountId = acc.id;
            toUpdate.add(tempContact);
        }
        insert toUpdate;
    }
}
```

### Apex Test Class

```
@isTest
public with sharing class AddPrimaryContactTest {
    @TestSetup
    static void makeData(){
        List <Account> accounts = new List<Account>();
        for (Integer i = 0; i < 50; i++) {
            accounts.add(new Account(Name='TestAccNY' + i, BillingState = 'NY'));
        }
        for (Integer i = 0; i < 50; i++) {
            accounts.add(new Account(Name='TestAccCA' + i, BillingState = 'CA'));
        }
        INSERT accounts;
    }
    @isTest static void AddPrimaryContactTest() {
        Contact cont = new Contact(LastName='testContact');
        AddPrimaryContact apc = new AddPrimaryContact(cont, 'CA');
        Test.startTest();
        System.enqueueJob(apc);
        Test.stopTest();
        System.assertEquals(50, [SELECT count() FROM Contact WHERE LastName='testContact']);
    }
}
``` 
