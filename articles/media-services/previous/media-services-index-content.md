---
title: Indexando arquivos de mídia com o Azure Media Indexer
description: O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Este tópico mostra como usar o indexador de mídia.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 0987e15b1619fcd4c1c79f9a61420c092a7da886
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529198"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexando arquivos de mídia com o Azure Media Indexer

> [!NOTE]
> O processador de mídia [Azure Media indexer](media-services-index-content.md) será desativado em 1º de outubro de 2020. Os [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substituem esse processador de mídia herdado. Para obter mais informações, consulte [migrar do Azure Media indexer e Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).

O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Processe um arquivo de mídia ou diversos arquivos de mídia em um lote.  

Ao indexar conteúdo, certifique-se de usar arquivos de mídia com fala clara (sem música em segundo plano, ruído, efeitos ou assovio de microfone). Alguns exemplos de conteúdo apropriado são: reuniões, palestras ou apresentações gravadas. O conteúdo a seguir pode não ser adequado para indexação: filmes, programas de TV, qualquer coisa com efeitos de áudio e som mistos, conteúdo mal gravado com ruídos de fundo (assovio).

Um trabalho de indexação pode gerar as seguintes saídas:

* Arquivos de legenda oculta nos seguintes formatos: **SAMI**, **TTML** e **WebVTT**.
  
    Arquivos de legenda codificada incluem uma marca chamada reconhecível, que pontua um trabalho de indexação com base em como o reconhecimento da fala no vídeo de origem é.  Você pode usar o valor de capacidade de reconhecimento para arquivos de saída de tela para usabilidade. Uma pontuação baixa significaria resultados de indexação insatisfatórios devido à qualidade de áudio.
* Arquivo de palavra-chave (XML).
* Arquivo de blob de indexação de áudio (AIB) para uso com o SQL Server.
  
    Para obter mais informações, consulte [Usando arquivos de AIB com o indexador de mídia do Azure e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Este artigo mostra como criar trabalhos de indexação para **indexar um ativo** e **indexar vários arquivos**.

Para obter as atualizações mais recentes do Indexador de Mídia do Azure, consulte [blogs dos Serviços de Mídia](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Usando arquivos de configuração e de manifesto para tarefas de indexação
Você pode especificar mais detalhes para suas tarefas de indexação usando uma configuração de tarefa. Por exemplo, você pode especificar quais metadados usar para o arquivo de mídia. Esses metadados são usados pelo mecanismo de linguagem para expandir seu vocabulário e melhora muito a precisão do reconhecimento de fala.  Você também pode especificar os arquivos de saída desejados.

Você também pode processar vários arquivos de mídia ao mesmo tempo usando um arquivo de manifesto.

Para saber mais, consulte [Predefinição de tarefa para o Indexador de Mídia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexar um ativo
O método a seguir carrega um arquivo de mídia como um ativo e cria um trabalho para indexar o ativo.

Se nenhum arquivo de configuração for especificado, o arquivo de mídia será indexado com todas as configurações padrão.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a id="output_files"></a>Arquivos de saída
Por padrão, um trabalho de indexação gera os seguintes arquivos de saída. Os arquivos são armazenados no primeiro ativo de saída.

Quando há mais de um arquivo de mídia de entrada, o indexador gera um arquivo de manifesto para as saídas do trabalho, chamado ' JobResult. txt '. Para cada arquivo de mídia de entrada, os arquivos AIB, SAMI, TTML, WebVTT e keyword resultantes são numerados sequencialmente e nomeados usando o "alias".

| Nome do arquivo | Descrição |
| --- | --- |
| **InputFileName.aib** |Arquivo de blob de indexação de áudio. <br/><br/> O arquivo AIB (blob de indexação de áudio) é um arquivo binário que pode ser pesquisado no Microsoft SQL Server usando a pesquisa de texto completo.  O arquivo AIB é mais potente do que os arquivos de legenda simples, pois contém alternativas para cada palavra, permitindo uma experiência de pesquisa muito mais rica. <br/> <br/>Ele requer a instalação do complemento do indexador SQL em um computador que executa o Microsoft SQL Server 2008 ou posterior. Pesquisar o AIB usando a pesquisa de texto completo do Microsoft SQL Server fornece resultados de pesquisa mais precisos do que Pesquisar os arquivos de legenda codificados gerados pelo WAMI. Isso ocorre porque o AIB contém alternativas de palavras que parecem semelhantes, enquanto os arquivos de legenda oculta contêm a palavra de confiança mais alta para cada segmento do áudio. Se a pesquisa de palavras faladas for mais importante, é recomendável usar o AIB em conjunto com Microsoft SQL Server. <br/><br/>Também é possível utilizar outros mecanismos de pesquisa como o Apache Lucene/Solr para simplesmente indexar o vídeo com base na legenda oculta e nos arquivos XML de palavra-chave, mas isso resultará em resultados de pesquisa menos precisos. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Arquivos de Legenda Oculta (CC) nos formatos SAMI, TTML e WebVTT.<br/><br/>Eles podem ser usados para tornar os arquivos de áudio e vídeo acessíveis a pessoas com deficiência auditiva.<br/><br/>Arquivos de legenda oculta incluem uma marca chamada <b>Recognizability</b>, que classifica um trabalho de indexação com base no quanto a fala é reconhecível no vídeo de origem.  Você pode usar o valor de <b>Reconhecimento</b> para mostrar os arquivos de saída na tela e facilitar o uso. Uma pontuação baixa significaria resultados de indexação insatisfatórios devido à qualidade de áudio. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Arquivos de palavra-chave e de informações. <br/><br/>O arquivo de palavra-chave é um arquivo XML que contém palavras-chaves extraídas do conteúdo de fala, com informações de frequência e de deslocamento. <br/><br/>O arquivo de informações é um arquivo de texto sem formatação que contém informações granulares sobre cada termo reconhecido. A primeira linha é especial e contém a pontuação de reconhecimento. Cada linha subsequente é uma lista separada por tabulação dos seguintes dados: hora de início, hora de término, palavra/frase, confiança. Os tempos são fornecidos em segundos e a confiança é dada como um número de 0-1. <br/><br/>Linha de exemplo: "1,20 1,45 palavra 0,67" <br/><br/>Esses arquivos podem ser usados para várias finalidades, como, para executar análises de fala ou expostos aos mecanismos de pesquisa, como Bing, Google ou Microsoft SharePoint, para tornar os arquivos de mídia mais detectáveis ou até mesmo usados para fornecer anúncios mais relevantes. |
| **JobResult.txt** |Manifesto de saída, presente apenas durante a indexação de vários arquivos, contendo as seguintes informações:<br/><br/><table border="1"><tr><th>InputFile</th><th>Receber</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a. MP4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b. MP4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c. MP4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Se nem todos os arquivos de mídia de entrada forem indexados com êxito, o trabalho de indexação falhará com o código de erro 4000. Para saber mais, consulte [Códigos de erro](#error_codes).

## <a name="index-multiple-files"></a>Indexar vários arquivos
O método a seguir carrega vários arquivos de mídia como um ativo e cria um trabalho para indexar todos esses arquivos em um lote.

Um arquivo de manifesto com a extensão ". lst" é criado e carregado no ativo. O arquivo de manifesto contém a lista de todos os arquivos de ativo. Para saber mais, consulte [Predefinição de tarefa para o Indexador de Mídia do Azure](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Trabalho parcialmente bem-sucedido
Se nem todos os arquivos de mídia de entrada são indexados com êxito, o trabalho de indexação falhará com o código de erro 4000. Para saber mais, consulte [Códigos de erro](#error_codes).

As mesmas saídas (como trabalhos com êxito) são geradas. Você pode consultar o arquivo de manifesto de saída para descobrir quais arquivos de entrada falharam, de acordo com os valores de coluna de erro. Para arquivos de entrada que falharam, os arquivos AIB, SAMI, TTML, WebVTT e keyword resultantes não serão gerados.

### <a id="preset"></a> Predefinição de tarefa para o Indexador de Mídia do Azure
O processamento de Azure Media Indexer pode ser personalizado fornecendo uma predefinição de tarefa opcional juntamente com a tarefa.  O seguinte descreve o formato desse XML de configuração.

| NaME | Necessária | Descrição |
| --- | --- | --- |
| **input** |false |Arquivo (s) de ativo que você deseja indexar.</p><p>O Azure Media Indexer dá suporte aos seguintes formatos de arquivo de mídia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Você pode especificar os nomes dos arquivos no atributo **name** ou **list** do elemento **input** (como mostrado abaixo). Se você não especificar qual arquivo de ativo será indexado, o arquivo primário será o escolhido. Se nenhum arquivo de ativo principal for definido, o primeiro arquivo no ativo de entrada será indexado.</p><p>Para especificar explicitamente o nome do arquivo de ativo, faça:<br/>`<input name="TestFile.wmv">`<br/><br/>Você também pode indexar vários arquivos de ativo de uma vez (até 10 arquivos). Para fazer isso:<br/><br/><ol class="ordered"><li><p>Crie um arquivo de texto (arquivo de manifesto) e dê a ele uma extensão. lst. </p></li><li><p>Adicione uma lista de todos os nomes de arquivo de ativo em seu ativo de entrada para esse arquivo de manifesto. </p></li><li><p>Adicione (carregue) o arquivo de manifesto ao ativo.  </p></li><li><p>Especifique o nome do arquivo de manifesto no atributo de lista da entrada.<br/>`<input list="input.lst">`</li></ol><br/><br/>Observação: se você adicionar mais de 10 arquivos ao arquivo de manifesto, o trabalho de indexação falhará com o código de erro 2006. |
| **metadados** |false |Metadados para os arquivos de ativo especificados usados para adaptação de vocabulário.  Útil para preparar o indexador para reconhecer palavras de vocabulário não padrão, como nomes próprios.<br/>`<metadata key="..." value="..."/>` <br/><br/>Você pode fornecer **valores** para **chaves** predefinidas. No momento, as chaves a seguir têm suporte:<br/><br/>"título" e "Descrição" – usado para adaptação de vocabulário para ajustar o modelo de linguagem para seu trabalho e melhorar a precisão do reconhecimento de fala.  Os valores propagam pesquisas na Internet para encontrar documentos de texto contextualmente relevantes, usando o conteúdo para aumentar o dicionário interno durante sua tarefa de Indexação.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **funcionalidades** <br/><br/> Adicionado na versão 1.2. Atualmente, o único recurso com suporte é o reconhecimento de fala ("ASR"). |false |O recurso de reconhecimento de fala tem as seguintes chaves de configuração:<table><tr><th><p>Chave</p></th>        <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Linguagem</p></td><td><p>O idioma natural a ser reconhecido no arquivo multimídia.</p></td><td><p>Inglês, espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista separada por ponto-e-vírgula dos formatos de legenda de saída desejados (se houver)</p></td><td><p>ttml; Sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Um sinalizador booliano que especifica se um arquivo AIB é necessário ou não (para uso com o SQL Server e o IFilter do Indexador do cliente).  Para obter mais informações, consulte <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Usando arquivos de AIB com o indexador de mídia do Azure e SQL Server</a>.</p></td><td><p>True For</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Um sinalizador booliano que especifica se um arquivo XML de palavra-chave é necessário ou não.</p></td><td><p>True For. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Um sinalizador booliano que especifica se deve ou não forçar legendas completas (independentemente do nível de confiança).  </p><p>O padrão é false; nesse caso, as palavras e frases que têm um nível de confiança inferior a 50% são omitidas das saídas de legenda final e substituídas por reticências ("...").  As reticências são úteis para controle de qualidade de legenda e auditoria.</p></td><td><p>True For. </p></td></tr></table> |

### <a id="error_codes"></a>Códigos de erro
No caso de um erro, Azure Media Indexer deve reportar um dos seguintes códigos de erro:

| Codificar | NaME | Motivos possíveis |
| --- | --- | --- |
| 2000 |Configuração inválida |Configuração inválida |
| 2001 |Ativos de entrada inválidos |Ativos de entrada ou ativo vazio ausentes. |
| 2002 |Manifesto inválido |O manifesto está vazio ou o manifesto contém itens inválidos. |
| 2003 |Falha ao baixar arquivo de mídia |URL inválida no arquivo de manifesto. |
| 2004 |Protocolo sem suporte |Não há suporte para o protocolo da URL de mídia. |
| 2005 |Tipo de arquivo sem suporte |Não há suporte para o tipo de arquivo de mídia de entrada. |
| 2006 |Muitos arquivos de entrada |Há mais de 10 arquivos no manifesto de entrada. |
| 3000 |Falha ao decodificar arquivo de mídia |Codec de mídia sem suporte <br/>ou<br/> Arquivo de mídia corrompido <br/>ou<br/> Nenhum fluxo de áudio na mídia de entrada. |
| 4000 |A indexação do lote foi parcialmente bem-sucedida |Falha ao indexar alguns dos arquivos de mídia de entrada. Para obter mais informações, consulte <a href="#output_files">Arquivos de saída</a>. |
| other |Erros internos |Entre em contato com a equipe de suporte. indexer@microsoft.com |

## <a id="supported_languages"></a>Idiomas com suporte
Atualmente, há suporte para os idiomas inglês e espanhol. Para saber mais, consulte [a postagem no blog sobre a versão v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos serviços de mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Usando arquivos AIB com o indexador de mídia do Azure e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexando arquivos de mídia com a Preview do Indexador de Mídia do Azure 2](media-services-process-content-with-indexer2.md)

