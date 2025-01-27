---
title: Habilitar sincronização offline para o seu Aplicativo móvel do Azure (Android)
description: Aprenda a usar os aplicativos móveis do Serviço de Aplicativo para cache e sincronização de dados offline no aplicativo Android.
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9c845c09c6b05436017f98323dfa78185cd58aa6
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388991"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral
Este tutorial aborda o recurso de sincronização offline de Aplicativos móveis do Azure para Android. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o back-end remoto.

Se essa for sua primeira experiência com os Aplicativos Móveis do Azure, você deve primeiro concluir o tutorial [Criar um aplicativo do iOS]. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar o aplicativo para dar suporte à sincronização offline
Com sincronização offline você lê e grava de uma *tabela de sincronização* (usando a interface *IMobileServiceSyncTable*), que é parte de um banco de dados **SQLite** no dispositivo.

Para enviar e receber alterações entre o dispositivo e os serviços móveis do Azure, você deve usar um o *contexto de sincronização* (*MobileServiceClient.SyncContext*), que você inicializar com o banco de dados local para armazenar dados localmente.

1. Em `TodoActivity.java`, comente a definição existente de `mToDoTable` e remova a versão da tabela de sincronização:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. No método `onCreate`, comente a inicialização existente de `mToDoTable` e remova essa definição:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. Em `refreshItemsFromTable`, comente a definição de `results` e remova essa definição:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Comente a definição de `refreshItemsFromMobileServiceTable`:
5. Remova a definição de `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Remova a definição de `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testar o aplicativo
Nesta seção, você testará o comportamento com o WiFi ativado e depois o desativará para criar um cenário offline.

Quando você adiciona itens de dados, eles são mantidos no armazenamento local do SQL Lite , mas não sincronizados para o serviço móvel, até que você pressione o botão **Atualizar** . Outros aplicativos podem ter necessidades diferentes em relação a quando os dados precisam ser sincronizados, mas para fins de demonstração neste tutorial o usuário tem que solicitá-lo explicitamente.

Quando você pressiona o botão, inicia uma nova tarefa em segundo plano. Primeiro, ela envia por push todas as alterações feitas no repositório local usando o contexto de sincronização, e depois recebe todos os dados alterados do Azure para a tabela local.

### <a name="offline-testing"></a>Teste offline
1. Coloque o dispositivo ou o simulador em *Modo de Avião*. Isso cria um cenário offline.
2. Adicione alguns itens *ToDo* ou marque alguns itens como concluídos. Feche o dispositivo ou simulador (ou feche forçadamente o aplicativo) e reinicie. Por ainda estarem vigentes no armazenamento do SQL Lite local, as alterações ficam mantidas no dispositivo.
3. Exiba os conteúdos da tabela *TodoItem* do Azure com uma ferramenta SQL, como o *SQL Server Management Studio*, ou então com um cliente REST, como o *Fiddler* ou o *Postman*. Confirme que os novos itens *não* foram sincronizados com o servidor
   
       + Para um back-end Node.js, vá para o [portal do Azure](https://portal.azure.com/) e no back-end do aplicativo móvel clique em **Tabelas Fáceis** > **TodoItem** para exibir o conteúdo da tabela `TodoItem`.
       + Para um back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL como o *SQL Server Management Studio*, ou então com um cliente REST, como o *Fiddler* ou o *Postman*.
4. Ative o WiFi no simulador ou dispositivo. Em seguida, pressione o botão **Atualizar** .
5. Exiba os dados de TodoItem novamente no portal do Azure. Os TodoItems novos e modificados agora devem aparecer.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicativos Móveis do Azure]
* [Cloud Cover: sincronização offline em serviços móveis do Azure] \(observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure\)

<!-- URLs. -->

[Sincronização de Dados Offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md

[Criar um aplicativo do iOS]: app-service-mobile-android-get-started.md

[Cloud Cover: sincronização offline em serviços móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

