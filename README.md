# Passo 1 - Alterando o projeto

## Criar novo workspace2
Baixar o projeto mci-clientes-api e descompactar em kdi/workspace2/mci-clientes-api
- abrir /kdi/eclipse-jee-luna-SR2/eclipse
- na aba project explorer use o menu de contexto e escolha import -> import...

Procure por existing maven projects e depois clique em next
- clique browser, procure e selecione a pasta /kdi/workspace2/mci-clientes-api
- clique em finish

Apos termino da importação faça
- no projeto clique com menu de contexto, escolha: maven -> update project.. -> OK
- no projeto clique com menu de contexto, escolha: Run As -> maven project
- preencha em goals: clean package
- selecione: skip tests
- clique em run

Obter o tomcat 7
- no site http://tomcat.apache.org/ navegque em Download -> tomcat 7
- baixe o arquivo tar.gz do tomcat 7.0.68 ou mais recente

Decompacte o arquivo baixado apache-tomcat-7.0.68.tar.gz em /kdi

No eclipse adicione o tomcat
- no menu Windows -> Preferences -> Server -> Runtime Environments
- clique em add... escolha apache e depois Apache Tomcat V7.0 e em next
- clique em browse e procure a pasta /kdi/apache-tomcat-7.0.68 e clique em OK -> Finish -> OK
- procure a aba Servers e clique para adicionar um servidor
- escolha Tomcat 7 e next e finish

# Passo 2 - Testando a aplicação
Adicione a aplicação
- em Add and Remove.. -> Add all -> Finish
- ublique e inicie o tomcat (Run)
- acesse http://localhost:8080/mci-clientes-api/
- depois http://localhost:8080/mci-clientes-api/api/clientes

Alerando a aplicação:
- copie os jar da pasta Donwloas/lib para a pasta webapp/WEB-INF/lib

Altere o arquivo web.xml
- acrescente após welcome-file-list
```
<listener>
    <listener-class>org.jboss.weld.environment.servlet.Listener</listener-class>
</listener>
	
<servlet>
    <servlet-name>jersey-serlvet</servlet-name>
    <servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>
    <init-param>
        <param-name>com.sun.jersey.config.property.packages</param-name>
        <param-value>br.com.bb.mci</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
 
<servlet-mapping>
    <servlet-name>jersey-serlvet</servlet-name>
    <url-pattern>/api/*</url-pattern>
</servlet-mapping>
```

Adicione o plugin
- de https://addons.mozilla.org/pt-BR/firefox/addon/rested/

# Passo 3 - Modificar mci-clientes-api
Na classe cliente em mci-clientes-api
- selecionar o campo codigo e apertar a combinação de teclas Ctrl+Shit+R  duas vezes
- Abrirá uma caixa de diálogo e marque "Rename getter" e "Rename setter"
- mude o nome de codigo para mci;
 ```
 private Integer codigo; 
 ```
 para
 ```
 private Integer mci;
 ```
 Faça de novo:
 - mude o nome de documentoIdentificacao para documento;
```
private String documentoIdentificacao;
```
para
```
private String documento;
```

# Passo 4 - Inicio angularjs na aplicação Estatica
Adicione uma pasta app
- em /src/main/webapp/

Adicione arquivo index.html

```
<!DOCTYPE html>
<html lang="pt-br" ng-app>
<head>
<meta charset="UTF-8">
 
  <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>MCI Clientes AngularJS</title>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.10/angular.js"></script>
</head>
<body>
    <div>
      <label>Nome:</label>
      <input type="text" ng-model="seuNome" placeholder="Informe um nome aqui">
      <hr>
      <h1>Olá {{seuNome}}!</h1>
    </div>
</body>
</html>
```

## Resumo
O AngularJs é um framework MVC no lado do cliente. 

### Wiki-pédia:
```
Model-view-controller (MVC), em português modelo-visão-controlador, é um padrão de arquitetura de software (não confundir com um design pattern) que separa a representação da informação da interação do usuário com ele. O modelo (model) consiste nos dados da aplicação, regras de negócios, lógica e funções. Uma visão (view) pode ser qualquer saída de representação dos dados, como uma tabela ou um diagrama. É possível ter várias visões do mesmo dado, como um gráfico de barras para gerenciamento e uma visão tabular para contadores. O controlador (controller) faz a mediação da entrada, convertendo-a em comandos para o modelo ou visão. As ideias centrais por trás do MVC são a reusabilidade de código e separação de conceitos.
```

Criado como um projeto interno da Google e liberado para o público em 2009.

Ele tem como foco a criação de Single Page Applications (SPAs). Este tipo de aplicação não recarrega a página durante o seu uso, o que dá uma experiência de fluidez para o usuário.

# Passo 5 - Montando a aplicação principal clientes

Criar o arquivo app.js em /src/main/webapp/app/app.js

conteudo:
angular.module('clientes', []);

alterar /src/main/webapp/app/index.html adicionar 
<script src="/mci-clientes/app/app.js"></script>

Este é o nosso módulo principal. Este ponto por onde nós interagirmos com o framework, sendo de escopo global.
Ele recebe dois parâmetros. O primeiro o nome do módulo, o segundo o nome de todas as dependências que forem necessárias, 
mas não temos nenhuma ainda.

adicionamos o móduloa na tag html e atributo ng-app:
ng-app="clientes"

isso significa que todos os elementos filhos do html e o proprio html serão gerenciados pelo angularjs.

ng-app é chamado de diretiva, esta não existe no html. As diretivas do angularjs ampliam novos vocabulários ao html, novos truques.


# Passo 6 - Criando um Controller para clientes

adicione novo arquivo em /src/main/webapp/app/controllers
clientes.controller.js

adicione a nova entrada em /src/main/webapp/app/index.html
<script src="/mci-clientes/app/controllers/clientes.controller.js"></script>

Conteudo:
angular.module('clientes')
.controller('ClientesController', function(){
	var cliente = {
			'mci' : 1,
			'nome' : 'Pedro',
			'documento' : 123
			};
});

Vamos associoar este controller em um elemento do html. Neste caso colocaremos na tag body e modificaremos o seu conteúdo:

<body ng-controller="ClientesController">
    <div>
      MCI: {{cliente.mci}} <br/>
      Nome: {{cliente.nome}} <br/>
      Documento: {{cliente.documento}} <br/>
    </div>
</body>

Ainda não funciona pois a variável cliente ainda está no escopo privado. Também não colocaremos no escopo global.
Usaremos uma ponte de ligação do angular chamado $scope.

Vamos modificar o controller
angular.module('clientes')
.controller('ClientesController', function($scope){
	$scope.cliente = {
			'mci' : 1,
			'nome' : 'Pedro',
			'documento' : 123
			};
});

Agora devemos ver os dados que estão no Controller na View.
Esta associação entre o controller e a view chamamos de data binding

O que vimos até agora:
o papel do modelo MVC
o objeto global angular
importação do angular e criação do módulo principal
as diretivas ng-app e ng-controller
angular expression (AE)
criação do controller
conceito de data-binding










