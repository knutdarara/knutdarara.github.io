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
    // CSR 유지 방식
    public static double[][] sparseCSR(int[][] ii, int[][] jj, double[][] A, int colCnt, int rowCnt) {
        double[][] result = new double[colCnt][rowCnt];

        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                result[row][col] += A[i][j];
            }
        }
        return result;
    }

    // CSR -> CSC 변환 방식
    public static double[][] sparseCSC(int[][] ii, int[][] jj, double[][] A, int colCnt, int rowCnt) {
        double[][] result = new double[rowCnt][colCnt]; // 행과 열을 반대로 저장

        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int col = ii[i][j];  // CSR에서 row였던 것을 col로 변경
                int row = jj[i][j];  // CSR에서 col이었던 것을 row로 변경
                result[row][col] += A[i][j];
            }
        }
        return result;
    }
}
