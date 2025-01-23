2nd day of oncall only important parts which will help my career . 



**********  1st Issue 

Fujifilm Instax Mini Film (3 Twin Packs, 60 Total Pictures) : Bundle 

1item : 20 papers for polaroid , 
Bundle(item) : 3 * 1Item = 60 pictures 


Issue : 

SourcingRequest {
    
    line1 :- item (normally in the request ) with requestedQty = 1
    line2 :- bundle item , bundleQty = 3 , bundleRequestedQty = 33 ,  TotalRequestedQty of the item = 99 

}


giving internal failure 




Solution : 


Found out check reservation does reservation only against one node , 
which was getting reserved against lMD node for line 1 in promise 


Order Id : 200012905274554 

Offer Id : E14FE2532F8C477EB3D0E3BA84248805


Bundle Offer : 
bundle ComponentQty : 3
Bundle Requested qTY : 33 
tOTAL requested QTY : 99 


{
  "edd": "2025-01-22T14:59:00.000-0800",
  "expoId": "-5-yD|-z9kX|1t_Vh|1xDgg|2DeeA|2Seuk|3d8P6|3my_E|3vg1U|7LVTN|7bxBA|8jLpJ|9TmFO|9d_MY|D2rwb|ElQ_n|JWUCC|OIg9i|OQe8n|Oq82M|QfpGm|QphTZ|RsJl3|cF_EP|dGDGp|fdm-7|h_H8p|lY8Pl|tvSBn|ubaxS|wgV7S",
  "multiItemGenerationAlgorithm": "LEGACY",
  "isV5Request": true,
  "leadTime": 0,
  "isACSEnabled": null,
  "isMixedBasketCS": false,
  "promisePath": "999901988~90154",
  "isRapidResponse": false,
  "carrierMethodId": "90154",
  "esd": "2025-01-21T15:30:00.000-0800",
  "fcapPoolId": "1",
  "shipNodeId": "999901988",
  "strategy": "SLA_TIER_BASED_PROMISE",
  "productType": null,
  "additionalItemAttributes": null,
  "strategyAlgo": "MOF"
}



Promised through National Carrier 





Check Reservation : 999901963 Qty reserved against it :- 1 



Non Bundle Offer 
Requested Quantity  : 1 



{
  "edd": "2025-01-21T14:59:00.000-0800",
  "expoId": "-5-yD|-z9kX|1t_Vh|1xDgg|2DeeA|2Seuk|3d8P6|3my_E|3vg1U|7LVTN|7bxBA|8jLpJ|9TmFO|9d_MY|D2rwb|ElQ_n|JWUCC|OIg9i|OQe8n|Oq82M|QfpGm|QphTZ|RsJl3|cF_EP|dGDGp|fdm-7|h_H8p|lY8Pl|tvSBn|ubaxS|wgV7S",
  "multiItemGenerationAlgorithm": "LEGACY",
  "isV5Request": true,
  "leadTime": 0,
  "isACSEnabled": null,
  "isMixedBasketCS": false,
  "promisePath": "999901963~100002",
  "isRapidResponse": false,
  "carrierMethodId": "100002",
  "esd": "2025-01-21T23:30:00.000-0800",
  "fcapPoolId": "2",
  "shipNodeId": "999901963",
  "strategy": "SLA_TIER_BASED_PROMISE",
  "productType": null,
  "additionalItemAttributes": null,
  "strategyAlgo": "MOF"
}



As per promise , reservation happens against only any 1 node , which happened to be LMD one for which non-bundle line came to us in the request 

But during sourcing , we saw total quantity required is 100 : Non-Bundle Requested Quantity + BundleRequested Quantity = 1 + 99 = 100 

hence we there internal failure .


Not sure if these lines are expected from Upstream or not . 



Fix : {
    
    Add a logic in promise that , if any of the request Promise Line has virtual pack , dont send additional PromiseDetails so that place order reservation does not happen 

}



*********  2nd Issue 

Issue Reported : 
we are observing L2 Read-Repair cache exception spikes


My Learnings : 

I know that L2 caches are used in our application , 
message id ---> Mcse response 


Now this L2 cache is maintained by apps called "middas"
Midas is where our L2 cache hits go . 


Todo : 
Learning more about : spade, preso, middas 

What are these Apps and how does this Application interact with our application 








