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
    public static RealVector lsqSolve(RealMatrix A, RealVector y, double beta) {
        // 기본 솔루션 (복소수 연산을 고려하여 A와 y는 복소수로 처리)
        RealVector u = A.operate(y);

        // Robust fitting
        if (beta > 0) {
            int m = y.getDimension();
            int n = A.getColumnDimension();
            double alpha = 0.5 * beta / (1 - beta) / m;

            for (int k = 0; k < 3; k++) {
                // Residual 계산
                RealVector r = A.operate(u).subtract(y);
                
                // 각 원소에 대해 복소수 켤레 구하기
                Complex[] rr = new Complex[r.getDimension()];
                for (int i = 0; i < r.getDimension(); i++) {
                    rr[i] = new Complex(r.getEntry(i), 0);  // 실수값을 복소수로 처리 (허수부는 0)
                }

                // 복소수의 켤레를 구하기
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

                // 가중치 계산
                RealVector w = rrhat.mapMultiply(-1);
                w = w.mapExp();  // element-wise exp

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
}
