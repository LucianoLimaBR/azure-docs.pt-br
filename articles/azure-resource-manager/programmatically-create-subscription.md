---
title: Criar programaticamente assinaturas do Azure | Microsoft Docs
description: Saiba como criar assinaturas adicionais do Azure programaticamente.
services: azure-resource-manager
author: amberb
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 5d977fd6ce74f9cabedd0553c5815fd64d4d09a7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376021"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Criar programaticamente assinaturas do Azure (versão prévia)

Os clientes do Azure com uma conta de cobrança [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ) ou [Microsoft Partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) podem criar assinaturas programaticamente. Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Quando você cria uma assinatura do Azure programaticamente, essa assinatura é regida pelo contrato sob o qual você adquiriu os serviços do Azure da Microsoft ou de um revendedor autorizado. Para saber mais, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Criar assinaturas para uma conta de cobrança de EA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função de proprietário em uma conta de registro para criar uma assinatura. Há duas maneiras de obter a função:

* O administrador corporativo do seu registro pode [torná-lo um proprietário de conta](https://ea.azure.com/helpdocs/addNewAccount) (entrada necessária) que torna o proprietário da conta de registro.

* Um proprietário existente da conta de inscrição pode [conceder acesso a você](grant-access-to-create-subscription.md). Da mesma forma, se você quiser usar uma entidade de serviço para criar uma assinatura de EA, deverá [conceder a essa entidade de serviço a capacidade de criar assinaturas](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você for adicionado a uma conta de registro associada a um proprietário de conta, o Azure usará a relação de conta para registro para determinar onde cobrar os encargos de assinatura. Todas as assinaturas criadas na conta são cobradas no registro de EA em que a conta está. Para criar assinaturas, você deve passar valores sobre a conta de registro e as entidades de usuário a fim de ser dono da assinatura. 

Para executar os comandos a seguir, você deve estar conectado ao *diretório base* do proprietário da conta, que é o diretório em que as assinaturas são criadas por padrão.

### <a name="resttabrest"></a>[REST](#tab/rest)

Solicitação para listar todas as contas de registro às quais você tem acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

A resposta da API lista todas as contas de registro às quais você tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, se você quisesse criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esse identificador é a ID de objeto da conta de registro. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Abra [Azure cloud Shell](https://shell.azure.com/) e selecione PowerShell.

Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista de contas de registro às quais você tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `ObjectId` dessa conta. Por exemplo, se você quisesse criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole essa ID de objeto em algum lugar para que você possa usá-la na próxima etapa como o `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso.

```azurecli-interactive 
az billing enrollment-account list
```

O Azure responde com uma lista de contas de registro às quais você tem acesso:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, se você quisesse criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esse identificador é a ID de objeto da conta de registro. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico

O exemplo a seguir cria uma assinatura chamada *assinatura da equipe de desenvolvimento* na conta de registro selecionada na etapa anterior. A oferta de assinatura é *MS-AZR-0017P* (regular Microsoft Enterprise Agreement). Ele também adiciona dois usuários como Proprietários RBAC para a assinatura.

### <a name="resttabrest"></a>[REST](#tab/rest)

Faça a solicitação a seguir, substituindo `<enrollmentAccountObjectId>` pelo `name` copiado da primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome do elemento  | obrigatórios | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não      | string | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | SIM      | string | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não       | string | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |

Em resposta, você obtém um objeto `subscriptionOperation` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionOperation` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, instale este módulo de visualização executando `Install-Module Az.Subscription -AllowPrerelease`. Para certificar-se de que `-AllowPrerelease` funciona, instale uma versão recente do PowerShellGet do [Módulo Get do PowerShellGet](/powershell/gallery/installing-psget).

Execute o comando [New-AzSubscription](/powershell/module/az.subscription) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `ObjectId` coletado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do elemento  | obrigatórios | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não      | string | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `OfferType`   | SIM      | string | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | SIM       | string | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Não       | string | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `OwnerSignInName`    | Não       | string | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro, em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não       | string | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro, em vez de `OwnerObjectId`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para ver a lista completa de todos os parâmetros, confira [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale essa extensão de visualização executando `az extension add --name subscription`.

Execute o comando [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `name` que você copiou na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do elemento  | obrigatórios | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não      | string | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offer-type`   | SIM      | string | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | SIM       | string | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `az billing enrollment-account list`. |
| `owner-object-id`      | Não       | string | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `owner-upn`    | Não       | string | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro, em vez de `owner-object-id`.|
| `owner-spn` | Não       | string | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro, em vez de `owner-object-id`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para ver uma lista completa de todos os parâmetros, consulte [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente as assinaturas do Azure Enterprise podem ser criadas usando esta API.
- Há um limite de 200 assinaturas por conta de registro. Depois disso, mais assinaturas para a conta só podem ser criadas no portal do Azure. Se você quiser criar mais assinaturas por meio da API, crie outra conta de registro.
- Os usuários que não são proprietários da conta, mas foram adicionados a uma conta de registro via RBAC, não podem criar assinaturas no portal do Azure.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Criar assinaturas para uma conta MCA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função de proprietário, colaborador ou criador de assinatura do Azure em uma seção de fatura ou função de proprietário ou colaborador em um perfil de cobrança ou uma conta de cobrança para criar assinaturas. Para obter mais informações, confira [Funções e tarefas da cobrança de assinatura](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

O exemplo mostrado abaixo usa APIs REST. Atualmente, não há suporte para PowerShell e CLI do Azure.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Localizar contas de cobrança às quais você tem acesso 

Faça a solicitação abaixo para listar todas as contas de cobrança.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de cobrança às quais você tem acesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreeementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, se você quiser criar uma assinatura para a conta de cobrança `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Localizar seções de fatura para criar assinaturas

Os encargos de sua assinatura aparecem em uma seção da fatura de um perfil de cobrança. Use a API a seguir para obter a lista de seções de fatura e perfis de cobrança nos quais você tem permissão para criar assinaturas do Azure.

Faça a solicitação a seguir, substituindo `<billingAccountName>` pelo `name` copiado da primeira etapa (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
A resposta da API lista todas as seções de fatura e seus perfis de cobrança nos quais você tem acesso para criar assinaturas:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

Use a propriedade `invoiceSectionDisplayName` para identificar a seção da nota fiscal para a qual você deseja criar assinaturas. Copie o `invoiceSectionId`, `billingProfileId` e um dos `skuId` para a seção fatura. Por exemplo, se você quiser criar uma assinatura do tipo `Microsoft Azure plan` para a seção fatura `Development`, copie `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` e `0001`. Cole esses valores em algum lugar para que você possa usá-los na próxima etapa.

### <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma assinatura para uma seção de fatura

O exemplo a seguir cria uma assinatura denominada *assinatura da equipe de desenvolvimento* do tipo *Microsoft Azure plano* para a seção de fatura de *desenvolvimento* . A assinatura será cobrada no perfil de cobrança de *finanças da Contoso* e aparecerá na seção de *desenvolvimento* de sua fatura. 

Faça a solicitação a seguir, substituindo `<invoiceSectionId>` pelo `invoiceSectionId` copiado da segunda etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Você precisa passar o `billingProfileId` e o `skuId` copiados da segunda etapa nos parâmetros de solicitação da API. Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nome do elemento  | obrigatórios | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | SIM      | string | O nome de exibição da assinatura.|
| `billingProfileId`   | SIM      | string | A ID do perfil de cobrança que será cobrado pelos encargos da assinatura.  |
| `skuId` | SIM      | string | A ID do SKU que determina o tipo de plano do Azure. |
| `owners`      | Não       | string | A ID de objeto de qualquer usuário ou entidade de serviço que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `costCenter` | Não      | string | O centro de custo associado à assinatura. Ele aparece no arquivo CSV de uso. |
| `managementGroupId` | Não      | string | A ID do grupo de gerenciamento ao qual a assinatura será adicionada. Para obter a lista de grupos de gerenciamento, consulte [API de lista de grupos de gerenciamento](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Use a ID de um grupo de gerenciamento da API. |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionCreationResult` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Criar assinaturas para uma conta de cobrança do MPA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função de administrador global ou agente de administrador na conta do provedor de soluções de nuvem da sua organização para criar uma assinatura para sua conta de cobrança. Para obter mais informações, consulte [Partner Center – atribuir funções e permissões de usuários](https://docs.microsoft.com/partner-center/permissions-overview).

O exemplo mostrado abaixo usa APIs REST. Atualmente, não há suporte para PowerShell e CLI do Azure.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Localizar as contas de cobrança às quais você tem acesso 

Faça a solicitação abaixo para listar todas as contas de cobrança às quais você tem acesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreeementType da conta é *MicrosoftPartnerAgreement*. Copie o `name` para a conta. Por exemplo, se você quiser criar uma assinatura para a conta de cobrança `Contoso`, copie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa.

### <a name="find-customers-that-have-azure-plans"></a>Encontre clientes que têm planos do Azure

Faça a solicitação a seguir, substituindo `<billingAccountName>` pelo `name` copiado da primeira etapa (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) para listar todos os clientes na conta de cobrança para quem você pode criar assinaturas do Azure. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de cobrança com planos do Azure. Você pode criar assinaturas para esses clientes.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

Use a propriedade `displayName` para identificar o cliente para o qual você deseja criar assinaturas. Copie o `id` para o cliente. Por exemplo, se você quiser criar uma assinatura para `Fabrikam toys`, copie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cole esse valor em algum lugar para usá-lo nas etapas subsequentes.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para provedores indiretos: obter os revendedores para um cliente

Se você for um provedor indireto no modelo de duas camadas do CSP, poderá especificar um revendedor ao criar assinaturas para os clientes. 

Faça a solicitação a seguir, substituindo `<customerId>` pelo `id` copiado da segunda etapa (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) para listar todos os revendedores disponíveis para um cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
A resposta da API lista os revendedores do cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Use a propriedade `description` para identificar o revendedor que será associado à assinatura. Copie o `resellerId` para o revendedor. Por exemplo, se você quiser associar `Wingtip`, copie `3xxxxx`. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa.

### <a name="create-a-subscription-for-a-customer"></a>Criar uma assinatura para um cliente

O exemplo a seguir cria uma assinatura chamada *assinatura da equipe de desenvolvimento* para a *Fabrikam Toys* e associa o revendedor *Wingtip* à assinatura. T

Faça a solicitação a seguir, substituindo `<customerId>` pelo `id` copiado da primeira etapa (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passe o *revendedorid* opcional copiado da segunda etapa nos parâmetros de solicitação da API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome do elemento  | obrigatórios | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | SIM      | string | O nome de exibição da assinatura.|
| `skuId` | SIM      | string | A ID de SKU do plano do Azure. Use *0001* para assinaturas do tipo Microsoft Azure plano |
| `resellerId`      | Não       | string | A ID de MPN do revendedor que será associada à assinatura.  |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionCreationResult` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

## <a name="next-steps"></a>Próximos passos

* Para obter um exemplo de como criar uma assinatura do EA (Enterprise Agreement) usando o .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](management-groups-overview.md)
