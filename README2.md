# Passo 8 - Repetição de código
Primeiro vamos arrumar as variáveis de clientes:
- cria uma variável chamada clientes, do tipo array no $scope
- preenche a variável clientes com os três clientes já utilizados:
```js
angular.module('clientes').controller('ClientesController', function($scope) {
	$scope.clientes = [ {
		'mci' : 1,
		'nome' : 'Pedro',
		'documento' : 123
	}, {
		'mci' : 2,
		'nome' : 'Maria',
		'documento' : 456
	}, {
		'mci' : 3,
		'nome' : 'João',
		'documento' : 798
	}

	];
});
```
Vamos arrumar a apresentação (View)

Para isso usaremos uma nova diretiva ng-repeat que será colocada na tag html <tr> do primeiro cliente.
	
A sintaxe é: ng-repeat="_apelido_ in _nomeDaVariavel_"

O apelido será preenchido com cada item do array e repedito em todo código interno da tag <tr>
	
```html
<table class="table">
   <tr>
    	<th>MCI</th><th>Nome</th><th>Documento</th>
   <tr>
   <tr ng-repeat="cliente in clientes">
    	<td>{{cliente.mci}}</td><td>{{cliente.nome}}</td><td>{{cliente.documento}}</td>
   <tr>
</table>
```
Visualmente não houve diferença, mas estruturalmente está mais organizado e permite alterações mais fáceis, ou seja, é mais flexível.

# Passo 9 - Recuperar dados do servidor
Para a aplicação ser mais util e flexível, precisamos obter as informações de uma fonte de dados.

Para isso utizaremos um outro recurso do AngularJS, serviço  $http:

- altere o arquivo /src/main/webapp/app/controllers/cliente.controller.js acrescentando um parâmetro $http na function
```js
angular.module('clientes').controller('ClientesController', function($scope, $http) {
```
Esse pedido de serviço é chamado injeção de dependência, utilizado tanto com $scope e $http.

Ao utilizar o serviço $http o que ele retorna é uma _promise_, promessa, da execução do pedido, podendo ter sucesso ou não.

Quando a promessa for cumprida, daí temos acesso aos dados retornados.

```js
var promisse = $http.get('/mci-clientes-api/api/clientes');
	
promise.then(function(retorno){
	$scope.clientes = retorno.data;
});
```




