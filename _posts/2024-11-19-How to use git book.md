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
   - 정보, 필요한 파일import org.apache.commons.math3.linear.*;

public class SplineFit {

    public static void main(String[] args) {
        // Test example
        double[] x = {0, 1, 2, 3, 4, 5};
        double[] y = {0, 1, 4, 9, 16, 25};
        double[] breaks = {0, 2.5, 5};

        // Call splineFit
        double[][] coefficients = splinefit(x, y, breaks);

        // Print coefficients matrix
        System.out.println("Spline Coefficients:");
        printMatrix(coefficients);
    }

    /**
     * Main spline fitting function. Fixed order = 4.
     *
     * @param x      Input x values (independent variable)
     * @param y      Input y values (dependent variable)
     * @param breaks Breakpoints for the spline
     * @return Coefficients of the spline
     */
    public static double[][] splinefit(double[] x, double[] y, double[] breaks) {
        int order = 4; // Fixed order
        return splinefitInternal(x, y, breaks, order);
    }

    /**
     * Internal spline fitting function with fixed order.
     *
     * @param x      Input x values (independent variable)
     * @param y      Input y values (dependent variable)
     * @param breaks Breakpoints for the spline
     * @param order  Order of the spline
     * @return Coefficients of the spline
     */
    private static double[][] splinefitInternal(double[] x, double[] y, double[] breaks, int order) {
        int nPieces = breaks.length - 1; // Number of spline pieces
        int nCoeffs = order;            // Number of coefficients per piece

        // Validate inputs
        if (x.length != y.length) {
            throw new IllegalArgumentException("x and y must have the same length.");
        }
        if (x.length < order) {
            throw new IllegalArgumentException("Insufficient data points for the specified order.");
        }

        // Construct the spline base matrix
        double[][] baseMatrix = splinebase(x, breaks, order);

        // Solve the linear system to find coefficients using SVD
        double[][] coefficients = lsqsolve(baseMatrix, y);

        return reshapeCoefficients(coefficients, nPieces, order);
    }

    /**
     * Constructs the spline base matrix.
     *
     * @param x      Input x values
     * @param breaks Breakpoints
     * @param order  Order of the spline
     * @return Base matrix for the spline
     */
    private static double[][] splinebase(double[] x, double[] breaks, int order) {
        int nPieces = breaks.length - 1;
        int nCoeffs = order;
        int nData = x.length;

        double[][] baseMatrix = new double[nData][nPieces * nCoeffs];

        for (int i = 0; i < nData; i++) {
            for (int p = 0; p < nPieces; p++) {
                if (x[i] >= breaks[p] && x[i] <= breaks[p + 1]) {
                    for (int k = 0; k < nCoeffs; k++) {
                        baseMatrix[i][p * nCoeffs + k] = Math.pow(x[i] - breaks[p], k);
                    }
                }
            }
        }

        return baseMatrix;
    }

    /**
     * Solves the least squares problem using SVD to compute spline coefficients.
     *
     * @param baseMatrix Base matrix from splinebase
     * @param y          Input y values
     * @return Flattened coefficients array
     */
    private static double[][] lsqsolve(double[][] baseMatrix, double[] y) {
        // Convert the baseMatrix to RealMatrix for SVD
        RealMatrix matrix = MatrixUtils.createRealMatrix(baseMatrix);
        RealVector vector = new ArrayRealVector(y);

        // Perform SVD
        SingularValueDecomposition svd = new SingularValueDecomposition(matrix);

        // Solve the least-squares problem: x = V * Sigma^-1 * U^T * y
        RealMatrix U = svd.getU();
        RealMatrix Sigma = svd.getSigma();
        RealMatrix V = svd.getV();

        // Compute Sigma^-1 * U^T * y
        RealMatrix SigmaInv = new DiagonalMatrix(1.0 / Sigma.getColumn(0)); // Just an example approach
        RealVector solutionVector = V.transpose().operate(SigmaInv.operate(U.transpose().operate(vector)));

        // Return the solution as a 2D array
        return new double[][] {solutionVector.toArray()};
    }

    /**
     * Reshapes the coefficients into a 2D array for each spline piece.
     *
     * @param coefficients Flattened coefficients array
     * @param nPieces      Number of spline pieces
     * @param order        Order of the spline
     * @return Reshaped coefficients array
     */
    private static double[][] reshapeCoefficients(double[][] coefficients, int nPieces, int order) {
        double[][] reshaped = new double[nPieces][order];
        for (int i = 0; i < nPieces; i++) {
            System.arraycopy(coefficients[0], i * order, reshaped[i], 0, order);
        }
        return reshaped;
    }

    /**
     * Helper method to print a 2D matrix.
     *
     * @param matrix The 2D matrix to print.
     */
    private static void printMatrix(double[][] matrix) {
        for (double[] row : matrix) {
            System.out.println(Arrays.toString(row));
        }
    }
}
