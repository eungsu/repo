# AWS RDS (Amazon Relational Database Service)

## AWS RDS란?
- **AWS RDS (Amazon Relational Database Service)** 는 Amazon Web Services(AWS)에서 제공하는 완전 관리형 관계형 데이터베이스 서비스다.
- 사용자는 데이터베이스의 설치, 설정, 운영, 유지보수 등의 복잡한 작업을 AWS에 위임할 수 있으며, 높은 가용성, 확장성, 보안성을 기본적으로 제공한다.
- 다양한 데이터베이스 엔진을 지원하며, AWS 클라우드 환경에서 빠르고 효율적으로 관계형 데이터베이스를 생성하고 운영할 수 있다.

### AWS RDS의 주요 특징
- **완전 관리형 서비스**: AWS가 자동으로 소프트웨어 설치, 패치, 백업, 복구, 모니터링 및 확장성을 관리한다.
- **고가용성**: Amazon RDS는 Multi-AZ(다중 가용 영역) 배포 옵션을 통해 장애 발생 시 자동으로 데이터베이스를 복구할 수 있다.
- **자동 백업**: RDS는 지정된 백업 기간 동안 자동으로 데이터베이스를 백업하며, 사용자는 복구 시점을 지정하여 데이터베이스를 복구할 수 있다.
- **보안**: AWS IAM(Identity and Access Management)을 통한 접근 제어, VPC 내에서의 안전한 네트워크 연결, 저장 및 전송 시 데이터 암호화를 지원한다.
- **확장성**: 필요에 따라 인스턴스의 크기를 조정할 수 있으며, 읽기 부하가 많은 경우 읽기 전용 복제본(Read Replica)으로 수직 또는 수평 확장이 가능하다.
- **다양한 데이터베이스 엔진 지원**:
  - Amazon Aurora (MySQL 및 PostgreSQL 호환): 고성능, 고가용성, MySQL 및 PostgreSQL 호환 
  - PostgreSQL: 오픈 소스, 강력한 ACID 지원
  - MySQL: 가장 널리 사용되는 오픈 소스 관계형 데이터베이스
  - MariaDB: MySQL의 포크, 커뮤니티 중심의 빠른 성능
  - Oracle: 상용 데이터베이스로 강력한 보안 및 성능 제공
  - Microsoft SQL Server: Microsoft의 상용 데이터베이스, 기업 환경에서 널리 사용
