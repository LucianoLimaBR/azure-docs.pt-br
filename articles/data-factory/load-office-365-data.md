---
title: Carregar dados do Office 365 usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados do Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: da3d407b39ef00154b717b54213a3b40d2d110bb
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754651"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregar dados do Office 365 usando o Azure Data Factory

Este artigo mostra como usar o Data Factory para _carregar dados do Office 365 no Armazenamento de Blobs do Azure_. Você pode seguir etapas semelhantes para copiar dados para o Azure Data Lake Gen1 ou Gen2. Consulte o [artigo de Conector do Office 365](connector-office-365.md) sobre como copiar dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir:
      
   ![Página de novo data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "o nome do data Factory *LoadFromOffice365Demo* não está disponível", insira um nome diferente para o data Factory. Por exemplo, você pode usar o nome _**seunome**_ **LoadFromOffice365Demo**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **V2**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Banco de Dados SQL do Azure e assim por diante.

3. Clique em **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir:
   
   ![Página inicial do data factory](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Introdução", selecione **Criar pipeline**.
 
    ![Criar um pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **guia Geral** do pipeline, insira "CopyPipeline" para o **Nome** do pipeline.

3. Na caixa de ferramentas de Atividades > categoria Mover e Transformar > arraste e solte **Copiar atividade** da caixa de ferramentas para a superfície do designer de pipeline. Especifique "CopyFromOffice365ToBlob" como nome da atividade.

### <a name="configure-source"></a>Configurar fonte

1. Vá para o pipeline > **guia Origem**, clique em **+New** para criar um conjunto de dados de origem. 

2. Na janela novo conjunto de novos DataSet, selecione **Office 365**e, em seguida, selecione **continuar**.
 
3. Agora você está na guia de configuração da atividade de cópia. Clique no botão **Editar** ao lado do conjunto de dados do Office 365 para continuar a configuração do dado.

    ![Configurar o conjunto de dados do Office 365 – Guia Geral](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Você vê uma nova guia aberta para o conjunto de dados do Office 365. Na **guia Geral** na parte inferior da janela Propriedades, digite "SourceOffice365Dataset" para nome.
 
5. Vá para a **guia Conexão** na janela Propriedades. Clique em **+ Novo**, ao lado da caixa de texto Serviço vinculado.

6. Na janela novo serviço vinculado, digite "Office365LinkedService" como nome, insira a ID da entidade de serviço e a chave da entidade de serviço, teste a conexão e selecione **criar** para implantar o serviço vinculado.

    ![Novo serviço vinculado do Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Depois que o serviço vinculado for criado, você retornará para as configurações do conjunto de dados. Ao lado de **tabela**, escolha a seta para baixo para expandir a lista de conjuntos de itens do Office 365 disponíveis e escolha "BasicDataSet_v0. Message_v0 "na lista suspensa:

    ![Configurar conjunto de dados do Office 365 – Tabela](./media/load-office-365-data/edit-dataset.png)

8. Agora, volte para a**guia origem** do **pipeline**  >  para continuar Configurando propriedades adicionais para extração de dados do Office 365.  O escopo do usuário e o filtro de escopo do usuário são predicados opcionais que você pode definir para restringir os dados que deseja extrair do Office 365. Consulte a seção [Propriedades do conjunto de banco de 365 do Office](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) para saber como definir essas configurações.

9. É necessário escolher um dos filtros de data e fornecer os valores de hora de início e hora de término.

10. Clique na guia **importar esquema** para importar o esquema para o conjunto de mensagens de mensagem.

    ![Configurar conjunto de dados do Office 365 – Esquema](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurar coletor

1. Vá para o pipeline > **guia Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.
 
2. Na janela novo conjunto de notícias, observe que somente os destinos com suporte são selecionados ao copiar do Office 365. Selecione **armazenamento de BLOBs do Azure**, selecione formato binário e, em seguida, selecione **continuar**.  Neste tutorial, você pode copiar dados do Office 365 em um Armazenamento de Blobs do Azure.

3. Clique no botão **Editar** ao lado do conjunto de dados do armazenamento de BLOBs do Azure para continuar a configuração do dado.

4. Na **guia Geral** da janela Propriedades, em Nome, insira "OutputBlobDataset".

5. Vá para a **guia Conexão** na janela Propriedades. Selecione **+ Novo** ao lado da caixa de texto Serviço vinculado.

6. Na janela novo serviço vinculado, digite "AzureStorageLinkedService" como nome, selecione "entidade de serviço" na lista suspensa de métodos de autenticação, preencha o ponto de extremidade de serviço, locatário, ID da entidade de serviço e chave da entidade de serviço e, em seguida, selecione salvar em implante o serviço vinculado.  Consulte [aqui](connector-azure-blob-storage.md#service-principal-authentication) para saber como configurar a autenticação de entidade de serviço para o Armazenamento de Blobs do Azure.

    ![Novo serviço vinculado de Blob](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Validar o pipeline

Selecione **Validar** na barra de ferramentas para validar as configurações de pipeline.

Você também pode ver o código JSON associado ao pipeline clicando em Código no canto superior direito.

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Na barra de ferramentas superior, selecione **Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente

Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página Execução de Pipeline, selecione **Concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

Vá para a guia **Monitorar** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **Ações** para exibir detalhes da atividade e executar o pipeline novamente.

![Monitorar o pipeline](./media/load-office-365-data/pipeline-status.png) 

Para ver a atividade em execução associada à execução do pipeline, selecione o link **Exibir atividades em execução** na coluna Ações. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **detalhes** (ícone de óculos) na coluna ações.

![Monitorar a atividade](./media/load-office-365-data/activity-status.png) 

Se esta for a primeira vez que você está solicitando dados para esse contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá a atividade de cópia status como **em andamento**, e somente quando você clicar no link "detalhes" em ações, verá o status como **RequesetingConsent**.  Um membro do grupo de aprovadores de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que seja possível prosseguir com a extração de dados.

_Status como solicitando consentimento:_ 
![Detalhes de execução de atividade – solicitar consentimento](./media/load-office-365-data/activity-details-request-consent.png) 

_Status como extraindo dados:_

![Detalhes da execução de atividade – extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Depois que o consentimento for fornecido, a extração de dados continuará e, após algum tempo, a execução do pipeline será mostrada como bem-sucedida.

![Monitorar pipeline – bem-sucedido](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora, acesse o armazenamento de BLOBs do Azure de destino e verifique se os dados do Office 365 foram extraídos em formato binário.

## <a name="next-steps"></a>Próximos passos

Ir para o seguinte artigo para saber mais sobre o suporte do SQL Data Warehouse do Azure: 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)