---
title: Git blog 사용법 정리
author: knutdarara
date: 2024-11-19
categories: ["git","git page"]
layout: post
---
# 처음 셋업을 위해서 필요한 정보 정리.
1. 현재 환경
   - 환경 셋업 방법 정리
2. git action
3. 테마 설정
   - 정보, 필요한 파일


import java.util.HashMap;
import java.util.Map;

public class SparseMatrix {
    private Map<Pair<Integer, Integer>, Double> matrix;
    private int rows, cols;

    // Constructor to create a sparse matrix of size m x n
    public SparseMatrix(int m, int n) {
        matrix = new HashMap<>();
        this.rows = m;
        this.cols = n;
    }

    // Insert a value into the sparse matrix using arrays for i, j, s
    public void insert(int[][] i, int[][] j, double[] s, int numRows) {
        // Check if the arrays i, j, and s have valid lengths
        if (i.length == numRows && j.length == numRows && s.length == numRows) {
            for (int row = 0; row < numRows; row++) {
                for (int col = 0; col < i[row].length; col++) {
                    int rowIndex = i[row][col];
                    int colIndex = j[row][col];
                    double value = s[row];
                    if (value != 0) {
                        matrix.put(new Pair<>(rowIndex, colIndex), value);
                    }
                }
            }
        } else {
            System.out.println("Error: Input arrays are not of valid length.");
        }
    }

    // Get a value from the sparse matrix
    public double get(int i, int j) {
        return matrix.getOrDefault(new Pair<>(i, j), 0.0);
    }

    // Display the sparse matrix representation
    public String toString() {
        StringBuilder sb = new StringBuilder();
        for (Map.Entry<Pair<Integer, Integer>, Double> entry : matrix.entrySet()) {
            sb.append("Row: ").append(entry.getKey().getFirst())
              .append(", Col: ").append(entry.getKey().getSecond())
              .append(", Value: ").append(entry.getValue()).append("\n");
        }
        return sb.toString();
    }

    public static void main(String[] args) {
        // Example of creating a sparse matrix of size 4x2000
        SparseMatrix sparseMatrix = new SparseMatrix(4, 2000);

        // 4x2000 arrays: rows (i), columns (j)
        int[][] rows = {
            {0, 1, 2, 3}, // Example of row indices
            {1, 2, 3, 0},
            {2, 1, 0, 3},
            {3, 0, 1, 2}
        };

        int[][] cols = {
            {0, 1, 2, 3}, // Example of column indices
            {1, 2, 3, 0},
            {2, 1, 0, 3},
            {3, 0, 1, 2}
        };

        // 2000 size array: s (values corresponding to the indices)
        double[] values = {7.0, 5.0, 3.0, 1.0}; // Example values, this will be applied for each row

        // Insert these values into the sparse matrix
        sparseMatrix.insert(rows, cols, values, 4);

        // Retrieve and print values from the sparse matrix
        System.out.println("Value at (1, 2): " + sparseMatrix.get(1, 2));  // Output: 5.0
        System.out.println("Value at (3, 4): " + sparseMatrix.get(3, 4));  // Output: 3.0
        System.out.println("Value at (0, 0): " + sparseMatrix.get(0, 0));  // Output: 7.0
        System.out.println("Value at (2, 3): " + sparseMatrix.get(2, 3));  // Output: 1.0
        System.out.println("Value at (1, 1): " + sparseMatrix.get(1, 1));  // Output: 8.0
        System.out.println("Value at (2, 2): " + sparseMatrix.get(2, 2));  // Output: 0.0

        // Display the sparse matrix representation
        System.out.println(sparseMatrix.toString());
    }
}

// Helper class to store row and column index pairs
class Pair<K, V> {
    private K first;
    private V second;

    public Pair(K first, V second) {
        this.first = first;
        this.second = second;
    }

    public K getFirst() {
        return first;
    }

    public V getSecond() {
        return second;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Pair<?, ?> pair = (Pair<?, ?>) o;
        return first.equals(pair.first) && second.equals(pair.second);
    }

    @Override
    public int hashCode() {
        return first.hashCode() * 31 + second.hashCode();
    }
}
