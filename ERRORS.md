# Problemas identificados

## 1. Falha ao iniciar o servidor por dependência ausente
- **Sintoma**: `npm start` aborta imediatamente com `Error: Cannot find module 'google-auth-library'` ao carregar `server.js`.
- **Causa raiz**: O pacote `google-auth-library` não está presente em `node_modules`. A tentativa de `npm install` falhou com erro 403 ao buscar esse pacote no registry.
- **Onde corrigir no código**:
  - A importação acontece logo no topo de `server.js` (linhas 5–9), junto das demais dependências Express. É esse `require('google-auth-library')` que dispara o erro quando a dependência não está instalada.
  - A dependência já está declarada em `package.json` dentro de `dependencies.google-auth-library` (`^9.11.0`); o caminho correto para inserir ou ajustar a versão é a seção `"dependencies"` neste arquivo.
  - Caso precise travar o registry, crie um arquivo `.npmrc` na raiz do projeto (mesmo diretório de `package.json`) apontando para o registro acessível pelo ambiente, por exemplo: `registry=https://registry.npmjs.org/`.
  - Se quiser garantir no próprio `package.json` que o pacote correto será instalado, confirme que a entrada abaixo está presente em `dependencies` (adicione se faltar):

    ```json
    "google-auth-library": "^9.11.0"
    ```
- **Como resolver**:
  1. Garantir acesso ao registro do npm (ou ao espelho interno permitido pelo ambiente) e executar `npm install` na raiz do projeto para baixar `google-auth-library@^9.11.0` — o artefato ficará em `node_modules/google-auth-library`.
  2. Se o ambiente bloquear o registro público, configurar o `.npmrc` na raiz conforme acima ou adicionar o pacote vendorizado manualmente dentro de `node_modules/google-auth-library`.
  3. Se o registry público estiver indisponível, altere a URL em `.npmrc` para o espelho permitido (ou configure proxy corporativo) e rode `npm install` novamente.
  4. Após a instalação, rodar `npm start` novamente para confirmar que o servidor Express sobe sem erros.

> Histórico de comandos de diagnóstico: `npm start -- --help` mostrou o erro de módulo ausente, e `npm install` evidenciou o 403 ao baixar o pacote.
