# monitoring

로그 + 메트릭 통합 모니터링을 위한 Docker 기반 프로젝트.

주요 구성 요소
- Vector: Docker 컨테이너 로그 수집
- Loki: 로그 저장소
- Prometheus: 메트릭 저장소
- Node Exporter: 호스트 서버 메트릭 수집
- cAdvisor: 컨테이너 리소스 메트릭 수집
- Grafana: 로그 및 메트릭 대시보드

```mermaid
flowchart LR
    subgraph Docker_Host[Docker Host]
        A1[Python App #1]:::app
        A2[Python App #2]:::app
        A3[Python App #N]:::app
        subgraph Engine[Docker Engine]
            LOGS[(Container Logs)]
        end
    end

    subgraph Vector[Vector Agent]
        V[Source: docker_logs<br/>Sink: loki]
    end

    subgraph Loki[Loki]
        L[(Log Storage)]
    end

    subgraph Node_Exporter[Node Exporter]
        NE[System Metrics]
    end

    subgraph cAdvisor[cAdvisor]
        CA[Container Metrics]
    end

    subgraph Prometheus[Prometheus]
        P[(Metrics Storage)]
    end

    subgraph Grafana[Grafana]
        G[Dashboards<br/>Log + Metric Query]
    end

    %% Logs path
    A1 --> LOGS
    A2 --> LOGS
    A3 --> LOGS
    LOGS --> V --> L

    %% Metrics path
    NE --> P
    CA --> P

    %% Grafana pulls both
    L --> G
    P --> G

```
Reverse Proxy (NGINX) ──▶ Grafana (:3000)
