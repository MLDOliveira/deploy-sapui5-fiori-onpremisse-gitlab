# PT-BR - Versão em português

## deploy-sapui5-fiori-onpremisse-gitlab
Como configurar seu projeto para realizar deploy de uma aplicação sapui5 diretamente no ambiente FIORI on-premisse através do gitlab

## O que você irá aprender
* Como fazer build e deploy da sua aplicação SAPUI5 em um ambiente local.
* Como fazer deploy diretamente no seu ambiente FIORI através de um merge ou commit em seu repositório GIT do GitLab.

## Pré-requisitos
* Dentro do seu ambiente SAP acessar a transação SMICM, ir no menu "Ir Para > Serviços", anote o HOST e a PORTA que estão na linha HTTP para utilizar posteriormente
* É preciso que seu servidor FIORI esteja exposto para internet, dessa forma, confirme com o BASIS se o IP e PORTA anotados anteriormente estão expostos a internet.

## Passo a passo

### 1. Copiando arquivos
Copie os arquivos .npmrc, Gruntfile.js e .gitlab-ci.yml desse projeto para seu projeto

### 2. Configurando package.json
Verifique o arquivo package.json desse projeto e adicione as dependências ao arquivo package.json do seu projeto

![package.json](https://github.com/MLDOliveira/deploy-sapui5-fiori-onpremisse/blob/master/webapp/images/package-json.png)

### 3. Configurando arquivo ui5.yaml
A configuração do arquivo ui5.yaml é importântissíma para o correto funcionando do build e deploy, leia abaixo como cada campo deve ser preenchido

Copie o trecho de código abaixo para o final seu arquivo ui5.yaml

```javascript
 - name: ui5-task-nwabap-deployer
      afterTask: generateVersionInfo
      configuration: 
        resources:
          path: dist
          pattern: "**/*.*"
        connection:
          server: http://YOUR_FIORI_HOST:PORT
          client: 100
        authentication:
          user: SAP_USERNAME
          password: SAP_PASSWORD
        ui5:
          language: PT
          package: Z_PACKAGE
          bspContainer: Z_BSP_APP_NAME
          bspContainerText: Descrição da Aplicação BSP
          transportNo: TRANSPORTE_REQUEST_NUMBER
          calculateApplicationIndex: true
```

Após isso, preencha cada campo conforme as instruções abaixo

* server: Aqui você deve informar a URL com porta (anotado nos pré-requisitos) para acesso ao seu ambiente FIORI, caso não saiba essa informação entre na transação 
* client: Mandante
* user: Usuário do SAP ERP
* password: Senha do usuário do SAP ERP
* language: Idioma e acesso do SAP ERP
* package: Pacote SAP onde os objetos serão gravados
* bspContainer: Nome da aplicação BSP que será gerada (Limite de 15 posições)
* bspContainerText: Descrição da aplicação BSP que será gerada
* transportNo: Número da request onde as alterações serão gravadas

### 4. Endentendo o arquivo .gitlab-ci.yml
Nesse arquivo estamos configurando o Pipeline do GitLab, basicamente o que está sendo feito é, toda fez que alguma aletração for realizada na branch 'master' o GitLab irá iniciar o Pipeline e executar os 3 comandos que estão dentro da tag 'script'.

```
image: node:latest
stages:
  - deploy

deploy:
  stage: deploy
  artifacts:
    paths:
    - node_modules/
    - dist/
  script:
    - npm config set @sap:registry https://npm.sap.com
    - npm install
    - npm run build
  only:
    - master
```

### 5. Merge ou Commit na Branch do seu projeto
Após finalizar as modificações no seu projeto e enviar para seu repositório no GitLab automaticamente será iniciado um JOB com as tarefas mapeadas no arquivo .gitlab-ci.yml.
Você pode acompanhar a execução desse JOB acessando o "Repositório do seu projeto no GitLab > Menu CI/CD > Pipelines".

### 6. Confirmando deploy no seu ambiente SAP ERP
Para confirmar se o deploy foi realizado com sucesso, acesse o seu ambiente SAP, entre na transação SE80, selecione aplicações BSP e procure pelo nome da aplicação informado no campo bspContainer do passo 3.

## Fontes
* https://github.com/pfefferf/ui5-nwabap-deployer/blob/master/packages/grunt-nwabap-ui5uploader


# EN - Versão em inglês




