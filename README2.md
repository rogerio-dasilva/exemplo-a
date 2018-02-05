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
