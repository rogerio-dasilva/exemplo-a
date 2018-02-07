# Passo 11 - Revisão das atividades
- utilizamos os projetos anteriores, _mci_clientes_ (Estático) e _mci-clientes-api_ (Dinâmico), importando os fontes e fizemos adaptações para o servidor Tomcat, nos arquivos pom.xml, server.xml, e criamos a pasta lib com os jar necessários para CDI e REST (estes já estão disponíveis no weblogic)
- iniciamos aconfiguração inicial do AngularJS: importando o arquivo javascript necessário "<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>", adicionando as diretivas do angular para iniciar a aplicaço "ng-app", criamos uma variável de input com "ng-model="seuNome"" e exibimos, com angular expression, o valor da variável em outro lugar da página com a expressão "{{seuNome}}"
- criamos o módulo principal de nossa aplicação no arquivo app.js, chamado "clientes", e o referenciamos na single page application, arquivo index.html, com ng-app="clientes"
- criamos um controller para colocarmos nossa lógica da aplicação chamado 'ClientesController', e com os serviços $scope colocamos variáveis com dados fixos para nosso teste. Na página SPA informamos onde queremos que o controller seja usado para trabalharmos com os nossos dados em < body ng-controller="ClientesController" >
- em seguida reduzimos a repetição de código com a diretiva do angular ng-repeat="cliente in clientes"
- alteramos o 'ClientesController' para buscar as informações da aplicação Dinâmica (API) com o serviço $http do angular
- trabalhos com o retorno da promise do serviço $http, podendo ter sucesso ou erro.


# Passo 12 - Criando rotas entre views
Agora vamos criar visualizações (views) para nossas ação de incluir, editar, detalhar e excluir clientes

No AngularJS temos o foco em Sigle Page Aplications (Aplicação de única página), tipo de aplicação que não recarrega seu contéudo no seu uso. Então como fazer isso sem redirecionar para outra página que recarrega tudo noavmente.

Vamos perceber agora que a página index.html não será recarregada durante a navegação, e o que permitirá isso será através do que é chamado no angular de _rotas_. Essas rotas buscam  apágina que são chamadas de views parciais. Nesse tipo de view o conteúdo html não pode ter as tags _head e body_, pois serão inseridas dentro de index.html, ou seja, a página principal.

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
        <label for="inputMci">MCI</label>
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <input type="text" class="form-control" id="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome">
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

## Solução
- arquivo edicao.html
```html
<div class="row" >
    <h2 ng-show="!selecionado.mci">Novo</h2>
    <h2 ng-show="selecionado.mci">Edição</h2>
</div>
    
<form>
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputMci">MCI</label>
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <input type="text" class="form-control" id="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome">
    </div>
    
    <div class="form-group">
        <label for="inputDocumento">Documento</label>
        <input type="text" class="form-control" id="inputDocumento" placeholder="Digite o documento" ng-model="selecionado.documento">
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoCodigo">Código do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoCodigo" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.codigo">
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoDescricao">Descrição do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoDescricao" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.descricao">
    </div>
</form>

<div class="row">
    <span class="btn btn-primary" ng-click="voltar()">Voltar</span>
    <span class="btn btn-primary" ng-click="incluir()" ng-show="!selecionado.mci">Salvar</span>
</div>
```
- arquivo principal.html
```html
<div class="row" >
    <h2>Listagem</h2>
</div>

<div class="row">
<table class="table">
	<tr>
		<th>MCI</th><th>Nome</th><th>Documento</th><th>Cód. tipo doc.</th><th>Descrição tipo doc</th>
	<tr>
	<tr ng-repeat="item in clientes">
		<tr ng-repeat="item in clientes">
        <td>{{item.mci}} </td> <td>{{item.nome}} </td><td>{{item.documento}} </td><td>{{item.tipoDocumento.codigo}} </td><td>{{item.tipoDocumento.descricao}} </td>
	</tr>
</table>
</div>

<div class="row">
    <span class="btn btn-primary" ng-click="novo()">Novo</span>
</div>
```


