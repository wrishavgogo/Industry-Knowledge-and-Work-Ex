I am working on an already written algo named FrequencyBased algo , which i will create proper short notes on some future date . 

Problem Statement : 

In today's date we have 3 algoRithms , 

1. LEGACY
2. FREQUENCY_BASED_ALGO
3. LOCAL_GUIDED_SEARCH 

We have a config called 

multi.item.solution.generation.algorithm.config -> {
  "algorithmConfigurationMap": {
    "OMS": {
      "enabledAlgo": "FREQUENCY_BASED_ALGO",
      "algoListEnabledForShadowMode": ["FREQUENCY_BASED_ALGO","LEGACY" ],
      "algorithmToNotSupportedServices": {
        "LOCAL_GUIDED_SEARCH": [
          "PREFERRED_SOURCING",
          "BUNDLE",
          "MULTIHOP"
        ],
        "FREQUENCY_BASED_ALGO": [
          "BUNDLE"
        ]
      }
    }
  "misAlgoDetails": {
    "FREQUENCY_BASED_ALGO": {
      "sortOrder": [
        "DIRECTED_SOURCING",
        "FREQUENCY",
        "EDD",
        "PICKABILITY"
      ],
      "algoSteps": [
        "READER",
        "GENERATOR"
      ],
      "frequencyType": "NODE_CM",
      "maxSolutionSize": 10,
      "maxSolutionSizeForParallel": 45,
      "timeout": 600
    }
  }
}


So you see there are few attributes like "algoListEnabledForShadowMode" and "enabledAlgo" , 
So this enabledAlgo -> runs in the main thread , 

"algoListEnabledForShadowMode" ->  ["FREQUENCY_BASED_ALGO","LEGACY" ], these are submitted to an ThreadPoolExecutor which does the job for us . 
But the thing is Main thread does not wait for  ThreadPoolExecutor to complete . 

So we need to write a code such that this ThreadPoolExecutor fetches the result for the exact algo we want and compare the result between the two 



Solution : 

Noting down how did we handle it . 

First of all we modified the config json and introduced a new field 

multi.item.solution.generation.algorithm.config -> {
  "algorithmConfigurationMap": {
    "OMS": {
      "enabledAlgo": "FREQUENCY_BASED_ALGO",
      "algoListEnabledForShadowMode": ["FREQUENCY_BASED_ALGO","LEGACY" ],
      "comparisonAlog": "LEGACY"  -----> new field 
      "algorithmToNotSupportedServices": {
        "LOCAL_GUIDED_SEARCH": [
          "PREFERRED_SOURCING",
          "BUNDLE",
          "MULTIHOP"
        ],
        "FREQUENCY_BASED_ALGO": [
          "BUNDLE"
        ]
      }
    }....
......


Modified the configuration Object also , as we are using JSON to Object Mapper . 

Object :- 

@Data
@NoArgsConstructor
@AllArgsConstructor
public class MultiItemSolutionGenerationAlgorithmConfig {

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public static class AlgorithmConfiguration {
        private Algorithm enabledAlgo;
        private Algorithm comparisonAlgo;  --->  New field 
        private EnumSet<Algorithm> algoListEnabledForShadowMode = EnumSet.noneOf(Algorithm.class);
        private EnumMap<Algorithm, EnumSet<ServicesEnum>> algorithmToNotSupportedServices = new EnumMap<>(Algorithm.class);
        private Map<Algorithm, AlgorithmDetails> misAlgoDetails = new HashMap<>();
    }

    private Map<String, AlgorithmConfiguration> algorithmConfigurationMap = new HashMap<>();
    private Map<Algorithm, AlgorithmDetails> misAlgoDetails = new HashMap<>();
}


The Part where I handled the code well is 

So whichever implementation of the Algorithm we call , it returns us List<SlaBasedBestMultiItemSolutionWithDateAndCost> , 




Map<Algorithm , Future<List<SlaBasedBestMultiItemSolutionWithDateAndCost>>> algorithmToSolutionFutureMap = new HashMap<>(); -> created a map of algorithms to futures 


****** For each of the Algo we called the implementation and submitted it to the threadPoolExecutor , And threadPoolExecutor returns us the future Object of whatever List we get . 
***** so we maintain a map of the Algorithm and Future object 

Code : 
Algorithm shadowModeAlgo = shadowModeMultiItemGeneratorService.getMultiItemSolutionGenerationAlgorithm();
                Future<List<SlaBasedBestMultiItemSolutionWithDateAndCost>> futureSolutionlist = multiItemSolutionAlgoExecutor
                        .submit(() -> {
                    try {
                        return shadowModeMultiItemGeneratorService.getSlaBasedBestMultiItemSolutionWithDateAndCosts(completeOfferList,
                                finalCompleteDistributorList, finalInvAvailabilityList, finalDcCapacityList, sourcingContext, requestParameters, holidayEventInfo,
                                destinationRestrictionsList, transportationMapList,
                                finalSlaBasedSingleItemSolutionsWithDateAndCost,
                                preferredDistributors, boxes, carrierMethodLogisticConfigurations, baseDistributors,
                                carrierAccesorialConfigs, carrierAccesorialConfigsV2, distributorCapacityUtilizationCosts,
                                cmChargedByDestZip, cmExtendedAreaChargeDestZip, carrierMethodConfigs, distToConfiguredCarrierMethodMap,
                                completeCarrierMethods, nonEmptySlaBasedSingleItemSolutionsWithDate, baseCarrierMethods, businessUnit,
                                carrierCapacities, finalSlaBasedSingleItemSolutionsWithDate, distributorZipCmTntZonesFromThreadLocal,
                                carrierZoneChargesFromThreadLocal, distributorStoreCmTntZonesFromThreadLocal);
                    } catch (BusinessException e) {
                        LOG.error("Error while executing MIS generation algorithm : {} and error : {}", shadowModeMultiItemGeneratorService.getMultiItemSolutionGenerationAlgorithm().name() , e.getMessage());
                        return Collections.emptyList();
                    }
                });
                algorithmToSolutionFutureMap.put(shadowModeAlgo , futureSolutionlist);


******now we checked that if comparisonAlgo is not null , then we called the .get() method of it 


try {
                comparisonSlaBasedBestMultiItemSolutionWithDateAndCostList = algorithmToSolutionFutureMap.
                        get(sourcingContext.getComparisonAlgo()).get();
            } catch (Exception e ) {
                LOG.error("Error while executing MIS generation using comparison Algo : {} and error : {}", sourcingContext.getComparisonAlgo() , e.getMessage());

            }


Now another Java perk i learnt :- 

So 

public class SlaBasedBestMultiItemSolutionWithDateAndCost extends BaseSlaBasedSolutions {

    private MultiItemSolutionWithDateAndCost bestSolution;
    private MultiItemSolutionWithDateAndCost notChosenBestSolutionForDs;
  

public class MultiItemSolutionWithDateAndCost {

    private List<ShipmentWithDateAndCost> shipmentsWithDateAndCost;


So chain is like SlaBasedBestMultiItemSolutionWithDateAndCost -> MultiItemSolutionWithDateAndCost bestSolution -> List<ShipmentWithDateAndCost> shipmentsWithDateAndCost; 


Now for the list of SlaBasedBestMultiItemSolutionWithDateAndCost i.e List<SlaBasedBestMultiItemSolutionWithDateAndCost> , we have to fetch 


So here java Streams really comes in handy to write clean code 

int totalShipmentSizeEnabledAlgo = CollectionUtils.emptyIfNull(slaBasedBestMultiItemSolutionWithDateAndCostList).
                    stream().filter(slaBasedBestMultiItemSolutionWithDateAndCost ->
                            Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost) && Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution())
                                    && Objects.nonNull(slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution().getShipmentsWithDateAndCost()))
                    .map(slaBasedBestMultiItemSolutionWithDateAndCost -> slaBasedBestMultiItemSolutionWithDateAndCost.getBestSolution().getShipmentsWithDateAndCost())
                    .mapToInt(shipments -> shipments.size()).sum();


what here we did is , we streamed through the List<SlaBasedBestMultiItemSolutionWithDateAndCost> and we apply filter such that the item and item.getBestSolution is not null 
and then we mapped each SlaBasedBestMultiItemSolutionWithDateAndCost --> SlaBasedBestMultiItemSolutionWithDateAndCost.bestSolution.shipmentsList() 

and i got introduced to a new function called mapToInt( shipment ----> shipments.size()) -> it basically converted the thing into number and we did a .sum() 
at the end to get the sum total . 






