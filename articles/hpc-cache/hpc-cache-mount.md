---
title: Montar um cache HPC do Azure (versão prévia)
description: Como conectar clientes a um serviço de cache do Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: 3257cf92c628650fc50e0a36ec37fcab920aba2f
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254577"
---
# <a name="mount-the-azure-hpc-cache-preview"></a>Montar o cache do HPC do Azure (visualização)

Depois que o cache é criado, os clientes NFS podem acessá-lo com um comando de montagem simples.

O comando Mount é composto de dois elementos:

* Um dos endereços de montagem do cache (listado na página Visão geral do cache)
* O caminho de namespace virtual que você definiu quando criou o destino de armazenamento

![captura de tela da página de visão geral da instância do cache HPC do Azure, com uma caixa de realce em volta da lista de endereços de montagem no canto inferior direito](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede do cache. Em um grupo de recursos, essas NICs são listadas com nomes que terminam em `-cluster-nic-` e um número. Não altere ou exclua essas interfaces, ou o cache ficará indisponível.

Os caminhos de namespace virtual são mostrados na página **destinos de armazenamento** . Clique em um nome de destino de armazenamento individual para ver seus detalhes, incluindo caminhos de namespace agregados associados a ele.

![captura de tela do painel de destino de armazenamento do cache, com uma caixa de realce em volta de uma entrada na coluna PATH da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Sintaxe de comando de montagem

Use um comando de montagem como o seguinte:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*Opções*}

Exemplo:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Depois que esse comando for executado com sucesso, o conteúdo da exportação de armazenamento deverá estar visível no diretório ``hpccache`` no cliente.

> [!NOTE] 
> Os clientes devem ser capazes de acessar a rede virtual e a sub-rede que hospeda o cache. Por exemplo, crie VMs de cliente na mesma rede virtual ou use um ponto de extremidade, gateway ou outra solução na rede virtual para acesso de fora. Lembre-se de que nada mais pode ser hospedado na sub-rede do cache.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem de cliente robusta, passe essas configurações e argumentos no comando mount: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Configurações de comando de montagem recomendadas | |
--- | --- 
``hard`` | As montagens suaves no cache do HPC do Azure estão associadas a falhas do aplicativo e à possível perda de dados. 
``proto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

## <a name="next-steps"></a>Próximas etapas

* Para mover dados para os destinos de armazenamento do cache, leia [popular novo armazenamento de BLOBs do Azure](hpc-cache-ingest.md).
