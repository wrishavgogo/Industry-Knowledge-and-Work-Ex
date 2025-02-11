
HealthyInventory Median Cost fix :- 
While developing a feature , i had made a miss and an attribute which was required in a function to evaluate it correctly , was populating after the function was getting evaluated .
So i added the fix , but I missed that the new impl which i had done in the function and also populating in the field .

So my Principal Engineer told me to fix the it and add a Generic Method in a commonUtil class which can be used .
He showed the code reusabilty character of a programmer .
From next time onwards keep in mind always reuse code . 






Mobius Contract change . 

InsideMobiusShipmentDetails ---> List<ResponseBox> boxList ---> boxInstanceId ( which is unique for each box ) 

@Data
@AllArgsConstructor
@NoArgsConstructor
public class MobiusShipmentDetails {
    private Integer requestedQuantity;
    private String shipNode;
    private String carrierMethodId;
    private String priorityID;
    private DateTime estimatedShipDate;
    private DateTime orderProcessingDate;
    private String fcapPoolId;
    private String fcapIdInMCSE;
    private String ccapIDInMCSE;
    private DateTimeZone timeZone;
    private List<ResponseBox> boxList;

}


public class ResponseBox implements Serializable {

    /**
     * Field serialVersionUID. (value is 2155329195214341898)
     */
    private static final long serialVersionUID = 2155329195214341898L;

    /**
     * Field boxSequence.
     */
    @JsonProperty("boxSequence")
    private Integer boxSequence;

    /**
     * Field boxInstanceId.
     */
    @JsonProperty("boxInstanceId")
    private String boxInstanceId;

    /**
     * Field boxName.
     */
    @JsonProperty("boxName")
    private String boxName;
}



Now in this one i wrote the code something by using 

Collections.synchronisedSet(new HashSet<>())
And ConcurrentHashMap<>() ; ---> this marks each ( key , value ) pair thread safe . 



 Map<String , Set<String>> ccapIdToUniqueBoxInstanceIdMap = new ConcurrentHashMap<>();

        ccapIdToShipmentListMap.forEach((ccapId , mobiusShipmentDetailsList) -> {

            Set<String> boxInstanceSet = Collections.synchronizedSet(new HashSet<>());
            mobiusShipmentDetailsList.parallelStream().filter(mobiusShipmentDetails1 -> Objects.nonNull(mobiusShipmentDetails1.getBoxList()))
                    .map(mobiusShipmentDetails1 -> mobiusShipmentDetails1.getBoxList())
                    .forEach(responseBoxes ->
                            boxInstanceSet.addAll( responseBoxes.stream().filter(responseBox ->
                                    StringUtils.isNotEmpty(responseBox.getBoxInstanceId()))
                                            .map(responseBox -> responseBox.getBoxInstanceId())
                                    .collect(Collectors.toSet())));

            ccapIdToUniqueBoxInstanceIdMap.put(ccapId , boxInstanceSet);
        });


this is the code that i wrote . 

But i think groupBy and all can be done 

Chatgpt told me that GroupBy & FlatMapping can be used . 

TODO : STUDY GROUPBY , FLATMAPPING in java streams and implement examples and get a good hold of it 
                                                                               
                                                                               
