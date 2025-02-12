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
        // 입력 크기 확인 (ii, jj의 크기가 같아야 함)
        int rowSize = ii.length;
        int colSize = ii[0].length;

        if (jj.length != rowSize || jj[0].length != colSize) {
            throw new IllegalArgumentException("ii, jj의 크기가 일치하지 않습니다.");
        }

        // A의 크기 검증 및 유연한 매핑 (비대칭 대응)
        int aRowSize = A.length;
        int aColSize = A[0].length;
        if (!(aRowSize == rowSize && aColSize == colSize) && !(aRowSize == colSize && aColSize == rowSize)) {
            throw new IllegalArgumentException("A의 크기가 ii, jj와 호환되지 않습니다.");
        }

        // 최대 행 인덱스 계산
        int rowCnt = 0;
        for (int i = 0; i < rowSize; i++) {
            for (int j = 0; j < colSize; j++) {
                rowCnt = Math.max(rowCnt, ii[i][j]);
            }
        }
        rowCnt++; // 0-based index 고려하여 크기 증가

        // 결과 행렬 초기화
        double[][] result = new double[rowCnt][colCnt];

        // 희소 행렬 채우기
        for (int i = 0; i < rowSize; i++) {
            for (int j = 0; j < colSize; j++) {
                int row = ii[i][j];
                int col = jj[i][j];

                if (row >= rowCnt || col >= colCnt) {
                    throw new IndexOutOfBoundsException("인덱스 범위를 초과했습니다.");
                }

                // A가 비대칭일 경우, A[i][j] 또는 A[j][i]로 매핑
                double value = (A.length == rowSize) ? A[i][j] : A[j][i];

                result[row][col] += value; // 같은 위치 값 합산
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
        // 예제 1: ii, jj가 [4][36], A가 [36][4]일 경우
        int[][] ii = new int[4][36];
        int[][] jj = new int[4][36];
        double[][] A = new double[36][4]; // A가 비대칭

        // 테스트 데이터 채우기 (간단한 패턴)
        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 36; j++) {
                ii[i][j] = j % 10;
                jj[i][j] = i % 4;
                A[j][i] = (i + 1) * (j + 1) * 0.1; // 임의의 값 설정
            }
        }

        int colCnt = 5;

        // 희소 행렬 생성
        double[][] sparseMatrix = sparse(ii, jj, A, colCnt);

        // 결과 출력
        printMatrix(sparseMatrix);
    }
}
