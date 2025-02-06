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




ㄴㅇㄹ
public class PPVal {
    private double[] breaks;  // 구간 경계점
    private double[][] coefs; // 각 구간별 다항식 계수 (4개)
    private int order;        // 다항식 차수 (4)

    public PPVal(double[] breaks, double[][] coefs) {
        this.breaks = breaks;
        this.coefs = coefs;
        this.order = 4; // 3차 다항식 (4개 계수)
    }

    public double[] evaluate(double x) {
        int idx = findInterval(x);

        if (idx == -1) {
            throw new IllegalArgumentException("x 값이 구간을 벗어났습니다.");
        }

        // 기준 구간의 시작점 기준으로 x 이동
        double x_shifted = x - breaks[idx];
        double[] poly = coefs[idx];

        // 디버깅 출력
        System.out.printf("x = %.9f, interval = %d, x_shifted = %.9f%n", x, idx, x_shifted);
        System.out.printf("Using coefficients: [%.6f, %.6f, %.6f, %.6f]%n", poly[0], poly[1], poly[2], poly[3]);

        // 다항식 평가
        double f_x = poly[0] * Math.pow(x_shifted, 3) + poly[1] * Math.pow(x_shifted, 2) + poly[2] * x_shifted + poly[3];

        // 도함수 계산 (수정된 공식 적용)
        double d1 = 3 * poly[0] * Math.pow(x_shifted, 2) + 2 * poly[1] * x_shifted + poly[2];
        double d2 = 6 * poly[0] * x_shifted + 2 * poly[1];
        double d3 = 6 * poly[0];

        // 결과 출력
        System.out.printf("f(x) = %.9f%n", f_x);
        System.out.printf("f'(x) = %.9f%n", d1);
        System.out.printf("f''(x) = %.9f%n", d2);
        System.out.printf("f'''(x) = %.9e%n", d3);

        return new double[]{f_x, d1, d2, d3};
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
        // 주어진 데이터
        double[] breaks = {120, 122.97, 125.95, 128.92, 131.90, 134.87, 137.85, 140.93, 143.80, 146.78};

        // MATLAB의 spline 계산 결과에 맞춰 coefs 수정
        double[][] coefs = {
            {-0.0063, 0.056456, -0.16802, 0.1666666}, // 첫 번째 구간 계수
            {-0.0063, 0.056456, -0.16802, 0.1666666}, 
            {-0.0063, 0.056456, -0.16802, 0.1666666}, 
            {-0.0063, 0.056456, -0.16802, 0.1666666},
            {-0.0063, 0.056456, -0.16802, 0.1666666},
            {-0.0063, 0.056456, -0.16802, 0.1666666},
            {-0.0063, 0.056456, -0.16802, 0.1666666},
            {-0.0063, 0.056456, -0.16802, 0.1666666}
        };

        PPVal ppval = new PPVal(breaks, coefs);
        double[] result = ppval.evaluate(120.0035409);

        // 예상 값과 비교
        System.out.printf("Expected: [%.6f, %.6f, %.6f, %.6e]%n", 0.166072, 0.66665, 0.167262, 2.81E-10);
        System.out.printf("Computed: [%.6f, %.6f, %.6f, %.6e]%n", result[0], result[1], result[2], result[3]);
    }
}