# Passo 15 - Editando um cliente
- vamos editar o arquivo principal.html e colocar mais uma coluna chamada "Ações":
```html
<th>Ações</th>
```
- logo abaixo adicione na lista em que está usao ng-repeat para incluir uma coluna com botão de edição:
```html
<span class="btn btn-info" ng-click="selecionar(item)">Editar</span>
```
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
# Passo 16 - Exercício
Acrescente as funcionalidades de Detalhar e Excluir um cliente:
- faça uma nova rota para cada ação
- crie as partials views, uma para cada ação
- crie os métodos necessários no controller


## Solução

Adicionalmente vamos corrigir um bug que está na aplicação mci-clientes-api:

- pare o servidor tomcat
- altere o arquivo ClienteResource.java
- altere o método alterar como se segue:
```java
	@PUT
	@Consumes(MediaType.APPLICATION_JSON)
	@Path("/{id}")
	public Response alterar(@PathParam("id") Integer id, Cliente cliente) {
		for (Cliente c : clientesDAO.getListaClientes()) {
			if (c.getMci().equals(id)) {
				c.setNome(cliente.getNome());
				c.setDocumento(cliente.getDocumento());
				if(cliente.getTipoDocumento() != null){
					c.setTipoDocumento(cliente.getTipoDocumento());
				}
				return Response.ok().entity(c).build();
			}
		}

		return Response.status(404).build();
	}
```

Em seguida

- alterar o arquivo app.js
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
	
	 $routeProvider.when('/detalhe', {
	        templateUrl: 'partials/detalhe.html',
	        controller: 'ClientesController'
	    });
	 
	 $routeProvider.when('/exclusao', {
	        templateUrl: 'partials/exclusao.html',
	        controller: 'ClientesController'
	    });
	
	$routeProvider.otherwise({ 
		templateUrl: 'partials/principal.html',
		controller: 'ClientesController'
	});
});
```
- criar o arquivo detalhe.html na pasta partials:
```html
<div class="row" >
    <h2 >Detalhe</h2>
</div>
   
<form>
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputMci">MCI</label>
        <br/>{{selecionado.mci}}
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <br/>{{selecionado.nome}}
    </div>
    <div class="form-group">
        <label for="inputDocumento">Documento</label>
        <br/>{{selecionado.documento}}
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoCodigo">Código do tipo de documento</label>
        <br/>{{selecionado.tipoDocumento.codigo}}
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoDescricao">Descrição do tipo de documento</label>
        <br/>{{selecionado.tipoDocumento.descricao}}
    </div>
</form>

<div class="row">
    <span class="btn btn-primary" ng-click="voltar()">Voltar</span>
</div>
```
- criar o arquivo exclusao.html na pasta partials:
```html
<div class="row" >
    <h2 >Exclusão</h2>
</div>
   
<form>
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputMci">MCI</label>
        <br/>{{selecionado.mci}}
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <br/>{{selecionado.nome}}
    </div>
    <div class="form-group">
        <label for="inputDocumento">Documento</label>
        <br/>{{selecionado.documento}}
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoCodigo">Código do tipo de documento</label>
        <br/>{{selecionado.tipoDocumento.codigo}}
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoDescricao">Descrição do tipo de documento</label>
        <br/>{{selecionado.tipoDocumento.descricao}}
    </div>
</form>

<div class="row">
    <span class="btn btn-primary" ng-click="voltar()">Voltar</span>
    <span class="btn btn-danger" ng-click="excluir()" >Confirmar</span>
</div>
```
- alterar o arquivo clientes.controller.js:
```javascript
angular.module('clientes').controller('ClientesController', function($scope, $http, $location, $rootScope) {
    $scope.selecionado = {};
    
    if($rootScope.selecionado){
        $scope.selecionado = $rootScope.selecionado;
    }

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
		$rootScope.selecionado = null;
		$location.url('/edicao');
	}


	$scope.voltar = function(){
		$location.url('/clientes');
	};
	
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
	
	$scope.selecionar = function(cliente){
		$rootScope.selecionado = cliente;
		$location.url('/edicao');
	}
	
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
	
	 $scope.detalhar = function(cliente){
	        $rootScope.selecionado = cliente;
	        $location.url('/detalhe');
	    };
	    
	    $scope.confirmarExclusao = function(cliente){
	        $rootScope.selecionado = cliente;
	        $location.url('/exclusao');
	    };
	 
	$scope.excluir = function(){
	        $http.delete('/mci-clientes-api/api/clientes/' + $scope.selecionado.mci)
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

	$scope.lista();

});
```
- alterar o arquivo principal.html:
```html
<div class="row" >
    <h2>Listagem</h2>
