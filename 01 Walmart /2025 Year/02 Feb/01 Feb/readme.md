I have been assigned with a task of Frequency Algo generation

This algo is responsible for creating MIS from sis.

lets say there are 5 items in the basket . ( i1 , i2 , i3 , i4 , i5)

(dccm)1 ( can serve ) ( i1 , i2 , i3)
(dccm)2 ( can serve ) ( i2 , i4 ) 
(dccm)3 (can serve) ( i4 , i5 ) 
(dccm)4 (can serve) (i1 , i4 )

Now in today's date how the solution is generated , is by creating all the possible combinations of (dc~cm)i

Basically generating power set and checking which subset is satisfying all items

(This is a high computation algo )

To replace this we have brought in place , Frequency Algo ,

What frequency Algo does is .

It creates a frequencyMap of DC~CM ---> #iTEMS it can fulfill .

Step 1 : so we get a sorted map by frequency ( descending to ascending order )

Step 2 : Pick the dc~cm with largest frequency,

Step 3 : remove all the items satisfied by it from the list and again create the frequency map .

Step 4 : Just this time , pick the item id which can be satisfied by only 1 dc~cm . ( terminal node )

Step 5 : Repeat from step 1 untill all items are satisfied
