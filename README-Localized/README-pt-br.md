---
page_type: sample
products:
- office-365
- office-outlook
languages:
- javascript
extensions:
  contentType: samples
  createdDate: 12/9/2014 3:58:58 PM
---
## Cordova Mail Client App

Este aplicativo de exemplo mostra o passo a passo de como criar a partir do zero um aplicativo Cordova usando o Ionic Framework e os serviços do O365 Outlook.

É altamente recomendável ler [Ferramentas do Visual Studio para Apache Cordova](http://www.visualstudio.com/en-us/explore/cordova-vs.aspx) e [Introdução às ferramentas do Visual Studio para Apache Cordova](http://msdn.microsoft.com/en-us/library/dn771545.aspx) para configurar o ambiente de desenvolvimento do Cordova.

Neste tutorial, você verá estas etapas

1. Criar Cordova em branco usando o Visual Studio
2. Adicionar o Ionic Framework
3. Adicionar os serviços O365 ao aplicativo
4. Definir permissões para o locatário de e-mail do O365 para conceder acesso apropriado ao aplicativo
5. Criar uma estrutura de pastas de aplicativos, layout e roteamento da interface do usuário usando controles e navegação Ionic
6. Adquirir um token de acesso e obter o cliente de serviços do Outlook usando o factory do AngularJS
7. Usar a API do O365 para buscar a.) E-mails sinalizados como importantes, b.) E-mails não lidos e c.) Todos os e-mails
8. Usar a API do O365 para excluir e-mails
9. Executar o aplicativo!

![Página de logon do aplicativo](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/login.png)
![Exibição de e-mail de aplicativo](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Mail-list.png)
![Caixa de diálogo de confirmação de e-mail de exclusão do aplicativo](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Mail-delete.png)

### Etapa 1: Criar Cordova em branco usando o Visual Studio
Crie um novo projeto do Cordova no Visual Studio escolhendo: Arquivo –> Novo projeto –> JavaScript –> Aplicativos Apache Cordova –> Modelo de aplicativo em branco. Este exemplo usa código JavaScript, mas também é possível escrever seu aplicativo do Cordova em TypeScript.

### Etapa 2: Adicionar o Ionic Framework
1.	No site do Ionic Framework, escolha baixar versão beta.
2.	Extraia o zip
3.	Gerenciador de soluções do Visual Studio crie uma nova pasta chamada lib em projeto do Cordova e copie o conteúdo extraído para esta pasta.

![Estrutura de pastas para o projeto](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Ionic.png)

**Atualize as referências de script**
- e em index.html, adicione as seguintes referências do Ionic ao elemento ``` <head> ```, depois das referências de script do Cordova e de platformOverrides.

