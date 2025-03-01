
While discussing a low level design question , with Mayank , I was showing Mayank the frequency Algo approach and showed him how Custom Comparators and Composite Comparators 
were written . 


We both got a doubt that how is the composite comparator working , isn't it like sort the whole array first by first comparator then 2nd comparator and then third comparator . 

The doubt of us got clear , sharing this whatsapp convo among us to make me remeber how the doubt got cleared 

return (nf1, nf2) -> {
    for (Comparator<NodeFrequency> comparator : comparators) {
        int result = comparator.compare(nf1, nf2);
        if (result != 0) {  // If any comparator determines an order, return it
            return result;
        }
    }
    return 0; // If all comparators return 0, objects are considered equal
}; 


bhai ye sorting order isliye maintain ho rha kyunki code mai hum 


aisa kuch kr rhe hai 

(a , b ) -> {
    
    if(a.first == b.first ) {
        then a.second wala comparison 
            else a.third wala comparison

    }
        return result
}


hum confuse ho rhe the cause hume lg rha tha 


arrays.sort(new comparator1() );
arrays.sort(new comparator2() );
arrays.sort(new comparator3() );

but actually ho rha 

(a , b ) -> {
    
    apply list of comparators on these two items 
        first place where they mismatch 
            return result 
        else go to next comparator
}



Then i wrote my own customComposite Comparator and i also learnt a new and easier way of definiing a comparator  , let me show you 

Java code below 


package com.java.trellomachinecoding.service;

import lombok.AllArgsConstructor;
import lombok.Data;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

@Data
@AllArgsConstructor
public class CustomComparatorTest {

    private int first;
    private int second;
    private int third;
    private int fourth;


    public static void main(String[] args) {

        List<CustomComparatorTest> objectList = new ArrayList<>();
        objectList.add(new CustomComparatorTest(1 , 2 , 3 , 4));
        objectList.add(new CustomComparatorTest(2 , 3 , 2 , 1));
        objectList.add(new CustomComparatorTest(5 , 2 , 1 , 9));
        objectList.add(new CustomComparatorTest(12 , 2 , 3 , 4));
        objectList.add(new CustomComparatorTest(4 , 2 , 1 , 10));

        objectList.sort(getCompositeComparator());

        System.out.println(objectList);
    }

    /***
     * This function defines my customComparator Class where am basically
     * trying to create a composite comparator , and then returning the object
     * ***/
    public static Comparator<CustomComparatorTest> getCompositeComparator() {

        /**
         * This is just the arrayList where we are creating and adding all the customers
         * **/
        List<Comparator> customComparatorList = new ArrayList<>();
        /**
         * This is  a new way I have discovered to write a comparator
         * its basically Comparator.comparing(R Function(T t)) ---> this is the format of writing it just
         * supply it the function and it will do the work for you
         * **/
        customComparatorList.add(Comparator.comparing(CustomComparatorTest :: getFirst));
        /****
         * Below i have written the lambda and above i have written using method reference
         * now you see , why sometimes method reference makes the code looks cleaner sometimes
         * */
        customComparatorList.add(Comparator.comparing((CustomComparatorTest obj) -> {
            return obj.getSecond();
        }));
        customComparatorList.add(Comparator.comparing(CustomComparatorTest :: getThird));
        customComparatorList.add(Comparator.comparing(CustomComparatorTest :: getFourth));

        // here we will write the lambda function
        Comparator compositeComparator = (obj1 , obj2 ) -> {

            for(Comparator comparator : customComparatorList) {

                int result = comparator.compare(obj1 , obj2);
                if(result != 0 ) {
                    return result;
                }
            }

            return 0;
        };

        return compositeComparator;
    }
}


