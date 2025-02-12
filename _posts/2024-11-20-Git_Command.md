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

public class SparseMatrix {

    public static double[][] sparse(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        // 입력값 검증
        if (ii.length != jj.length || ii.length != A.length) {
            throw new IllegalArgumentException("ii, jj, A의 크기가 다릅니다.");
        }

        // 행렬 크기 찾기
        int rowCnt = 0;
        for (int[] row : ii) {
            for (int r : row) {
                rowCnt = Math.max(rowCnt, r);
            }
        }
        rowCnt++; // 0-based index를 고려하여 행 개수 설정

        // 결과 행렬 초기화
        double[][] result = new double[rowCnt][colCnt];

        // 희소 행렬 채우기
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                if (row >= rowCnt || col >= colCnt) {
                    throw new IndexOutOfBoundsException("인덱스 범위를 초과했습니다.");
                }
                result[row][col] += A[i][j]; // 중복 좌표에 대한 값 합산 (MATLAB과 동일한 방식)
            }
        }

        return result;
    }

    public static void printMatrix(double[][] matrix) {
        for (double[] row : matrix) {
            System.out.println(Arrays.toString(row));
        }
    }

    public static void main(String[] args) {
        // 입력 데이터 예시
        int[][] ii = {{0, 1, 2}, {1, 2, 3}};
        int[][] jj = {{1, 2, 3}, {0, 2, 3}};
        double[][] A = {{5.0, 8.2, 3.5}, {4.1, 2.0, 6.7}};
        int colCnt = 4;

        // 희소 행렬 생성
        double[][] sparseMatrix = sparse(ii, jj, A, colCnt);

        // 결과 출력
        printMatrix(sparseMatrix);
    }
}
