# RetroBolsa API

Backend do **RetroBolsa**, um simulador gamificado de investimentos baseado em cenários históricos reais do mercado brasileiro.

O jogador recebe um orçamento fictício e monta uma carteira com ativos **anonimizados** (ações e títulos públicos apresentados por codinomes, apenas com seus indicadores fundamentalistas). O motor de simulação aplica os preços históricos do período, gera o ranking entre os jogadores e, no fim da rodada, revela quais empresas estavam por trás de cada codinome.

> Projeto desenvolvido em equipe. Este repositório é um fork do [repositório original](https://github.com/caiosemblano/retrobolsa-api), mantido no meu perfil como portfólio.
> Frontend (web + mobile): [retrobolsa_app](https://github.com/rodriguezruan/retrobolsa_app)

---

## Funcionalidades

- **Autenticação stateless** com JWT, registro com validação e senhas em BCrypt
- **Perfis de acesso** (jogador e administrador), com admin criado automaticamente na primeira execução
- **Ciclo de vida das competições**: rascunho → aberta → encerrada → simulada → revelada, com automação por agendamento e controles manuais para o admin
- **Montagem de carteira** com ativos anonimizados e indicadores (P/L, P/VP, ROE, Dividend Yield, Dívida/PL, Margem EBITDA, CAGR de receita e lucro)
- **Motor de simulação** que calcula a rentabilidade com cotações históricas reais
- **Rankings** por rodada, por temporada e global
- **Hub educacional** com aulas (texto e vídeo) e **conquistas** desbloqueáveis
- **Cenários históricos** prontos via migrations (boom das commodities 2004–2011, recessão e Lava Jato, pandemia e alta da Selic)

## Minhas contribuições

- Estrutura base de autenticação: registro, login, JWT, tratamento de erros em JSON e CORS
- Ciclo de competição, automação das rodadas e endpoints administrativos
- Cálculo de pontuação e histórico de partidas no perfil do usuário
- Hub educacional (aulas com vídeo e conteúdo) e sistema de conquistas, com backfill para quem já jogou
- Novos cenários históricos e validação da criação de rodadas pelo admin
- Configuração de acesso em rede local para partidas com vários jogadores

## Tecnologias

| Camada | Stack |
| --- | --- |
| Linguagem | Java 21 |
| Framework | Spring Boot 4 (Web MVC, Data JPA, Security, Validation, Actuator) |
| Banco de dados | PostgreSQL + Flyway (migrations e seeds) |
| Autenticação | Spring Security + JJWT |
| Documentação | springdoc-openapi (Swagger UI) |
| Testes | JUnit, Spring Boot Test, H2, Testcontainers |
| Infra | Docker, Docker Compose, deploy no Railway |

## Endpoints principais

| Recurso | Base | Exemplos |
| --- | --- | --- |
| Autenticação | `/api/auth` | `POST /register`, `POST /login` |
| Competições | `/api/competitions` | `GET /active`, `GET /latest`, `POST /{id}/simulate` |
| Carteiras | `/api/portfolios` | `GET /my-last-result` |
| Rankings | `/api/rankings` | `GET /global`, `GET /season/current` |
| Usuários | `/api/users` | `GET /me`, `GET /profile` |
| Aulas | `/api/articles` | listagem e conteúdo das aulas |
| Admin | `/api/admin/competitions` | `POST /{id}/publish`, `/start`, `/close`, `/reveal`, `POST /next-round` |

Com a aplicação rodando, a documentação interativa fica em `http://localhost:8081/swagger-ui.html`.

---

## Como executar

### Pré-requisitos

- Java 21
- Docker e Docker Compose

### Passo a passo

1. Suba o PostgreSQL (porta `5433`) e o Redis (porta `6379`):
   ```bash
   docker-compose up -d
   ```

2. Defina o segredo do JWT (obrigatório, sem valor padrão):
   ```bash
   export JWT_SECRET="um-segredo-local-com-pelo-menos-32-bytes"
   ```
   No PowerShell: `$env:JWT_SECRET="um-segredo-local-com-pelo-menos-32-bytes"`

3. Inicie a aplicação:
   ```bash
   ./mvnw spring-boot:run
   ```
   No Windows: `.\mvnw.cmd spring-boot:run`

   A API sobe em `http://localhost:8081` e o Flyway cria e popula o banco automaticamente.

### Jogando em rede local

Para jogar com outras pessoas na mesma rede Wi-Fi:

- descubra o IPv4 do computador com `ipconfig`;
- inicie o frontend com o host `0.0.0.0`;
- configure a URL da API no frontend como `http://IP_DO_COMPUTADOR:8081`;
- acesse o frontend pelo IP do computador, por exemplo `http://192.168.0.10:5173`.

Se o Firewall do Windows pedir permissão para Java/Node, libere o acesso em redes privadas. Cada jogador deve usar uma conta diferente.

---

## Documentação

- [Visão geral e status de integração](docs/README.md)
- [Modelo de dados](data_model.md)
- [Documentação da API](docs/api_documentation.md)
- [Diagrama UML comportamental](docs/UML_COMPORTAMENTAL.md) ([visualizador HTML](docs/uml_preview.html))
- [Documentação do frontend](docs/frontend_documentation.md)
- [Roteiro de integração](docs/integration_roadmap.md)

## Equipe

- [Caio Semblano](https://github.com/caiosemblano)
- [Rafael Mello](https://github.com/RafaelMelloBarbosadaSilva)
- [Ruan Rodrigues](https://github.com/rodriguezruan)

## Licença

Distribuído sob a licença MIT. Veja [LICENSE](LICENSE).
