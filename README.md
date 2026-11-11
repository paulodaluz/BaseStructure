# BaseStructure

Base de aplicação **Node.js, TypeScript e Express** criada para iniciar APIs com separação entre rotas, controllers e serviços. Contém respostas demonstrativas e validações, como ponto de partida para outros projetos.

[![TypeScript](https://img.shields.io/badge/TypeScript-project-3178C6)](https://www.typescriptlang.org/docs/)
[![License](https://img.shields.io/badge/License-AGPL_3.0-blue)](LICENSE)

## 📋 Table of contents

- [✨ Features](#features)
- [🧪 Technologies](#technologies)
- [🛠️ Environment setup](#environment-setup)
- [🚀 Getting started](#getting-started)
- [🔌 API usage](#api-usage)
- [🗂️ Project structure](#project-structure)
- [✅ Testing and quality](#testing-and-quality)
- [🔎 Troubleshooting](#troubleshooting)
- [🤝 Contributing](#contributing)
- [📜 License](#license)

<a id="features"></a>

## ✨ Features

- Estrutura Express com rotas, controllers, serviços e validação com express-validator.
- Rotas de exemplo GET/POST, verificação de saúde e versão.
- Logs com Winston e exemplo de teste com Jest.
- Base para novos projetos, sem persistência ou autenticação implementadas.

<a id="technologies"></a>

## 🧪 Technologies

Versões declaradas em [package.json](package.json); as faixas não identificam a versão resolvida sem lockfile.

| Tecnologia | Versão declarada | Finalidade |
| --- | --- | --- |
| [Node.js](https://nodejs.org/) | `14.16.0` | Runtime definido em `engines.node` |
| [TypeScript](https://www.typescriptlang.org/docs/) | `^4.2.3` | Compilação para `dist/` |
| [Express](https://expressjs.com/) | `^4.17.1` | Servidor HTTP |
| [express-validator](https://express-validator.github.io/docs/) | `^6.10.0` | Validação de parâmetros |
| [Jest](https://jestjs.io/docs/getting-started) | `^26.6.3` | Teste de exemplo |
| [Winston](https://github.com/winstonjs/winston) | `^3.3.3` | Logs |

<a id="environment-setup"></a>

## 🛠️ Environment setup

Use Git, [nvm](https://github.com/nvm-sh/nvm) e Node.js **14.16.0**. Os comandos abaixo usam npm, fornecido com o Node.js. Não há lockfile versionado; use `npm install`, e não `npm ci`. A resolução das faixas de versão pode variar entre instalações.

```bash
nvm install 14.16.0
nvm use 14.16.0
git clone https://github.com/paulodaluz/BaseStructure.git
cd BaseStructure
npm install
```

`NODE_PORT` é opcional e usa `3002` como padrão em `src/index.ts`. O nodemon carrega `.env` por meio de `dotenv/config`; um exemplo local é `NODE_PORT=3002`.

O projeto declara Husky `^5.2.0`, mas a configuração `husky.hooks` segue o formato antigo. Não há `prepare` nem diretório `.husky/`: a instalação não comprova que o hook esteja ativo. O hook descrito executaria testes e `npm version patch`; revisar a configuração é uma pendência, sem instalar Husky globalmente.

<a id="getting-started"></a>

## 🚀 Getting started

```bash
npm run dev
```

O comando usa `nodemon.json` e `ts-node`. Há uma pendência: `tsconfig-paths/register` é requerido pelo nodemon, mas `tsconfig-paths` não está declarado no manifesto. Nesta instalação o módulo apareceu como dependência transitiva (`3.15.0`), mas sua disponibilidade não é um contrato direto do manifesto. A declaração deve ser revisada em uma alteração própria.

Não existe script `build`; o compilador local pode ser chamado assim:

```bash
./node_modules/.bin/tsc
npm start
```

`npm start` executa `dist/index.js`, que só existe após compilação bem-sucedida. URL padrão: `http://localhost:3002`.

```bash
curl http://localhost:3002/healthcheck
```

A rota implementada responde `{"status":"UP"}` quando o servidor está iniciado.

<a id="api-usage"></a>

## 🔌 API usage

Não há autenticação ou prefixo global.

| Método | Caminho | Finalidade |
| --- | --- | --- |
| GET | `/healthcheck` | Saúde do processo |
| GET | `/version` | Versão do manifesto |
| GET | `/exampleRoute` | JSON de exemplo |
| POST | `/exampleRoute` | Texto de exemplo |
| GET | `/exampleRouterId/:id` | Retorna o ID; exige valor numérico |

```bash
curl http://localhost:3002/exampleRouterId/1
curl -X POST http://localhost:3002/exampleRoute
```

<a id="project-structure"></a>

## 🗂️ Project structure

| Caminho | Responsabilidade |
| --- | --- |
| `src/index.ts` | Criação e início do Express |
| `src/routes/` | Rotas HTTP |
| `src/controllers/` | Validação e encaminhamento das requisições |
| `src/services/` | Respostas de exemplo e logger |
| `src/validators/` | Validação do parâmetro `id` |
| `src/tests/unit/` | Teste de soma com Jest |
| `nodemon.json` / `tsconfig.json` | Desenvolvimento e compilação |

<a id="testing-and-quality"></a>

## ✅ Testing and quality

| Comando | Escopo |
| --- | --- |
| `npm test -- --runInBand` | Teste de soma; não verifica as rotas |
| `npm run eslint` | ESLint nos arquivos TypeScript |
| `./node_modules/.bin/tsc` | Compilação para `dist/` |

Não há configuração de transformação TypeScript para Jest. O arquivo de teste atual usa sintaxe JavaScript, apesar da extensão `.ts`.

### Validation recorded

Em 11/11/2026, `npm install` concluiu com Node 14.16.0/npm 6.14.11. A compilação falhou em declarações de `@types/node` resolvidas (26.6.6) incompatíveis com TypeScript 4.9.5. O cenário de soma passou em duas suítes/duas execuções: uma no arquivo fonte e outra na cópia emitida em `dist/tests/unit/test.js`. Trata-se do mesmo teste duplicado, sem ampliar a cobertura das rotas. O início via nodemon não abriu a porta durante a tentativa; `npm start`, sobre o JavaScript emitido, falhou com `SyntaxError: Unexpected token '||='` em dependência do logger. O uso de `NODE_PORT=4200` foi temporário da validação; o padrão continua 3002. As rotas não foram verificadas.

<a id="troubleshooting"></a>

## 🔎 Troubleshooting

- `Cannot find module 'tsconfig-paths/register'`: dependência usada pelo nodemon ausente do manifesto; corrigir em tarefa de código/dependências.
- `dist/index.js` ausente: concluir a compilação antes de `npm start`.
- Versões diferentes das citadas no README antigo: o manifesto atual usa Jest e TypeScript; Chai, Mocha e concurrently não estão declarados.
- A licença do manifesto (`ISC`) diverge do arquivo `LICENSE` (`AGPL-3.0`). O arquivo foi preservado e é a referência desta documentação.

<a id="contributing"></a>

## 🤝 Contributing

Crie uma branch a partir de `master`, execute instalação, compilação, teste e ESLint e abra um pull request com o resultado de cada verificação. Revise os hooks antes de habilitá-los, pois o hook legado pretende alterar a versão do pacote.

<a id="license"></a>

## 📜 License

O arquivo [LICENSE](LICENSE) contém a **GNU Affero General Public License v3.0**. A declaração `ISC` em `package.json` é divergente e fica para revisão; nenhum texto de licença foi alterado.

---

<p align="center">Made with 💜 by <a href="https://github.com/paulodaluz">Paulo da Luz</a></p>
