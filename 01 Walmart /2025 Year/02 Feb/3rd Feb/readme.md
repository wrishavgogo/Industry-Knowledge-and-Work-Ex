So Today i had a meeting with Ravi (dir)  , Chirag ( Pr Eng ) & Navin ( Manager ) .


********** 1st Explanation


We were discussing how have I implemented the evaluation of separate threads in frequency Algo . 

Explained them the thread process of how i have a Map<Algorithm , Future<SlaBasedSingleItemSolution> > map ; 


************* 2nd explanation 

Then Navin explained me how we want to change Mobius (Simulation platform ) .

We want to remove APIs and keep everything in - memory . That way we can save time and run simulations much faster . 

Why we want to do it this way is because . Of overall cost network optimisation . 

In Today's date we were doing 

for a particular order Date , take how many orders have EDD today and take the orders of them together and see how many you can batch together . 

What we want to do in future is 

for a group of order , lets say we take esd -> x 

and for future orders which might get the same edd , we check what trailers might be able to ship it together . 

Example : 

order 1  -> 

2 items 
 {
   Item1 -> node1 
   Item2 -> node2 
 }


 Future Order  :- 

 2 items  {
  Item3 -> node1 
  Item4 -> node2 
    
 }

 So you batch the (Order1 and Future Order ) :- So that it can be shipped together and trailer can satisfy both the order 
 Node1 ( order1 & future Order ) , Node2 ( order1 & future order ) .


 This batching of orders we want to make possible , for which we want to explore mobius implementation. 


 To this Navin added we could do 

 MILP -> could be used to check which order batching can be done amazingLy . 


 MILP = MILP stands for Mixed-Integer Linear Programming 

 This I need to study . 






****** Today night got review comments on the same PR , How to clean up my code , I will learn from it. 

My code : 


int totalShipmentSizeEnabledAlgo = CollectionUtils.emptyIfNull(slaBasedBestMultiItemSolutionWithDateAndCostList).stream().filter(slaBasedBestMultiItemSolutionWithDateAndCost ->
                            Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost) && Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution())
                                    && Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution().getShipmentsWithDateAndCost()))
                    .map(slaBasedBestMultiItemSolutionWithDateAndCost -> slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution().getShipmentsWithDateAndCost())
                    .mapToInt(shipments -> shipments.size()).sum();



How i was told to write the code 

 int totalShipmentSizeEnabledAlgo = CollectionUtils.emptyIfNull(slaBasedBestMultiItemSolutionWithDateAndCostList).
                    stream()
                    .filter(slaBasedSol -> Objects.nonNull(slaBasedSol))
                    .filter(slaBasedSol -> Objects.nonNull(slaBasedSol.getBestSolution()))
                    .filter(slaBasedSol -> Objects.nonNull(slaBasedSol.getBestSolution().getShipmentsWithDateAndCost()))
                    .map(slaBasedSol -> slaBasedSol.getBestSolution().getShipmentsWithDateAndCost())
                    .mapToInt(shipments -> shipments.size()).sum();


look how clear the code looks 

"Simple things are costly my friend " 



 
