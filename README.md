## Architecture

![AWS High Availability Architecture](./Architecture%20diagram.png)

<details>
  <summary>국문버전</summary>

## 개요

이 프로젝트는 트래픽 변동이 큰 특정 시기에 안정적이고 확장 가능한 AWS 고가용성 아키텍처를 구현하여 쇼핑몰 서비스를 무중단으로 운영하기 위한 예제를 다룹니다. 주요 목표는 트래픽이 증가할 때 웹 서버가 자동으로 확장되고, 데이터베이스의 고가용성을 확보하며, 주요 서비스 장애 시 빠르게 대체 서버와 백업 사이트로 전환하는 것입니다.

## 아키텍처 구성 요소

- **VPC (Virtual Private Cloud)**: 네트워크 격리 및 보안을 제공하는 VPC 환경에 배포됩니다.
- **가용 영역**: 장애 대응력을 강화하기 위해 두 개의 가용 영역(AZ)에 걸쳐 배포됩니다.
- **퍼블릭 서브넷**: NAT 게이트웨이를 포함하여 웹 트래픽이 진입하는 지점으로 사용됩니다.
- **프라이빗 서브넷**: EC2 웹 서버, Amazon RDS 데이터베이스 인스턴스 및 Amazon EFS 마운트 타겟이 배치됩니다.
- **Amazon Route 53**: 주 사이트 장애 시 트래픽을 Amazon S3에 호스팅된 정적 백업 사이트로 빠르게 전환하기 위한 장애 조치 라우팅이 구성됩니다.
- **애플리케이션 로드 밸런서(ELB)**: 프라이빗 서브넷의 여러 EC2 인스턴스 간에 트래픽을 분산하여 성능과 안정성을 향상시킵니다.
- **Auto Scaling 그룹**: 트래픽 수요에 따라 EC2 인스턴스를 자동으로 조정하여 최적의 성능을 보장합니다.
- **Amazon RDS (Relational Database Service)**: 고가용성을 위해 다중 AZ 배포가 구성된 기본 및 대기 인스턴스를 사용합니다.
- **Amazon EFS (Elastic File System)**: 여러 EC2 인스턴스에서 접근 가능한 확장 가능한 파일 저장소를 제공합니다.
- **NAT 게이트웨이**: 프라이빗 서브넷에 위치한 리소스가 인터넷에 액세스할 수 있도록 지원하며, 보안을 유지합니다.

## 주요 기능

- **EC2 인스턴스 Auto Scaling**: 트래픽 증가 시 인스턴스를 자동으로 확장하여 성능을 보장합니다.
- **다중 AZ RDS 배포**: 기본 인스턴스 장애 발생 시 자동 장애 조치로 데이터베이스 가용성을 유지합니다.
- **애플리케이션 로드 밸런서**: 여러 EC2 인스턴스로 들어오는 트래픽을 분산시켜 성능과 안정성을 보장합니다.
- **Amazon Route 53 장애 조치 라우팅**: 주 사이트 장애 시 정적 백업 사이트로 트래픽을 빠르게 전환합니다.
- **Elastic File System (EFS)**: EC2 인스턴스 간에 공유되는 확장 가능한 파일 저장소를 제공합니다.

## 사전 준비 사항

- AWS 계정 및 적절한 IAM 권한
- AWS CLI 설치 및 구성
- AWS 서비스 및 네트워크에 대한 기본 지식

## 구현 단계

1. **VPC 및 서브넷 생성**: 여러 가용 영역에 걸쳐 퍼블릭 및 프라이빗 서브넷이 포함된 VPC 구성.
2. **Route 53 설정**: 장애 조치 라우팅 정책을 구성하여 주 사이트 장애 시 트래픽을 전환.
3. **로드 밸런서 설정**: 애플리케이션 로드 밸런서를 배포하여 트래픽 분산.
4. **EC2 Auto Scaling 그룹 구성**: 트래픽 패턴에 따라 EC2 인스턴스 자동 조정.
5. **Amazon RDS 구성**: 다중 AZ 지원 기본 및 대기 데이터베이스 인스턴스 생성.
6. **EFS 설정**: 공유 데이터 저장소를 위한 EFS 파일 시스템 구성.
7. **NAT 게이트웨이 설정**: 프라이빗 서브넷 내 리소스에 대한 인터넷 액세스를 제공.

## 결론

이 아키텍처는 트래픽 변동에도 대응할 수 있는 확장성 및 장애 대응력을 제공하여 쇼핑몰 서비스의 무중단 운영을 보장합니다. 주요 AWS 서비스를 활용하여 높은 가용성과 데이터 보호를 실현합니다.

</details>

## Overview

This project demonstrates an example of building a stable and scalable AWS high-availability architecture to ensure uninterrupted operation of an e-commerce service, especially during traffic fluctuations. The primary goals are to enable automatic scaling of web servers with increasing traffic, ensure high availability of the database, and switch to backup servers and backup sites quickly in case of a major service outage.

## Architecture Components

- **VPC (Virtual Private Cloud)**: Deployed within a VPC that provides network isolation and security.
- **Availability Zones**: Deployed across two availability zones (AZs) to enhance fault tolerance.
- **Public Subnets**: Used as entry points for web traffic and contain NAT gateways.
- **Private Subnets**: Host EC2 web servers, Amazon RDS database instances, and Amazon EFS mount targets.
- **Amazon Route 53**: Configured with failover routing to switch traffic to a static backup site on Amazon S3 in case of a primary site failure.
- **Application Load Balancer (ALB)**: Distributes traffic across multiple EC2 instances in private subnets to improve performance and reliability.
- **Auto Scaling Group**: Automatically scales EC2 instances in response to traffic demand changes, ensuring optimal performance.
- **Amazon RDS (Relational Database Service)**: Utilizes a Multi-AZ deployment with primary and standby instances for high availability.
- **Amazon EFS (Elastic File System)**: Provides scalable file storage accessible by multiple EC2 instances.
- **NAT Gateway**: Allows resources in private subnets to access the internet securely.

## Key Features

- **Auto Scaling for EC2 Instances**: Automatically scales instances during traffic spikes to maintain performance.
- **Multi-AZ RDS Deployment**: Ensures database availability with automatic failover in case of a primary instance failure.
- **Application Load Balancer**: Distributes incoming traffic across multiple EC2 instances to improve reliability and performance.
- **Amazon Route 53 Failover Routing**: Quickly redirects traffic to a static backup site in case of a primary site failure.
- **Elastic File System (EFS)**: Provides shared, scalable file storage across EC2 instances.

## Prerequisites

- AWS account with appropriate IAM permissions
- AWS CLI installed and configured
- Basic knowledge of AWS services and networking

## Implementation Steps

1. **Create VPC and Subnets**: Set up a VPC with public and private subnets across multiple availability zones.
2. **Configure Route 53**: Set up failover routing policies to redirect traffic in case of primary site failure.
3. **Set Up Load Balancer**: Deploy an Application Load Balancer to distribute traffic.
4. **Configure EC2 Auto Scaling Group**: Automatically adjust EC2 instances based on traffic patterns.
5. **Set Up Amazon RDS**: Create primary and standby database instances with Multi-AZ support.
6. **Set Up EFS**: Deploy an EFS file system for shared storage across instances.
7. **Configure NAT Gateways**: Allow internet access for resources in private subnets while maintaining security.

## Conclusion

This architecture provides a scalable and fault-tolerant infrastructure capable of handling varying traffic loads, ensuring high availability and data protection. Leveraging key AWS services, it meets the company's requirements for uninterrupted service and disaster recovery.
