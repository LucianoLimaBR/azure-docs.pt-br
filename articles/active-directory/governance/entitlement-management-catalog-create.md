---
title: Criar e gerenciar um catálogo de recursos no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba como criar um novo contêiner de recursos e pacotes de acesso no gerenciamento de direitos Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85479abfd19110703d5ce9f59f545830e84feadb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429584"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management-preview"></a>Criar e gerenciar um catálogo de recursos no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contêiner de recursos e pacotes de acesso. Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso. Qualquer pessoa que criar o catálogo se tornará o primeiro proprietário do catálogo. Um proprietário de catálogo pode adicionar proprietários de catálogos adicionais.

**Função de pré-requisito:** Administrador global, administrador de usuário ou criador de catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos**.

    ![Catálogos de gerenciamento de direitos no portal do Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **novo catálogo**.

1. Insira um nome exclusivo para o catálogo e forneça uma descrição.

    Os usuários verão essas informações nos detalhes do pacote de acesso.

1. Se você quiser que os pacotes de acesso nesse catálogo estejam disponíveis para que os usuários solicitem assim que eles forem criados, defina **habilitado** como **Sim**.

1. Se você quiser permitir que os usuários em diretórios externos selecionados possam solicitar pacotes de acesso neste catálogo, defina **habilitado para usuários externos** como **Sim**.

    ![Painel novo catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Clique em **criar** para criar o catálogo.

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos em um pacote do Access, os recursos devem existir em um catálogo. Os tipos de recursos que você pode adicionar são grupos, aplicativos e sites do SharePoint Online. Os grupos podem ser grupos do Office 365 criados na nuvem ou grupos de segurança do Azure AD criados na nuvem. Os aplicativos podem ser aplicativos corporativos do Azure AD, incluindo aplicativos SaaS e seus próprios aplicativos federados para o Azure AD. Os sites podem ser sites do SharePoint Online ou coleções de sites do SharePoint Online.

**Função de pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar recursos.

1. No menu à esquerda, clique em **recursos**.

1. Clique em **Adicionar recursos**.

1. Clique em um tipo de recurso: **grupos e equipes**, **aplicativos**ou **sites do SharePoint**.

    Se você não vir um recurso que deseja adicionar ou não puder adicionar um recurso, verifique se você tem a função de gerenciamento de direitos e funções de diretório do Azure AD necessária. Talvez seja necessário ter alguém com as funções necessárias para adicionar o recurso ao seu catálogo. Para obter mais informações, consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selecione um ou mais recursos do tipo que você deseja adicionar ao catálogo.

    ![Adicionar recursos a um catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Quando terminar, clique em **Adicionar**.

    Esses recursos agora podem ser incluídos em pacotes do Access no catálogo.

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Você pode remover recursos de um catálogo. Um recurso só poderá ser removido de um catálogo se não estiver sendo usado em nenhum dos pacotes de acesso do catálogo.

**Função de pré-requisito:** Consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo do qual você deseja remover os recursos.

1. No menu à esquerda, clique em **recursos**.

1. Selecione os recursos que você deseja remover.

1. Clique em **remover** (ou clique nas reticências ( **...** ) e, em seguida, clique em **remover recurso**).

## <a name="add-additional-catalog-owners"></a>Adicionar proprietários de catálogos adicionais

O usuário que criou um catálogo torna-se o primeiro proprietário do catálogo. Para delegar o gerenciamento de um catálogo, você adiciona usuários à função de proprietário do catálogo. Isso ajuda a compartilhar as responsabilidades de gerenciamento de catálogo. 

Siga estas etapas para atribuir um usuário à função de proprietário do catálogo:

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo ao qual você deseja adicionar administradores.

1. No menu à esquerda, clique em **funções e administradores**.

    ![Cataloga funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique em **Adicionar proprietários** para selecionar os membros para essas funções.

1. Clique em **selecionar** para adicionar esses membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Você pode editar o nome e a descrição de um catálogo. Os usuários veem essas informações nos detalhes de um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja editar.

1. Na página **visão geral** do catálogo, clique em **Editar**.

1. Edite o nome do catálogo, a descrição ou as configurações habilitadas.

    ![Editar configurações do catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Clique em **Save** (Salvar).

## <a name="delete-a-catalog"></a>Excluir um catálogo

Você pode excluir um catálogo, mas somente se ele não tiver nenhum pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo que você deseja excluir.

1. Na **visão geral**do catálogo, clique em **excluir**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

## <a name="next-steps"></a>Próximos passos

- [Delegar governança de acesso para acessar gerenciadores de pacotes](entitlement-management-delegate-managers.md)
