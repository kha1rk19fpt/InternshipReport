---
title: "Workshop"
date: 2026-11-20
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
# CPAWS - Xây dựng nền tảng dự đoán giá hàng hóa serverless trên AWS

#### Tổng quan

**CPAWS** là ứng dụng web dự đoán giá vàng, bạc, dầu Brent và WTI trong 1–7 ngày tới, hiển thị theo USD và VND, kèm chatbot tiếng Việt trên Amazon Bedrock giải thích dự đoán. Toàn bộ hệ thống chạy serverless theo lịch, không có tài nguyên chạy 24/7.

Trong workshop này, bạn sẽ học cách:
- Thu thập dữ liệu giá theo lịch giao dịch và lưu vào data lake trên Amazon S3
- Xây dựng mốc dự báo phân vị không rò rỉ dữ liệu tương lai
- Triển khai hạ tầng bằng AWS CDK: S3, ECR, Lambda container, DynamoDB
- Tự động hóa luồng hằng ngày với EventBridge Scheduler và Lambda Destinations
- Mở API có xác thực bằng API Gateway + Cognito
- Gọi Amazon Bedrock Converse API với tool use để chatbot chỉ dùng số liệu từ hệ thống

#### Nội dung

1. [Giới thiệu](5.1-Workshop-overview/)
2. [Yêu cầu tiên quyết](5.2-Prerequiste/)
3. [Dữ liệu và mô hình](5.3-Data-and-model/)
4. [Triển khai hạ tầng với CDK](5.4-Infrastructure/)
5. [Chatbot với Amazon Bedrock](5.5-Chatbot/)
6. [Ước tính và tối ưu chi phí](5.6-Cost/)
7. [Dọn dẹp](5.7-Cleanup/)
