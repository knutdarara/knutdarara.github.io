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
    public static Double[][] createSparseMatrix(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        // 행의 수를 결정합니다 (ii 배열의 길이)
        int rowCnt = ii.length;
        
        // 결과 행렬을 초기화합니다
        Double[][] result = new Double[rowCnt][colCnt];
        
        // 모든 요소를 null로 초기화합니다 (sparse representation)
        for (int i = 0; i < rowCnt; i++) {
            for (int j = 0; j < colCnt; j++) {
                result[i][j] = null;
            }
        }
        
        // 주어진 데이터로 sparse matrix를 채웁니다
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                double value = A[i][j];
                
                // 값이 0이 아닌 경우에만 저장합니다
                if (value != 0) {
                    result[row][col] = value;
                }
            }
        }
        
        return result;
    }
}
