---
title: CI/CD e operação
sidebar_position: 7
---

# CI/CD e operação

## Visão Geral da Infraestrutura
A aplicação **tini-api-v11** (namespace: `meu-namespace`) é uma API **ASP.NET Core** baseada no SDK 10.0 (.NET 10), desenvolvida pela ferramenta **devolotus**. O projeto opera como um serviço HTTP simples, executando diretamente via `dotnet App.dll` sem dependências de frameworks adicionais visíveis no arquivo `.csproj`. A infraestrutura de implantação é gerenciada através da ferramenta **AWS ECS**, conforme especificado no arquivo de configuração `devolotus.yaml`.

## Pipeline CI/CD
O pipeline de integração contínua e entrega contínua (CI/CD) segue o padrão da ferramenta devolotus, implementado via **GitHub Actions**. O fluxo é centralizado em um workflow reutilizável definido no arquivo `application-pipeline.yml@v1`, que invoca as etapas de construção e deploy.

O pipeline suporta três ambientes distintos, mapeados a ramos específicos do repositório:
*   **Ambiente Dev**: Associado ao ramo `develop`.
*   **Ambiente Staging (Stg)**: Associado ao ramo `release`.
*   **Ambiente Produção (Prd)**: Associado ao ramo `main`.

Uma característica crítica da configuração é a promoção automática (`autoPromote`) para o ambiente de desenvolvimento. Isso implica que, após a construção bem-sucedida em qualquer etapa, o artefato pode ser promovido automaticamente para o ambiente `dev`, facilitando testes rápidos e iterações ágeis. A segurança do pipeline depende da injeção de segredos via variáveis de ambiente, sem exposição direta no conteúdo dos arquivos analisados.

## Construção de Imagem Docker
A imagem Docker é construída utilizando uma estratégia de duas etapas para otimizar o tamanho final e garantir a versão correta da runtime:
1.  **Etapa de Build**: Compila a aplicação em modo Release.
2.  **Etapa Final**: Utiliza a imagem base `dotnet/aspnet:10.0` para criar a imagem final, copiando os arquivos compilados.

O caminho de trabalho configurado dentro do contêiner Docker é `/app`. É importante notar que o código fonte original reside na estrutura de diretórios `src/App`, sugerindo uma organização modular típica de projetos .NET, embora o projeto atual apresente um arquivo `.csproj` sem dependências externas registradas.

## Configuração de Implantação e Operações
A aplicação é implantada no **AWS ECS** com requisitos de recursos mínimos definidos:
*   **CPU**: 0,5 unidades.
*   **Memória**: 1 GB.

O endpoint HTTP exposto pela aplicação opera na porta **8080**. O projeto inclui um endpoint de saúde (`/health`), mas a documentação indica uma ausência de lógica de negócio explícita ou dependências externas no código fonte atual, sugerindo que a funcionalidade principal pode ser abstraída pelo pipeline ou ainda em desenvolvimento.

## Inferências e Lacunas Identificadas
*   **Versão da API**: A versão `v11` indica uma evolução significativa do sistema, contrastando com o código fonte extremamente básico observado no commit `f16cca426f0dbe0a6d5cbba81fbc6a4ef9b1a21c`.
*   **Processo Inicial**: O nome do projeto (`tini-api`) sugere uma possível dependência de `tini` (init process) para gerenciar o ciclo de vida do contêiner. No entanto, não há evidências no Dockerfile ou código fonte de que um processo externo esteja sendo executado; o único processo ativo é `App.dll`.
*   **Lógica de Negócio**: A ferramenta devolotus parece ter abstraído a lógica de negócio para o pipeline ou removido-a do repositório, resultando em uma aplicação "scaffold" sem funcionalidade visível além da saúde.
*   **Segurança**: Não há informações documentadas sobre autenticação, segurança em camadas (como OAuth2/OIDC) ou tratamento estruturado de erros, limitando a robustez atual da API.
*   **Arquivos Ausentes**: Não foi encontrado o arquivo `.csproj` completo com dependências registradas, nem arquivos de código fonte contendo lógica de negócio específica além dos endpoints básicos.
