---
published: true
title: Kadane's Algo
layout: post
---
Problem Statement:
This is one of my favorite algorithms!
Given an array containing both negative and positive integers. Find the contiguous sub-array with maximum sum.

Reference URL: [Kadane's Algo](https://practice.geeksforgeeks.org/problems/kadanes-algorithm/0)

Example:

Input: 1 2 3 Output: 6

Input: -1 -2 -3 -4 Output: -1


~~~ java
import java.util.Scanner;

//URL: https://practice.geeksforgeeks.org/problems/kadanes-algorithm/0
//Description: Given an array containing both negative and positive integers. Find the contiguous sub-array with maximum sum.
public class Kadane {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int testCases = scanner.nextInt();

        for (int i = 0; i < testCases; i++) {
            int arrLength = scanner.nextInt();
            int[] arr = new int[arrLength];

            for (int j = 0; j < arrLength; j++) {
                arr[j] = scanner.nextInt();
            }

            System.out.println(maxSum(arr));
        }
    }

    public static int maxSum(int[] arr) {
        int sum = 0;
        int maxSum = Integer.MIN_VALUE;

        for (int i = 0; i < arr.length; i++) {
            sum += arr[i];
            //keep track of maximum sum
            maxSum = Math.max(sum, maxSum);

            //if sum is negative then reset sum
            //this ensures that we consider any sub-array which yield positive sum
            if (sum < 0) {
                sum = 0;
            }
        }

        return maxSum;
    }
}
~~~
