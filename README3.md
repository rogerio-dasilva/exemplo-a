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
- faça importação no index.html o script deste novo componente com < script src="/mci-clientes/app/services/clientes.servico.js"></script> logo abaixo da importação de cliente.controller.js
- colocar o conteúdo de clientes.servico.js
```javascript
angular.module('clientes').factory('ClientesService', function($http){
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
Para o campo tipo de documento, podemos recuperar os tipos de documentos pré-determinados e exibí-los em uma combobox para seleção ao invés de sempre digitar.

Assim vamos substituir o dois campos, Código do Tipo de Documento e Descrição do Tipo de Documento, por uma lista pré-definida de Tipos de Documento

- substituir os dois campos de
```html
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
```
- para:
```html
   <div class="form-group" >
        <label for="selectTipoDocumento">Tipo de documento</label>
        <select id="selectTipoDocumento" name="selectTipoDocumento" class="form-control"
            ng-controller="TiposDocumentosController"
            ng-model="selecionado.tipoDocumento"
            ng-options="tipo.descricao for tipo in tiposDocumentos track by tipo.codigo"
            required>
        </select>
        
        <span class="form-control alert-danger" ng-show="formCliente.selectTipoDocumento.$error.required && formCliente.$submitted">
               O tipo de documento é obrigatório
	</span>
    </div>
```
- remova a validação ng-pattern da input id="inputDocumento" e o elemento span que exibi sua mensagem;
- crie novo controlador com arquivo de nome tiposDocumentos.controller.js na pasta webapp/app/controllers e conteúdo:
```javascript
angular.module('clientes').controller('TiposDocumentosController', function($scope, $http, $location, $rootScope) {
	
    $scope.tiposDocumentos = {};
    
    $scope.exibirErro = function (erro) {
		var id = '#msgDanger';
        $(id).text(erro);
        $(id).css('display', 'block');
        setTimeout(function () {
            $(id).css('display', 'none');
        }, 5000);
	}
    
	$scope.listaTiposDocumentos = function(){
		$scope.tiposDocumentos = [
		    {'codigo' : 1, 'descricao': 'Registro Geral' },
		    {'codigo' : 2, 'descricao': 'Cadastro de Pessoa Física' }
		]
	}

	$scope.listaTiposDocumentos();

});
- adicione a importação do novo controller no arquivo index.html < script src="/mci-clientes/app/controllers/tiposDocumentos.controller.js"></script> , logo após a importaçãp de clientes.controller.js
```
- teste a combobox

# Passo 33 - Exercício
- adicione uma nova service para trabalhar com o Tipo de Documento, com o nome tipoDocumento.servico.js na pasta services.
- use como exemplo o serviço feito para Clientes e adicione um método para listar os tipos
- adicione a importação no arquivo index.html
- altere o controlador tiposDocumentos.controller.js para usar este novo serviço para recuperar os dados da lista de tipos de documentos

# Passo 34 - Usando filtros
Podemos transformar a presentação de dados na página através de filtros. 

O angular possui o filtro _uppercase_ que transforma todos os caracteres em maiúsculo.

Esta transformação será aplicada apenas na apresentação e não nos dados do modelo. A sintaxe é: _propriedade_ | _filtro_ : _parametro_. O parâmetro é opcional e depende de cada filtro.
- altere a página principal.html, colocando após a propriedade item.nome e item.tipoDocumento.descricao o filtro uppercase. {{item.nome | uppercase}} e {{item.tipoDocumento.descricao | uppercase}}
- veja o resultado na página

O angular possui muitos outros filtros. Por exemplo:

- currency : Formatar um número como moeda;
- date: Formatar um objeto date num formato especificado.
- filter: Selecionar items de um array.
- json: Formatar um objeto em string JSON.
- limitTo: Limitar um array/string, em um especificado número de elementos/characteres.
- lowercase: Formatar uma string em caixa baixa.
- number: Formatar um number para uma string.
- orderBy: Ordernar um array por uma expressão.
- uppercase: Formatar uma string em caixa alta.

Vamos adicionar uma propriedade do time Timestamp o objeto cliente para termos uma data de inclusão do cliente
- adicione uma propriedade (private java.util.Date incluido;) na classe Cliente.java e crie seu setter e getter
- na classe ClienteDAO.java acrescente no construtor, após o preenchimento dos dois clientes, o preenchimento da data de inclusão:
```java
for(Cliente c: listaClientes){
    c.setIncluido(new Date()));
}
```
- adicione um novo campo no arquivo detalhe.html para exibir a nova propriedade
- veja o resultado:
- coloque após o filtro date o formato desejado. Exemplo: {{campo | date : 'dd/MM/yyyy' }}

# Passo 35 - Exercícios
- no projeto mci-clientes-api acrescente a data de inclusão quando for adicionado novo cliente
- adicione uma propriedade para data de alteração. Esta data será a mesma na inclusão do cliente, e deve ser atualizada quando realizado a alteração do cadastro. Mostre a data formatada no detalhe.html
- use um formato que apresente as horas, minutos e segundos para poder ver as modificações no cliente
- retire a validação de tamanho máximo do campo nome e apresente na lista principal o filtro _limiteTo_ para limitar em até 15 caracteres. O campo nome já tem um filtro, para acrescentar outro adicione a barra | e em seguida coloque o filtro desejado. Procure como usar os parâmetros no filtro.
# Passo 36 - Filtro personalizado
- adicione uma nova pasta em webapp/app chamado filters
- adicione um nova arquivo chamado aspas.filtro.js e acreste a importação em index.html
- coloque o conteúdo:
```javascript
angular.module('clientes').filter('aspas', function() {
    return function(texto) {
    	 if (texto) {
    		return '\"' + texto + '\"';
    	} else {
    		return '';
    	}
        
    };
});
```
- coloque o filtro na página principal, no campo documento para apresentá-lo entre aspas: <td>{{item.documento | aspas}} </td>
# Passo 37 - Exercícios
- crie um novo filtro que pegue um nome e faça uma sigla com as iniciais de cada parte deste nome, em caixa alta, e aplique na página principal.html no campo nome, no lugar do uppercase e limitTo.
- 

# Passo 38 - Desafio


