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
    public static double[][] sparse(int[][] ii, int[][] jj, double[][] A, int m, int n) {
        // 결과 행렬 초기화
        double[][] S = new double[m][n];

        // 데이터 채우기
        for (int k = 0; k < ii.length; k++) {
            for (int l = 0; l < ii[k].length; l++) {
                int i = ii[k][l] - 1; // MATLAB은 1-based indexing이므로 1을 빼줍니다.
                int j = jj[k][l] - 1;
                if (i >= 0 && i < m && j >= 0 && j < n) {
                    S[i][j] += A[k][l]; // 중복된 인덱스의 경우 값을 더합니다.
                }
            }
        }

        return S;
    }
}
