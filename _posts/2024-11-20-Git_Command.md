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
    private int m;  // 행 수
    private int n;  // 열 수
    private int nzmax;  // 비零 요소의 최대 수
    private int[] ir;  // 행 인덱스
    private int[] jc;  // 열 포인터
    private double[] pr;  // 비零 값

    public SparseMatrix(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        this.n = colCnt;
        this.m = 0;
        for (int[] row : ii) {
            for (int i : row) {
                this.m = Math.max(this.m, i + 1);
            }
        }

        // 총 비零 요소 수 계산
        this.nzmax = 0;
        for (double[] row : A) {
            this.nzmax += row.length;
        }

        this.ir = new int[nzmax];
        this.jc = new int[n + 1];
        this.pr = new double[nzmax];

        int[] colCounts = new int[n];
        int idx = 0;

        // 데이터 채우기
        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                double val = A[i][j];

                if (col >= 0 && col < n) {
                    ir[idx] = row;
                    pr[idx] = val;
                    colCounts[col]++;
                    idx++;
                }
            }
        }

        // jc 배열 채우기
        int sum = 0;
        for (int i = 0; i < n; i++) {
            jc[i] = sum;
            sum += colCounts[i];
        }
        jc[n] = sum;

        // 열 내에서 행 인덱스 정렬
        for (int j = 0; j < n; j++) {
            int start = jc[j];
            int end = jc[j + 1];
            sortColumnEntries(start, end);
        }
    }

    private void sortColumnEntries(int start, int end) {
        for (int i = start; i < end; i++) {
            for (int j = i + 1; j < end; j++) {
                if (ir[i] > ir[j]) {
                    // Swap ir
                    int tempIr = ir[i];
                    ir[i] = ir[j];
                    ir[j] = tempIr;
                    // Swap pr
                    double tempPr = pr[i];
                    pr[i] = pr[j];
                    pr[j] = tempPr;
                }
            }
        }
    }

    // Getter 메소드들
    public int getM() { return m; }
    public int getN() { return n; }
    public int getNzmax() { return nzmax; }
    public int[] getIr() { return ir; }
    public int[] getJc() { return jc; }
    public double[] getPr() { return pr; }
}
