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
import java.util.*;

public class SparseMatrix {

    public static double[][] sparse(int[][] ii, int[][] jj, double[][] A, int colCnt) {
        List<int[]> coordinates = new ArrayList<>();
        List<Double> values = new ArrayList<>();

        int rowSize = ii.length;
        int colSize = ii[0].length;

        if (jj.length != rowSize || jj[0].length != colSize) {
            throw new IllegalArgumentException("ii, jj의 크기가 일치하지 않습니다.");
        }

        // A의 크기 유연성 검토
        int aRowSize = A.length;
        int aColSize = A[0].length;
        boolean transposeA = (aRowSize == colSize && aColSize == rowSize);

        // 데이터를 리스트에 저장
        for (int i = 0; i < rowSize; i++) {
            for (int j = 0; j < colSize; j++) {
                int row = ii[i][j];
                int col = jj[i][j];
                double value = transposeA ? A[j][i] : A[i][j];

                coordinates.add(new int[]{row, col});
                values.add(value);
            }
        }

        // 좌표를 (row, col) 기준으로 정렬
        Collections.sort(coordinates, (a, b) -> a[0] == b[0] ? Integer.compare(a[1], b[1]) : Integer.compare(a[0], b[0]));

        // 중복된 좌표 값 합산
        Map<String, Double> sparseMap = new LinkedHashMap<>();
        for (int i = 0; i < coordinates.size(); i++) {
            int row = coordinates.get(i)[0];
            int col = coordinates.get(i)[1];
            String key = row + "," + col;

            sparseMap.put(key, sparseMap.getOrDefault(key, 0.0) + values.get(i));
        }

        // 희소 행렬 초기화
        int rowCnt = coordinates.stream().mapToInt(c -> c[0]).max().orElse(0) + 1;
        double[][] result = new double[rowCnt][colCnt];

        // 희소 행렬 생성
        for (Map.Entry<String, Double> entry : sparseMap.entrySet()) {
            String[] rc = entry.getKey().split(",");
            int row = Integer.parseInt(rc[0]);
            int col = Integer.parseInt(rc[1]);
            result[row][col] = entry.getValue();
        }

        return result;
    }

    public static void printMatrix(double[][] matrix) {
        for (double[] row : matrix) {
            System.out.println(Arrays.toString(row));
        }
    }

    public static void main(String[] args) {
        // 테스트 데이터 (논문과 동일한 방식 적용)
        int[][] ii = {
            {0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3}
        };
        int[][] jj = {
            {1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0}
        };
        double[][] A = {
            {5.0, 8.2, 3.5, 4.1, 2.0, 6.7, 1.1, 7.3, 5.5, 9.0, 2.8, 3.2}
        };

        int colCnt = 5;

        // 희소 행렬 생성
        double[][] sparseMatrix = sparse(ii, jj, A, colCnt);

        // 결과 출력
        printMatrix(sparseMatrix);
    }
}
