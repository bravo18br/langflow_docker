# LangFlow Docker Setup

Este projeto configura e executa o serviço [LangFlow](https://github.com/langflow-ai/langflow) utilizando Docker. O setup inclui um `Dockerfile` personalizado para instalar o `curl` e ajustar o fuso horário, além de um arquivo `docker-compose.yml` para orquestrar o container.

## Estrutura do Projeto

```
.
├── Dockerfile
├── docker-compose.yml
└── README.md
```

### Arquivos

- **Dockerfile**: Personaliza a imagem base de LangFlow para instalar o `curl` e configurar o fuso horário.
- **docker-compose.yml**: Orquestra o container, expõe a porta necessária e define um healthcheck.

---

## Pré-requisitos

- Docker e Docker Compose instalados na máquina.  
  Consulte a [documentação oficial do Docker](https://docs.docker.com/get-docker/) para instruções de instalação.

---

## Como Usar

1. **Clone o repositório** (ou copie os arquivos para o seu ambiente local):
   ```bash
   git clone <URL_DO_REPOSITORIO>
   cd langflow_docker
   ```

2. **Construa a imagem personalizada**:
   ```bash
   docker-compose build
   ```

3. **Inicie os serviços**:
   ```bash
   docker-compose up -d
   ```

4. **Verifique se o container está funcionando**:
   ```bash
   docker ps
   ```

5. **Acesse o LangFlow no navegador**:
   Abra [http://localhost:7860](http://localhost:7860) no seu navegador.

---

## Detalhes Técnicos

### Dockerfile

O `Dockerfile` adiciona o `curl` ao container LangFlow e configura o fuso horário:
```dockerfile
FROM langflowai/langflow

# Instala o curl e configura fuso horário
USER root
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

# Configura o fuso horário
ENV TZ=America/Sao_Paulo

# Define o comando de entrada
ENTRYPOINT ["langflow", "run"]
```

### docker-compose.yml

O `docker-compose.yml` configura a rede, expõe a porta 7860 e adiciona um healthcheck:
```yaml
services:
  langflow:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: langflow
    environment:
      - TZ=America/Sao_Paulo
    ports:
      - "7860:7860"
    healthcheck:
      test: ["CMD", "curl", "-sSf", "http://localhost:7860"]
      interval: 10s
      timeout: 5s
      retries: 3
    restart: always
    networks:
      - langflow_net

networks:
  langflow_net:
    name: langflow_net
    driver: bridge
    attachable: true
```

---

## Healthcheck

O healthcheck verifica se o LangFlow está acessível na porta 7860. Caso a aplicação falhe, o Docker tentará reiniciar o container automaticamente.

---

## Troubleshooting

- **Problema**: O container não inicia.  
  **Solução**: Verifique os logs do container com:
  ```bash
  docker logs langflow
  ```

- **Problema**: O healthcheck falha.  
  **Solução**: Certifique-se de que a porta 7860 não está sendo usada por outro serviço.

---

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou enviar pull requests.

---

## Licença

Este projeto é licenciado sob a licença MIT. Consulte o arquivo [LICENSE](LICENSE) para mais detalhes.
