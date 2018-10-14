---
published: true
title: Longest Common Substring
layout: post
---
Given two strings X and Y, find the length of the longest common substring.

Examples:

Input : X = "GeeksforGeeks", Y = "GeeksQuiz"

Output : 5 The longest common substring is "Geeks" and is of length 5.


Input : X = "abcdxyz", y = "xyzabcd"

Output : 4 The longest common substring is "abcd" and is of length 4.

~~~ java

import java.util.Scanner;

//URL: https://practice.geeksforgeeks.org/problems/longest-common-substring/0
//Description: Given two strings ‘X’ and ‘Y’, find the length of the longest common substring.

public class LongestCommonSubstring {

    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);
        int testCases = scanner.nextInt();

        for (int i = 0; i < testCases; i++) {
            int len1 = scanner.nextInt();
            int len2 = scanner.nextInt();

            String str1 = scanner.next();
            String str2 = scanner.next();

            System.out.println(getLongestCommonSubstringLength(str1, str2));
        }

    }

    //Logic: Length of the common string can maximum be length of the shortest string among two strings.
    //So, first find shortest among 2 strings. Start with finding the shorter string in the longer string and if not found then find the longest substring (all) of the shorter string in the longer string. Repeat this until all substrings are covered.
    public static int getLongestCommonSubstringLength(String str1, String str2) {
        int length1 = str1.length();
        int length2 = str2.length();

        String shortString = length1 > length2 ? str2 : str1;
        String longString = length1 > length2 ? str1 : str2;

        int shortStringLength = shortString.length();
        int maxLength = shortStringLength;

        while (maxLength > 0) {

            for (int i = 0; i + maxLength - 1 < shortStringLength; i++) {
                if (longString.contains(shortString.substring(i, i + maxLength))) {
                    System.out.println(shortString.substring(i, i + maxLength));
                    return maxLength;
                }
            }

            maxLength--;
        }

        return 0;
    }

}
~~~
