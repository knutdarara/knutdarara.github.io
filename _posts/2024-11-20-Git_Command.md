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
import org.apache.commons.math3.linear.OpenMapRealMatrix;
import org.apache.commons.math3.linear.RealMatrix;

public class SparseMatrixApache {
    
    public static RealMatrix createSparseMatrix(int[][] ii, int[][] jj, double[][] A, int colCnt, int rowCnt) {
        RealMatrix sparseMatrix = new OpenMapRealMatrix(rowCnt, colCnt); // 희소 행렬 생성
        
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                double value = A[j][i]; // 값 매핑
                
                sparseMatrix.setEntry(row, col, value); // 희소 행렬에 값 삽입
            }
        }
        return sparseMatrix;
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

        // 희소 행렬 변환
        RealMatrix sparseMatrix = createSparseMatrix(ii, jj, A, colCnt, rowCnt);

        // 출력
        System.out.println("Sparse Matrix:");
        for (int i = 0; i < rowCnt; i++) {
            for (int j = 0; j < colCnt; j++) {
                System.out.printf("%.0f ", sparseMatrix.getEntry(i, j));
            }
            System.out.println();
        }
    }
}