</div>

<div class="row">
<table class="table">
	<tr>
		<th>MCI</th><th>Nome</th><th>Documento</th><th>Cód. tipo doc.</th><th>Descrição tipo doc</th><th>Ações</th>
	<tr>
	<tr ng-repeat="item in clientes">
		<tr ng-repeat="item in clientes">
        <td>{{item.mci}} </td> <td>{{item.nome}} </td><td>{{item.documento}} </td><td>{{item.tipoDocumento.codigo}} </td><td>{{item.tipoDocumento.descricao}} </td>
        <td>
        <span class="btn btn-info" ng-click="detalhar(item)">Detalhar</span>
        <span class="btn btn-info" ng-click="selecionar(item)">Editar</span>
        <span class="btn btn-danger" ng-click="confirmarExclusao(item)">Excluir</span>
        <td>
	</tr>
</table>
</div>

<div class="row">
    <span class="btn btn-primary" ng-click="novo()">Novo</span>
</div>
```


# Passo 17 - Validando o formulario pelo Controller
Em um formulário html, o angular cria, implicitamente, um objeto com o mesmo nome do atributo name do formulário. Esse objeto especial dá acesso ao formulario, seus campos, e campos especiais, por exemplo, $error, $valid, $invalid e $submitted

Para usar a validação do AngularJS, precisamos abdicar da validação do html5.
- no arquivo edicao.html, na tag form, vamos desabilitar a validação padrão do html5, acrescentando o atributo novalidate.
- acrescente também a diretiva ng-submit="salvar()", e o atributo name="formCliente", ficando: < form novalidate name="formCliente" ng-submit="salvar()">
- a div, com os botões, deve ficar dentro da tag form e modificado para:
```html
 <div class="row">
    <span class="btn btn-info" ng-click="voltar()">Voltar</span>
    <button type="submit" class="btn btn-primary" >Salvar</button>
</div>
```

Podemos validar os dados, antes de submeter para a api pelo controller, acessando estes campos especiais.

- no arquivo edicao.html para cada campo de input, adicione o atributo _name_ com o mesmo nome que está no atributo _id_
- no arquivo edicao.html para cada campo de input, adicionae o atributo required em cada input
Onde está:
```html
<input type="text" class="form-control" id="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome" >
```
Ficará:
```html
<input type="text" class="form-control" id="inputNome" name="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome" required >
```
Exemplo:
```html
<div class="row" >
    <h2 ng-show="!selecionado.mci">Novo</h2>
    <h2 ng-show="selecionado.mci">Edição</h2>
</div>
    
<form novalidate name="formCliente" ng-submit="salvar()">
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputMci">MCI</label>
        <br/>{{selecionado.mci}}
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <input type="text" class="form-control" id="inputNome" name="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome" required>
    </div>
    
    <div class="form-group">
        <label for="inputDocumento">Documento</label>
        <input type="text" class="form-control" id="inputDocumento" name="inputDocumento" placeholder="Digite o documento" ng-model="selecionado.documento" required>
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoCodigo">Código do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoCodigo" name="inputTipoDocumentoCodigo" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.codigo" required>
    </div>
    <div class="form-group" >
        <label for="inputTipoDocumentoDescricao">Descrição do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoDescricao" name="inputTipoDocumentoDescricao" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.descricao" required>
    </div>
    
    <div class="row">
	    <span class="btn btn-info" ng-click="voltar()">Voltar</span>
	    <button type="submit" class="btn btn-primary" >Salvar</button>
	</div>
</form>
```

- Vamos alterar o controlador para ele decidir qual operação realizar e fazer as validaçes. Em clientes.controller.js acrescente:
```javascript
	$scope.salvar = function(){
		if($scope.formCliente.$valid){
			if($scope.selecionado.mci){
				$scope.alterar();
			} else {
				$scope.incluir();
			}	
		} else {
			var mensagem = '';
			if($scope.formCliente.inputNome.$error.required){
				mensagem+='O nome é obrigatório.\n';
			}
			if($scope.formCliente.inputDocumento.$error.required){
				mensagem+='O documento é obrigatório.\n';
			}
			if($scope.formCliente.inputTipoDocumentoCodigo.$error.required){
				mensagem+='O código do tipo de documento é obrigatório.\n';
			}
			if($scope.formCliente.inputTipoDocumentoDescricao.$error.required){
				mensagem+='A descrição do tipo de documento é obrigatório.\n';
			}			
			
			if(mensagem){
				var id = '#msgDanger';
		        $(id).text(mensagem);
		        $(id).css('display', 'block');
		        setTimeout(function () {
		            $(id).css('display', 'none');
		        }, 5000);
			}
			
		}
		
	}