```html
<script src="lib/ionic/js/ionic.bundle.min.js"></script>
```
- No index.html, adicione a seguinte referência css do Ionic.
```html
 <link href="lib/ionic/css/ionic.min.css" rel="stylesheet" />
```
### Etapa 3: Adicionar os serviços O365 ao aplicativo
Confira na documentação [Configurar seu ambiente de desenvolvimento do Office 365](http://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment) sobre como se Inscrever no site de desenvolvedor do Office 365 e Configurar o acesso ao Azure Active Directory para seu site de desenvolvedor.

Siga estas etapas para adicionar e configurar as APIs do Office 365 usando o Gerenciador de Serviços do Visual Studio.

1. Baixar e instalar as Ferramentas de API do Office 365 na Galeria do Visual Studio
2. No nó do projeto, clique com o botão direito do mouse e escolha **Adicionar –> Serviço conectado**
3. Na parte superior da caixa de diálogo Gerenciador de Serviços, escolha o link do Office 365 e, em seguida, escolha Registrar seu aplicativo. Entre com uma conta de administrador locatário na sua organização de desenvolvedor do Office 365.
![Caixa de diálogo de entrada do Gerenciador de Serviços do O365](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/ServiceManager.png)

### Etapa 4: Definir permissões para o locatário de e-mail do O365 para conceder acesso apropriado ao aplicativo
Selecione E-mail e clique em Permissões... link no painel direito e, em seguida, selecione ler e escrever para o e-mail do usuário já que o aplicativo deve executar a operação de ler e excluir e-mail. Da mesma forma, se quiser que o aplicativo envie e-mails, selecione e-mail como uma opção de usuário.

![Escopos de permissão do O365 para caixa de diálogo do e-mail](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Mail-permission.png)

Clique em Aplicar e em OK para definir a permissão e adicionar a API do O365 ao projeto. Isso adicionará ao projeto a pasta Serviço que contêm as bibliotecas JavaScript.

![árvore de pastas do projeto depois de adicionar permissões](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/service-folder.png)

Em index.html, adicione as seguintes referências do O365 ao elemento ``` <head>```.
```html
<script src="services/office365/scripts/o365loader.js"></script>  
<script src="services/office365/settings/settings.js"></script>
```
**Etapa 5: Criar uma estrutura de pastas de aplicativos, layout e roteamento da interface do usuário usando controles e navegação Ionic**

1. Criar uma pasta de aplicativos no nó da raiz do projeto. A pasta de aplicativos conterá os arquivos específicos do aplicativo. Cada componente da interface do usuário que busca e vincula os dados à interface do usuário terá um controlador correspondente, semelhante à interface do usuário e ao código oculto do padrão. Por exemplo, mail-list.html mostrará o controle de lista para exibir os e-mails do usuário e mail-list-ctrl.js conterá código para buscar e-mails de usuários usando a API do O365.

![estrutura de árvore da pasta do projeto para o aplicativo](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Mail-app-folder.png)

**Detalhes da pasta e do arquivo:**
- **auth** contém a interface do usuário e o código para entrar e sair
- **layout** contém a interface do usuário para exibir o conteúdo do aplicativo e a navegação, como o painel, os menus laterias, a barra de navegação do ion e o código para associar o nome de usuário.
- **app.js** contém o roteamento da interface do usuário para navegar por páginas diferentes
- **service-o365.js** contém a função utilitária para obter o token de acesso, criar objeto de clientes de serviços do Outlook, sair e obter o nome de usuário. Isso é implementado como uma factory Angular para que essas funções possam ser expostas como funções utilitárias em diferentes páginas.

**Aplicativo.js de exemplo definindo o roteamento ui**
```javascript
// Layout page
    .state('app', {
        abstract: true,
        url: "/app",
        templateUrl: "app/layout/layout.html"
    })

    // Sign-in page
     .state('sign-in', {
         url: "/sign-in",
         templateUrl: "app/auth/sign-in.html"
     })   

    // Mail list page
    .state('app.mail', {
        url: "/mail",
        views: {
            'mainContent': {
                templateUrl: "app/mail/mail-tabs.html"
            }
        }
    })

    // Mail list containing mails flagged as important
    .state('app.mail.imp', {
        url: "/imp/id:important",
        views: {
            "tab-imp-mail": {
                templateUrl: "app/mail/mail-list.html"
            }
        }
    })

    // Mail detail page
    .state('app.mail-detail', {
        url: "/mail/:id",
        views: {
            'mainContent': {
                templateUrl: "app/mail/mail-detail.html"
            }
        }
    });   

    // Navigate to sign-in page when app starts.
    $urlRouterProvider.otherwise('sign-in');
```
**Layout do aplicativo (menu, barra de navegação)**
```html
<ion-side-menus ng-controller="layoutCtrl as vm">
    <ion-pane ion-side-menu-content>
        <ion-nav-bar class="bar-positive">
            <ion-nav-back-button class="button-clear icon ion-ios7-arrow-back"></ion-nav-back-button>
            <button menu-toggle="left" class="button button-icon icon ion-navicon"></button>
        </ion-nav-bar>
        <ion-nav-view name="mainContent" animation="slide-left-right"></ion-nav-view>
    </ion-pane>

    <ion-side-menu side="left">
        <header class="bar bar-header bar-positive">
            <h1 class="title">{{vm.userName}}</h1>
        </header>
        <ion-content class="has-header">
            <ion-list>                            
                <ion-item nav-clear menu-close ui-sref="app.sign-out">Sign-out</ion-item>
            </ion-list>
    </ion-side-menu>
</ion-side-menus>
```
**Crie página de guia de e-mail para mostrar todos os e-mails importantes e não lidos em guias diferentes**
```html
<ion-view>
    <ion-tabs class="tabs-positive tabs-icon-top">
        <ion-tab title="Imp" icon="ion-star" ui-sref="app.mail.imp">
            <ion-nav-view name="tab-imp-mail"></ion-nav-view>
        </ion-tab>
        <ion-tab title="Unread" icon="ion-ios7-email-outline" ui-sref="app.mail.unread">
            <ion-nav-view name="tab-unread-mail"></ion-nav-view>
        </ion-tab>
        <ion-tab title="All" icon="ion-email" ui-sref="app.mail.all">
            <ion-nav-view name="tab-all-mail"></ion-nav-view>
        </ion-tab>
    </ion-tabs>
</ion-view>
```

### Etapa 6: Adquirir um token de acesso e obter o cliente de serviços do Outlook usando o factory do AngularJS

**Adquirir um token de acesso**
```javascript
var authContext = new O365Auth.Context();
authContext.getIdToken("https://outlook.office365.com/")
.then((function (token) {
     // Get auth token
     authtoken = token;
     // Get user name from token object.
     userName = token.givenName + " " + token.familyName;
    }), function (error) {
      // Log sign-in error message.
      console.log('Failed to login. Error = ' + error.message);
 });
```
**criar objeto de cliente de serviços do Outlook**
```javascript
var outlookClient = new Microsoft.OutlookServices.Client('https://outlook.office365.com/api/v1.0', authtoken.getAccessTokenFn('https://outlook.office365.com'));
```
### Etapa 7: Usar a API do O365 para buscar a.) E-mails sinalizados como importantes, b.) E-mails não lidos e c.) Todos os e-mails

**Buscar todos os e-mails marcados como importantes**
```javascript
 function getImpMails() {          
 // Filter to fetch all important mails received after 2000-10-20
var filterQuery = "Importance eq 'High' and DateTimeReceived gt 2000-10-20";
outlookClient.me.folders.getFolder("Inbox").messages.getMessages().filter(filterQuery)
.orderBy('Importance,DateTimeReceived desc').fetch()           
 .then(function (mails) {
   // Get current page. Use getNextPage() to fetch next set of mails.
   vm.mails = mails.currentPage;
   $scope.$apply();                   
  }, function (error) {
     console.log("Error encountered while fetching mails. Error: " + error.message);
    });            
 };
```

**Buscar todos os e-mails não lidos**
```javascript
var filterQuery = 'IsRead eq false';
outlookClient.me.folders.getFolder("Inbox").messages.getMessages().filter(filterQuery).fetch()            
.then(function (mails) {
    // Get current page. Use getNextPage() to fetch next set of mails.
    vm.mails = mails.currentPage;
    $scope.$apply();                   
 });     
```
**Fetch all mails**
```javascript
outlookClient.me.folders.getFolder("Inbox").messages.getMessages().fetch()            
.then(function (mails) {
    // Get current page. Use getNextPage() to fetch next set of mails.
    vm.mails = mails.currentPage;
    $scope.$apply();                   
 }); 
```

### Etapa 8: Usar a API do O365 para excluir e-mails
Objeto de cliente do Outlook pode ser usado para excluir e-mails. Primeiro, obtenha os e-mails que você desja excluir usando a ID de e-mail e, em seguida, chame delete() no objeto de e-mail específico. Delete() exclui permanentemente os e-mails. Para mover os e-mails para itens excluídos, use a função move().
```javascript
 outlookClient.me.folders.getFolder("Inbox").messages.getMessage(mail.id).fetch()
 .then(function (mail) {
     // Delete the mail.
     mail.delete()
     .then((function (response) {
          console.log('Mail deleted successfully.');
      }), function (error) {                            
          console.log('Fail to delete mail. Error = ' + error.message);                            
  });
});
```

### Etapa 9: Executar o aplicativo

1. Selecione Android e o destino como um Emulador ou Dispositivo Android. Observe que atualmente o Ripple não é compatível com a autenticação do O365.

![barra de seleção da plataforma de destino](https://github.com/abhikum/mobiledev/blob/gh-pages/O365AppImages/Android%20-%20Run.PNG)

**F5 para executar o aplicativo**

Confira em [Implantar e executar seu aplicativo criado com as ferramentas do Visual Studio para Apache Cordova](http://msdn.microsoft.com/en-us/library/dn757049.aspx) mais detalhes sobre como executar o aplicativo Cordova em diferentes plataformas.


Este projeto adotou o [Código de Conduta de Código Aberto da Microsoft](https://opensource.microsoft.com/codeofconduct/).  Para saber mais, confira [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate [opencode@microsoft.com](mailto:opencode@microsoft.com) se tiver outras dúvidas ou comentários.
