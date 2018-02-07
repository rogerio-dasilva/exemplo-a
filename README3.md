# Passo 30 - Revisão das atividades
- utilização de rotas e views parciais: com a importação de outro componente chamado angular-route.js, adicionamos a capacidade de utilizar rotas, ou seja, mapear um caminho de url para trecho de página que será apresentada na aplicação de página única (SPA). Vimos os novos serviços e suas configurações: injetamos o ngRoute no módulo para uso da aplicação e fizemos sua configaração com $routeProvider; a diretiva ng-view para apresentará a view partial no local onde ela foi declarada; usamos o serviço $location do angular para fazer a mudança de rota pela aplicação
- acrescentamos a funcionalidade e incluir cliente e vimos novas direticas do angular: ng-show - para apresentar ou não um elemento da página - e ng-click - para realizar chamadas de método/função do nosso controlador. O uso do serviço $http.post
- acrescentamos a funcionalidade de editar cliente e aqui vimos o uso de $rootScope, que uma área de armazenamento global. O uso do serviço $http.put
- nos exercícios adicionamos mais duas views para detalhar e excluir clientes, e aqui usamos $http.delete
- colocamos validações de fomulários utilizando novas direticas ng-submit e objectos especias do formulário criado implicitamente pelo angular para manipularmos o formulário ($error, $valid, $submitted) e saber se está com os seus campos válidos com required, ng-minlength, ng-maxlength e ng-pattern;

# Passo 31 - Separação de responsabilidade com componente de serviço

# Passo 32 - Utilizando Combobox

# Passo 33 - Desafio
