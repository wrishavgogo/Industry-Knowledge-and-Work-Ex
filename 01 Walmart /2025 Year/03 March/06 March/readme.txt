Learnings from today's extensive writting of Unit test 


1. Basic Early learning 

Whenever you dont need any object to be actually initialised , when the object is the flow of the code that you are testing the use 

@Mock
private SolutionGenerationCcmContext solutionGenerationCcmContext ; 

@Mock
private SolutionRequestBucket requestBucket;


2. If somewhere in the flow which is being tested , the mock object is being used as function call , if it is taking no args , then we can use the following syntax

Mockito.when(solutionGenerationCcmContext.getSolutionRequestBucket).thenReturn(requestBucket);



3. But in #2 there is a twist , the thing is if there is a method argument for this mock object then the syntax is a bit different 

Mockito.doNothing().when(here goes your mockObject).methodCallOfTheObject(Mockito.anyMap() , Mockito.any().... here goes your arguments ) ;


4. Another  

Lets say you have two Maps 

Map<String, Integer> expectedMap = Map.of("A", 1, "B", 2);
        Map<String, Integer> thirdMap = Map.of("B", 2, "A", 1);

assertEquals(expectedMap, thirdMap); 

Maps can be compared and no order does not matter , just checking each key value pair is there or not 




5. My principal engineer requested me to write unit tests for the code i had written for frequency based algo 

And you wont believe i caught an error while writing unit tests which I would not have caught other wise . and it was such an error that does not throw 
NPE or any exception , it makes the result ambiguous 

If you remember 

For item1 reqQty = 10 ,  n1 = 5 , n2 = 5 

so for the sis we make them as 

Sis of it will be like 

item1 -> virtualItem1 req = 5 , item1 -> virtualItem2  req = 5 

Now my code was producing virtualOffer 

for distributor 
like virtualOffer1 -> actualOffer
     virtualOffer2 -> actualOffer 

     for remapping again 


Now problem with this is 

if two items are there then key is getting overwritten 

virtualOffer1 -> actualOffer1
virtualOffer2 -> actualOffer1
virtualOffer1 -> actualOffer2
virtualOffer2 -> actualOffer2

so in the map only last one remains 

virtualOffer1 -> actualOffer2
virtualOffer2 -> actualOffer2


So to solve it I appened actualoffer to the virtualOffer as well 

virtualOffer1~actualOffer1 -> actualOffer1
virtualOffer2~actualOffer1 -> actualOffer1
virtualOffer1~actualOffer2 -> actualOffer2
virtualOffer2~actualOffer2 -> actualOffer2


Now all the keys remain and remapping does not cause any issue 

7. Also i just revised how to created on spot map 

Map<?  , ? > mp = new HashMap<>() {{ 
    put(1 , 2 );
    put(3 , 4 );
}};

easy way to put the map 

So always write unit tests 
