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
import org.apache.commons.math3.linear.*;

public class PiecewisePolynomialSolver {
    public static void main(String[] args) {
        // 예제 데이터 (x, y 값들)
        double[] x = {0, 1, 2, 3};  // x 값들
        double[] y = {0, 1, 0, -1}; // y 값들

        // 스플라인 계수 계산
        double[][] coefficients = solvePiecewisePolynomial(x, y);

        // 결과 출력
        for (int i = 0; i < coefficients.length; i++) {
            System.out.printf("Segment %d coefficients: ", i);
            for (int j = 0; j < coefficients[i].length; j++) {
                System.out.printf("%.4f ", coefficients[i][j]);
            }
            System.out.println();
        }
    }

    public static double[][] solvePiecewisePolynomial(double[] x, double[] y) {
        int n = x.length - 1; // 구간 개수
        int degree = 3; // Cubic 스플라인 (3차)

        // 제약 조건 행렬 A 생성
        RealMatrix A = buildConstraintMatrix(x, n, degree);

        // 결과 벡터 b 생성
        RealVector b = buildConstraintVector(y, n, degree);

        // 선형 시스템 풀기
        DecompositionSolver solver = new LUDecomposition(A).getSolver();
        RealVector solution = solver.solve(b);

        // 결과를 세그먼트별로 나누어 계수 배열로 변환
        double[][] coefficients = new double[n][degree + 1];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= degree; j++) {
                coefficients[i][j] = solution.getEntry(i * (degree + 1) + j);
            }
        }

        return coefficients;
    }

    // 제약 조건 행렬 A 생성
    private static RealMatrix buildConstraintMatrix(double[] x, int n, int degree) {
        int rows = n * (degree + 1); // 총 방정식 수
        int cols = rows; // 변수 개수
        RealMatrix A = new Array2DRowRealMatrix(rows, cols);

        // 각 구간에 대한 스플라인 항목 설정
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= degree; j++) {
                A.setEntry(i * (degree + 1) + j, i * (degree + 1) + j, Math.pow(x[i], degree - j));
            }
        }

        // 제약 조건 추가 (구간 연속성, 미분 등)
        // TODO: 예를 들어 연속성 조건 (f(x0) = y0), 미분 연속성 (f'(x0) = f'(x1)) 추가

        return A;
    }

    // 결과 벡터 b 생성
    private static RealVector buildConstraintVector(double[] y, int n, int degree) {
        RealVector b = new ArrayRealVector(n * (degree + 1));

        // y 값에 따라 벡터 설정
        for (int i = 0; i < n; i++) {
            b.setEntry(i * (degree + 1), y[i]);
        }

        // 경계 조건 추가
        // TODO: 경계 조건 예시 (예: 첫 번째 및 마지막 구간의 미분 값 설정)

        return b;
    }
}
import org.apache.commons.math3.linear.*;
import java.util.*;

public class SplineFit {

    public static PiecewisePolynomial splinefit(double[] x, double[] y, double[] breaks) {
        int nBreaks = breaks.length;
        int nSegments = nBreaks - 1;
        int order = 4; // Default spline order (cubic spline)
        int nCoeffs = order * nSegments;

        // Construct the design matrix and the target vector
        RealMatrix designMatrix = constructDesignMatrix(x, breaks, order);
        RealVector targetVector = new ArrayRealVector(y);

        // Solve the least squares problem
        DecompositionSolver solver = new QRDecomposition(designMatrix).getSolver();
        RealVector coefficients = solver.solve(targetVector);

        // Split coefficients into segments
        double[][] segmentCoeffs = new double[nSegments][order];
        for (int i = 0; i < nSegments; i++) {
            for (int j = 0; j < order; j++) {
                segmentCoeffs[i][j] = coefficients.getEntry(i * order + j);
            }
        }

        return new PiecewisePolynomial(breaks, segmentCoeffs);
    }

    private static RealMatrix constructDesignMatrix(double[] x, double[] breaks, int order) {
        int nData = x.length;
        int nSegments = breaks.length - 1;
        int nCoeffs = order * nSegments;

        double[][] matrix = new double[nData][nCoeffs];

        for (int i = 0; i < nData; i++) {
            int segmentIndex = findSegmentIndex(x[i], breaks);
            double[] powers = computePowers(x[i] - breaks[segmentIndex], order);

            for (int j = 0; j < order; j++) {
                matrix[i][segmentIndex * order + j] = powers[j];
            }
        }

        return new Array2DRowRealMatrix(matrix, false);
    }

    private static int findSegmentIndex(double x, double[] breaks) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (x >= breaks[i] && x < breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // Handle edge case
    }

    private static double[] computePowers(double x, int order) {
        double[] powers = new double[order];
        powers[0] = 1.0;
        for (int i = 1; i < order; i++) {
            powers[i] = powers[i - 1] * x;
        }
        return powers;
    }

    public static class PiecewisePolynomial {
        private final double[] breaks;
        private final double[][] coefficients;

        public PiecewisePolynomial(double[] breaks, double[][] coefficients) {
            this.breaks = breaks;
            this.coefficients = coefficients;
        }

        public double evaluate(double x) {
            int segmentIndex = findSegmentIndex(x, breaks);
            double[] coeffs = coefficients[segmentIndex];
            double dx = x - breaks[segmentIndex];

            double value = 0.0;
            for (int i = coeffs.length - 1; i >= 0; i--) {
                value = value * dx + coeffs[i];
            }
            return value;
        }

        public double[] getBreaks() {
            return breaks;
        }

        public double[][] getCoefficients() {
            return coefficients;
        }
    }
}
