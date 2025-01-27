---
title: Executar um pacote do SSIS com a atividade executar pacote SSIS-Azure | Microsoft Docs
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) em um pipeline Azure Data Factory usando a atividade executar pacote do SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 965bb0641aac3224ac98820006f308e6b5fb0f71
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255647"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote SSIS com a atividade Executar pacote SSIS no Azure Data Factory
Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) em um pipeline Azure Data Factory usando a atividade executar pacote do SSIS. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um IR (tempo de execução de integração) do Azure-SSIS, se você ainda não tiver um, seguindo as instruções passo a passo no [Tutorial: Provisionando o Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal do Azure
Nesta seção, você usará o Data Factory interface do usuário ou o aplicativo para criar um pipeline Data Factory com uma atividade executar pacote do SSIS que executa seu pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS
Nesta etapa, você usa a interface do usuário do Data Factory ou o aplicativo para criar um pipeline. Adiciona uma atividade Executar pacote SSIS ao pipeline e configure-a para executar o pacote SSIS. 

1. Em sua visão geral do Data Factory ou home page no portal do Azure, selecione o bloco **criar & monitor** para iniciar a interface do usuário do data Factory ou o aplicativo em uma guia separada. 

   ![Home page do Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **Introdução**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na caixa de ferramentas **atividades** , expanda **geral**. Em seguida, arraste uma atividade **executar pacote do SSIS** para a superfície do designer de pipeline. 

   ![Arraste uma atividade Executar pacote SSIS para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na guia **geral** da atividade executar pacote do SSIS, forneça um nome e uma descrição para a atividade. Defina valores de **tempo limite** e de **repetição** opcionais.

   ![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na guia **configurações** da atividade executar pacote SSIS, selecione um Azure-SSIS ir em que você deseja executar o pacote. Se o pacote usar a autenticação do Windows para acessar armazenamentos de dados (por exemplo, servidores SQL ou compartilhamentos de arquivos locais ou do Azure), marque a caixa de seleção **autenticação do Windows** . Insira os valores para as credenciais de execução do pacote nas caixas **domínio**, **nome de usuário**e **senha** . 

    Como alternativa, você pode usar os segredos armazenados em seu cofre de chaves do Azure como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado da credencial relevante. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome ou a versão do segredo para seu valor de credencial.

    Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seus segredos diretamente no seguinte formato: `<Key vault linked service name>/<secret name>/<secret version>`. Se seu pacote precisar de tempo de execução de 32 bits para executar, marque a caixa de seleção **tempo de execução de 32 bits** .

   Para **local do pacote**, **selecione SSISDB**, **sistema de arquivos (pacote)** ou **sistema de arquivos (projeto)** . Se você selecionar **SSISDB** como o local do pacote, que será selecionado automaticamente se o Azure-SSIS ir tiver sido provisionado com o catálogo do SSIS (SSISDB) hospedado por um servidor de banco de dados SQL do Azure ou instância gerenciada, especifique o pacote a ser executado que foi implantado em SSISDB. 

    Se o Azure-SSIS IR estiver em execução e a caixa de seleção **entradas manuais** estiver desmarcada, procure e selecione suas pastas, projetos, pacotes ou ambientes existentes do SSISDB. Selecione **Atualizar** para buscar suas pastas, projetos, pacotes ou ambientes recém-adicionados do SSISDB para que fiquem disponíveis para navegação e seleção. Para procurar ou selecionar os ambientes para suas execuções de pacote, você deve configurar seus projetos com antecedência para adicionar esses ambientes como referências das mesmas pastas em SSISDB. Para obter mais informações, consulte [criar e mapear ambientes SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 

   ![Definir propriedades na guia Configurações — Automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se o Azure-SSIS IR não estiver em execução ou se a caixa de seleção **entradas manuais** estiver marcada, insira os caminhos do pacote e do ambiente do SSISDB diretamente nos seguintes formatos: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se você selecionar **sistema de arquivos (pacote)** como o local do pacote, que será selecionado automaticamente se o Azure-SSIS ir tiver sido provisionado sem o SSISDB, especifique o pacote a ser executado fornecendo um caminho UNC (Convenção de nomenclatura universal) ao arquivo de pacote (@no __t-1) na caixa **caminho do pacote** . Por exemplo, se você armazenar o pacote em arquivos do Azure, o caminho do pacote será `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Se você configurar o pacote em um arquivo separado, também precisará fornecer um caminho UNC para o arquivo de configuração (`.dtsConfig`) na caixa **caminho de configuração** . Por exemplo, se você armazenar sua configuração em arquivos do Azure, seu caminho de configuração será `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se você selecionar **sistema de arquivos (projeto)** como o local do pacote, especifique o pacote a ser executado fornecendo um caminho UNC para o arquivo de projeto (`.ispac`) na caixa **caminho do projeto** e um arquivo de pacote (`.dtsx`) do seu projeto no **nome do pacote** quadro. Por exemplo, se você armazenar seu projeto em arquivos do Azure, seu caminho de projeto será `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Em seguida, especifique as credenciais para acessar seu projeto, pacote ou arquivos de configuração. Se você inseriu anteriormente os valores para suas credenciais de execução de pacote (consulte anterior), você pode reutilizá-los selecionando o **mesmo que as credenciais de execução de pacote** caixa de seleção. Caso contrário, insira os valores para o pacote de credenciais de acesso nas caixas **domínio**, **nome de usuário**e **senha** . Por exemplo, se você armazenar seu projeto, pacote ou configuração em arquivos do Azure, o domínio será `Azure`, o nome de usuário será `<storage account name>` e a senha será `<storage account key>`. 

   Como alternativa, você pode usar os segredos armazenados em seu cofre de chaves como seus valores (consulte anterior). Essas credenciais são usadas para acessar o pacote e pacotes filho na tarefa Executar Pacote, tudo a partir de seu próprio caminho ou do mesmo projeto, bem como configurações, que incluem aqueles especificados em seus pacotes. 
   
   Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SQL Server Data Tools, insira o valor para sua senha na caixa **senha de criptografia** . Como alternativa, você pode usar um segredo armazenado em seu cofre de chaves como seu valor (consulte anterior). Se você usou o nível de proteção **EncryptSensitiveWithUserKey** , insira novamente seus valores confidenciais nos arquivos de configuração ou nas guias **parâmetros do SSIS**, **gerenciadores de conexões**ou **substituições de propriedades** (consulte mais adiante). 

   Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte. Você precisa reconfigurar o pacote para usar outro nível de proteção por meio de SQL Server Data Tools ou o utilitário de linha de comando `dtutil`. 
   
   Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique a pasta de log fornecendo seu caminho UNC na caixa **caminho de log** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o caminho de log será `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Uma subpasta é criada nesse caminho para cada execução de pacote individual e nomeada depois da ID de execução de atividade do pacote SSIS de execução, na qual os arquivos de log são gerados a cada cinco minutos. 
   
   Por fim, especifique as credenciais para acessar a pasta de log. Se você inseriu anteriormente os valores para suas credenciais de acesso de pacote (veja anteriormente), poderá reutilizá-los selecionando a caixa de seleção **igual a pacotes de credenciais de acesso** . Caso contrário, insira os valores para suas credenciais de acesso de log nas caixas **domínio**, **nome de usuário**e **senha** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio será `Azure`, o nome de usuário será `<storage account name>` e a senha será `<storage account key>`. 

    Como alternativa, você pode usar os segredos armazenados em seu cofre de chaves como seus valores (consulte anterior). Essas credenciais são usadas para armazenar seus logs. 
   
   Para todos os caminhos UNC mencionados anteriormente, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

1. Na guia **parâmetros SSIS** da atividade executar pacote SSIS, se o Azure-SSIS ir estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, o SSIS existente os parâmetros no projeto ou pacote selecionado do SSISDB são exibidos para você atribuir valores a eles. Caso contrário, você pode inseri-los um a um para atribuir valores a eles manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools e o **sistema de arquivos (pacote)** ou o **sistema de arquivos (projeto)** for selecionado como o local do pacote, você também precisará reinserir seus parâmetros confidenciais para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores aos parâmetros, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis do sistema e Data Factory parâmetros ou variáveis de pipeline. Como alternativa, você pode usar os segredos armazenados em seu cofre de chaves como seus valores (consulte anterior).

   ![Definir propriedades na guia Parâmetros SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na guia **gerenciadores de conexões** da atividade executar pacote SSIS, se o Azure-SSIS ir estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, a existente os gerenciadores de conexões no projeto ou pacote selecionado do SSISDB são exibidos para que você atribua valores às suas propriedades. Caso contrário, você pode inseri-las uma a uma para atribuir valores às suas propriedades manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools e o **sistema de arquivos (pacote)** ou o **sistema de arquivos (projeto)** for selecionado como o local do pacote, você também precisará reinserir suas propriedades confidenciais do Gerenciador de conexões para atribuir valores a elas em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores às suas propriedades do Gerenciador de conexões, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis do sistema e Data Factory parâmetros ou variáveis de pipeline. Como alternativa, você pode usar os segredos armazenados em seu cofre de chaves como seus valores (consulte anterior).

   ![Definir propriedades na guia Gerenciadores de Conexões](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na guia **substituições de propriedade** da atividade executar pacote SSIS, insira os caminhos das propriedades existentes no pacote selecionado um a um para atribuir valores a elas manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. Por exemplo, para substituir o valor de sua variável de usuário, insira seu caminho no seguinte formato: `\Package.Variables[User::<variable name>].Value`. 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools e o **sistema de arquivos (pacote)** ou o **sistema de arquivos (projeto)** for selecionado como o local do pacote, você também precisará reinserir suas propriedades confidenciais para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Ao atribuir valores às suas propriedades, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis do sistema e Data Factory parâmetros ou variáveis de pipeline.

   ![Definir propriedades na guia Substituições de Propriedades](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Os valores atribuídos nos arquivos de configuração e na guia **parâmetros do SSIS** podem ser substituídos usando as guias **gerenciadores de conexões** ou **substituições de propriedades** . Os valores atribuídos na guia **gerenciadores de conexões** também podem ser substituídos usando a guia **substituições de propriedade** .

1. Para validar a configuração de pipeline, selecione **validar** na barra de ferramentas. Para fechar o **relatório de validação de pipeline**, selecione **>>** .

1. Para publicar o pipeline para Data Factory, selecione **publicar tudo**. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Nesta etapa, você dispara uma execução de pipeline. 

1. Para disparar uma execução de pipeline, selecione **gatilho** na barra de ferramentas e selecione **disparar agora**. 

   ![Disparar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Execução de Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você vê a execução do pipeline e seu status junto com outras informações, como a hora de **início da execução** . Para atualizar a exibição, selecione **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecione o link **Exibir execuções de atividade** na coluna **Ações**. Você vê apenas uma execução de atividade porque o pipeline tem apenas uma atividade. É a atividade executar pacote SSIS.

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Execute a consulta a seguir no banco de dados SSISDB no SQL Server para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar as execuções do pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Você também pode obter a ID de execução do SSISDB da saída da execução da atividade do pipeline e usar a ID para verificar logs de execução mais abrangentes e mensagens de erro no SQL Server Management Studio.

   ![Obter a ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho

Você também pode criar um gatilho agendado para o pipeline para que o pipeline seja executado em uma agenda, como por hora ou por dia. Para um exemplo, consulte [Criar uma data factory - Interface do Usuário do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com o PowerShell
Nesta seção, você usará Azure PowerShell para criar um pipeline de Data Factory com uma atividade executar pacote do SSIS que executa seu pacote SSIS. 

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções passo a passo em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Criar um data factory com Azure-SSIS IR
Você pode usar um data factory existente que já tenha Azure-SSIS IR provisionado ou criar um novo data factory com Azure-SSIS IR. Execute as instruções passo a passo no [Tutorial: Implantar pacotes SSIS no Azure via PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS 
Nesta etapa, você cria um pipeline com uma atividade Executar pacote SSIS. A atividade é executada em seu pacote SSIS. 

1. Crie um arquivo JSON chamado *RunSSISPackagePipeline. JSON* na pasta *C:\ADF\RunSSISPackage* com conteúdo semelhante ao exemplo a seguir.

   > [!IMPORTANT]
   > Substitua nomes de objeto, descrições e caminhos, valores de propriedade ou parâmetro, senhas e outros valores de variáveis antes de salvar o arquivo. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Para executar pacotes armazenados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure, insira os valores para as propriedades do pacote ou local do log da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes em projetos armazenados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure, insira os valores para a propriedade local do pacote da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Em Azure PowerShell, alterne para a pasta *C:\ADF\RunSSISPackage* .

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o cmdlet **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Aqui está a saída de exemplo:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Use o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie ou cole o script a seguir na janela do PowerShell e selecione Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Você também pode monitorar o pipeline usando o portal do Azure. Para obter instruções passo a passo, consulte [Monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho
Na etapa anterior, você executou o pipeline sob demanda. Você também pode criar um gatilho de agendamento para executar o pipeline em um agendamento, como por hora ou por dia.

1. Crie um arquivo JSON chamado *mytrigger. JSON* na pasta *C:\ADF\RunSSISPackage* com o seguinte conteúdo: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Em Azure PowerShell, alterne para a pasta *C:\ADF\RunSSISPackage* .
1. Execute o cmdlet **set-AzDataFactoryV2Trigger** , que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Por padrão, o gatilho está no estado interrompido. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Execute o seguinte comando após a próxima hora. Por exemplo, se a hora atual for 15:25 UTC, execute o comando às 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Execute a consulta a seguir no banco de dados SSISDB no SQL Server para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Próximas etapas
Consulte a postagem blog a seguir:
- [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines de Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
