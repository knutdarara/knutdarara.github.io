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
import org.apache.commons.math3.complex.Complex;
import org.apache.commons.math3.linear.*;

public class LsqSolve {

    // LSQ Solve 함수
    public static RealVector lsqSolve(RealMatrix A, RealVector y, double beta) {
        // 기본 솔루션 (A * u = y) -> u = A^-1 * y
        RealVector u = A.operate(y);

        // Robust fitting (가중치 업데이트를 위한 루프)
        if (beta > 0) {
            int m = y.getDimension();
            int n = A.getColumnDimension();
            double alpha = 0.5 * beta / (1 - beta) / m;

            for (int k = 0; k < 3; k++) {
                // Residual 계산
                RealVector r = A.operate(u).subtract(y);
                
                // 복소수 켤레 계산 (실수와 허수를 분리하여 처리)
                Complex[] rr = new Complex[r.getDimension()];
                for (int i = 0; i < r.getDimension(); i++) {
                    rr[i] = new Complex(r.getEntry(i), 0);  // 실수값을 복소수로 처리 (허수부는 0)
                }

                // 복소수 켤레 구하기
                Complex[] rrConjugate = new Complex[rr.length];
                for (int i = 0; i < rr.length; i++) {
                    rrConjugate[i] = rr[i].conjugate();  // 복소수의 켤레
                }

                // 켤레 복소수 연산 후 결과를 실수로 변환하여 사용
                double[] rrReal = new double[rrConjugate.length];
                for (int i = 0; i < rrConjugate.length; i++) {
                    rrReal[i] = rrConjugate[i].getReal();  // 실수 부분 추출
                }

                RealVector rrMean = new ArrayRealVector(rrReal);

                // rrmean 계산
                for (int i = 0; i < rrMean.getDimension(); i++) {
                    if (rrMean.getEntry(i) == 0) {
                        rrMean.setEntry(i, 1);
                    }
                }

                // rrhat 계산 (복소수 연산)
                RealVector rrhat = rrMean.ebeDivide(r);

                // 가중치 계산 (원소별 exp 적용)
                RealVector w = rrhat.mapMultiply(-1);

                // 원소별로 지수 함수 적용
                for (int i = 0; i < w.getDimension(); i++) {
                    w.setEntry(i, Math.exp(w.getEntry(i)));  // exp 함수를 원소별로 적용
                }

                // 가중치 행렬을 대각 행렬로 변환
                RealMatrix spw = MatrixUtils.createRealIdentityMatrix(n);
                for (int i = 0; i < n; i++) {
                    spw.setEntry(i, i, w.getEntry(i));
                }

                // 가중 문제 해결
                RealVector ySpw = y.ebeMultiply(w);
                u = A.transpose().operate(spw.operate(ySpw));
            }
        }

        return u;
    }

    // 테스트용 main 메서드
    public static void main(String[] args) {
        // 예시 행렬 A와 벡터 y
        double[][] matrixData = {
            {1.0, 2.0},
            {3.0, 4.0}
        };
        double[] vectorData = {1.0, 2.0};

        RealMatrix A = MatrixUtils.createRealMatrix(matrixData);
        RealVector y = new ArrayRealVector(vectorData);

        double beta = 0.5;

        // LSQ 해결
        RealVector result = lsqSolve(A, y, beta);
        System.out.println("Result: " + result);
    }
}
