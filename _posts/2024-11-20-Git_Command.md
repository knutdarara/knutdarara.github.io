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

public class SparseMatrixCSR {

    public static Double[][] toSparseMatrix(int[][] ii, int[][] jj, double[][] A, int colCnt, int rowCnt) {
        // 결과 희소 행렬 초기화
        Double[][] S = new Double[rowCnt][colCnt];

        // 0으로 초기화
        for (int i = 0; i < rowCnt; i++) {
            Arrays.fill(S[i], 0.0);
        }

        // ii, jj, A의 크기가 동일하다고 가정
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];  // 행 인덱스
                int col = jj[i][j];  // 열 인덱스
                double value = A[j][i]; // 값 매핑

                // 희소 행렬에 값 할당
                S[row][col] = value;
            }
        }
        return S;
    }

    public static void main(String[] args) {
        // 예제 입력
        int[][] ii = {
            {1,2,3,4,5,6,7,8,9,1,2,3,4,5,6,7,8,9},
            {2,3,4,5,6,7,8,9,10,2,3,4,5,6,7,8,9,10},
            {3,4,5,6,7,8,9,10,11,3,4,5,6,7,8,9,10,11},
            {4,5,6,7,8,9,10,11,12,4,5,6,7,8,9,10,11,12}
        };

        int[][] jj = {
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18},
            {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18}
        };

        double[][] A = {
            {1, 11, 111, 1111},
            {2, 22, 222, 2222},
            {3, 33, 333, 3333},
            {4, 44, 444, 4444},
            {5, 55, 555, 5555},
            {6, 66, 666, 6666}
        };

        int colCnt = 36;
        int rowCnt = 12;

        // 변환 실행
        Double[][] S = toSparseMatrix(ii, jj, A, colCnt, rowCnt);

        // 결과 출력
        for (Double[] row : S) {
            System.out.println(Arrays.toString(row));
        }
    }
}
