---
title: Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ef4ddc422041de623b96f3a0c85f067427cacd7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374222"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, você terá uma política personalizada de inscrição e entrada com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS apresentado aos usuários. Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

## <a name="page-ui-customization"></a>Personalização da interface do usuário da página

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

É assim que ela funciona: o Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos entre Origens)](https://www.w3.org/TR/cors/). Primeiro, especifique uma URL na política personalizada com um conteúdo personalizado em HTML. O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo HTML carregado da URL e exibe a página para o cliente.

## <a name="create-your-html5-content"></a>Crie seu conteúdo em HTML5

Crie conteúdo em HTML com o nome da marca de seu produto no título.

1. Copie o snippet de HTML a seguir. É um HTML5 bem formado com um elemento vazio chamado *\<div id = "api"\>\</div\>* localizado dentro das marcas *\<body\>* . Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Cole o snippet copiado em um editor de texto e salve o arquivo como *customize-ui.html*.

> [!NOTE]
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se você usar login.microsoftonline.com. Use b2clogin.com se você quiser usar elementos de formulário HTML em seu conteúdo HTML personalizado. Consulte [usar b2clogin.com](b2clogin.md) para obter outros benefícios.

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de Armazenamento de Blobs do Azure

>[!NOTE]
> Neste artigo, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar seu conteúdo em um servidor Web, mas deve [habilitar CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar esse conteúdo HTML no armazenamento de BLOBs, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu **Hub**, selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
1. Selecione uma **Assinatura** para a conta de armazenamento.
1. Crie um **Grupo de recursos** ou selecione um existente.
1. Insira um **Nome** exclusivo para sua conta de armazenamento.
1. Selecione a **Localização geográfica** para sua conta de armazenamento.
1. O **Modelo de implantação** pode permanecer **Gerenciador de Recursos**.
1. O **Desempenho** pode permanecer **Padrão**.
1. Altere **Tipo de Conta** para **Armazenamento de blobs**.
1. A **Replicação** pode permanecer **RA-GRS**.
1. A **Camada de acesso** pode permanecer **Dinâmica**.
1. Clique em **examinar + criar** para criar a conta de armazenamento.
    Depois que a implantação for concluída, a página **conta de armazenamento** será aberta automaticamente.

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner público no armazenamento de BLOBs, execute as seguintes etapas:

1. Em **serviço blob** no menu à esquerda, selecione **BLOBs**.
1. Clique em **+ contêiner**.
1. Para **nome**, insira *raiz*. Isso pode ser um nome de sua escolha, por exemplo, *wingtiptoys*, mas usamos *root* neste exemplo para simplificar.
1. Para **nível de acesso público**, selecione **blob**e **OK**.
1. Clique em **raiz** para abrir o novo contêiner.
1. Clique em **Carregar**.
1. Clique no ícone de pasta ao lado de **Selecionar um arquivo**.
1. Navegue até e selecione **Customize-UI. html** que você criou anteriormente na seção personalização da interface do usuário da página.
1. Se você quiser carregar para uma subpasta, expanda **avançado** e insira um nome de pasta em **carregar para a pasta**.
1. Escolha **Carregar**.
1. Selecione o blob **Customize-UI. html** que você carregou.
1. À direita da caixa de texto **URL** , selecione o ícone **copiar para área de transferência** para copiar a URL para a área de transferência.
1. No navegador da Web, navegue até a URL que você copiou para verificar se o blob que você carregou está acessível. Se ele estiver inacessível, por exemplo, se você encontrar um erro `ResourceNotFound`, verifique se o tipo de acesso do contêiner está definido como **blob**.

## <a name="configure-cors"></a>Configurar o CORS

Configure o armazenamento de BLOB para compartilhamento de recursos entre origens executando as seguintes etapas:

1. No menu, selecione **CORS**.
1. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao digitar o nome do seu locatário.
1. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos**, digite um asterisco (*).
1. Para **cabeçalhos expostos**, digite um asterisco (*).
1. Para **Idade máxima de**, insira 200.
1. Clique em **Save** (Salvar).

## <a name="test-cors"></a>Testar o CORS

Valide se você está pronto executando as seguintes etapas:

1. Acesse o site [www.test-cors.org](https://www.test-cors.org/) e cole a URL na caixa **URL Remota**.
1. Clique em **Enviar Solicitação**.
    Se você receber um erro, verifique se as [Configurações do CORS](#configure-cors) estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.

## <a name="modify-the-extensions-file"></a>Modificar o arquivo de extensões

Para configurar a personalização da interface do usuário, você deve copiar o **ContentDefinition** e seus elementos filho do arquivo de base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> . Esse é um dos arquivos de política incluídos no pacote de início de política personalizada, que você deve ter obtido no pré-requisito, [introdução às políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions**.
1. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como filho do elemento **BuildingBlocks**.
1. Pesquise o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML copiado.
1. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Sua política personalizada deverá ter a seguinte aparência:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salve o arquivo de extensões.

## <a name="upload-your-updated-custom-policy"></a>Carregar a política personalizada atualizada

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Clique em **Todas as Políticas**.
1. Clique em **Carregar Política**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Teste a política personalizada usando a opção **Executar Agora**

1. Na página **Azure ad B2C** , vá para **todas as políticas**.
1. Selecione a política personalizada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="reference"></a>Referência

### <a name="sample-templates"></a>Modelos de exemplo
Encontre modelos de exemplo para personalização da interface do usuário aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta sample_templates/wingtip contém os seguintes arquivos HTML:

| Modelo do HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Use esse arquivo como modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Use esse arquivo como modelo para uma página de esquecimento de senha. |
| *selfasserted.html* | Use esse arquivo como modelo para uma página de inscrição de conta social ou uma página de inscrição de conta local. |
| *unified.html* | Use esse arquivo como modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Use esse arquivo como modelo para uma página de atualização de perfil. |

Aqui estão as etapas sobre como usar o exemplo:

1. Clone o repositório em seu computador local. Escolha uma pasta de modelo em sample_templates. Você pode usar `wingtip` ou `contoso`.
1. Carregue todos os arquivos nas pastas `css`, `fonts` e `images` para o armazenamento de BLOB, conforme descrito nas seções anteriores.
1. Em seguida, abra cada arquivo \*. html na raiz de `wingtip` ou `contoso` (o que você selecionou na primeira etapa) e substitua todas as instâncias de "http://localhost" pelas URLs do CSS, imagens e arquivos de fontes carregados na etapa 2.
1. Salve os arquivos \*. html e carregue-os no armazenamento de BLOBs.
1. Agora, modifique o arquivo de extensões conforme mencionado anteriormente em [Modificar o arquivo de extensões](#modify-the-extensions-file).
1. Se você vir fontes, imagens ou CSS ausentes, verifique suas referências na política de extensões e os arquivos de \*. html.

### <a name="content-definition-ids"></a>IDs da definição de conteúdo

Na seção Modificar sua política personalizada de inscrição ou entrada, você configurou a definição de conteúdo para `api.idpselections`. O conjunto completo de IDs de definição de conteúdo reconhecidas pelo framework de experiência de identidade do Azure AD B2C e suas descrições estão na tabela a seguir:

| ID de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *api.error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | **Página de seleção de provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.idpselections.signup* | **Seleção de provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.localaccountpasswordreset* | **Página de esquecimento de senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar sua redefinição de senha.  |
| *api.localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de entrada para entradas com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição para inscrições com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que deve ser preenchido pelos usuários ao se inscreverem usando uma conta existente de um provedor de identidade social, como o Facebook ou Google+. Esta página é semelhante à página de inscrição de conta social anterior, exceto os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem usar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. |
| *api.signuporsignin* | **Página de inscrição ou entrada unificada**. Esta página controla tanto a inscrição quanto a entrada de usuários que podem usar provedores de identidade empresarial ou provedores de identidade social, como Facebook, Google+ ou contas locais.  |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os elementos da interface do usuário que podem ser personalizados, consulte o [Guia de referência para personalização da interface do usuário para políticas internas](active-directory-b2c-reference-ui-customization.md).
