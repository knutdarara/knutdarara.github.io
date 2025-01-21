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
import java.util.ArrayList;
import java.util.List;

public class PiecewisePolynomial {
    private List<double[]> coefficients; // 다항식 계수 리스트
    private double[] breaks;            // 구간 분할점

    public PiecewisePolynomial(List<double[]> coefficients, double[] breaks) {
        this.coefficients = coefficients;
        this.breaks = breaks;
    }

    public double evaluate(double x) {
        int segmentIndex = findSegmentIndex(x);
        if (segmentIndex == -1) {
            throw new IllegalArgumentException("Input x is out of the range of breaks.");
        }

        double[] coeff = coefficients.get(segmentIndex);
        double xShifted = x - breaks[segmentIndex]; // x를 해당 구간 기준으로 변환
        double result = 0;

        // Horner's method를 사용해 다항식 계산
        for (int i = 0; i < coeff.length; i++) {
            result = result * xShifted + coeff[i];
        }
        return result;
    }

    private int findSegmentIndex(double x) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (x >= breaks[i] && x <= breaks[i + 1]) {
                return i;
            }
        }
        return -1; // 범위 외
    }

    public List<Double> evaluate(double[] xValues) {
        List<Double> results = new ArrayList<>();
        for (double x : xValues) {
            results.add(evaluate(x));
        }
        return results;
    }
}

import java.util.Arrays;

public class SplineFit {

    // Main method to perform spline fitting
    public static double[][] splinefit(double[] x, double[] y, double[] breaks) {
        int n = breaks.length - 1; // Number of spline segments
        int order = 4; // Default spline order (cubic)

        // Generate B-spline basis
        double[][] basis = splinebase(breaks, order, x);

        // Solve for spline coefficients using least squares
        double[][] coefs = lsqsolve(basis, y, order);

        return coefs;
    }

    // Method to generate B-spline basis
    public static double[][] splinebase(double[] breaks, int order, double[] x) {
        int numBreaks = breaks.length;
        int numKnots = numBreaks + order - 2;
        double[] knots = new double[numKnots];

        // Generate knots (extend break points)
        System.arraycopy(breaks, 0, knots, 0, numBreaks);
        for (int i = 0; i < order - 1; i++) {
            knots[numBreaks + i] = breaks[numBreaks - 1];
        }

        int numBases = numKnots - order + 1;
        double[][] basis = new double[x.length][numBases];

        // Compute basis functions
        for (int i = 0; i < numBases; i++) {
            for (int j = 0; j < x.length; j++) {
                basis[j][i] = bsplineBasis(knots, order, i, x[j]);
            }
        }

        return basis;
    }

    // Recursive B-spline basis function
    private static double bsplineBasis(double[] knots, int order, int i, double x) {
        if (order == 1) {
            return (x >= knots[i] && x < knots[i + 1]) ? 1.0 : 0.0;
        }

        double left = (x - knots[i]) / (knots[i + order - 1] - knots[i]);
        double right = (knots[i + order] - x) / (knots[i + order] - knots[i + 1]);

        left = Double.isNaN(left) ? 0 : left;
        right = Double.isNaN(right) ? 0 : right;

        return left * bsplineBasis(knots, order - 1, i, x)
                + right * bsplineBasis(knots, order - 1, i + 1, x);
    }

    // Solve least squares problem
    public static double[][] lsqsolve(double[][] basis, double[] y, int order) {
        int m = basis.length;
        int n = basis[0].length;

        // Compute the transpose of the basis
        double[][] basisT = transpose(basis);

        // Compute A = basisT * basis
        double[][] A = multiply(basisT, basis);

        // Compute B = basisT * y
        double[] B = multiply(basisT, y);

        // Solve the linear system A * coefs = B
        return gaussianElimination(A, B);
    }

    // Transpose a matrix
    private static double[][] transpose(double[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        double[][] transposed = new double[cols][rows];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                transposed[j][i] = matrix[i][j];
            }
        }
        return transposed;
    }

    // Matrix multiplication
    private static double[][] multiply(double[][] A, double[][] B) {
        int rowsA = A.length;
        int colsA = A[0].length;
        int colsB = B[0].length;
        double[][] result = new double[rowsA][colsB];

        for (int i = 0; i < rowsA; i++) {
            for (int j = 0; j < colsB; j++) {
                for (int k = 0; k < colsA; k++) {
                    result[i][j] += A[i][k] * B[k][j];
                }
            }
        }
        return result;
    }

    // Matrix-vector multiplication
    private static double[] multiply(double[][] A, double[] B) {
        int rowsA = A.length;
        int colsA = A[0].length;
        double[] result = new double[rowsA];

        for (int i = 0; i < rowsA; i++) {
            for (int j = 0; j < colsA; j++) {
                result[i] += A[i][j] * B[j];
            }
        }
        return result;
    }

    // Solve a linear system using Gaussian elimination
    private static double[][] gaussianElimination(double[][] A, double[] B) {
        // Gaussian elimination logic here
        return new double[0][0];
    }

    public static void main(String[] args) {
        // Test the implementation with sample data
        double[] x = {0, 1, 2, 3, 4, 5};
        double[] y = {0, 1, 4, 9, 16, 25};
        double[] breaks = {0, 2.5, 5};

        double[][] coefs = splinefit(x, y, breaks);

        System.out.println("Spline coefficients:");
        for (double[] row : coefs) {
            System.out.println(Arrays.toString(row));
        }
    }
}

