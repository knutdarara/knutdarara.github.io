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




import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

public class OutlierDetection {

    // 이동 중앙값을 계산하는 함수
    public static double[] movingMedian(double[] data, int windowSize) {
        double[] result = new double[data.length];
        int halfWindow = windowSize / 2;
        
        for (int i = 0; i < data.length; i++) {
            int start = Math.max(i - halfWindow, 0);
            int end = Math.min(i + halfWindow, data.length - 1);
            
            // 창 내의 값들을 추출하여 정렬
            double[] window = Arrays.copyOfRange(data, start, end + 1);
            Arrays.sort(window);
            
            // 중앙값 계산
            int mid = window.length / 2;
            result[i] = window[mid];
        }
        
        return result;
    }

    // 이상값을 결정하는 함수
    public static boolean[] detectOutliers(double[] data, int windowSize, double threshold) {
        double[] movingMedians = movingMedian(data, windowSize);
        boolean[] outliers = new boolean[data.length];
        
        for (int i = 0; i < data.length; i++) {
            // 중앙값에서 너무 멀리 떨어져 있으면 이상값으로 간주
            if (Math.abs(data[i] - movingMedians[i]) > threshold) {
                outliers[i] = true;
            } else {
                outliers[i] = false;
            }
        }
        
        return outliers;
    }

    public static void main(String[] args) {
        double[] data = {1, 2, 3, 4, 100, 6, 7, 8, 9, 10};
        int windowSize = 3; // 이동 평균을 계산할 창 크기
        double threshold = 15; // 중앙값에서 너무 먼 값은 이상값으로 간주

        boolean[] outliers = detectOutliers(data, windowSize, threshold);

        System.out.println("Data: " + Arrays.toString(data));
        System.out.println("Outliers: " + Arrays.toString(outliers));
    }
}




-----------------
import java.util.*;

class Baermap {
    double value; // Baermap의 값
    int index;    // 해당 값의 인덱스

    public Baermap(double value, int index) {
        this.value = value;
        this.index = index;
    }
}

public class MovMedianOutlierDetection {

    public static List<Boolean> isoutlier(List<Baermap> asdf, int windowSize) {
        List<Boolean> outliers = new ArrayList<>();
        int n = asdf.size();

        // 이동 중앙값 기반 이상치 탐지
        for (int i = 0; i < n; i++) {
            // 윈도우 범위 계산
            int start = Math.max(0, i - windowSize / 2);
            int end = Math.min(n, i + windowSize / 2 + 1);

            // 윈도우 값 추출
            List<Double> windowValues = new ArrayList<>();
            for (int j = start; j < end; j++) {
                windowValues.add(asdf.get(j).value);
            }

            // 중앙값 계산
            double median = computeMedian(windowValues);

            // 기준값 계산: 1.5 * IQR
            double iqr = computeIQR(windowValues);
            double threshold = 1.5 * iqr;

            // 현재 값과 중앙값의 차이 확인
            double currentValue = asdf.get(i).value;
            boolean isOutlier = Math.abs(currentValue - median) > threshold;

            // 결과 저장
            outliers.add(isOutlier);
        }

        return outliers;
    }

    // 중앙값 계산 함수
    private static double computeMedian(List<Double> values) {
        Collections.sort(values);
        int size = values.size();
        if (size % 2 == 0) {
            return (values.get(size / 2 - 1) + values.get(size / 2)) / 2.0;
        } else {
            return values.get(size / 2);
        }
    }

    // IQR 계산 함수
    private static double computeIQR(List<Double> values) {
        Collections.sort(values);
        double q1 = getPercentile(values, 25);
        double q3 = getPercentile(values, 75);
        return q3 - q1;
    }

    // 퍼센타일 계산 함수
    private static double getPercentile(List<Double> values, double percentile) {
        int n = values.size();
        double rank = (percentile / 100.0) * (n - 1);
        int lower = (int) Math.floor(rank);
        int upper = (int) Math.ceil(rank);
        if (lower == upper) {
            return values.get(lower);
        }
        return values.get(lower) + (values.get(upper) - values.get(lower)) * (rank - lower);
    }

    public static void main(String[] args) {
        // 샘플 데이터
        List<Baermap> data = Arrays.asList(
            new Baermap(10.0, 0),
            new Baermap(12.0, 1),
            new Baermap(14.0, 2),
            new Baermap(100.0, 3), // 이상치
            new Baermap(16.0, 4),
            new Baermap(18.0, 5),
            new Baermap(20.0, 6)
        );

        // 윈도우 크기
        int windowSize = 3;

        // 이상치 탐지
        List<Boolean> outliers = isoutlier(data, windowSize);

        // 결과 출력
        for (int i = 0; i < outliers.size(); i++) {
            System.out.println("Index " + data.get(i).index + " (Value: " + data.get(i).value + "): " + (outliers.get(i) ? "Outlier" : "Normal"));
        }
    }
}

