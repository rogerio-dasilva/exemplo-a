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
- primeiro vamos criar uma nova pasta chamada partials em /src/main/webapp/app/ e depois, para a lista de clientes, criamos um novo arquivo chamado principal.html nesta pasta criada, com o conteúdo:
```html
<div class="row" >
    <h2>Listagem</h2>
</div>

<div class="row">
<table class="table">
	<tr>
		<th>MCI</th><th>Nome</th><th>Documento</th>
	<tr>
	<tr ng-repeat="item in clientes">
		<td>{{item.mci}} </td> <td>{{item.nome}} </td><td>{{item.documento}} </td>
	</tr>
</table>

<div class="row">
    <span class="btn btn-primary" ng-click="novo()">Novo</span>
</div>
```
- em segundo, criamos um novo arquivo chamado edicao.html na pasta partials, com o conteúdo:
```html
<div class="row" >
    <h2 ng-show="!selecionado.mci">Novo</h2>
    <h2 ng-show="selecionado.mci">Edição</h2>
</div>
    
<form>
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputNome">MCI</label>
    </div>
    <div class="form-group">
        <label for="inputDocumento">Nome</label>
        <input type="text" class="form-control" id="inputAlterarDocumento" placeholder="Digite o documento" ng-model="selecionado.nome">
    </div>
</form>

<div class="row">
    <span class="btn btn-primary" ng-click="voltar()">Voltar</span>
    <span class="btn btn-primary" ng-click="incluir()" ng-show="!selecionado.mci">Salvar</span>
</div>
```
- agora, para o comportamento para a navegação, precisamos alterar o clientes.controller.js com o código:
```javascript
angular.module('clientes').controller('ClientesController', function($scope, $http, $location) {
$scope.selecionado = {};

	$scope.lista = function(){
		$http.get('/mci-clientes-api/api/clientes')
		.success(function(retorno){
			$scope.clientes = retorno.listaClientes;
		}).error(function(erro){
			console.log(JSON.stringify(erro));
			var id = '#msgDanger';
	        $(id).text(erro);
	        $(id).css('display', 'block');
	        setTimeout(function () {
	            $(id).css('display', 'none');
	        }, 5000);
		});

	}

	$scope.novo = function(){
		$location.url('/edicao');
	}


	$scope.voltar = function(){
		$location.url('/clientes');
	};

	$scope.lista();

});
```
Com a inclusão do componente _angular-route.js_ temos um novo serviço fornecido pelo angular, o _$location_. Com ele podemos fazer redirecionamento das views de edição e lista de clientes, com os botões de ações, "Novo" e "Voltar".

- acesse a página http://localhost:8080/mci-clientes/app/ e clique em "Novo". Veja o endereço que aparece no navegador.
- clique depois em voltar e veja novamente o endereço do navegador.
- a direita da marcar "#", podemos ver a views que foi carregada a partir da configuraço de rotas.

# Passo 13 - Incluíndo um cliente
Aqui veremos mais duas diretivas do angular:
- ng-click: para executar uma função (método) do controller
- ng-show: para exibir ou não elemento do html de acordo com o resultado do valor ser true ou false.
Para incluirmos um cliente, precisamos adicionar uma nova função em clientes.controller.js.
- acrescente após o término da função $scope.voltar:
```javascript
                $scope.incluir = function(){
		$http.post('/mci-clientes-api/api/clientes', $scope.selecionado)
		.success(function(retorno){
			$scope.clientes = retorno.listaClientes;
			$scope.lista();
			$location.url('/clientes');
		}).error(function(erro){
			console.log(JSON.stringify(erro));
			var id = '#msgDanger';
	        $(id).text(erro);
	        $(id).css('display', 'block');
	        setTimeout(function () {
	            $(id).css('display', 'none');
	        }, 5000);
		});
	}
```
- agora teste a inclusão
# Passo 14 - Exercício
Acrescente os campos Documento, Tipo de Documento e Descrição do documento para inclusão pela aplicação.

# Passo 15 - Editando um cliente
- vamos editar o arquivo principal.html e colocar mais uma coluna chamada "Ações":
```html
<th>Ações</th>
```
- logo abaixo adicione na lista em que está usao ng-repeat para incluir uma coluna com botão de edição:
```html
<span class="btn btn-info" ng-click="selecionar(item)">Editar</span>

Como estamos navegando entre views, cada view tem um escopo com suas variáveis. Precisamos do cliente selecionado quando mudarmos da lista para edição. Vamos usar o $rootScope fornecido pelo angular
- altere o clientes.controller.js acrescentando um novo parâmetro no método após $location e antes do fechamento do parêntese de fechamento da função, que será $rootScope
- acrescente um if após $scope.selecionado para verificar a existência do valor no $rootScope:
```javascript
if($rootScope.selecionado){
    $scope.selecionado = $rootScope.selecionado;
}
```
- precisamos também da função que colocará no $rootScope o cliente selecionado na lista. Acrescente no mesmo arquivo:
```javascript
$scope.selecionar = function(cliente){
	$rootScope.selecionado = cliente;
	$location.url('/edicao');
}
```
- agora adicionamos o método que irá fazer a atualização no clientes.controller.js:
```javascript
$scope.alterar = function(){
		$http.put('/mci-clientes-api/api/clientes/' + $scope.selecionado.mci, $scope.selecionado)
		.success(function(retorno){
			$scope.clientes = retorno.listaClientes;
			$scope.lista();
			$location.url('/clientes');
		}).error(function(erro){
			var id = '#msgDanger';
	        $(id).text(erro);
	        $(id).css('display', 'block');
	        setTimeout(function () {
	            $(id).css('display', 'none');
	        }, 5000);
		});
		
	}
```
- no arquivo edicao.html coloque após o label onde tem MCI:
```html
<br/>{{selecionado.mci}}
```
- adicione mais um botão em seguida do que faz a inclusão:
```html
<span class="btn btn-primary" ng-click="alterar()" ng-show="selecionado.mci">Salvar</span>
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
