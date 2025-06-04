# 11-ellu-apm

## 개요
**Looper 서비스 OpenTelemetry APM(Application Performance Monitoring) 모니터링 스택**
```
Spring Boot 애플리케이션 → OpenTelemetry Collector → Prometheus/Jaeger/Loki
                                                    ↓
                                                 Grafana 대시보드
```

## 모니터링 스택

### 핵심 컴포넌트
- **OpenTelemetry Collector**: 텔레메트리 데이터 수집 및 처리
- **Grafana**: 시각화 및 대시보드
- **Prometheus**: 메트릭 저장소
- **Jaeger**: 분산 추적
- **Loki**: 로그 수집

### 추가 컴포넌트
- **Promtail**: 로그 수집
- **cAdvisor**: 컨테이너 메트릭
- **Node Exporter**: 시스템 메트릭

## 필요한 설정 파일

### 모니터링 설정 파일
- `docker-compose.monitoring.yml` - 모니터링 스택
- `otel-collector-config.yaml` - OpenTelemetry Collector 설정
- `prometheus.yaml` - Prometheus 설정
- `loki-config.yaml` - Loki 설정
- `promtail-config.yaml` - Promtail 설정
- `grafana/provisioning/datasources/datasources.yml` - Grafana 데이터 소스

### 애플리케이션 설정 파일
- `docker-compose.app.yml` - OpenTelemetry 활성화된 애플리케이션

## 주요 기능

### APM 모니터링
- **요청 추적**: HTTP 요청별 성능 분석
- **데이터베이스 모니터링**: JDBC 쿼리 성능 추적
- **에러율 추적**: 4xx/5xx 에러 모니터링
- **지연시간 분석**: P50, P95, P99 지연시간 측정

### 인프라 모니터링
- **JVM 메트릭**: 메모리, GC, 스레드 모니터링
- **시스템 메트릭**: CPU, 메모리, 디스크, 네트워크
- **컨테이너 메트릭**: Docker 컨테이너 리소스 사용량

### 분산 추적
- **서비스 의존성 맵**: 마이크로서비스 간 호출 관계
- **트레이스 분석**: 요청의 전체 경로 추적
- **성능 병목 식별**: 느린 구간 식별

## 대시보드

### Grafana 대시보드
1. **OpenTelemetry APM** (ID: 19419) - 종합 APM 대시보드
2. **Spring Boot Statistics** (ID: 12900) - Spring Boot 전용 대시보드

### 접속 방법

#### SSH 터널링
```bash
ssh -L 3001:localhost:3001 \
    -L 9090:localhost:9090 \
    -L 16686:localhost:16686 \
    looper@looper-server-ip
```

#### 접속 URL
- **Grafana**: http://localhost:3001 (admin/admin123)
- **Prometheus**: http://localhost:9090
- **Jaeger**: http://localhost:16686

## 설정 커스터마이징

### OpenTelemetry 샘플링 비율 조정
```yaml
environment:
  - OTEL_TRACES_SAMPLER_ARG=0.1  # 10% 샘플링
```

### 메트릭 수집 간격 조정
```yaml
# prometheus.yaml
global:
  scrape_interval: 30s  # 기본 15s에서 변경
```

### 로그 보존 기간 설정
```yaml
# loki-config.yaml의 limits_config에 추가
retention_period: 168h  # 7일
```

## 성능 최적화

### 리소스 사용량 최적화
- **메모리 제한**: OpenTelemetry Collector 메모리 제한 설정
- **배치 처리**: 배치 크기 조정으로 처리량 향상
- **샘플링**: 고트래픽 환경에서 샘플링 비율 조정

### 스토리지 최적화
- **메트릭 보존**: Prometheus 데이터 보존 기간 설정
- **로그 압축**: Loki 압축 설정 활성화

## 보안 고려사항

1. **환경변수**: 민감한 정보를 `.env` 파일로 분리
2. **네트워크**: 모니터링 포트 접근 제한
3. **인증**: Grafana 기본 비밀번호 변경

## 참고 자료

- [OpenTelemetry 공식 문서](https://opentelemetry.io/docs/)
- [Grafana 대시보드 갤러리](https://grafana.com/grafana/dashboards/)
- [Prometheus 설정 가이드](https://prometheus.io/docs/prometheus/latest/configuration/)
- [OpenTelemetry APM](https://github.com/blueswen/opentelemetry-apm)