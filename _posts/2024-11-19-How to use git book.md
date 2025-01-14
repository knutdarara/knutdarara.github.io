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

public class CubicSplineFit {
    public static void main(String[] args) {
        // 입력 데이터
        double[] x = {1, 3, 5, 6};
        double[] y = {2, 1, 2, 3};
        int pieces = 3;

        // 3차 스플라인 계산
        SplineResult result = cubicSplineFit(x, y, pieces);

        // 결과 출력
        System.out.println("Breaks: " + Arrays.toString(result.breaks));
        System.out.println("Coefficients: " + Arrays.deepToString(result.coefficients));

        // 스플라인 평가 (예: xx = linspace(1, 6, 100))
        double[] xx = linspace(1, 6, 100);
        double[] yy = evaluateSpline(xx, result);
        System.out.println("Evaluated Values: " + Arrays.toString(yy));
    }

    // 스플라인 계산 함수
    public static SplineResult cubicSplineFit(double[] x, double[] y, int pieces) {
        int n = x.length - 1;
        double[] breaks = new double[pieces + 1];
        double[][] coefficients = new double[pieces][4];

        // 구간 분할
        for (int i = 0; i <= pieces; i++) {
            breaks[i] = x[0] + (x[x.length - 1] - x[0]) * i / pieces;
        }

        // 3차 스플라인 계산 (구간별 다항식 계수 a, b, c, d)
        for (int i = 0; i < pieces; i++) {
            double x1 = breaks[i];
            double x2 = breaks[i + 1];

            double h = x2 - x1;
            double y1 = interpolateValue(x, y, x1);
            double y2 = interpolateValue(x, y, x2);

            // 선형 근사 (a, b), c=0, d=0로 설정
            coefficients[i][0] = y1;
            coefficients[i][1] = (y2 - y1) / h;
            coefficients[i][2] = 0;
            coefficients[i][3] = 0;
        }

        return new SplineResult(breaks, coefficients);
    }

    // 특정 x 값에 대해 스플라인 평가
    public static double[] evaluateSpline(double[] xx, SplineResult spline) {
        double[] yy = new double[xx.length];
        for (int i = 0; i < xx.length; i++) {
            yy[i] = evaluatePoint(xx[i], spline);
        }
        return yy;
    }

    public static double evaluatePoint(double x, SplineResult spline) {
        double[] breaks = spline.breaks;
        double[][] coefficients = spline.coefficients;

        // 해당 x 값이 속하는 구간 찾기
        int i = 0;
        while (i < breaks.length - 1 && x > breaks[i + 1]) {
            i++;
        }

        // 계수 가져오기
        double a = coefficients[i][0];
        double b = coefficients[i][1];
        double c = coefficients[i][2];
        double d = coefficients[i][3];

        double dx = x - breaks[i];
        return a + b * dx + c * Math.pow(dx, 2) + d * Math.pow(dx, 3);
    }

    // 선형 보간법
    public static double interpolateValue(double[] x, double[] y, double xi) {
        for (int i = 0; i < x.length - 1; i++) {
            if (xi >= x[i] && xi <= x[i + 1]) {
                double t = (xi - x[i]) / (x[i + 1] - x[i]);
                return y[i] + t * (y[i + 1] - y[i]);
            }
        }
        return 0;
    }

    // linspace 구현
    public static double[] linspace(double start, double end, int num) {
        double[] result = new double[num];
        double step = (end - start) / (num - 1);
        for (int i = 0; i < num; i++) {
            result[i] = start + i * step;
        }
        return result;
    }

    // 결과 저장용 클래스
    static class SplineResult {
        double[] breaks;
        double[][] coefficients;

        SplineResult(double[] breaks, double[][] coefficients) {
            this.breaks = breaks;
            this.coefficients = coefficients;
        }
    }
}
