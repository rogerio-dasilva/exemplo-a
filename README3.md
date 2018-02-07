# Passo 30 - Revisão das atividades
- utilização de rotas e views parciais: com a importação de outro componente chamado angular-route.js, adicionamos a capacidade de utilizar rotas, ou seja, mapear um caminho de url para trecho de página que será apresentada na aplicação de página única (SPA). Vimos os novos serviços e suas configurações: injetamos o ngRoute no módulo para uso da aplicação e fizemos sua configaração com $routeProvider; a diretiva ng-view para apresentará a view partial no local onde ela foi declarada; usamos o serviço $location do angular para fazer a mudança de rota pela aplicação
- acrescentamos a funcionalidade e incluir cliente e vimos novas direticas do angular: ng-show - para apresentar ou não um elemento da página - e ng-click - para realizar chamadas de método/função do nosso controlador. O uso do serviço $http.post
- acrescentamos a funcionalidade de editar cliente e aqui vimos o uso de $rootScope, que uma área de armazenamento global. O uso do serviço $http.put
- nos exercícios adicionamos mais duas views para detalhar e excluir clientes, e aqui usamos $http.delete
- colocamos validações de fomulários utilizando novas direticas ng-submit e objectos especias do formulário criado implicitamente pelo angular para manipularmos o formulário ($error, $valid, $submitted) e saber se está com os seus campos válidos com required, ng-minlength, ng-maxlength e ng-pattern;

# Passo 31 - Separação de responsabilidade com componente de serviço
Para separar a responsabilidade de acessar o end-point REST, com todas as operações disponíveis, vamos separar nosso código em um novo componente chamadado Serviço (Service) no angular.
- crie uma pasta com nome _services_ dentro de webapp/app/
- crie novo arquivo de nome clientes.servico.js nesta nova pasta
- faça importação no index.html o script deste novo componente com < script src="/mci-clientes/app/sevices/clientes.servico.js"></script> logo abaixo da importação de cliente.controller.js
- colocar o conteúdo de clientes.servico.js
```javascript
angular.module('clientes').factory('ClientesService', function($q, $http){
	var service = {};
	
	service.listar = function(resolve,reject){
		$http.get('/mci-clientes-api/api/clientes').success(function(retorno){
			resolve(retorno.listaClientes);
		}).error(function(erro){
			reject(erro);
		});
	}
	
	service.recuperar = function(mci,resolve,reject){
		$http.get('/mci-clientes-api/api/clientes/' + mci).success(function(retorno){
			resolve(retorno);
		}).error(function(erro){
			reject(erro);
		});
	}
	
	service.incluir = function(cliente, resolve, reject) {
		$http.post('/mci-clientes-api/api/clientes', cliente)
		.success(function(retorno){
			resolve();
		}).error(function(erro){
			reject(erro);
		});
	}
	
	service.alterar = function(cliente, resolve, reject){
		$http.put('/mci-clientes-api/api/clientes/' + cliente.mci, cliente)
		.success(function(retorno){
			resolve();
		}).error(function(erro){
			reject(erro);
		});
	}
	
	service.excluir = function(mci, resolve, reject){
        $http.delete('/mci-clientes-api/api/clientes/' + mci)
        .success(function(retorno){
        	resolve();
        }).error(function(erro){
        	reject(erro);
        });
    } 
	
	return service;
});
```
- altere o conteúdo de clientes.controller.js
```javascript
angular.module('clientes').controller('ClientesController', function($scope, $http, $location, $rootScope, ClientesService) {
	
	
    $scope.selecionado = {};
    
    $scope.exibirErro = function (erro) {
		var id = '#msgDanger';
        $(id).text(erro);
        $(id).css('display', 'block');
        setTimeout(function () {
            $(id).css('display', 'none');
        }, 5000);
	}
    
    if($rootScope.selecionado){
        $scope.selecionado = $rootScope.selecionado;
    }

	$scope.lista = function(){
		ClientesService.listar(function(clientes){
			$scope.clientes = clientes;
		}, function(erro){
			$scope.exibirErro(erro);
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
		ClientesService.incluir($scope.selecionado, function(){
			$location.url('/clientes');
		},function(erro){
			$scope.exibirErro(erro);
		});
	}
	
	$scope.selecionar = function(cliente){
		ClientesService.recuperar(cliente.mci, function(resposta){
			$rootScope.selecionado = resposta;
			$location.url('/edicao');
		},function(erro){
			$scope.exibirErro(erro);
		});
	}
	
	$scope.alterar = function(){
		ClientesService.alterar($scope.selecionado, function(){
			$location.url('/clientes');
		},function(erro){
			$scope.exibirErro(erro);
		});
	}
	
	 $scope.detalhar = function(cliente){
        ClientesService.recuperar(cliente.mci, function(resposta){
			$rootScope.selecionado = resposta;
			$location.url('/detalhe');
		},function(erro){
			$scope.exibirErro(erro);
		});
    };
	    
    $scope.confirmarExclusao = function(cliente){
        ClientesService.recuperar(cliente.mci, function(resposta){
			$rootScope.selecionado = resposta;
			$location.url('/exclusao');
		},function(erro){
			$scope.exibirErro(erro);
		});
    };
	 
	$scope.excluir = function(){
		ClientesService.excluir($scope.selecionado.mci, function(){
			$location.url('/clientes');
		},function(erro){
			$scope.exibirErro(erro);
		});       
	}
	
	$scope.salvar = function(){
		if($scope.selecionado.mci){
			$scope.alterar();
		} else {
			$scope.incluir();
		}
	}

	$scope.lista();

});
```

# Passo 32 - Utilizando Combobox

# Passo 33 - Desafio
