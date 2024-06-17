### RAG를 활용한 스타트업 법률 상담 챗봇
- langflow 활용


### 사전 필요사항

- docker
- docker-compose

### langflow 설치

```
version: "3.9"
services:
  db:
    image: postgres:16
    container_name: Langflow-DB
    hostname: langflow-db
    mem_limit: 1g
    cpu_shares: 1024
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "langflow", "-U", "langflowuser"]
      timeout: 45s
      interval: 10s
      retries: 10
    volumes:
      - /volume1/docker/langflow/db:/var/lib/postgresql/data:rw
    environment:
      POSTGRES_DB: langflow
      POSTGRES_USER: langflowuser
      POSTGRES_PASSWORD: langflowpass
    restart: on-failure:5

  langflow:
    image: logspace/langflow:latest
    container_name: Langflow
    ports:
      - 7860:7860
    healthcheck:
      test: timeout 10s bash -c ':> /dev/tcp/127.0.0.1/7860' || exit 1
      interval: 10s
      timeout: 5s
      retries: 3
      start_period: 90s
    depends_on:
      - db
    environment:
      - LANGFLOW_DATABASE_URL=postgresql://langflowuser:langflowpass@langflow-db:5432/langflow?sslmode=disable
      - LANGFLOW_CONFIG_DIR=/var/lib/langflow
    volumes:
      - /volume1/docker/langflow/data:/var/lib/langflow:rw
```

### flow import

1. /flow/OpenAI 창업 TEXT Loader.json 
![Text Loader](./tutorial/text_loader.png)

1. /flow/OpenAI 창업 LLM(동작확인).json
![Text Loader](./tutorial/text_loader.png)


### 창업 정보 QnA

- qna/qna.txt 파일을 TEXT loader에 입력

