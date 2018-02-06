# Passo 11 - Revisão das atividades
- utilizamos os projetos anteriores, _mci_clientes_ (Estático) e _mci-clientes-api_ (Dinâmico), importando os fontes e fizemos adaptações para o servidor Tomcat, nos arquivos pom.xml, server.xml, e criamos a pasta lib com os jar necessários para CDI e REST (estes já estão disponíveis no weblogic)
- iniciamos aconfiguração inicial do AngularJS: importando o arquivo javascript necessáro "<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>", adicionando as diretivas do angular para iniciar a aplicaço "ng-app", criamos uma variável de input com "ng-model="seuNome"" e exibimos, com angular expression, o valor da variável em outro lugar da página com a expressão "{{seuNome}}"
- criamos o módulo principal de nossa aplicação no arquivo app.js, chamado "clientes", e o referenciamos na single page application, arquivo index.html, com ng-app="clientes"
- criamos um controller para colocarmos nossa lógica da aplicação chamado 'ClientesController', e com os serviços $scope colocamos variáveis com dados fixos para nosso teste. Na página SPA informamos onde queremos que o controller seja usado para trabalharmos com os nossos dados em < body ng-controller="ClientesController" >
- em seguida reduzimos a repetição de código com a diretiva do angular ng-repeart="cliente in clientes"
- alteramos o 'ClientesController' para buscar as informações da aplicação Dinâmica (API) com o serviço $http do angular
- trabalhos com o retorno da promise do serviço $http, podendo ter sucesso ou erro.
# Passo 12 - Criando nossa própria diretiva
