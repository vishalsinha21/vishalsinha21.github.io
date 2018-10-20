---
published: true
title: Array Subset of another array
layout: post
---
Problem Statement:
Given two arrays: arr1[0..m-1] of size m and arr2[0..n-1] of size n. Task is to check whether arr2[] is a subset of arr1[] or not. Both the arrays can be both unsorted or sorted. It may be assumed that elements in both array are distinct.

Reference URL: [Array Subset](https://practice.geeksforgeeks.org/problems/array-subset-of-another-array/0)

Example:

Input: arr1: 11 1 13 21 3 7 arr2: 11 3 7 1 Output: Yes

Input: arr1: 10 5 2 23 19 arr2: 19 5 3 Output: No


~~~ java
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

//URL: https://practice.geeksforgeeks.org/problems/array-subset-of-another-array/0
//Description: Given 2 array, check whether arr2[] is a subset of arr1[] or not
public class ArraySubset {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int testCases = scanner.nextInt();

        for (int i = 0; i < testCases; i++) {
            int arr1Length = scanner.nextInt();
            int arr2Length = scanner.nextInt();

            int[] arr1 = new int[arr1Length];
            int[] arr2 = new int[arr2Length];

            for (int j = 0; j < arr1Length; j++) {
                arr1[j] = scanner.nextInt();
            }
            for (int j = 0; j < arr2Length; j++) {
                arr2[j] = scanner.nextInt();
            }

            System.out.println(isSubset(arr1, arr2));
        }
    }

    //Logic: put first array arr1 in map. Then iterate arr2 and for each element check if it is present in map.
    //if even a single element is not present then return No, else Yes
    public static String isSubset(int[] arr1, int[] arr2) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < arr1.length; i++) {
            map.put(arr1[i], 1);
        }

        for (int i = 0; i < arr2.length; i++) {
            if (!map.containsKey(arr2[i])) {
                return "No";
            }
        }

        return "Yes";
    }
}
~~~
