---
title: Configuração e execução local
sidebar_position: 5
---

# Configuração e execução local

## Visão Geral da Aplicação
A aplicação documentada é um scaffold de API desenvolvido em **ASP.NET Core** utilizando o SDK 10.0 (.NET 10). O projeto foi gerado pela ferramenta `devolotus` e identificado como `tini-api-v11` no namespace `meu-namespace`. A estrutura do projeto reside na pasta `src/App`, conforme indicado nos resumos de arquivos.

A aplicação é projetada para ser executada diretamente via o arquivo compilado `App.dll`, sem a necessidade de frameworks adicionais visíveis além do SDK Web padrão. O endpoint principal expõe-se na porta **8080**. A infraestrutura original foi definida para rodar em **AWS ECS** com recursos mínimos (0,5 CPU e 1 GB), mas a configuração local foca na execução direta da aplicação compilada.

## Pré-requisitos
Para executar a aplicação localmente, é necessário ter o **.NET SDK 10.0** instalado no ambiente de desenvolvimento. A versão específica do SDK é crítica, pois o projeto foi construído especificamente para esta versão, utilizando recursos como tipos nulos implícitos e usings implícitos que podem não estar disponíveis em versões anteriores ou posteriores sem ajustes manuais.

## Configuração do Ambiente
O projeto utiliza um modelo de estrutura de diretórios padrão da Microsoft, com a aplicação contida no subdiretório `src/App`. Não foram encontradas configurações explícitas de dependências externas registradas no arquivo `.csproj`, o que sugere que a aplicação depende apenas das bibliotecas do framework .NET Core.

A configuração de segredos e variáveis de ambiente, originalmente definida para o pipeline CI/CD via `devolotus.yaml`, não está visível no conteúdo dos arquivos locais. Portanto, qualquer configuração de autenticação ou injeção de dados deve ser realizada manualmente através de variáveis de ambiente padrão do sistema operacional antes da execução.

## Execução Local
A aplicação foi compilada em um arquivo binário estático (`App.dll`). Para executá-la localmente, o comando `dotnet` é utilizado para invocar diretamente este arquivo, contornando a necessidade de restaurar pacotes ou executar o projeto como uma solução completa durante a fase de execução de produção.

O caminho de trabalho no container Docker original foi definido como `/app`, enquanto o código fonte reside em `src/App`. Para fins locais, o diretório de execução deve ser ajustado para refletir a localização do arquivo binário compilado.

### Comandos de Execução
Execute os seguintes comandos no terminal, assumindo que o diretório atual contém o arquivo `App.dll` ou que você está navegando até a pasta `src/App`:

```bash
# Opção 1: Executar diretamente o arquivo DLL (Recomendado para produção local)
dotnet App.dll

# Opção 2: Restaurar e executar como projeto (Se pacotes forem necessários)
cd src/App
dotnet restore
dotnet run
```

## Observações sobre a Estrutura do Projeto
*   **Arquivo de Projeto**: O arquivo `.csproj` não contém dependências externas registradas, indicando uma aplicação minimalista ou com lógica abstraída.
*   **Dockerfile**: A construção do container utiliza duas etapas: uma etapa de build para publicar em Release e uma etapa final baseada na imagem `dotnet/aspnet:10.0`. O endpoint exposto é fixo em 8080.
*   **Pipeline CI/CD**: Embora o pipeline original utilize GitHub Actions com workflows reutilizáveis (`application-pipeline.yml@v1`), a execução local não requer configuração de pipelines, focando apenas na ferramenta `dotnet`.
*   **Ambientes**: O projeto suporta três ambientes (`dev`, `stg`, `prd`) via promoção automática no pipeline, mas a execução local ignora essas distinções, rodando o binário compilado diretamente.

## Lacunas e Inferências Identificadas
1.  **Ausência de Código Fonte Visível**: Os arquivos de código fonte apresentados são extremamente básicos. A lógica de negócio parece ter sido abstraída para o pipeline ou removida intencionalmente, deixando apenas os endpoints `/` e `/health`.
2.  **Segurança e Autenticação**: Não há evidências de implementação de autenticação, segurança em camadas ou tratamento de erros além do endpoint de saúde (`/health`). A aplicação está exposta sem proteções visíveis no código local.
3.  **Dependência de `tini`**: O nome do projeto (`tini-api`) sugere uma possível dependência do processo inicial `tini`, mas não há evidências no código ou Dockerfile de que um contêiner externo esteja sendo executado; o processo principal é apenas `App.dll`.
4.  **Versão da API**: A versão `v11` indica evolução significativa, mas o código fonte atual reflete uma estrutura mínima, criando uma lacuna entre a complexidade histórica e o estado atual do scaffold.
5.  **Credenciais**: Nenhuma credencial foi encontrada ou exposta nos arquivos analisados.

## Conclusão
A aplicação local pode ser executada imediatamente utilizando `dotnet App.dll` após garantir o SDK 10.0. No entanto, devido à natureza minimalista do scaffold e à ausência de lógica de negócio visível, a aplicação serve primariamente como um ponto de entrada para testes de infraestrutura ou como base para desenvolvimento futuro que dependerá das abstrações do pipeline `devolotus`.
