---
title: "[Salesforce Trailhead] Asynchronous Apex - Use Batch Apex"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Asynchronous Apex
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2023-02-01
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Asynchronous Apex](https://trailhead.salesforce.com/ko/content/learn/modules/asynchronous_apex?trail_id=force_com_dev_intermediate) 모듈의 [Use Batch Apex](https://trailhead.salesforce.com/ko/content/learn/modules/asynchronous_apex/async_apex_batch?trail_id=force_com_dev_intermediate)를 설명한다. 참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/ko/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>
요구하는 과제는 아래와 같다. <br>
![01](https://user-images.githubusercontent.com/69135840/215943990-bb8d9d5f-a25d-41dd-a54c-71c18e6ac1b2.png)<br><br>

### Batch Apex Class의 구조
Salesforce에서 Batch는 10,000개 20,000개 수준의 대량의 record를 처리할 때 사용하는 기술이다.<br>
Batch를 실행하고 테스트할 수 있는 Apex class의 기본적인 구조는 아래와 같다. <br>
![02](https://user-images.githubusercontent.com/69135840/215946180-ad2f3261-6f8c-4261-92b6-4d8136c6c9c5.png) <br><br>

먼저 Batch를 실행하려는 Apex class는 Database.Batchable<sObject>를 상속받아야하며, 위의 그림처럼
start(), execute(), finish() 메서드를 만들어야한다. 만약 하나라도 만들지 않는다면 syntax error가 발생한다.<br>
start()의 return type은 Database.QueryLocator인데, 이것은 execute()에서 작업할 record들을 정해주는 역할을 한다. 여기서는 기존 record의 내용을 수정하기 때문에 SOQL의 SELECT 구문으로 필요한 record들을 모아 리턴할 것이다. <br>
start()에서 리턴된 값은 execute()에서 List<P> records 파라미터가 된다. execute()에서 records 파라미터를 받아 가공하고, DML을 실행하여 작업한 사항을 반영한다. <br>
마지막으로 finish()에서는 batch 작업이 끝난 후에 필요한 일들 (ex. 실행결과를 로그로 보여주기)을 하게 된다. finish()메서드가 꼭 필요한 것은 아니지만 선언하지 않으면 syntax error가 발생하기 때문에 빈껍데기만이라도 만들어두긴 해야한다. <br><br>
이제, 만든 Batch class가 제대로 실행되는지 확인하기 위해 Test class를 작성해야한다. Test class는 test용 record들을 생성하는 setup()메서드와 테스트용 record를 가지고 Batch class를 돌리는 test()메서드로 구성된다. 

### Batch Apex Class
```java
//LeadProcessor.apxc
public class LeadProcessor implements Database.Batchable<sObject>, Database.Stateful {
    public Integer recordsProcessed = 0;
    // start : determine which records will you deal with by SOQL 
    public Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator(
            'SELECT LastName, LeadSource, Company FROM Lead'
        );
    }
    
    // execute : execute DML for the records you intend to do
    public void execute(Database.BatchableContext bc, List<Lead> leads){
        // process each batch of records
        for (Lead lead : leads) {
            lead.LeadSource = 'Dreamforce';
            // increment the instance member counter
            recordsProcessed = recordsProcessed + 1;
        }
        update leads;
    }
    
    // finish : review the result of the execute
    public void finish(Database.BatchableContext bc){
        System.debug(recordsProcessed + ' records processed.');
        AsyncApexJob job = [SELECT Id, Status, NumberOfErrors,
                            JobItemsProcessed,
                            TotalJobItems, CreatedById, CompletedDate, 
                            MethodName, ExtendedStatus, JobType
                            FROM AsyncApexJob
                            WHERE Id = :bc.getJobId()];
        System.debug(job);
    }
}
```
### Batch Apex Test Class
```java
//LeadProcessorTest.apxc
@isTest
public class LeadProcessorTest {
    @testSetup // prepare 200 leads for test
    static void setup() {
        List<Lead> leads = new List<Lead>();
        for (Integer i=0; i<200; i++) {
            leads.add(new Lead(LastName='Lead '+i, Company='Aethna Home Products', Status='Working - Contacted'
                              ));
        }
        insert leads;
    }
    @isTest //execute test 
    static void test() {
        //It is needed that type the Batch apex class between startTest() and stopTest() 
        Test.startTest();
        LeadProcessor leadProcessor = new LeadProcessor();
        Id batchId = Database.executeBatch(leadProcessor);
        Test.stopTest();
        // after the testing stops, assert records were updated properly
        System.assertEquals(200, [SELECT count() FROM lead WHERE LeadSource = 'Dreamforce']);
    }
}
```
Test class를 실행해보면 아래와 같이 200개의 test용 record에 대해 작업이 완료되었다는 log가 뜸을 확인할 수 있다. <br>
![03](https://user-images.githubusercontent.com/69135840/215966317-0bab4046-ab07-4a1f-a41f-6aa575ba338d.png)