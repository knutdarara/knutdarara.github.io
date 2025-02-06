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


public class PPVal {
    private double[] breaks;  // 구간 경계점
    private double[][] coefs; // 각 구간별 다항식 계수 (4개: 3차 다항식)
    private int order;        // 다항식 차수 (4개 계수: 3차 다항식)

    public PPVal(double[] breaks, double[][] coefs) {
        this.breaks = breaks;
        this.coefs = coefs;
        this.order = 4; // Cubic spline (3차 다항식)
    }

    public double[] evaluate(double x) {
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

        // 추가로 도함수 계산 (dx/dx = 1, dx^2/dx = 2x, dx^3/dx = 3x^2)
        double d1 = 3 * poly[0] * Math.pow(x_shifted, 2) + 2 * poly[1] * x_shifted + poly[2];
        double d2 = 6 * poly[0] * x_shifted + 2 * poly[1];
        double d3 = 6 * poly[0];

        return new double[]{result, d1, d2, d3};
    }

    private int findInterval(double x) {
        if (x < breaks[0] || x > breaks[breaks.length - 1]) {
            return -1;
        }
        for (int i = 0; i < breaks.length - 1; i++) {
            if (x >= breaks[i] && x < breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2;
    }

    public static void main(String[] args) {
        // MATLAB의 pp 구조를 기반으로 한 예제 데이터
        double[] breaks = {120, 122.97, 125.95, 128.92, 131.90, 134.87, 137.85, 140.93, 143.80, 146.78};

        double[][] coefs = {
            {-0.0063, 0.0565, -0.1580, 0.1667},  // 첫 번째 구간
            {-0.0059, 0.0510, -0.1400, 0.1600},  // 두 번째 구간 (예제 추가)
            {-0.0055, 0.0460, -0.1220, 0.1530},  // 세 번째 구간 (예제 추가)
            {-0.0051, 0.0415, -0.1050, 0.1470},  // 네 번째 구간 (예제 추가)
            {-0.0047, 0.0375, -0.0890, 0.1410},  // 다섯 번째 구간 (예제 추가)
            {-0.0043, 0.0340, -0.0740, 0.1360},  // 여섯 번째 구간 (예제 추가)
            {-0.0039, 0.0309, -0.0600, 0.1310},  // 일곱 번째 구간 (예제 추가)
            {-0.0035, 0.0282, -0.0470, 0.1260},  // 여덟 번째 구간 (예제 추가)
            {-0.0031, 0.0259, -0.0350, 0.1220}   // 아홉 번째 구간 (예제 추가)
        };

        PPVal pp = new PPVal(breaks, coefs);

        // MATLAB과 동일한 x 값 입력
        double x = 120.0035409;
        double[] result = pp.evaluate(x);

        System.out.println("Interpolated values:");
        System.out.printf("f(x) = %.6f%n", result[0]);
        System.out.printf("f'(x) = %.6f%n", result[1]);
        System.out.printf("f''(x) = %.6f%n", result[2]);
        System.out.printf("f'''(x) = %.6e%n", result[3]); // 작은 값은 지수 표기
    }
}
