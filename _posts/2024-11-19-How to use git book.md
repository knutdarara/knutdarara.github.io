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

public class SplineFit {

    // Linspace 구현 (주어진 구간을 균등하게 나누는 함수)
    public static double[] linspace(double start, double end, int numPoints) {
        double[] result = new double[numPoints];
        double step = (end - start) / (numPoints - 1);
        for (int i = 0; i < numPoints; i++) {
            result[i] = start + i * step;
        }
        return result;
    }

    // 최소제곱법을 통한 3차 스플라인 보간
    public static double[][] splinefit(double[] x, double[] y, double[] breaks) {
        int n = breaks.length - 1; // 구간 수
        double[][] coefficients = new double[n][4]; // 각 구간에 대한 3차 다항식 계수 [a3, a2, a1, a0]

        // 3차 스플라인을 위한 최소제곱법
        double[] h = new double[x.length - 1]; // 각 구간의 길이
        double[] alpha = new double[x.length];
        
        // 각 구간 길이 계산
        for (int i = 0; i < x.length - 1; i++) {
            h[i] = x[i + 1] - x[i];
        }
        
        // alpha 값 계산
        for (int i = 1; i < x.length - 1; i++) {
            alpha[i] = (3.0 / h[i]) * (y[i + 1] - y[i]) - (3.0 / h[i - 1]) * (y[i] - y[i - 1]);
        }

        // Tridiagonal Matrix Algorithm (Thomas Algorithm)
        double[] l = new double[x.length];
        double[] mu = new double[x.length];
        double[] z = new double[x.length];
        l[0] = 1.0;
        mu[0] = 0.0;
        z[0] = 0.0;

        // tridiagonal matrix forward pass
        for (int i = 1; i < x.length - 1; i++) {
            l[i] = 2.0 * (x[i + 1] - x[i - 1]) - h[i - 1] * mu[i - 1];
            mu[i] = h[i] / l[i];
            z[i] = (alpha[i] - h[i - 1] * z[i - 1]) / l[i];
        }

        l[x.length - 1] = 1.0;
        z[x.length - 1] = 0.0;
        double[] c = new double[x.length];
        c[x.length - 1] = 0.0;

        // tridiagonal matrix back-substitution
        for (int i = x.length - 2; i >= 0; i--) {
            c[i] = z[i] - mu[i] * c[i + 1];
        }

        double[] b = new double[x.length - 1];
        double[] d = new double[x.length - 1];
        for (int i = 0; i < x.length - 1; i++) {
            b[i] = (y[i + 1] - y[i]) / h[i] - h[i] * (c[i + 1] + 2.0 * c[i]) / 3.0;
            d[i] = (c[i + 1] - c[i]) / (3.0 * h[i]);
        }

        // 각 구간의 스플라인 계수 (a, b, c, d) 저장
        for (int i = 0; i < n; i++) {
            coefficients[i][0] = d[i];
            coefficients[i][1] = c[i];
            coefficients[i][2] = b[i];
            coefficients[i][3] = y[i];
        }

        return coefficients;
    }

    // Ppval 구현: 주어진 x 값에 대한 보간값 계산
    public static double ppval(double[][] coefficients, double[] breaks, double xi) {
        int interval = findInterval(breaks, xi);
        double diff = xi - breaks[interval];
        double[] poly = coefficients[interval];
        return poly[0] * Math.pow(diff, 3) + poly[1] * Math.pow(diff, 2) + poly[2] * diff + poly[3];
    }

    // 특정 구간을 찾는 함수
    private static int findInterval(double[] breaks, double xi) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (xi >= breaks[i] && xi <= breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // 마지막 구간
    }

