---
published: true
title: Longest consecutive sub-sequence
layout: post
---
Problem Statement:
Given an array of integers, find the length of the longest sub-sequence such that elements in the sub-sequence are consecutive integers, the consecutive numbers can be in any order.

Reference URL: [Longest consecutive sub-sequence](https://practice.geeksforgeeks.org/problems/longest-consecutive-subsequence/0)

Example:

Input: 2 6 1 9 4 5 3 Output: 6

Input: 1 9 3 10 4 20 2 Output: 4

~~~ java
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

//URL: https://practice.geeksforgeeks.org/problems/longest-consecutive-subsequence/0
//Description: Given an array of integers, find the length of the longest sub-sequence
//such that elements in the sub-sequence are consecutive integers, the consecutive numbers can be in any order.

public class LongestConsecutiveSubSequence {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        int testCases = scanner.nextInt();
        for (int i = 0; i < testCases; i++) {
            int arrLength = scanner.nextInt();
            int[] arr = new int[arrLength];

            for (int j = 0; j < arrLength; j++) {
                arr[j] = scanner.nextInt();
            }

            System.out.println(getLengthOfLongestConsecutiveSubSequence(arr));
        }
    }

    //Logic: is to add all array elements to map
    //Then iterate array, and for any element, increment by 1 and find in map, decrement by 1 and find in map
    //increment counter if found, and remove entry from hashmap if found
    //keep track of max count and return once all elements are iterated
    public static int getLengthOfLongestConsecutiveSubSequence(int[] arr) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        int count = 0, maxCount = 0, key = 0;

        for (int i = 0; i < arr.length; i++) {
            map.put(arr[i], 1);
        }

        for (int i = 0; i < arr.length; i++) {
            key = arr[i];
            count = 1;
            map.remove(key);

            //keep on adding 1 to key and incrementing counter if found
            while (true) {
                ++key;
                if (map.containsKey(key)) {
                    count++;
                    map.remove(key);
                } else {
                    break;
                }
            }

            key = arr[i];
            //keep on subtracting 1 from key and incrementing counter if found
            while (true) {
                --key;
                if (map.containsKey(key)) {
                    count++;
                    map.remove(key);
                } else {
                    break;
                }
            }

            maxCount = Math.max(maxCount, count);
        }

        return maxCount;
    }
}
~~~
