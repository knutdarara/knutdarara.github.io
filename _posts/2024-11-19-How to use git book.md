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

    // Insert a value into the sparse matrix with 5 input values
    public void insert(int[] i, int[] j, double[] s) {
        // Ensure that the length of i, j, s arrays are the same
        if (i.length == j.length && j.length == s.length) {
            for (int index = 0; index < i.length; index++) {
                if (s[index] != 0) {
                    matrix.put(new Pair<>(i[index], j[index]), s[index]);
                }
            }
        } else {
            System.out.println("Error: Input arrays are not of the same length.");
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
        // Example of creating a sparse matrix of size 4x5
        SparseMatrix sparseMatrix = new SparseMatrix(4, 5);

        // 5 input values: rows (i), columns (j), and values (s)
        int[] rows = {0, 1, 3, 2, 1};
        int[] cols = {0, 2, 4, 3, 1};
        double[] values = {7.0, 5.0, 3.0, 1.0, 8.0};

        // Insert these values into the sparse matrix
        sparseMatrix.insert(rows, cols, values);

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
