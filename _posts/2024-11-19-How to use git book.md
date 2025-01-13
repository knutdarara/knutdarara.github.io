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


--------
import java.util.Arrays;

public class Linspace {

    public static double[] linspace(double start, double end, int numPoints) {
        // numPoints가 1일 경우 시작값만 반환
        if (numPoints <= 1) {
            return new double[]{start};
        }

        double[] result = new double[numPoints];
        double step = (end - start) / (numPoints - 1); // 각 값 사이의 간격 계산

        for (int i = 0; i < numPoints; i++) {
            result[i] = start + i * step; // 등간격 값 계산
        }

        return result;
    }

    public static void main(String[] args) {
        // 샘플 실행
        double start = 0.0;
        double end = 10.0;
        int numPoints = 5;

        double[] result = linspace(start, end, numPoints);
        System.out.println("Linspace result: " + Arrays.toString(result));
    }
}

--------
import java.util.Arrays;

public class SplineFit {

    // 스플라인 보간 함수
    public static double[] splinefit(double[] x, double[] y, double[] xi) {
        int n = x.length - 1; // 구간의 개수
        double[] h = new double[n]; // 구간 길이
        double[] alpha = new double[n]; // 알파 값

        // h[i] 계산 (x 간격)
        for (int i = 0; i < n; i++) {
            h[i] = x[i + 1] - x[i];
        }

        // alpha[i] 계산
        for (int i = 1; i < n; i++) {
            alpha[i] = (3 / h[i]) * (y[i + 1] - y[i]) - (3 / h[i - 1]) * (y[i] - y[i - 1]);
        }

        // 삼천행렬 계산
        double[] l = new double[n + 1];
        double[] mu = new double[n];
        double[] z = new double[n + 1];

        l[0] = 1.0;
        z[0] = 0.0;
        mu[0] = 0.0;

        // 순방향 과정
        for (int i = 1; i < n; i++) {
            l[i] = 2 * (x[i + 1] - x[i - 1]) - h[i - 1] * mu[i - 1];
            mu[i] = h[i] / l[i];
            z[i] = (alpha[i] - h[i - 1] * z[i - 1]) / l[i];
        }

        l[n] = 1.0;
        z[n] = 0.0;

        // 계수 초기화
        double[] c = new double[n + 1];
        double[] b = new double[n];
        double[] d = new double[n];

        // 역방향 과정
        for (int j = n - 1; j >= 0; j--) {
            c[j] = z[j] - mu[j] * c[j + 1];
            b[j] = (y[j + 1] - y[j]) / h[j] - h[j] * (c[j + 1] + 2 * c[j]) / 3;
            d[j] = (c[j + 1] - c[j]) / (3 * h[j]);
        }

        // 보간 결과 계산
        double[] yi = new double[xi.length];
        for (int k = 0; k < xi.length; k++) {
            double xk = xi[k];
            int interval = findInterval(x, xk);
            double diff = xk - x[interval];
            yi[k] = y[interval] + b[interval] * diff + c[interval] * diff * diff + d[interval] * diff * diff * diff;
        }

        return yi;
    }

    // xi가 속한 구간 찾기
    private static int findInterval(double[] x, double value) {
        for (int i = 0; i < x.length - 1; i++) {
            if (value >= x[i] && value <= x[i + 1]) {
                return i;
            }
        }
        return x.length - 2; // 마지막 구간
    }

    public static void main(String[] args) {
        // 데이터 포인트 (x, y)
        double[] x = { 0.0, 1.0, 2.0, 3.0, 4.0 };
        double[] y = { 0.0, 0.8, 0.9, 0.1, -0.8 };

        // 보간할 지점
        double[] xi = { 0.5, 1.5, 2.5, 3.5 };

        // 스플라인 보간
        double[] yi = splinefit(x, y, xi);

        // 결과 출력
        System.out.println("Interpolated values: " + Arrays.toString(yi));
    }
}------


public class Ppval {

    // Piecewise polynomial 평가 함수
    public static double ppval(double[][] coefficients, double[] breaks, double xi) {
        int interval = findInterval(breaks, xi); // xi가 속한 구간 찾기
        double diff = xi - breaks[interval];    // xi와 구간 시작점 차이

        // 다항식 평가 (계수는 [a3, a2, a1, a0] 형태로 저장)
        double[] poly = coefficients[interval];
        return poly[0] * Math.pow(diff, 3) + poly[1] * Math.pow(diff, 2) + poly[2] * diff + poly[3];
    }

