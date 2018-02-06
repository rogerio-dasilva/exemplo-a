# Passo 11 - Revisão das atividades
- utilizamos os projetos anteriores, _mci_clientes_ (Estático) e _mci-clientes-api_ (Dinâmico), importando os fontes e fizemos adaptações para o servidor Tomcat, nos arquivos pom.xml, server.xml, e criamos a pasta lib com os jar necessários para CDI e REST (estes já estão disponíveis no weblogic)
- iniciamos aconfiguração inicial do AngularJS: importando o arquivo javascript necessáro "<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>", adicionando as diretivas do angular para iniciar a aplicaço "ng-app", criamos uma variável de input com "ng-model="seuNome"" e exibimos, com angular expression, o valor da variável em outro lugar da página com a expressão "{{seuNome}}"
- criamos o módulo principal de nossa aplicação no arquivo app.js, chamado "clientes", e o referenciamos na single page application, arquivo index.html, com ng-app="clientes"
- criamos um controller para colocarmos nossa lógica da aplicação chamado 'ClientesController', e com os serviços $scope colocamos variáveis com dados fixos para nosso teste. Na página SPA informamos onde queremos que o controller seja usado para trabalharmos com os nossos dados em < body ng-controller="ClientesController" >
- em seguida reduzimos a repetição de código com a diretiva do angular ng-repeart="cliente in clientes"
- alteramos o 'ClientesController' para buscar as informações da aplicação Dinâmica (API) com o serviço $http do angular
- trabalhos com o retorno da promise do serviço $http, podendo ter sucesso ou erro.
# Passo 12 - Criando nossa própria diretiva
- vamos criar uma nova pasta em /src/main/webapp/app chamada directives 
- nesta nova posta adicionar um novo arquivo clientes.diretiva.js , que será um novo módulo, com o conteúdo:
```javascript
angular.module('minhasDiretivas', []);
```
- alteramos o módulo principal 'clientes' para ligar com o novo módulo no arquivo app.js ficando:
```javascript
angular.module('clientes', ['minhasDiretivas']);
```
- agora declaramos a nova diretiva editando o arquivo clientes.diretiva.js:
```javascript
angular.module('minhasDiretivas', []).directive('bbPainel', function(){
  var ddo = {};
  return ddo;
});
```
- neste código foi criado uma variável chamada ddo (directive definition object) com  tipo object e vazio, no fim retornamos no final do método o ddo.
- o ddo conterá a configuração da diretiva e a primeira propriedade será a forma de uso da mesmo que pode ser "E" ou "A" ou "C" para Elemento, Atributo ou Comentário respectivamente, chamada de retrict:
```javascript
angular.module('minhasDiretivas', []).directive('bbPainel', function(){
  var ddo = {};
  ddo.restrict = "AE"
  return ddo;
});
```
- por fim adicionamos no index.html a importação do arquivo < script src="/mci-clientes/app/directives/clientes.diretiva.js"></script>
