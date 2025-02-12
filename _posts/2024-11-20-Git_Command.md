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


import java.util.ArrayList;
import java.util.List;

public class SparseMatrix {
    private final int rows;
    private final int cols;
    private final List<Double> values;
    private final List<Integer> colIndices;
    private final List<Integer> rowPointers;

    public SparseMatrix(int rows, int cols) {
        this.rows = rows;
        this.cols = cols;
        this.values = new ArrayList<>();
        this.colIndices = new ArrayList<>();
        this.rowPointers = new ArrayList<>();
        rowPointers.add(0);
    }

    public void addValue(int row, int col, double value) {
        if (value != 0) {
            values.add(value);
            colIndices.add(col);
        }
        if (rowPointers.size() <= row + 1) {
            rowPointers.add(values.size());
        } else {
            rowPointers.set(row + 1, values.size());
        }
    }

    public double getValue(int row, int col) {
        int rowStart = rowPointers.get(row);
        int rowEnd = rowPointers.get(row + 1);
        for (int i = rowStart; i < rowEnd; i++) {
            if (colIndices.get(i) == col) {
                return values.get(i);
            }
        }
        return 0.0;
    }

    // 추가적인 메서드 구현 가능
}
