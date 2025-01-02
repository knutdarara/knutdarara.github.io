---
title: Git blog 사용법 정리
author: knutdarara
date: 2024-11-19
categories: ["git","git page"]
layout: post
---
# 처음 셋업을 위해서 필요한 정보 정리.
1. 현재 환경
   - 환경 셋업 방법 정리
2. git action
3. 테마 설정
   - 정보, 필요한 파일


import java.util.Arrays;

public class OutlierDetector {
    public static boolean[] isOutlier(double[] data, double k) {
        // Step 1: Calculate median
        double median = calculateMedian(data);

        // Step 2: Calculate MAD
        double[] absoluteDeviations = new double[data.length];
        for (int i = 0; i < data.length; i++) {
            absoluteDeviations[i] = Math.abs(data[i] - median);
        }
        double mad = calculateMedian(absoluteDeviations);

        // Step 3: Scaling constant C (pre-calculated)
        double C = 1.4826; // Pre-calculated scaling constant

        // Step 4: Identify outliers
        boolean[] outliers = new boolean[data.length];
        for (int i = 0; i < data.length; i++) {
            outliers[i] = Math.abs(data[i] - median) > k * C * mad;
        }

        return outliers;
    }

    // Helper method to calculate median
    private static double calculateMedian(double[] data) {
        double[] sortedData = Arrays.copyOf(data, data.length);
        Arrays.sort(sortedData);

        int n = sortedData.length;
        if (n % 2 == 0) {
            return (sortedData[n / 2 - 1] + sortedData[n / 2]) / 2.0;
        } else {
            return sortedData[n / 2];
        }
    }

    public static void main(String[] args) {
        double[] data = {1, 2, 3, 100, 5, 6, 7};
        double k = 3.0; // Outlier threshold (default in MATLAB)

        boolean[] outliers = isOutlier(data, k);

        System.out.println("Data: " + Arrays.toString(data));
        System.out.println("Outliers: " + Arrays.toString(outliers));
    }
}







import java.util.Arrays;

public class OutlierDetector {
    public static boolean[] isOutlier(double[] data, double k) {
        // Step 1: Calculate median
        double median = calculateMedian(data);

        // Step 2: Calculate MAD
        double[] absoluteDeviations = new double[data.length];
        for (int i = 0; i < data.length; i++) {
            absoluteDeviations[i] = Math.abs(data[i] - median);
        }
        double mad = calculateMedian(absoluteDeviations);

        // Step 3: Identify outliers
        boolean[] outliers = new boolean[data.length];
        for (int i = 0; i < data.length; i++) {
            outliers[i] = Math.abs(data[i] - median) > k * mad;
        }

        return outliers;
    }

    // Helper method to calculate median
    private static double calculateMedian(double[] data) {
        double[] sortedData = Arrays.copyOf(data, data.length);
        Arrays.sort(sortedData);

        int n = sortedData.length;
        if (n % 2 == 0) {
            return (sortedData[n / 2 - 1] + sortedData[n / 2]) / 2.0;
        } else {
            return sortedData[n / 2];
        }
    }

    public static void main(String[] args) {
        double[] data = {1, 2, 3, 100, 5, 6, 7};
        double k = 3.0; // Outlier threshold (default in MATLAB)

        boolean[] outliers = isOutlier(data, k);

        System.out.println("Data: " + Arrays.toString(data));
        System.out.println("Outliers: " + Arrays.toString(outliers));
    }
}
