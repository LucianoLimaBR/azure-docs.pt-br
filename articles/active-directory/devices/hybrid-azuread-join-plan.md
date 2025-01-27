---
title: Como planejar a implementação de junção de Azure Active Directory híbrida no Azure Active Directory (AD do Azure) | Microsoft Docs
description: Saiba como configurar dispositivos ingressados no Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66e583a75f7103a7cccf560d537e440ba47cae5a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596330"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como planejar sua implementação de junção de Azure Active Directory híbrida

De forma semelhante a um usuário, um dispositivo é outra identidade principal que você deseja proteger e usá-lo para proteger seus recursos a qualquer momento e de qualquer local. Você pode atingir essa meta trazendo e Gerenciando identidades de dispositivo no Azure AD usando um dos seguintes métodos:

- Ingresso no Azure AD
- Ingresso no Azure AD híbrido
- Registro do Azure AD

Ao trazer seus dispositivos para o Azure AD, você maximiza a produtividade de seus usuários por meio de SSO (logon único) em sua nuvem e em seus recursos locais. Ao mesmo tempo, você pode proteger o acesso a seus recursos locais e de nuvem com [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Se você tiver um ambiente de Active Directory local (AD) e desejar ingressar seus computadores ingressados no domínio do AD no Azure AD, poderá fazer isso fazendo uma junção híbrida do Azure AD. Este artigo fornece as etapas relacionadas para implementar uma junção híbrida do Azure AD em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com a [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> A versão mínima necessária do controlador de domínio para o ingresso no Azure AD híbrido do Windows 10 é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do Azure AD, você deve se familiarizar com:

|   |   |
| --- | --- |
| ![Verificação][1] | Examinar dispositivos com suporte |
| ![Verificação][1] | Examine as coisas que você deve saber |
| ![Verificação][1] | Examinar a validação controlada do ingresso no Azure AD híbrido |
| ![Verificação][1] | Selecione seu cenário com base na sua infraestrutura de identidade |
| ![Verificação][1] | Examine o suporte de UPN do AD local para ingressar no Azure AD híbrido |

## <a name="review-supported-devices"></a>Examinar dispositivos com suporte

O ingresso no Azure AD híbrido dá suporte a uma ampla variedade de dispositivos Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Para dispositivos que executam o sistema operacional Windows desktop, a versão com suporte está listada neste artigo [informações de versão do Windows 10](https://docs.microsoft.com/windows/release-information/). Como prática recomendada, a Microsoft recomenda que você atualize para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- Windows 7. Para obter informações de suporte sobre o Windows 7, consulte o [suporte para o Windows 7 está terminando](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Para obter informações de suporte sobre o Windows Server 2008 e 2008 R2, consulte [preparar para o Windows server 2008 fim do suporte](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como uma primeira etapa de planejamento, você deve examinar seu ambiente e determinar se precisa dar suporte a dispositivos de nível inferior do Windows.

## <a name="review-things-you-should-know"></a>Examine as coisas que você deve saber

No momento, não há suporte para o ingresso do Azure AD híbrido se seu ambiente consistir em uma única floresta do AD Sincronizando dados de identidade para mais de um locatário do Azure AD.

Se seu ambiente usa VDI (Virtual Desktop Infrastructure), consulte [identidade do dispositivo e virtualização de área de trabalho](https://docs.microsoft.com/en-us/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

O ingresso no Azure AD híbrido tem suporte para TPM 2,0 compatível com FIPS e sem suporte para TPM 1,2. Se seus dispositivos tiverem o TPM 1,2 compatível com FIPS, você deverá desabilitá-los antes de prosseguir com a junção híbrida do Azure AD. A Microsoft não fornece nenhuma ferramenta para desabilitar o modo FIPS para TPMs, pois depende do fabricante do TPM. Entre em contato com o OEM de hardware para obter suporte. A partir da versão 10 1903 do WIndows, TPMs 1,2 não são usadas para o ingresso híbrido do Azure AD e os dispositivos com esses TPMs serão considerados como se não tivessem um TPM.

O ingresso no Azure AD híbrido não tem suporte no Windows Server que executa a função de controlador de domínio (DC).

O ingresso no Azure AD híbrido não tem suporte em dispositivos de nível inferior do Windows ao usar o roaming de credencial ou o roaming de perfil do usuário.

Se você estiver contando com a ferramenta de preparação do sistema (Sysprep) e se estiver usando uma imagem **anterior ao Windows 10 1809** para instalação, verifique se a imagem não é de um dispositivo que já está registrado com o Azure ad como uma junção híbrida do Azure AD.

Se você estiver contando com um instantâneo de VM (máquina virtual) para criar VMs adicionais, verifique se o instantâneo não é de uma VM que já está registrada com o Azure AD como uma junção híbrida do Azure AD.

Se seus dispositivos ingressados no domínio do Windows 10 forem [registrados no Azure ad](overview.md#getting-devices-in-azure-ad) para seu locatário, isso poderá levar a um estado duplo de ingressado no Azure ad híbrido e ao dispositivo registrado do Azure AD. É recomendável atualizar para o Windows 10 1803 (com o KB4489894 aplicado) ou superior para resolver esse cenário automaticamente. Em versões anteriores a 1803, você precisará remover manualmente o estado registrado do Azure AD antes de habilitar a junção híbrida do Azure AD. Nas versões 1803 e acima, as seguintes alterações foram feitas para evitar esse estado duplo:

- Qualquer estado registrado do Azure AD existente será removido automaticamente <i>depois que o dispositivo for ingressado no Azure ad híbrido</i>.
- Você pode impedir que o dispositivo ingressado no domínio seja registrado no Azure AD adicionando essa chave do registro-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- No Windows 10 1803, se você tiver o Windows Hello para empresas configurado, o usuário precisará reinstalar o Windows Hello para empresas após a limpeza do estado duplo. Esse problema foi resolvido com o KB4512509



## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Examinar a validação controlada do ingresso no Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows serão automaticamente registrados como dispositivos no seu locatário do Azure AD. O estado dessas identidades de dispositivo no Azure AD é chamado de junção híbrida do Azure AD. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas no artigo [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](overview.md).

As organizações podem querer fazer uma validação controlada do ingresso no Azure AD híbrido antes de habilitá-lo em toda a organização de uma só vez. Examine o artigo [validação controlada do ingresso no Azure ad híbrido](hybrid-azuread-join-control.md) para entender como realizá-lo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione seu cenário com base na sua infraestrutura de identidade

O ingresso no Azure AD híbrido funciona com ambientes gerenciados e federados dependendo se o UPN é roteável ou não roteável. Veja a parte inferior da página para ver a tabela em cenários com suporte.  

### <a name="managed-environment"></a>Ambiente gerenciado

Um ambiente gerenciado pode ser implantado por meio de [PHS (sincronização de hash de senha)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou [PTA (autenticação de passagem)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) com [logon único contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Esses cenários não exigem que você configure um servidor de Federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dê suporte aos requisitos a seguir. Se você tiver um ambiente federado usando Serviços de Federação do Active Directory (AD FS) (AD FS), os requisitos abaixo já terão suporte.

- **Declaração de WIAORMULTIAUTHN:** Essa declaração é necessária para fazer uma junção híbrida do Azure AD para dispositivos de nível inferior do Windows.
- **Protocolo WS-Trust:** Esse protocolo é necessário para autenticar dispositivos adicionados ao Azure AD híbridos atuais do Windows com o Azure AD. Quando você estiver usando AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **ADFS/Services/Trust/2005/windowstransport** ou **ADFS/Services/Trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e não devem ser expostos como pontos de extremidade voltados para a extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do Windows do WS-Trust, confira [desabilitar pontos de extremidade do Windows do WS-Trust no proxy](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Você pode ver quais pontos de extremidade são habilitados por meio do console de gerenciamento de AD FS em**pontos de extremidade**do **serviço**  > .

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

A partir da versão 1.1.819.0, Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Se a instalação da versão necessária do Azure AD Connect não for uma opção para você, consulte [como configurar manualmente o registro do dispositivo](hybrid-azuread-join-manual.md). 

Com base no cenário que corresponde à sua infraestrutura de identidade, consulte:

- [Configurar a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configurar a junção de Azure Active Directory híbrida para o ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Examine o suporte de UPN do AD local para ingressar no Azure AD híbrido

Às vezes, seus UPNs do AD local podem ser diferentes dos UPNs do Azure AD. Nesses casos, o ingresso no Azure AD híbrido do Windows 10 fornece suporte limitado para UPNs do AD local com base no [método de autenticação](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs locais do AD que podem existir em seu ambiente:

- UPN roteável: um UPN roteável tem um domínio verificado válido, que é registrado com um registrador de domínios. Por exemplo, se contoso.com for o domínio primário no Azure AD, contoso.org será o domínio primário no AD local de propriedade da Contoso e [verificado no Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- UPN não roteável: um UPN não roteável não tem um domínio verificado. Ele é aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com for o domínio primário no Azure AD, contoso. local será o domínio primário no AD local, mas não será um domínio verificável na Internet e usado somente na rede da contoso.

A tabela a seguir fornece detalhes sobre o suporte para esses UPNs locais do AD no ingresso no Azure AD híbrido do Windows 10

| Tipo de UPN local do AD | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Podem | Federado | Da versão 1703 | Disponível para o público geral |
| Não roteável | Federado | Da versão 1803 | Disponível para o público geral |
| Podem | Gerenciado | Da versão 1803 | Em geral, não há suporte para o SSPR do Azure AD na tela de bloqueio do Windows |
| Não roteável | Gerenciado | Sem suporte | |

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Configurar a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md)
> [Configurar a junção de Azure Active Directory híbrida para o ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
