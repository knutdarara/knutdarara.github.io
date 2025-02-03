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

public class SplineBase {

    public static class PiecewisePolynomial {
        double[] breaks;
        double[][] coefs;
        int order;

        public PiecewisePolynomial(double[] breaks, double[][] coefs, int order) {
            this.breaks = breaks;
            this.coefs = coefs;
            this.order = order;
        }

        public double[] getBreaks() {
            return breaks;
        }

        public double[][] getCoefs() {
            return coefs;
        }

        public int getOrder() {
            return order;
        }
    }

    public static PiecewisePolynomial splineBase(double[] breaks, int n) {
        breaks = Arrays.copyOf(breaks, breaks.length);   // Breaks
        double[] breaks0 = Arrays.copyOf(breaks, breaks.length); // Initial breaks
        double[] h = new double[breaks.length - 1];
        for (int i = 0; i < h.length; i++) {
            h[i] = breaks[i + 1] - breaks[i];  // Spacing
        }
        int pieces = h.length;
        int deg = n - 1;

        // Extend breaks periodically
        if (deg > 0) {
            double[] hcopy;
            if (deg <= pieces) {
                hcopy = Arrays.copyOf(h, h.length);
            } else {
                hcopy = new double[deg];
                for (int i = 0; i < deg; i++) {
                    hcopy[i] = h[i % pieces];
                }
            }

            // To the left
            double[] hl = new double[deg];
            for (int i = 0; i < deg; i++) {
                hl[i] = hcopy[hcopy.length - 1 - i];
            }
            double[] bl = new double[deg];
            bl[0] = breaks[0] - Arrays.stream(hl).sum();
            for (int i = 1; i < deg; i++) {
                bl[i] = bl[i - 1] - hl[i];
            }

            // To the right
            double[] hr = Arrays.copyOfRange(hcopy, 0, deg);
            double[] br = new double[deg];
            br[0] = breaks[pieces - 1] + Arrays.stream(hr).sum();
            for (int i = 1; i < deg; i++) {
                br[i] = br[i - 1] + hr[i];
            }

            // Add breaks
            breaks = new double[breaks.length + 2 * deg];
            System.arraycopy(bl, 0, breaks, 0, deg);
            System.arraycopy(breaks0, 0, breaks, deg, pieces);
            System.arraycopy(br, 0, breaks, deg + pieces, deg);
            h = new double[breaks.length - 1];
            for (int i = 0; i < h.length; i++) {
                h[i] = breaks[i + 1] - breaks[i];
            }
            pieces = h.length;
        }

        // Initiate polynomial coefficients
        double[][] coefs = new double[n * pieces][n];
        for (int i = 0; i < coefs.length; i += n) {
            coefs[i][0] = 1;
        }

        // Expand h
        int[][] ii = new int[deg + 1][pieces];
        for (int i = 0; i < pieces; i++) {
            ii[0][i] = i + 1;
        }
        for (int i = 1; i <= deg; i++) {
            for (int j = 0; j < pieces; j++) {
                ii[i][j] = ii[i - 1][j] + 1;
            }
        }
        int[] H = new int[ii.length * ii[0].length];
        int idx = 0;
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                H[idx++] = h[ii[i][j] - 1];
            }
        }

        // Recursive generation of B-splines
        for (int k = 2; k <= n; k++) {
            // Antiderivatives of splines
            for (int j = 0; j < k - 1; j++) {
                for (int i = 0; i < coefs.length; i++) {
                    coefs[i][j] = coefs[i][j] * H[i] / (k - j);
                }
            }

            double[] Q = new double[coefs.length];
            for (int i = 0; i < coefs.length; i++) {
                Q[i] = Arrays.stream(coefs[i]).sum();
            }

            double[][] QMat = new double[n][pieces];
            int col = 0;
            for (int i = 0; i < Q.length; i++) {
                QMat[i % n][col] = Q[i];
                if (i % n == n - 1) {
                    col++;
                }
            }

            double[] c0 = new double[Q.length + deg];
            for (int i = 0; i < deg; i++) {
                c0[i] = 0;
            }
            System.arraycopy(QMat[0], 0, c0, deg, QMat[0].length);

            // Normalize by max value
            double[] fmax = new double[Q.length];
            for (int i = 0; i < Q.length; i++) {
                fmax[i] = Q[i];
            }
            for (int j = 0; j < k; j++) {
                for (int i = 0; i < coefs.length; i++) {
                    coefs[i][j] = coefs[i][j] / fmax[i];
                }
            }

            // Differentiation of adjacent antiderivatives
            for (int i = 0; i < coefs.length - deg; i++) {
                for (int j = 0; j < k; j++) {
                    coefs[i][j] = coefs[i][j] - coefs[deg + i][j];
                }
            }
        }

        // Scale coefficients
        double[] scale = new double[H.length];
        Arrays.fill(scale, 1);
        for (int k = 0; k < n - 1; k++) {
            for (int i = 0; i < H.length; i++) {
                scale[i] = scale[i] / H[i];
            }
            for (int i = 0; i < coefs.length; i++) {
                coefs[i][n - k - 1] = scale[i] * coefs[i][n - k - 1];
            }
        }

        pieces = pieces - 2 * deg;

        // Create piecewise polynomial
        return new PiecewisePolynomial(breaks0, coefs, n);
    }

    public static void main(String[] args) {
        // Test the function
        double[] breaks = {0, 1, 2, 3, 4};
        int n = 4;
        PiecewisePolynomial pp = splineBase(breaks, n);
        
        System.out.println("Breaks: " + Arrays.toString(pp.getBreaks()));
        System.out.println("Coefs: ");
        for (double[] row : pp.getCoefs()) {
            System.out.println(Arrays.toString(row));
        }
    }
}

public class MatrixOperation {
    public static void main(String[] args) {
        // 예제 데이터 (행렬 크기 설정 필요)
        int rows = 10; // 총 행 개수
        int cols = 5;  // k 값
        int deg = 2;   // deg 값
        int n = rows - deg; // n 값 설정 (Matlab 코드 해석에 따라 rows - deg로 설정)

        double[][] coefs = new double[rows][cols]; // 원본 행렬
        double[][] Coef = new double[rows - deg][cols]; // 결과 행렬 (크기 조정)

        // 예제 행렬에 임의의 값 할당 (실제 사용 시 데이터 입력 필요)
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                coefs[i][j] = Math.random() * 10; // 0~10 사이 랜덤값
            }
        }

        // 변환된 Matlab 연산
        for (int i = 0; i < rows - deg; i++) { // 1:end-deg → Java에서는 0부터 시작
            for (int j = 0; j < cols; j++) {   // 1:k → 0부터 k-1까지
                if (n + i < rows) {  // Out of Bounds 방지 (Matlab에서는 자동 처리되지만 Java는 직접 확인 필요)
                    Coef[i][j] = coefs[i][j] - coefs[n + i][j];
                } else {
                    Coef[i][j] = coefs[i][j]; // 안전 처리 (필요시 다른 방식으로 조정 가능)
                }
            }
        }

        // 결과 출력 (확인용)
        for (double[] row : Coef) {
            for (double val : row) {
                System.out.printf("%.2f ", val);
            }
            System.out.println();
        }
    }
}