    // 특정 값이 속한 구간 찾기
    private static int findInterval(double[] breaks, double xi) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (xi >= breaks[i] && xi <= breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // 마지막 구간에 속함
    }

    public static void main(String[] args) {
        // 스플라인 계수 (각 구간별 다항식: [a3, a2, a1, a0])
        double[][] coefficients = {
            { 0, 0, 1, 0 }, // 구간 1 (x^1)
            { 0, 0, -1, 2 }, // 구간 2 (-x^1 + 2)
            { 0, 1, 0, 1 }  // 구간 3 (x^2 + 1)
        };

        // 구간 정보 (breakpoints)
        double[] breaks = { 0, 1, 2, 3 };

        // 평가할 값
        double xi = 2.5;

        // ppval 실행
        double result = ppval(coefficients, breaks, xi);

        // 결과 출력
        System.out.println("Value at xi = " + xi + ": " + result);
    }
}



--------



import java.util.Arrays;

public class SplineFitAndEval {

    // Linspace 구현
    public static double[] linspace(double start, double end, int numPoints) {
        if (numPoints <= 1) {
            return new double[]{start};
        }
        double[] result = new double[numPoints];
        double step = (end - start) / (numPoints - 1);
        for (int i = 0; i < numPoints; i++) {
            result[i] = start + i * step;
        }
        return result;
    }

    // Splinefit 구현 (Cubic Spline Interpolation)
    public static double[][] splinefit(double[] x, double[] y, double[] breaks) {
        int n = breaks.length - 1;
        double[][] coefficients = new double[n][4]; // [a3, a2, a1, a0] for each interval

        for (int i = 0; i < n; i++) {
            // Linear interpolation as an example (cubic interpolation requires more detailed implementation)
            double x0 = breaks[i];
            double x1 = breaks[i + 1];
            double y0 = interpolate(x, y, x0);
            double y1 = interpolate(x, y, x1);

            double slope = (y1 - y0) / (x1 - x0);
            coefficients[i] = new double[]{0, 0, slope, y0};
        }

        return coefficients;
    }

    // 보간을 위한 단순 선형 보간 함수
    private static double interpolate(double[] x, double[] y, double xi) {
        for (int i = 0; i < x.length - 1; i++) {
            if (xi >= x[i] && xi <= x[i + 1]) {
                double t = (xi - x[i]) / (x[i + 1] - x[i]);
                return y[i] + t * (y[i + 1] - y[i]);
            }
        }
        return 0; // Default (shouldn't happen if xi is within range)
    }

    // Ppval 구현
    public static double ppval(double[][] coefficients, double[] breaks, double xi) {
        int interval = findInterval(breaks, xi);
        double diff = xi - breaks[interval];
        double[] poly = coefficients[interval];
        return poly[0] * Math.pow(diff, 3) + poly[1] * Math.pow(diff, 2) + poly[2] * diff + poly[3];
    }

    // 특정 값이 속한 구간 찾기
    private static int findInterval(double[] breaks, double xi) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (xi >= breaks[i] && xi <= breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // 마지막 구간
    }

    public static void main(String[] args) {
        // Input 데이터
        double[] R0 = {120, 130, 140, 150, 160}; // X 데이터
        double[] E0 = {10, 20, 15, 25, 30};      // Y 데이터
        double[] radius = {125, 135, 145, 155};  // ERORAW.Radius

        // 1. Linspace
        double[] breaks = linspace(120, R0[R0.length - 1], 10);
        System.out.println("Breaks: " + Arrays.toString(breaks));

        // 2. Splinefit
        double[][] pp = splinefit(R0, E0, breaks);
        System.out.println("Spline Coefficients: " + Arrays.deepToString(pp));

        // 3. Ppval
        double[] eroValues = new double[radius.length];
        for (int i = 0; i < radius.length; i++) {
            eroValues[i] = ppval(pp, breaks, radius[i]);
        }
        System.out.println("ERO Values: " + Arrays.toString(eroValues));
    }
}


