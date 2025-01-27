---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692239"
---
>[!IMPORTANT]
>Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Serviço do Azure Machine Learning.

### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua todo o grupo de recursos para não gerar encargos:

1. No portal do Azure, selecione **Grupos de recursos** no lado esquerdo da janela.
 
   ![Excluir um grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. No lado direito da janela, selecione o botão de reticências ( **...** ).

1. Selecione **Excluir grupo de recursos**.

A exclusão de um grupo de recursos também exclui todos os recursos criados na interface visual.  

### <a name="delete-only-the-compute-target"></a>Excluir apenas o destino de computação

O destino de computação que você criou aqui é *dimensionado automaticamente* para zero nós quando não estiver sendo usado. Isso serve para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o workspace.

    ![Excluir o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. Na seção **Computação** do seu workspace, selecione o recurso.

1. Selecione **Excluir**.

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Na interface visual em que você criou seu experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **Excluir**. Os conjuntos de dados podem ter o registro cancelado do seu workspace. Para isso, selecione cada conjunto de dados e, em seguida, selecione **Cancelar registro**.

![Excluir ativos](./media/aml-ui-cleanup/delete-asset.png)