```
# Passo 18 - Exercício
Temos disponíveis outros tipos de validações, por exemplo: ng-minlength, ng-maxlengh e ng-pattern.
- ng-minlength recebe um valor numérico para validar o mínimo de caracteres permitidos: ng-minlength="3"
- ng-maxlength recebe um valor numérico para validar o máximo de caracteres permitidos: ng-maxlength="20"
- ng-pattern recebe uma expressão regular para validar o formato esperado para o campo: ng-pattern="[0-9]"

Nestas validações o nome no objeto ficará sem o prefixo ng nestes casos. Exemplo: 
```javascript
$scope.formCliente.inputNome.$error.minlength   para  ng-minlength
```

As diretivas acimas precisam ser adicionadas em cada input da página edicao.html

No arquivo clientes.controller.js precisará adicionar mais validações para cada novo tipo de validação e campo.

Adicione as validações para os campos:
- nome: mínino de 5 caracteres e máximo de 20
- documento: requerido e padrão do CPF (vamos considerar que só pode ser usado CPF na aplicação, procure na internet para validar com uma expressão regular - regexp)
- codigo do tipo de documento: requerido e máximo de 2 caracteres
- descrição do tipo do documento: requerido e máximo de 15 caracteres

# Passo 19 - Validando o formulário pela View

- altere a diretiva ng-submit para ng-submit="formCliente.$valid && salvar()", ficando: < form novalidate name="formCliente" ng-submit="formCliente.$valid && salvar()">

- mude no clientes.controller.js o metodo salvar para ficar assim:
```javascript
$scope.salvar = function(){
	if($scope.selecionado.mci){
		$scope.alterar();
	} else {
		$scope.incluir();
	}
}
```
- para cada elemento input deixe apenas a validação required por enquanto.

Para cada tipo de validação precisamos definir uma mensagem para ser exibida
- inclua abaixo do campo inputNome o elemento span com a mensagem de validação:
```html
<span class="form-control alert-danger" ng-show="formCliente.inputNome.$error.required">
       O nome é obrigatório.
</span>
```
- assim para adicione as mensagens para required conforme as validações necessárias de cada campo, substituindo os nome para o campos correspondente:
```html
 <span class="form-control alert-danger" ng-show="formCliente.inputDocumento.$error.required">
      	 O documento é obrigatório.
 </span>


<span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoCodigo.$error.required">
         O código do tipo de documento é obrigatório.
</span>


<span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoDescricao.$error.required">
       A descrição do tipo de documento é obrigatório.
</span>
```

Agora, atualizando a página de edição, aparecerá a mensagem de obrigatório já na entrada.

Se não quisermos este comportamento, e sim validar quando o formulário for submetido? Mudamos ng-show="formCliente.inputNome.$error.required" para ng-show="formCliente.inputNome.$error.required && formCliente.$submitted"


# Passo 20 - Exercício
Adicione novamente as validações na página edicao.html: ng-minlength, ng-maxlengh e ng-pattern.

Adicione as validações para os campos:
- nome: mínino de 5 caracteres e máximo de 20
- documento: requerido e padrão do CPF (vamos considerar que só pode ser usado CPF na aplicação, procure na internet para validar com uma expressão regular - regexp)
- codigo do tipo de documento: requerido e máximo de 2 caracteres
- descrição do tipo do documento: requerido e máximo de 15 caracteres

As validações agora serão feitas na própria view


# Solução
- arquivo edicao.html
```html
<div class="row" >
    <h2 ng-show="!selecionado.mci">Novo</h2>
    <h2 ng-show="selecionado.mci">Edição</h2>
</div>
    