    // 메인 함수
    public static void main(String[] args) {
        // Input 데이터 (예시)
        double[] R0 = {120, 125, 130, 135, 140, 145}; // X 값
        double[] E0 = {10, 15, 20, 18, 22, 30};       // Y 값
        double[] radius = {121, 128, 133, 144};       // ERORAW.RADIUS (보간 대상)

        // 1. Linspace로 breaks 생성
        double[] breaks = linspace(120, 146.7823, 10);
        System.out.println("Breaks: " + Arrays.toString(breaks));

        // 2. 스플라인 보간 데이터 생성
        double[][] coefficients = splinefit(R0, E0, breaks);
        System.out.println("Coefficients: " + Arrays.deepToString(coefficients));

        // 3. ppval로 보간값 계산
        double[] eroValues = new double[radius.length];
        for (int i = 0; i < radius.length; i++) {
            eroValues[i] = ppval(coefficients, breaks, radius[i]);
        }
        System.out.println("ERO Values: " + Arrays.toString(eroValues));
    }
}

import java.util.Arrays;

public class SplineFitConverter {

    // Spline fitting logic
    public static double[] splineFit(double[] x, double[] y, int numPieces) {
        if (x.length != y.length) {
            throw new IllegalArgumentException("x and y must have the same length");
        }

        // Compute breaks (knots)
        double[] breaks = linspace(x[0], x[x.length - 1], numPieces + 1);

        // Initialize coefficients (assuming cubic spline with continuity constraints)
        int numCoeffs = (breaks.length - 1) * 4; // 4 coefficients per interval (a, b, c, d)
        double[] coeffs = new double[numCoeffs];

        // Construct and solve for coefficients (simplified version)
        for (int i = 0; i < breaks.length - 1; i++) {
            // Assuming linear interpolation between breaks for simplicity
            double xi = breaks[i];
            double xj = breaks[i + 1];

            int idx = i * 4;
            coeffs[idx] = y[i];                // a (constant term)
            coeffs[idx + 1] = 0;              // b (linear term, placeholder)
            coeffs[idx + 2] = 0;              // c (quadratic term, placeholder)
            coeffs[idx + 3] = (y[i + 1] - y[i]) / (xj - xi); // d (cubic term, slope)
        }

        return coeffs;
    }

    // Evaluate the spline at specific points
    public static double[] evaluateSpline(double[] coeffs, double[] x, double[] breaks) {
        double[] result = new double[x.length];
        int numIntervals = breaks.length - 1;

        for (int i = 0; i < x.length; i++) {
            double xi = x[i];
            for (int j = 0; j < numIntervals; j++) {
                if (xi >= breaks[j] && xi <= breaks[j + 1]) {
                    int idx = j * 4;
                    double a = coeffs[idx];
                    double b = coeffs[idx + 1];
                    double c = coeffs[idx + 2];
                    double d = coeffs[idx + 3];
                    double t = xi - breaks[j];

                    result[i] = a + b * t + c * Math.pow(t, 2) + d * Math.pow(t, 3);
                    break;
                }
            }
        }

        return result;
    }

    // Generate equally spaced points
    public static double[] linspace(double start, double end, int numPoints) {
        double[] result = new double[numPoints];
        double step = (end - start) / (numPoints - 1);
        for (int i = 0; i < numPoints; i++) {
            result[i] = start + i * step;
        }
        return result;
    }

    // Main function to test
    public static void main(String[] args) {
        // MATLAB example
        double[] x = {1, 2, 3, 4, 5, 6};
        double[] y = {2, 1, 2, 1, 2, 3};
        int numPieces = 3;

        // Spline fitting
        double[] coeffs = splineFit(x, y, numPieces);

        // Generate evaluation points
        double[] xx = linspace(1, 6, 100);

        // Breaks for the spline
        double[] breaks = linspace(1, 6, numPieces + 1);

        // Evaluate spline at xx
        double[] yy = evaluateSpline(coeffs, xx, breaks);

        // Print results
        System.out.println("Fitted Coefficients: " + Arrays.toString(coeffs));
        System.out.println("Evaluation Points (xx): " + Arrays.toString(xx));
        System.out.println("Spline Values (yy): " + Arrays.toString(yy));
    }
}

