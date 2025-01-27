---
title: Perguntas frequentes sobre proteção de senha do Azure AD local-Azure Active Directory
description: Perguntas frequentes sobre proteção de senha do Azure AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c2e737360d6b1eeb8df28a95b8c36d4cca80ee4
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "71268638"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Proteção por senha do Azure AD local – perguntas frequentes

Esta seção fornece respostas para muitas perguntas frequentes sobre a proteção de senha do Azure AD.

## <a name="general-questions"></a>Perguntas gerais

**P: quais diretrizes os usuários devem receber sobre como selecionar uma senha segura?**

As diretrizes atuais da Microsoft sobre este tópico podem ser encontradas no seguinte link:

[Diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: a proteção de senha do Azure AD local tem suporte em nuvens não públicas?**

Não há suporte para a proteção de senha do Azure AD no local apenas na nuvem pública. Nenhuma data foi anunciada para disponibilidade de nuvem não pública.

O portal do AD do Azure permite a modificação da configuração de "proteção por senha para Windows Server Active Directory" específica no local, mesmo em nuvens não públicas; essas alterações serão persistidas, mas, caso contrário, nunca entrarão em vigor. O registro de florestas ou agentes de proxy local não tem suporte quando credenciais de nuvem não públicas são usadas e qualquer tentativa de registro desse tipo sempre falhará.

**P: como posso aplicar os benefícios da proteção de senha do Azure AD a um subconjunto de meus usuários locais?**

Sem suporte. Depois de implantado e habilitado, a proteção de senha do Azure AD não discriminar várias-todos os usuários recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de senha e um conjunto de senhas (ou redefinição)?**

Uma alteração de senha é quando um usuário escolhe uma nova senha depois de provar que tem conhecimento da senha antiga. Por exemplo, uma alteração de senha é o que acontece quando um usuário faz logon no Windows e, em seguida, é solicitado a escolher uma nova senha.

Um conjunto de senhas (às vezes chamado de redefinição de senha) é quando um administrador substitui a senha em uma conta com uma nova senha, por exemplo, usando a ferramenta de gerenciamento Active Directory usuários e computadores. Essa operação requer um alto nível de privilégio (geralmente administrador de domínio) e a pessoa que executa a operação geralmente não tem conhecimento da senha antiga. Cenários de assistência técnica geralmente executam conjuntos de senhas, por exemplo, ao auxiliar um usuário que esqueceu sua senha. Você também verá eventos de definição de senha quando uma conta de usuário totalmente nova estiver sendo criada pela primeira vez com uma senha.

A política de validação de senha comporta-se o mesmo, independentemente de uma alteração ou definição de senha estar sendo feita. O serviço de agente DC de proteção de senha do Azure AD registra eventos diferentes para informá-lo se uma operação de alteração ou de definição de senha foi feita.  Consulte [monitoramento e registro em log da proteção de senha do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**P: por que os eventos de rejeição de senha duplicados são registrados ao tentar definir uma senha fraca usando o snap-in de gerenciamento de usuários e computadores Active Directory?**

O snap-in de gerenciamento de usuários e computadores Active Directory primeiro tentará definir a nova senha usando o protocolo Kerberos. Após a falha, o snap-in fará uma segunda tentativa de definir a senha usando um protocolo herdado (SAM RPC) (os protocolos específicos usados não são importantes). Se a nova senha for considerada fraca pela proteção de senha do Azure AD, esse comportamento de snap-in resultará em um log de dois conjuntos de eventos de rejeição de redefinição de senha.

**P: por que os eventos de validação de senha da proteção de senha do Azure AD são registrados com um nome de usuário vazio?**

Active Directory dá suporte à capacidade de testar uma senha para ver se ela passa os requisitos de complexidade de senha atuais do domínio, por exemplo, usando a API [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Quando uma senha é validada dessa forma, o teste também inclui validação por produtos baseados em filtro de senha, como a proteção de senha do Azure AD, mas os nomes de usuário passados para uma determinada dll de filtro de senha ficarão vazios. Nesse cenário, a proteção de senha do Azure AD ainda validará a senha usando a política de senha atualmente em vigor e emitirá uma mensagem de log de eventos para capturar o resultado, no entanto, a mensagem de log de eventos terá campos de nome de usuário vazios.

**P: há suporte para instalar a proteção de senha do Azure AD lado a lado com outros produtos baseados em filtro de senha?**

Sim. O suporte para várias DLLs de filtro de senha registradas é um recurso principal do Windows e não é específico da proteção de senha do Azure AD. Todas as DLLs de filtro de senha registradas devem concordar antes que uma senha seja aceita.

**P: como posso implantar e configurar a proteção de senha do Azure AD no meu ambiente de Active Directory sem usar o Azure?**

Sem suporte. A proteção por senha do Azure AD é um recurso do Azure que dá suporte à extensão em um ambiente de Active Directory local.

**P: como posso modificar o conteúdo da política no nível de Active Directory?**

Sem suporte. A política só pode ser administrada usando o portal do AD do Azure. Consulte também a pergunta anterior.

**P: por que o DFSR é necessário para a replicação do SYSVOL?**

O FRS (a tecnologia predecessora para DFSR) tem muitos problemas conhecidos e não é totalmente suportado em versões mais recentes do Windows Server Active Directory. Será feito um teste zero de proteção de senha do Azure AD em domínios configurados pelo FRS.

Para obter mais informações, consulte os seguintes artigos:

[O caso para migrar a replicação do SYSVOL para o DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para o FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Se o seu domínio ainda não estiver usando o DFSR, você deverá migrá-lo para usar o DFSR antes de instalar a proteção de senha do Azure AD. Para obter mais informações, consulte o seguinte link:

[Guia de migração de replicação do SYSVOL: FRS para Replicação do DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> O software de agente DC de proteção por senha do Azure AD será instalado no momento em controladores de domínio em domínios que ainda estão usando o FRS para replicação SYSVOL, mas o software não funcionará corretamente nesse ambiente. Efeitos colaterais negativos adicionais incluem arquivos individuais que falham na replicação e os procedimentos de restauração de SYSVOL que aparecem com êxito, mas falham silenciosamente na replicação de todos os arquivos. Você deve migrar seu domínio para usar o DFSR assim que possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação da proteção de senha do Azure AD. Versões futuras do software serão desabilitadas automaticamente quando executadas em um domínio que ainda esteja usando o FRS.

**P: quanto espaço em disco o recurso requer no compartilhamento SYSVOL do domínio?**

A utilização de espaço preciso varia, pois depende de fatores como o número e o comprimento dos tokens proibidos na lista global banida da Microsoft e a lista personalizada por locatário, além da sobrecarga de criptografia. O conteúdo dessas listas provavelmente crescerá no futuro. Com isso em mente, uma expectativa razoável é que o recurso precisará de, pelo menos, cinco (5) megabytes de espaço no compartilhamento SYSVOL do domínio.

**P: por que uma reinicialização é necessária para instalar ou atualizar o software do agente de DC?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: há alguma maneira de configurar um agente de DC para usar um servidor proxy específico?**

Não. Como o servidor proxy não tem estado, não é importante qual servidor proxy específico é usado.

**P: não há problema em implantar o serviço proxy de proteção de senha do Azure AD lado a lado com outros serviços, como Azure AD Connect?**

Sim. O serviço proxy de proteção de senha do Azure AD e Azure AD Connect nunca devem entrar em conflito diretamente entre si.

Infelizmente, uma incompatibilidade foi encontrada entre a versão do serviço do atualizador do agente do Microsoft Azure AD Connect que é instalado pelo software de proxy de proteção de senha do Azure AD e a versão do serviço que é instalada pelo [Azure active ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)Software de proxy de aplicativo de diretório. Essa incompatibilidade pode fazer com que o serviço atualizador do agente não consiga contatar o Azure para atualizações de software. Não é recomendável instalar o proxy de proteção de senha do Azure AD e Proxy de Aplicativo do Azure Active Directory no mesmo computador.

**P: em que ordem os agentes de DC e os proxies serão instalados e registrados?**

Há suporte para qualquer ordem de instalação do agente de proxy, instalação do agente DC, registro de floresta e registro de proxy.

**P: devo me preocupar com o impacto sobre o desempenho em meus controladores de domínio de implantar esse recurso?**

O serviço de agente de DC de proteção de senha do Azure AD não deve afetar significativamente o desempenho do controlador de domínio em uma implantação de Active Directory íntegra existente.

Para a maioria das implantações Active Directory as operações de alteração de senha são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio específico. Como exemplo, imagine um domínio Active Directory com contas de usuário 10000 e uma política MaxPasswordAge definida como 30 dias. Em média, esse domínio verá 10000/30 = ~ 333 operações de alteração de senha por dia, que é um número secundário de operações para até mesmo um único controlador de domínio. Considere um possível cenário de pior caso: Suponha que essas ~ 333 alterações de senha em um único controlador de domínio tenham sido feitas em uma única hora. Por exemplo, esse cenário pode ocorrer quando muitos funcionários têm que trabalhar em uma manhã de segunda-feira. Mesmo nesse caso, ainda estamos olhando aproximadamente 333/60 minutos = seis alterações de senha por minuto, que novamente não é uma carga significativa.

No entanto, se os controladores de domínio atuais já estiverem sendo executados em níveis de desempenho limitado (por exemplo, maximizado em relação à CPU, espaço em disco, e/s de disco, etc.), é aconselhável adicionar outros controladores de domínio ou expandir o espaço disponível em disco, antes Implantando esse recurso. Consulte também a pergunta acima sobre o uso do espaço em disco do SYSVOL acima.

**P: quero testar a proteção de senha do Azure AD em apenas alguns DCs em meu domínio. É possível forçar as alterações de senha do usuário para usar esses DCs específicos?**

Não. O sistema operacional cliente do Windows controla qual controlador de domínio é usado quando um usuário altera sua senha. O controlador de domínio é selecionado com base em fatores como Active Directory atribuições de site e sub-rede, configuração de rede específica do ambiente, etc. A proteção de senha do Azure AD não controla esses fatores e não pode influenciar qual controlador de domínio é selecionado para alterar a senha de um usuário.

Uma maneira de atingir parcialmente essa meta seria implantar a proteção de senha do Azure AD em todos os controladores de domínio em um determinado site de Active Directory. Essa abordagem fornecerá cobertura razoável para os clientes do Windows que são atribuídos a esse site e, portanto, também para os usuários que fazem logon nesses clientes e alteram suas senhas.

**P: se eu instalar o serviço de agente de DC de proteção por senha do Azure AD apenas no controlador de domínio primário (PDC), todos os outros controladores de domínio no domínio também serão protegidos?**

Não. Quando a senha de um usuário é alterada em um determinado controlador de domínio não-PDC, a senha de texto não criptografada nunca é enviada ao PDC (essa ideia é uma percepção incorreta comum). Depois que uma nova senha é aceita em um determinado DC, esse DC usa essa senha para criar os vários hashes específicos de protocolo de autenticação dessa senha e, em seguida, persiste esses hashes no diretório. A senha de texto não criptografado não é persistente. Os hashes atualizados são então replicados para o PDC. As senhas de usuário podem, em alguns casos, ser alteradas diretamente no PDC, dependendo de vários fatores, como a topologia de rede e o design de Active Directory site. (Consulte a pergunta anterior.)

Em resumo, a implantação do serviço de agente de DC de proteção de senha do Azure AD no PDC é necessária para alcançar a cobertura de segurança de 100% do recurso em todo o domínio. A implantação do recurso no PDC só não fornece benefícios de segurança de proteção de senha do Azure AD para outros DCs no domínio.

**P: por que o bloqueio inteligente personalizado não funciona mesmo depois que os agentes são instalados em meu ambiente de Active Directory local?**

O bloqueio inteligente personalizado só tem suporte no Azure AD. As alterações nas configurações de bloqueio inteligente personalizadas no portal do Azure AD não têm nenhum efeito no ambiente de Active Directory local, mesmo com os agentes instalados.

**P: um pacote de gerenciamento do System Center Operations Manager disponível para a proteção de senha do Azure AD?**

Não.

**P: por que o Azure AD ainda rejeita senhas fracas, embora tenha configurado a política para estar no modo de auditoria?**

O modo de auditoria só tem suporte no ambiente de Active Directory local. O Azure AD está implicitamente sempre no modo "impor" quando avalia senhas.

**P: meus usuários veem a mensagem de erro tradicional do Windows quando uma senha é rejeitada pela proteção de senha do Azure AD. É possível personalizar essa mensagem de erro para que os usuários saibam o que realmente aconteceu?**

Não. A mensagem de erro vista pelos usuários quando uma senha é rejeitada por um controlador de domínio é controlada pelo computador cliente, não pelo controlador de domínio. Esse comportamento ocorre quando uma senha é rejeitada pelo padrão Active Directory políticas de senha ou por uma solução baseada em filtro de senha, como a proteção de senha do Azure AD.

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da documentação principal da proteção de senha do Azure AD, mas podem ser uma fonte útil de informações adicionais sobre o recurso.

[A proteção de senha do Azure AD já está disponível para o público geral!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de proteção por email phishing – parte 15: implemente o serviço de proteção de senha Microsoft Azure AD (para o local também!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[A proteção por senha e o bloqueio inteligente do Azure AD agora estão em visualização pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento de suporte do Microsoft Premier\Unified disponível

Se você estiver interessado em saber mais sobre a proteção de senha do Azure AD e implantá-la em seu ambiente, poderá aproveitar um serviço proativo da Microsoft disponível para esses clientes com um contrato de suporte Premier ou unificado. O serviço é chamado de Azure Active Directory: proteção por senha. Entre em contato com seu gerente técnico de conta para obter mais informações.

## <a name="next-steps"></a>Próximos passos

Se você tiver uma pergunta de proteção de senha do Azure AD local que não é respondida aqui, envie um item de comentário abaixo-Obrigado!

[Implantar a proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
