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
public class SparseMatrix {
    public static double[][] createSparseMatrix(int[] ii, int[] jj, double[] A, int rowCnt, int colCnt) {
        // 결과 행렬 초기화
        double[][] result = new double[rowCnt][colCnt];

        // 데이터 채우기
        for (int k = 0; k < ii.length; k++) {
            int row = ii[k] - 1; // MATLAB은 1-based indexing이므로 1을 빼줍니다.
            int col = jj[k] - 1;

            if (row >= 0 && row < rowCnt && col >= 0 && col < colCnt) {
                result[row][col] += A[k]; // 중복된 위치에서는 값을 더함
            }
        }

        return result;
    }

    public static void main(String[] args) {
        // 입력 데이터
        int[] ii = {1, 2, 3, 4, 5, 6, 7, 8, 9, 1, 2, 3, 4, 5, 6, 7, 8, 9};
        int[] jj = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18};
        double[] A = {-0.0063, 0.0565, -0.1658, 0.1667,
                      -0.0190, -0.1129, -0.1680,
                      /* 이하 생략 */};

        int rowCnt = 12; // 행의 개수
        int colCnt = 36; // 열의 개수

        // 희소 행렬 생성
        double[][] result = createSparseMatrix(ii, jj, A, rowCnt, colCnt);

        // 결과 출력
        for (double[] row : result) {
            for (double val : row) {
                System.out.printf("%8.4f ", val);
            }
            System.out.println();
        }
    }
}
