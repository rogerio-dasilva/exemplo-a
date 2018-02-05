# Solução Passo 7
clientes.controller.js
```js
angular.module('clientes')
.controller('ClientesController', function($scope){
	$scope.cliente = {
			'mci' : 1,
			'nome' : 'Pedro',
			'documento' : 123
			};
	$scope.cliente2 = {
			'mci' : 2,
			'nome' : 'Maria',
			'documento' : 456
			};
	$scope.cliente3 = {
			'mci' : 3,
			'nome' : 'João',
			'documento' : 798
			};
});
```
index.html
```html
<!DOCTYPE html>
<html lang="pt-br" ng-app="clientes">
<head>
<meta charset="UTF-8">
 
  <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>MCI Clientes AngularJS</title>
    
    
    <link rel="stylesheet" href="/mci-clientes/bootstrap.min.css">
    <link rel="stylesheet" href="/mci-clientes/styles.css">
    
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>
    <script src="/mci-clientes/bootstrap.min.js"></script>
    <script src="/mci-clientes/app/app.js"></script>
    <script src="/mci-clientes/app/controllers/clientes.controller.js"></script>
</head>
<body ng-controller="ClientesController">
    <div class="jumbotron text-center">
        <h1>Clientes</h1>
    </div>
    
    <div class="container">
     <!-- Cabeçalho -->
        <div class="row">
            <h2>Listagem</h2>
        </div>
        
    <div class="row">
    <table class="table">
    	<tr>
    		<th>MCI</th><th>Nome</th><th>Documento</th>
    	<tr>
        <tr>
    		<td>{{cliente.mci}}</td><td>{{cliente.nome}}</td><td>{{cliente.documento}}</td>
    	<tr>
    	<tr>
    		<td>{{cliente2.mci}}</td><td>{{cliente2.nome}}</td><td>{{cliente2.documento}}</td>
    	<tr>
    	<tr>
    		<td>{{cliente3.mci}}</td><td>{{cliente3.nome}}</td><td>{{cliente3.documento}}</td>
    	<tr>
    </table>
    </div>
    
    </div>
</body>
</html>
```
# Passo 8 - Repetição de código
Primeiro vamos arrumar as variáveis de clientes:
- mude de três variáveis para uma variável array:
```js
$sc
```
