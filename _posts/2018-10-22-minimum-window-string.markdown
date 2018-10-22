---
published: true
title: Minimum Window Substring
layout: post
---
Problem Statement:
Given a string S and text t. Output the smallest window in the string having all characters of the text. Both the string and text contains small case letters.

Reference URL: [Minimum Window Substring](https://practice.geeksforgeeks.org/problems/smallest-window-in-a-string-containing-all-the-characters-of-another-string/0)

Example:

Input: String: "timetopractice", Pattern: "toc" Output: "toprac"

Input: String: "zoomlazapzo", Pattern: "oza" Output: "apzo"


~~~ java
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;
import java.util.Set;

//URL: https://practice.geeksforgeeks.org/problems/smallest-window-in-a-string-containing-all-the-characters-of-another-string/0
//Description: Given a string S and text t. Output the smallest window in the string having all characters of the text. Both the string and text contains small case letters.
public class SmallestWindow {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int testCases = scanner.nextInt();

        for (int i = 0; i < testCases; i++) {
            String str = scanner.next();
            String text = scanner.next();
            System.out.println(getSmallestWindow(str, text));
        }
    }

    public static String getSmallestWindow(String longString, String pattern) {
        if (pattern.length() > longString.length()) {
            return "-1";
        }

        //left and right pointers to create sliding window
        int left = 0;
        int right = pattern.length();

        String minWindow = "-1";
        int minLen = Integer.MAX_VALUE;

        char[] chars = longString.toCharArray();
        while (right <= chars.length) {
            String substring = longString.substring(left, right);

            //if long string contain pattern, then increment left pointer until pattern is found to find minimum window string
            while (doesStringContainsPattern(substring, pattern)) {

                //keep track of minimum length of window and minimum window string found so far
                if (substring.length() < minLen) {
                    minLen = substring.length();
                    minWindow = substring;
                }
                left++;
                substring = longString.substring(left, right);
            }

            //increment right pointer
            right++;
        }

        return minWindow;
    }

    //to check whether long string contains the pattern, also check count of each character
    private static boolean doesStringContainsPattern(String str, String text) {
        Map<Character, Integer> strMap = getCharCountMap(str);
        Map<Character, Integer> textMap = getCharCountMap(text);

        Set<Character> set = textMap.keySet();

        for (Character ch : set) {
            if (!strMap.containsKey(ch) || strMap.get(ch) < textMap.get(ch)) {
                return false;
            }
        }

        return true;
    }

    //create a map of character counts in the string
    private static Map<Character, Integer> getCharCountMap(String str) {
        Map<Character, Integer> map = new HashMap<>();
        char[] chars = str.toCharArray();

        for (int i = 0; i < chars.length; i++) {
            if (map.containsKey(chars[i])) {
                map.put(chars[i], map.get(chars[i]) + 1);
            } else {
                map.put(chars[i], 1);
            }
        }

        return map;
    }

}
~~~
