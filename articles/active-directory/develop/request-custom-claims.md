---
title: Como solicitar declarações personalizadas usando o MSAL para iOS e macOS
titleSuffix: Microsoft identity platform
description: Saiba como solicitar declarações personalizadas.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c34da9e8faa8c2c2e24e7f00569e2b7c8af674f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802600"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Como: solicitar declarações personalizadas usando o MSAL para iOS e macOS

O OpenID Connect permite que você solicite opcionalmente o retorno de declarações individuais do ponto de extremidade de UserInfo e/ou no token de ID. Uma solicitação de declarações é representada como um objeto JSON que contém uma lista de declarações solicitadas. Consulte [OpenID Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) para obter mais detalhes.

A MSAL (biblioteca de autenticação da Microsoft) para iOS e macOS permite solicitar declarações específicas em cenários de aquisição de tokens interativos e silenciosos. Ele faz isso por meio do parâmetro `claimsRequest`.

Há vários cenários em que isso é necessário. Por exemplo:

- Solicitação de declarações fora do conjunto padrão para seu aplicativo.
- Solicitando combinações específicas das declarações padrão que não podem ser especificadas usando escopos para seu aplicativo. Por exemplo, se um token de acesso for rejeitado devido a declarações ausentes, o aplicativo poderá solicitar as declarações ausentes usando MSAL.

> [!NOTE]
> MSAL ignora o cache de token de acesso sempre que uma solicitação de declarações é especificada. É importante fornecer apenas `claimsRequest` parâmetro quando declarações adicionais são necessárias (em vez de fornecer sempre o mesmo parâmetro `claimsRequest` em cada chamada à API MSAL).

`claimsRequest` pode ser especificado em `MSALSilentTokenParameters` e `MSALInteractiveTokenParameters`:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` pode ser construída a partir de uma representação NSString da solicitação de declarações JSON. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Ele também pode ser modificado solicitando declarações específicas adicionais:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` deve ser definido nos parâmetros de token e fornecidos para uma das APIs de aquisições de token MSAL:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
