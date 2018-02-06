# Passo 11 - Revisão das atividades
- utilizamos os projetos anteriores, _mci_clientes_ (Estático) e _mci-clientes-api_ (Dinâmico), importando os fontes e fizemos adaptações para o servidor Tomcat, nos arquivos pom.xml, server.xml, e criamos a pasta lib com os jar necessários para CDI e REST (estes já estão disponíveis no weblogic)
- iniciamos aconfiguração inicial do AngularJS: importando o arquivo javascript necessáro "<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>", adicionando as diretivas do angular para iniciar a aplicaço "ng-app", criamos uma variável de input com "ng-model="seuNome"" e exibimos, com angular expression, o valor da variável em outro lugar da página com a expressão "{{seuNome}}"
- criamos o módulo principal de nossa aplicação no arquivo app.js, chamado "clientes", e o referenciamos na single page application, arquivo index.html, com ng-app="clientes"
- criamos um controller para colocarmos nossa lógica da aplicação chamado 'ClientesController', e com os serviços $scope colocamos variáveis com dados fixos para nosso teste. Na página SPA informamos onde queremos que o controller seja usado para trabalharmos com os nossos dados em < body ng-controller="ClientesController" >
- em seguida reduzimos a repetição de código com a diretiva do angular ng-repeart="cliente in clientes"
- alteramos o 'ClientesController' para buscar as informações da aplicação Dinâmica (API) com o serviço $http do angular
- trabalhos com o retorno da promise do serviço $http, podendo ter sucesso ou erro.

# Passo 12 - Criando rotas entre views
Agora vamos criar visualizaçes (views) para nossas ação de incluir, editar, detalhar e excluir clientes

No AngularJS temos o foco em Sigle Page Aplications (Aplicação de única página), aquele tipo de aplicação que não recarrega seu contéudo no seu uso. Então como fazer isso sem redirecionar para outra página que recarrega tudo nela.

Famos perceber agora que a página index.html não será recarregada durante a navegação, e o que permitirá isso será através do que é chamado no angular de _rotas_, e essas rotas buscam página que são chamadas de views parciais. Nesse tipo de view o conteúdo html não pode ter as tags _head e body_, pois serão inseridas dentro de index.html, ou seja, a página principal.

- vamos alterar index.html comentado a tabela de lista e seu cabeçalho:
```html
<!--
    <div class="row" >
            <h2>Listagem</h2>
    </div>
        
    <div class="row">
    <table class="table">
    	<tr>
    		<th>MCI</th><th>Nome</th><th>Documento</th><th>Tipo Documento</th><th>Descrição documento</th>
    	<tr>
    	<tr ng-repeat="item in clientes">
    		<td>{{item.mci}} </td> <td>{{item.nome}} </td><td>{{item.documento}} </td><td>{{item.tipoDocumento.codigo}} </td><td>{{item.tipoDocumento.descricao}} </td>
    	</tr>    	
</table> -->
```
-  adicionamos depois disso a diretiva ng-view assim:
```html
 <ng-view></ng-view>
```
- neste mesmo arquivo precisamos importar o componente do angular que trabalha com rotas acrescentando o script abaixo logo após o script que termina com /angularjs/1.5.10/angular.js:
```html
 <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular-route.js"></script>
```
- vamos acrescentar o componente do angular que realiza as rotas e configurá-las no arquivo /src/main/webapp/app/app.js:
```javascript
angular.module('clientes', ['ngRoute']).config(function($routeProvider){
	$routeProvider.when('/clientes', {
		templateUrl: 'partials/principal.html',
		controller: 'ClientesController'
	});
	
	$routeProvider.when('/edicao', {
		templateUrl: 'partials/edicao.html',
		controller: 'ClientesController'
	});
	
	$routeProvider.otherwise({ 
		templateUrl: 'partials/principal.html',
		controller: 'ClientesController'
	});
});
```
- precisamos criar as views parciais que serão usadas nas rotas.
- primeiro vamos criar uma nova pasta chamada partials em /src/main/webapp/app/ e depois, para a lista de clientes, criamos um novo arquivo chamado principal.html nesta pasta criada, com o contedo:
```html
<div class="row" >
    <h2>Listagem</h2>
</div>
    
<div class="row">
<table class="table">
	<tr>
		<th>MCI</th><th>Nome</th><th>Documento</th><th>Tipo Documento</th><th>Descrição documento</th>
	<tr>
	<tr ng-repeat="item in clientes">
		<td>{{item.mci}} </td> <td>{{item.nome}} </td><td>{{item.documento}} </td><td>{{item.tipoDocumento.codigo}} </td><td>{{item.tipoDocumento.descricao}} </td>
	</tr>
</table>

<div class="row">
    <span class="btn btn-primary" ng-click="novo()">Novo</span>
</div>
```



# Passo Elaborando - Criando nossa própria diretiva
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