<form novalidate name="formCliente" ng-submit="formCliente.$valid && salvar()">
    <div class="form-group" ng-show="selecionado.mci">
        <label for="inputMci">MCI</label>
        <br/>{{selecionado.mci}}
    </div>
    <div class="form-group">
        <label for="inputNome">Nome</label>
        <input type="text" class="form-control" id="inputNome" name="inputNome" placeholder="Digite o nome" ng-model="selecionado.nome" 
        required
        ng-minlength="5"
        ng-maxlength="20">
        
            <span class="form-control alert-danger" ng-show="formCliente.inputNome.$error.required && formCliente.$submitted">
			       O nome é obrigatório
			</span>
			<span class="form-control alert-danger" ng-show="formCliente.inputNome.$error.minlength && formCliente.$submitted">
			       O nome deve ter no mínimo 5 caracteres
			</span>
			<span class="form-control alert-danger" ng-show="formCliente.inputNome.$error.maxlength && formCliente.$submitted">
			       O nome deve ter no máximo 20 caracteres
			</span>
    </div>

    
    <div class="form-group">
        <label for="inputDocumento">Documento</label>
        <input type="text" class="form-control" id="inputDocumento" name="inputDocumento" placeholder="Digite o documento" ng-model="selecionado.documento" 
        required
        ng-pattern="/^\d{3}\.\d{3}\.\d{3}\-\d{2}$/" >
        
            <span class="form-control alert-danger" ng-show="formCliente.inputDocumento.$error.required && formCliente.$submitted">
		      	 O documento é obrigatório
		 	</span>
		 	<span class="form-control alert-danger" ng-show="formCliente.inputDocumento.$error.pattern && formCliente.$submitted">
		      	 O documento deve estar no formato CPF (999.999.999-99)
		 	</span>
    </div>

 
    <div class="form-group" >
        <label for="inputTipoDocumentoCodigo">Código do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoCodigo" name="inputTipoDocumentoCodigo" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.codigo" 
        required
        ng-maxlength="2">
        
            <span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoCodigo.$error.required && formCliente.$submitted">
		         O código do tipo de documento é obrigatório
			</span>
			<span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoCodigo.$error.maxlength && formCliente.$submitted">
		         O código do tipo de documento dve ter no máximo 2 caracteres
			</span>
    </div>


    <div class="form-group" >
        <label for="inputTipoDocumentoDescricao">Descrição do tipo de documento</label>
        <input type="text" class="form-control" id="inputTipoDocumentoDescricao" name="inputTipoDocumentoDescricao" placeholder="Digite o código do tipo de documento" ng-model="selecionado.tipoDocumento.descricao" 
        required
        ng-maxlength="15">
        
            <span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoDescricao.$error.required && formCliente.$submitted">
		       A descrição do tipo de documento é obrigatório
			</span>
			<span class="form-control alert-danger" ng-show="formCliente.inputTipoDocumentoDescricao.$error.maxlength && formCliente.$submitted">
		       A descrição do tipo de documento deve ter no máximo 15 caracteres
			</span>
    </div>

    
    <div class="row">
	    <span class="btn btn-info" ng-click="voltar()">Voltar</span>
	    <button type="submit" class="btn btn-primary" >Salvar</button>
	</div>
