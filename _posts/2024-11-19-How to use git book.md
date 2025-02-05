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

public class PPVal {
    private double[] breaks;  // 구간 경계점
    private double[][] coefs; // 각 구간별 다항식 계수 (MATLAB과 동일한 형식)
    private int order;        // 다항식 차수

    public PPVal(double[] breaks, double[][] coefs) {
        this.breaks = breaks;
        this.coefs = coefs;
        this.order = coefs[0].length; // 계수 배열에서 차수 설정
    }

    public double evaluate(double x) {
        int idx = findInterval(x);

        if (idx == -1) {
            throw new IllegalArgumentException("x 값이 구간을 벗어났습니다.");
        }

        // x 값을 현재 구간의 시작점 기준으로 변환
        double x_shifted = x - breaks[idx];
        double result = 0.0;
        double[] poly = coefs[idx];

        // MATLAB과 동일하게 높은 차수부터 다항식 계산 (Horner's method)
        for (int i = 0; i < order; i++) {
            result = result * x_shifted + poly[i];
        }

        return result;
    }

    private int findInterval(double x) {
        // x가 어느 구간에 속하는지 찾기 (이진 탐색 사용 가능)
        if (x < breaks[0] || x > breaks[breaks.length - 1]) {
            return -1;
        }
        for (int i = 0; i < breaks.length - 1; i++) {
            if (x >= breaks[i] && x < breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // 마지막 경계값 처리
    }

    public static void main(String[] args) {
        // MATLAB의 spline(x, y) 결과를 기반으로 한 예제 데이터
        double[] breaks = {1.0, 2.0, 3.0, 4.0};  // 구간 경계
        double[][] coefs = {  // MATLAB과 동일한 계수 배열 (높은 차수부터 저장)
            {1.0, -0.5, 2.0, 3.0},  // 첫 번째 구간의 다항식: f(x) = 1.0*x^3 - 0.5*x^2 + 2.0*x + 3.0
            {0.5, 1.2, -1.0, 0.8},  // 두 번째 구간
            {2.3, -0.8, 0.5, 1.1}   // 세 번째 구간
        };

        PPVal pp = new PPVal(breaks, coefs);

        // MATLAB과 동일한 x 값 입력
        double[] testX = {1.5, 2.5, 3.5};
        System.out.println("Interpolated values:");
        for (double x : testX) {
            System.out.println("x = " + x + " -> " + pp.evaluate(x));
        }
    }
}












ㅁㅁㅁㅁㅁㅁ


public class PPVal {
    private double[] breaks;
    private double[][] coefs;

    public PPVal(double[] breaks, double[][] coefs) {
        this.breaks = breaks;
        this.coefs = coefs;
    }

    public double evaluate(double x) {
        int n = breaks.length - 1;  // 조각 개수
        int idx = findInterval(x, n);

        if (idx == -1) {
            throw new IllegalArgumentException("x is out of range.");
        }

        // 다항식 계수 가져오기
        double[] poly = coefs[idx];
        double result = 0.0;
        double x_shifted = x - breaks[idx]; // 현재 구간의 시작점을 기준으로 변환

        // 다항식 계산 (Horner's method 사용)
        for (int i = 0; i < poly.length; i++) {
            result = result * x_shifted + poly[i];
        }

        return result;
    }

    private int findInterval(double x, int n) {
        // x가 어느 구간에 속하는지 이진 탐색(Binary Search)
        if (x < breaks[0] || x > breaks[n]) {
            return -1;
        }
        for (int i = 0; i < n; i++) {
            if (x >= breaks[i] && x < breaks[i + 1]) {
                return i;
            }
        }
        return n - 1; // 마지막 구간
    }

    public static void main(String[] args) {
        // 예제 데이터: 조각별 다항식 (spline 보간 예제)
        double[] breaks = {1.0, 2.0, 3.0, 4.0};  // 구간 경계
        double[][] coefs = {  // 다항식 계수 (2차 다항식 예제)
            {1.0, -0.5, 2.0},  // 첫 번째 구간의 다항식: f(x) = 1.0*x^2 - 0.5*x + 2.0
            {0.5, 1.2, -1.0},  // 두 번째 구간
            {2.3, -0.8, 0.5}   // 세 번째 구간
        };

        PPVal pp = new PPVal(breaks, coefs);

        // 특정 x 값에 대해 보간된 결과 확인
        double x = 2.5;
        System.out.println("Interpolated value at x = " + x + " : " + pp.evaluate(x));
    }
}
