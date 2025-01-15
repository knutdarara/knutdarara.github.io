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
import java.util.*;

public class SplineFit {

    public static class PP {
        public double[] breaks;
        public double[][] coefs;
        public int order;

        public PP(double[] breaks, double[][] coefs, int order) {
            this.breaks = breaks;
            this.coefs = coefs;
            this.order = order;
        }
    }

    public static PP splinefit(Object... args) {
        if (args.length < 3 || args.length > 8) {
            throw new IllegalArgumentException("Invalid number of arguments.");
        }

        // Parse arguments
        double[] x = (double[]) args[0];
        double[] y = (double[]) args[1];
        Object breaksObj = args[2];
        int n = 4; // Default spline order
        boolean periodic = false;
        boolean robust = false;
        double beta = 0.5;
        Constraint constr = null;

        for (int i = 3; i < args.length; i++) {
            Object arg = args[i];
            if (arg instanceof String && arg.equals("p")) {
                periodic = true;
            } else if (arg instanceof String && arg.equals("r")) {
                robust = true;
            } else if (arg instanceof Double && (Double) arg > 0 && (Double) arg < 1) {
                beta = (Double) arg;
                robust = true;
            } else if (arg instanceof Integer && (Integer) arg > 0) {
                n = (Integer) arg;
            } else if (arg instanceof Constraint) {
                constr = (Constraint) arg;
            } else {
                throw new IllegalArgumentException("Unknown argument: " + arg);
            }
        }

        // Handle breaks
        double[] breaks;
        if (breaksObj instanceof Integer) {
            int pieces = (Integer) breaksObj;
            breaks = interpolateBreaks(x, pieces);
        } else if (breaksObj instanceof double[]) {
            breaks = (double[]) breaksObj;
        } else {
            throw new IllegalArgumentException("Invalid breaks argument.");
        }

        // Generate B-spline base
        BBase base = splinebase(breaks, n);
        int pieces = base.pieces;
        double[][] A = ppval(base, x);

        // Handle constraints
        if (constr != null) {
            double[][] B = evalcon(base, constr, periodic);
            double[][] Z = solvecon(B, constr);
            // Constraint solution here...
        }

        // Solve system and return result
        double[][] coefs = computeCoefficients(base, A);
        return new PP(breaks, coefs, n);
    }

    public static class Constraint {
        public double[] xc;
        public double[] yc;
        public double[][] cc;

        public Constraint(double[] xc, double[] yc, double[][] cc) {
            this.xc = xc;
            this.yc = yc;
            this.cc = cc;
        }
    }

    private static double[] interpolateBreaks(double[] x, int pieces) {
        double[] breaks = new double[pieces + 1];
        double step = (x[x.length - 1] - x[0]) / pieces;
        for (int i = 0; i <= pieces; i++) {
            breaks[i] = x[0] + i * step;
        }
        return breaks;
    }

    private static class BBase {
        public double[] breaks;
        public int pieces;
        public int order;
        public double[][] coefs;

        public BBase(double[] breaks, int pieces, int order, double[][] coefs) {
            this.breaks = breaks;
            this.pieces = pieces;
            this.order = order;
            this.coefs = coefs;
        }
    }

    private static BBase splinebase(double[] breaks, int n) {
        int pieces = breaks.length - 1;
        double[][] coefs = new double[pieces][n];
        for (int i = 0; i < pieces; i++) {
            coefs[i][0] = 1.0; // Initialize with B-spline coefficients
        }
        return new BBase(breaks, pieces, n, coefs);
    }

    private static double[][] ppval(BBase base, double[] x) {
        int m = x.length;
        double[][] A = new double[m][base.pieces];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < base.pieces; j++) {
                A[i][j] = evaluateBasis(base, x[i], j);
            }
        }
        return A;
    }

    private static double evaluateBasis(BBase base, double x, int piece) {
        double h = base.breaks[piece + 1] - base.breaks[piece];
        return (x - base.breaks[piece]) / h;
    }

    private static double[][] evalcon(BBase base, Constraint constr, boolean periodic) {
        // Constraint evaluation logic
        return new double[0][0];
    }

    private static double[][] solvecon(double[][] B, Constraint constr) {
        // Solve constraint system
        return new double[0][0];
    }

    private static double[][] computeCoefficients(BBase base, double[][] A) {
        // Compute coefficients for the piecewise polynomial
        return new double[0][0];
    }

    public static void main(String[] args) {
        // Example usage
        double[] x = {0, 1, 2, 3, 4};
        double[] y = {0, 1, 4, 9, 16};
        double[] breaks = {0, 2, 4};
        PP pp = splinefit(x, y, breaks);
        System.out.println("Spline Fit Complete.");
    }
}
