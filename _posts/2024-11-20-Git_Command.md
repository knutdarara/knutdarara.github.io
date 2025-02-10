---
title: 자주 쓰는 git 명령어
author: knutdarara
date: 2024-11-20
categories: ["git","command"]
layout: post
---

# Git 자주쓰는 명령어
1. 

2. 


import java.util.Arrays;

public class SplineFitJava {
    // PP 클래스는 앞서 정의한 것과 동일하게 사용
    public static class PP {
        double[] breaks;
        double[][] coefs;

        public PP(double[] breaks, double[][] coefs) {
            this.breaks = breaks;
            this.coefs = coefs;
        }
    }

    // ppval 함수는 앞서 정의한 것과 동일하게 사용
    public static double[][] ppval(PP pp, double[] x) {
        double[] breaks = pp.breaks;
        double[][] coefs = pp.coefs;
        int pieces = breaks.length - 1;
        int order = coefs[0].length;

        double[][] y = new double[x.length][order]; // x.length * 4

        for (int i = 0; i < x.length; i++) {
            // 해당 x 값이 속한 구간 찾기
            int piece = findPiece(breaks, x[i]);

            // 구간 내에서의 상대적 위치 계산
            double dx = x[i] - breaks[piece];

            // 다항식 평가
            for (int j = 0; j < order; j++) {
                double value = 0;
                for (int k = order - 1; k >= 0; k--) {
                    value = value * dx + coefs[piece][k];
                }
                y[i][j] = value;
            }
        }

        return y;
    }

    private static int findPiece(double[] breaks, double x) {
        for (int i = 0; i < breaks.length - 1; i++) {
            if (x >= breaks[i] && x < breaks[i + 1]) {
                return i;
            }
        }
        return breaks.length - 2; // x가 마지막 break보다 크거나 같은 경우
    }

    // histc 함수를 Java로 구현
    public static int[] histc(double[] x, double[] breaks) {
        int[] ibin = new int[x.length];
        for (int i = 0; i < x.length; i++) {
            if (x[i] < breaks[1]) {
                ibin[i] = 0;
            } else if (x[i] >= breaks[breaks.length - 2]) {
                ibin[i] = breaks.length - 2;
            } else {
                for (int j = 1; j < breaks.length - 1; j++) {
                    if (x[i] >= breaks[j] && x[i] < breaks[j + 1]) {
                        ibin[i] = j;
                        break;
                    }
                }
            }
        }
        return ibin;
    }

    // sparse 행렬을 Java로 구현 (간단한 형태)
    public static class SparseMatrix {
        int rows;
        int cols;
        double[][] data;

        public SparseMatrix(int rows, int cols) {
            this.rows = rows;
            this.cols = cols;
            this.data = new double[rows][cols];
        }

        public void set(int row, int col, double value) {
            this.data[row][col] = value;
        }

        public double get(int row, int col) {
            return this.data[row][col];
        }
    }

    public static void main(String[] args) {
        // 예시 데이터
        double[] breaks = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
        double[] x = {0.5, 1.5, 2.5, 3.5, 4.5, 5.5, 6.5, 7.5, 8.5};
        double[] y = {1, 2, 3, 4, 5, 6, 7, 8, 9};
        int pieces = 9;
        int n = 4;

        // splinebase 함수 호출 (구현은 생략)
        PP base = new PP(breaks, new double[pieces][n]); // splinebase(breaks, n);
        double[][] A_values = ppval(base, x);

        // histc 함수 호출
        double[] breaks_for_histc = Arrays.copyOfRange(breaks, 1, breaks.length - 1);
        double[] extended_breaks = new double[breaks_for_histc.length + 2];
        extended_breaks[0] = Double.NEGATIVE_INFINITY;
        System.arraycopy(breaks_for_histc, 0, extended_breaks, 1, breaks_for_histc.length);
        extended_breaks[extended_breaks.length - 1] = Double.POSITIVE_INFINITY;
        int[] ibin = histc(x, extended_breaks);

        // Sparse matrix 생성
        int mx = x.length;
        int[][] ii = new int[n][mx];
        for (int j = 0; j < mx; j++) {
            ii[0][j] = ibin[j];
            for (int k = 1; k < n; k++) {
                ii[k][j] = ii[k - 1][j] + 1;
            }
        }

        int rows = pieces + n - 1;
        SparseMatrix sparseMatrix = new SparseMatrix(rows, mx);

        for (int j = 0; j < mx; j++) {
            for (int k = 0; k < n; k++) {
                int row = ii[k][j];
                sparseMatrix.set(row, j, A_values[j][k]); // A_values[j][k] 사용
            }
        }

        // 결과 확인 (sparse matrix의 일부 값 출력)
        System.out.println("Sparse Matrix:");
        for (int i = 0; i < Math.min(rows, 5); i++) {
            for (int j = 0; j < Math.min(mx, 5); j++) {
                System.out.print(sparseMatrix.get(i, j) + " ");
            }
            System.out.println();
        }
    }
}
