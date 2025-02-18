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
    public static double[][] createSparseMatrix(int[][] ii, int[][] jj, double[][] A, int rowCnt, int colCnt) {
        // 결과 행렬 초기화 (12x36 크기)
        double[][] result = new double[rowCnt][colCnt];

        // 데이터 채우기: 각 블록에 대해 반복
        for (int block = 0; block < ii.length; block++) { // 총 4개의 블록
            for (int k = 0; k < ii[block].length; k++) { // 각 블록 내 요소 반복 (36개)
                int row = ii[block][k] - 1 + block * 3; // 계단식으로 이동 (블록마다 3행씩 아래로 이동)
                int col = jj[block][k] - 1;

                if (row >= 0 && row < rowCnt && col >= 0 && col < colCnt) {
                    result[row][col] += A[k][block]; // 해당 위치에 값을 더함
                }
            }
        }

        return result;
    }

    public static void main(String[] args) {
        // 입력값 정의
        int[][] ii = {
            {1,2,3,4,5,6,7,8,9,1,2,3,4,5,6,7,8,9,
             1,2,3,4,5,6,7,8,9,1,2,3,4,5,6,7,8},
            {2 ,//생략}
