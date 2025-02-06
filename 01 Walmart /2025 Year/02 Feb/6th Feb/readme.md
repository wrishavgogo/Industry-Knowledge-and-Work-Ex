
1. 
So today during deployement my manager told me to very some requests in canary before promoting it to prod primary . 

I asked him , how to do it .
He said add a header 

color:green 



2. Chirag wrote sql queries for 


 select * from `wmt-edw-prod.US_SUPPLY_CHAIN_SCT_NONCAT_VM.CONSOLIDATION_SOL_DETAILS` cs 
where cs.DATA.consolidationSolDetails_chosenAlgo like 'LEGACY~%'  and DATA.orderDate > TIMESTAMP("2025-02-06 11:55:00+00")
and CAST(DATA.consolidationSolDetails_bestAlgo AS NUMERIC) > 0; 



CAST(DATA.consolidationSolDetails_bestAlgo ---> this function was used to convert String into number , and those which were not in number were not considered 
DATA.orderDate > TIMESTAMP("2025-02-06 11:55:00+00") ...  

this "2025-02-06 11:55:00+00" --> time is in UTC .



3. In this table we found an DATA.orderNo field which gave us value like 00507ff616a9bc28e3c5b86e8c54ad11bcda6e48900918755f00

   we can convert this to get promise request by using after first two zeroes add - and last two zereos add -
   00-507ff616a9bc28e3c5b86e8c54ad11bcda6e48900918755f-00

   and from left  "-" , count to 16 letters and put another "-"
   00-507ff616a9bc28e3c5b86e8c54ad11bc-da6e48900918755f-00


   but this in omni promise trace by gscope and you will get the v5 request 






