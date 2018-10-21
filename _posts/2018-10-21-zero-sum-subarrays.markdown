---
published: true
title: Zero Sum Subarrays
layout: post
---
Problem Statement:
Given an array of size N , print the total count of sub-arrays having their sum equal to 0

Reference URL: [Zero Sum Subarrays](https://practice.geeksforgeeks.org/problems/zero-sum-subarrays/0)

Example:

Input: 0 0 5 5 0 0 Output: 6

Input: 6  -1 -3 4 -2 2 4 6 -12 -7 Output: 4

~~~ java
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

//URL: https://practice.geeksforgeeks.org/problems/zero-sum-subarrays/0
//Description: Given an array of size N , print the total count of sub-arrays having their sum equal to 0
public class ZeroSumSubArrays {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int testCases = scanner.nextInt();

        for (int i = 0; i < testCases; i++) {
            int arrLength = scanner.nextInt();
            int[] arr = new int[arrLength];

            for (int j = 0; j < arrLength; j++) {
                arr[j] = scanner.nextInt();
            }

            System.out.println(zeroSumSubArrays(arr));
        }
    }

    public static int zeroSumSubArrays(int[] arr) {
        Map<Integer, Integer> map = new HashMap<>();

        int sum = 0;
        int zeroSumSubArraysCount = 0;

        //add a entry in map to handle zero sum
        map.put(0, 1);

        for (int i = 0; i < arr.length; i++) {
            sum += arr[i];

            //if the sum repeats then count all the subarrays between occurences of sum
            //for example if the sum s repeats once then count = 1, but if it repeats twice then count = 1+2 = 3
            //if it repeats thrice then count=1+2+3 = 6 (n * n+1)/2 where n is the number of occurence and that's what below logic does
            if (map.containsKey(sum)) {
                zeroSumSubArraysCount += map.get(sum);
                map.put(sum, map.get(sum) + 1);
            } else {
                map.put(sum, 1);
            }

        }

        return zeroSumSubArraysCount;
    }
}
~~~
