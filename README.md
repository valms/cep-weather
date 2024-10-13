# API de Clima por CEP

Este projeto faz parte da pós-graduação Full Cycle e cumpre o desafio de desenvolver um sistema em Go que recebe um CEP,
identifica a cidade e retorna o clima atual.

## Objetivo do Desafio

Desenvolver um sistema em Go que receba um CEP, identifica a cidade e retorna o clima atual (temperatura em graus
Celsius, Fahrenheit e Kelvin). O sistema deve ser publicado no Google Cloud Run.

## Requisitos do Desafio

- O sistema deve receber um CEP válido de 8 dígitos
- Deve realizar a pesquisa do CEP, encontrar o nome da localização e retornar as temperaturas formatadas em Celsius,
  Fahrenheit e Kelvin
- Respostas adequadas para os seguintes cenários:
	- Sucesso: Código HTTP 200, com body
  ``` json
  {
    "temp_C": 27.2,
    "temp_F": 80.9,
    "temp_K": 300.2
  }
  ```
	- CEP inválido: Código HTTP 422, mensagem "invalid zipcode"
	- CEP não encontrado: Código HTTP 404, mensagem "can not find zipcode"
- Deploy realizado no Google Cloud Run

## Estrutura do Projeto

```
.
├── cmd
│   └── main.go
├── README.md
├── Dockerfile
├── .gitignore
├── model
│   ├── via_cep.go
│   └── weather.go
└── go.mod
```

## Funcionalidades

- Busca informações da cidade a partir de um CEP usando a API ViaCEP
- Obtém dados meteorológicos para a cidade usando a WeatherAPI
- Converte a temperatura para Celsius, Fahrenheit e Kelvin

## Pré-requisitos

- Go 1.22 ou superior
- Docker (para containerização)

## Variáveis de Ambiente

- `PORT`: A porta em que o servidor será executado (padrão: 8080)
- `API_KEY`: Sua chave da WeatherAPI

## Compilação e Execução

### Desenvolvimento Local

1. Configure as variáveis de ambiente necessárias
2. Execute a aplicação:

```bash
go run cmd/main.go
```

### Usando Docker

1. Construa a imagem Docker:

```bash
docker build -t api-clima-cep .
```

2. Execute o container:

```bash
docker run -p 8080:8080 -e PORT=8080 -e API_KEY=sua_chave_api api-clima-cep
```

## Uso da API

Envie uma requisição GET para o endpoint raiz com o parâmetro de consulta `cep`:

```
GET /?cep=12345678
```

A API retornará uma resposta JSON com dados de temperatura em Celsius, Fahrenheit e Kelvin.

## Deploy

O deploy é realizado automaticamente no Google Cloud Run através de um workflow do GitHub Actions. O workflow é acionado
a cada push na branch `master`.

### Configuração do Deploy

1. Configure os seguintes secrets no seu repositório GitHub:
	- `GCP_SA_KEY`: Chave de conta de serviço do Google Cloud em formato JSON
	- `WEATHER_API_KEY`: Chave da WeatherAPI

2. O workflow realiza as seguintes etapas:
	- Autentica com o Google Cloud
	- Configura o Google Cloud SDK
	- Cria um repositório no Artifact Registry (se não existir)
	- Constrói e faz push da imagem Docker para o Artifact Registry
	- Deploya a imagem para o Cloud Run
	- Limpa imagens antigas do Artifact Registry

3. Após o deploy, a URL do serviço Cloud Run é exibida nos logs do workflow.

## Acesso ao Serviço Deployado

O serviço deployado no Cloud Run está configurado para permitir acesso não autenticado.

### URL do serviço

O serviço se encontra na seguinte URL: [https://cep-weather-api-iiuuzoq6ia-nn.a.run.app/](https://cep-weather-api-iiuuzoq6ia-rj.a.run.app)
