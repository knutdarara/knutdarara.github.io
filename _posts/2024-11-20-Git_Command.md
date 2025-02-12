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
    private int rows;
    private int cols;
    private Map<Integer, Map<Integer, Double>> matrix;

    public SparseMatrix(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        this.rows = ii.length;
        this.cols = colCnt;
        this.matrix = new HashMap<>();

        for (int i = 0; i < ii.length; i++) {
            for (int j = 0; j < ii[i].length; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                double value = A[i][j];
                matrix.computeIfAbsent(row, k -> new HashMap<>()).put(col, value);
            }
        }
    }

    public Double[][] toDenseMatrix() {
        Double[][] dense = new Double[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                dense[i][j] = get(i, j);
            }
        }
        return dense;
    }

    private Double get(int row, int col) {
        return matrix.getOrDefault(row, Collections.emptyMap()).getOrDefault(col, 0.0);
    }
}
