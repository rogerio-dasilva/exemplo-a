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
var promise = $http.get('/mci-clientes-api/api/clientes');
	
promise.then(function(retorno){
	$scope.clientes = retorno.data.listaClientes;
});
```
E se ocorre erro? A promise tem outro método em que encadeia este comportamento

O código ficará assim
```js
angular.module('clientes').controller('ClientesController', function($scope, $http) {
	
	var promise = $http.get('/mci-clientes-api/api/clientes');
	
	promise.then(function(retorno){
		$scope.clientes = retorno.data.listaClientes;
	}).catch(function(erro){
	    var id = '#msgDanger';
            $(id).text(erro.statusText);
            $(id).css('display', 'block');
            setTimeout(function () {
                $(id).css('display', 'none');
            }, 5000);
	});
});
```
Uma variação do código acima seria usar os metodos _success_ e _error_, a diferença será que no retorno não precisa usar o campo intermediário .data:
```js
   $http.get('/mci-clientes-api/api/clientesa')
	.success(function(retorno){
		$scope.clientes = retorno.listaClientes;
	}).error(function(erro){
		var id = '#msgDanger';
        $(id).text(erro);
        $(id).css('display', 'block');
        setTimeout(function () {
            $(id).css('display', 'none');
        }, 5000);
   });
```

# Exercícios
Vamos alterar a api para preencher mais alguns dados do cliente:
- altere a classe Cliente.java e adicione mais um construtor:
```java
public Cliente(Integer codigo, String nome, String documento, TipoDocumento tipo) {
		this.mci = codigo;
		this.nome = nome;
		this.documento = documento;
		this.tipoDocumento = tipo;
	}
```
- altere a classe ClienteDAO para inicializar com os dados do tipo de documento:
```java
listaClientes.add(new Cliente(1, "João", "123465-9", new TipoDocumento(1, "RG")));
listaClientes.add(new Cliente(2, "Maria","123.456.789-12", new TipoDocumento(1, "RG")));
```
- altere na classe ClienteResource para o metodo incluir ficar assim:
```java
@POST
@Consumes(MediaType.APPLICATION_JSON)
public Response incluir(Cliente cliente) {
```

## liste os clientes com o plugin do Firefox RESTED: 
- lista os clientes e vejam como fica o retorno dos dados do cliente com o tipo de documento alinhado:
- use a URL http://localhost:8080/mci-clientes-api/api/clientes
- use o Method GET
- clique em Send Request


## adicione mais cinco clientes usando o POST com o plugin RESTED
- use a URL com o mci do clientehttp://localhost:8080/mci-clientes-api/api/clientes/
- Adicione um HEADER: no campos à esquerda informe _Content-type_ e no campo à direta informe _application/json_
- escolha o Method POST
- no campo REQUEST BODY mude para CUSTOM e informe os dados json para incluir cliente
- clique em Send Request

## apresente os novos campos na Single Page Application SPA

# Fontes para consulta
- Guie de Estilo https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md, e em português https://github.com/johnpapa/angular-styleguide/blob/master/a1/i18n/pt-BR.md
- Canal YouTube https://www.youtube.com/user/rodrigobranas