</form>
```
- arquivo clientes.controller.js
```javascript
var app = (function (undefined) {
    'use strict';

    var clienteEmEdicao;

    $(document).ajaxStart(function () {
        $('#spinner').css('display', 'block');
    });

    $(document).ajaxStop(function () {
        $('#spinner').css('display', 'none');
    });

    function monitorarAlteracoesInput(id, callback) {
        $('#' + id).bind('propertychange change click keyup input paste', callback);
    }

    function mostrarMensagemSucesso(mensagem) {
        var id = '#msgSuccess';
        $(id).text(mensagem);
        $(id).css('display', 'block');
        setTimeout(function () {
            $(id).css('display', 'none');
        }, 5000);
    }

    function mostrarMensagemErro(mensagem) {
        var id = '#msgDanger';
        $(id).text(mensagem);
        $(id).css('display', 'block');
        setTimeout(function () {
            $(id).css('display', 'none');
        }, 5000);
    }

    function validaIncluirCliente() {
        var cliente = {};
        cliente.nome = $('#inputNome').val().trim();
        cliente.documento = $('#inputDocumento').val().trim();

        if (cliente.nome && cliente.documento) {
            $('#btnIncluirCliente').prop('disabled', false);
        } else {
            $('#btnIncluirCliente').prop('disabled', true);
        }
    }

    function init() {
        monitorarAlteracoesInput('inputNome', validaIncluirCliente);
        monitorarAlteracoesInput('inputDocumento', validaIncluirCliente);

        recuperaClientes(function (resposta) {
            atualizaListaClientes(resposta.listaClientes);
        });
    }

    function recuperaClientes(callback) {
        $.get('/mci-clientes-api/api/clientes', callback);
    }

    function detalharCliente(mci) {
        $.get('/mci-clientes-api/api/clientes/' + mci, function (cliente) {
            $('#tblDetalhaCliente tbody').empty();
            $('#tblDetalhaCliente tbody').append(
                '<tr><td>' + cliente.mci + '</td><td>' + cliente.nome + '</td><td>' + cliente.documento + '</td></tr>'
            );
            $('#mdlDetalhaCliente').modal('show');
        });
    }

    function atualizaListaClientes(clientes) {
        $('#tblClientes tbody').empty();
        clientes.forEach(function (cliente) {
            $('#tblClientes tbody').append(
                '<tr><td>' + cliente.mci + '</td>' +
                '<td>' + cliente.nome + '</td>' +
                '<td><div class="btn-group" role="group">' +
                '<button type="button" class="btn btn-info" onclick="app.detalharCliente(' + cliente.mci + ')">Detalhar</button>' +
                '<button type="button" class="btn btn-info" onclick="app.colocarClienteEmEdicao(' + cliente.mci + ')">Alterar</button>' +
                '<button type="button" class="btn btn-danger" onclick="app.excluirCliente(' + cliente.mci + ')">Excluir</button>' +
                '</div></td></tr>'
            );
        });
    }

    function incluirCliente() {
        var cliente = {};
        cliente.nome = $('#inputNome').val().trim();

        if (!cliente.nome) {
            window.alert('Nome não pode ser vazio!');
            return;
        }

        cliente.documento = $('#inputDocumento').val().trim();
        if (!cliente.documento) {
            window.alert('Documento não pode ser vazio!');
            return;
        }

        $.ajax({
            url: '/mci-clientes-api/api/clientes',
            type: 'POST',
            data: JSON.stringify(cliente),
            contentType: 'application/json',
            success: function () {
                mostrarMensagemSucesso('Cliente incluído com sucesso!');
                $('#inputNome').val('');
                $('#inputDocumento').val('');
                $('#mdlIncluirCliente').modal('hide');
                init();
            },
            error: function () {
                mostrarMensagemErro('Ocorreu um erro, tente mais tarde...');
            }
        });
    }

    function colocarClienteEmEdicao(mci) {
        $.get('/mci-clientes-api/api/clientes/' + mci, function (cliente) {
            clienteEmEdicao = cliente;
            $('#mciClienteEdicao').text(clienteEmEdicao.mci);
            $('#inputAlterarNome').val(clienteEmEdicao.nome);
            $('#inputAlterarDocumento').val(clienteEmEdicao.documento);
            $('#mdlAlterarCliente').modal('show');
        });
    }

    // Nova função
    function alterarCliente() {
        clienteEmEdicao.nome = $('#inputAlterarNome').val().trim();

        if (!clienteEmEdicao.nome) {
            window.alert('Nome não pode ser vazio!');
            return;
        }

        clienteEmEdicao.documento = $('#inputAlterarDocumento').val().trim();
        if (!clienteEmEdicao.documento) {
            window.alert('Documento não pode ser vazio!');
            return;
        }

        $.ajax({
            url: '/mci-clientes-api/api/clientes/' + clienteEmEdicao.mci,
            type: 'PUT',
            data: JSON.stringify(clienteEmEdicao),
            contentType: 'application/json',
            success: function () {
                $('#mdlAlterarCliente').modal('hide');
                init();
            }
        });
    }

    function excluirCliente(mci) {
        if (window.confirm('Deseja realmente excluir o cliente?')) {
            $.ajax({
                url: '/mci-clientes-api/api/clientes/' + mci,
                type: 'DELETE',
                success: function () {
                    init();
                }
            });
        }
    }

    return {
        init: init,
        detalharCliente: detalharCliente,
        incluirCliente: incluirCliente,
        alterarCliente: alterarCliente,
        colocarClienteEmEdicao: colocarClienteEmEdicao,
        excluirCliente: excluirCliente
    };
})();

app.init();
```


