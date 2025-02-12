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

    // sparse 함수
    public static double[][] sparse(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        int rowSize = ii.length;  // ii의 행 개수 (4)
        int colSize = ii[0].length;  // ii의 열 개수 (36)

        // 결과 행렬 초기화
        double[][] result = new double[rowSize][colSize];

        // ii와 jj를 기준으로 A 값 배치
        for (int i = 0; i < rowSize; i++) {
            for (int j = 0; j < colSize; j++) {
                // 1-based index를 0-based index로 변환
                int row = ii[i][j] - 1;  // ii의 값에 맞게 행 번호 설정
                int col = jj[i][j] - 1;  // jj의 값에 맞게 열 번호 설정

                if (row >= 0 && row < rowSize && col >= 0 && col < colSize) {
                    // A의 값을 result 행렬의 해당 위치에 배치
                    result[row][col] = A[j][i];  // A[j][i] 값을 배치
                }
            }
        }

        return result;
    }

    // 행렬 출력 함수
    public static void printMatrix(double[][] matrix) {
        for (double[] row : matrix) {
            System.out.println(Arrays.toString(row));
        }
    }

    public static void main(String[] args) {
        // 예제 데이터
        int[][] ii = {
            {1,2,3,4,5,6,7,8,9,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36},
            {2,3,4,5,6,7,8,9,10,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37},
            {3,4,5,6,7,8,9,10,11,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38},
            {4,5,6,7,8,9,10,11,12,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39}
        };

        int[][] jj = {
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36}
        };

        double[][] A = new double[36][4];
        for (int i = 0; i < 36; i++) {
            for (int j = 0; j < 4; j++) {
                A[i][j] = Math.pow(10, j) * (i + 1); // 1, 11, 111, 1111...
            }
        }

        int colCnt = 12;

        // 희소 행렬 생성
        double[][] sparseMatrix = sparse(ii, jj, A, colCnt);

        // 결과 출력
        printMatrix(sparseMatrix);
    }
}
