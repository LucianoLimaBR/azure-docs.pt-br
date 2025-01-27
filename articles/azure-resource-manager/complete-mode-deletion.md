---
title: Azure Resource Manager exclusão de modo completo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: de1b5080e72f79626ca0c749efe4122721f14922
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528595"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações de modo completo

Este artigo descreve como os tipos de recursos tratam da exclusão quando não estão em um modelo implantado no modo completo.

Os tipos de recurso marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recurso marcados **sem não são excluídos** automaticamente quando não estão no modelo; no entanto, elas serão excluídas se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. atestador](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. billing](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitivaservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. consumo](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. Data Box](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. databricks](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. datamigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomas](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Managedservices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrar](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. emparelhamento](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | DomainServices | Sim |
> | DomainServices/oucontainer | Não |
> | DomainServices/ReplicaSets | Sim |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | supportProviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | aadsupportcases | Não |
> | adicionaservices | Não |
> | Representantes | Não |
> | anonymousapiusers | Não |
> | configuração | Não |
> | logs | Não |
> | Fornece | Não |
> | servicehealthmetrics | Não |
> | Serviços | Não |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Figura | Não |
> | generateRecommendations | Não |
> | los | Não |
> | filmes | Não |
> | supressões | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | actionRules | Sim |
> | alertas | Não |
> | alertas | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | Receber | Não |
> | smartDetectorAlertRules | Sim |
> | smartDetectorRuntimeEnvironments | Não |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | servidores | Sim |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | Sim |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Surge | Sim |

## <a name="microsoftattestation"></a>Microsoft. atestador

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | attestationProviders | Não |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | classicAdministrators | Não |
> | Aliases de dataalias | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | Bloquea | Não |
> | permissões | Não |
> | policyAssignments | Não |
> | policyDefinitions | Não |
> | policySetDefinitions | Não |
> | providerOperations | Não |
> | roleAssignments | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | automationAccounts | Sim |
> | automationAccounts/configurações | Sim |
> | automationAccounts/trabalhos | Não |
> | automationAccounts/runbooks | Sim |
> | automationAccounts/softwareUpdateConfigurations | Não |
> | automationAccounts/WebHooks | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Sistemas | Não |
> | ambientes/contas | Não |
> | ambientes/contas/namespaces | Não |
> | ambientes/contas/namespaces/configurações | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | b2cDirectories | Sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | hybridDataManagers | Sim |
> | postgresInstances | Sim |
> | sqlBigDataClusters | Sim |
> | SQLInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations/sqlservers | Não |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | registros | Sim |
> | registros/customerSubscriptions | Não |
> | registros/produtos | Não |

## <a name="microsoftbatch"></a>Microsoft. batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | batchAccounts | Sim |

## <a name="microsoftbilling"></a>Microsoft. billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | billingAccounts/contratos | Não |
> | billingAccounts/billingPermissions | Não |
> | billingAccounts/billingProfiles | Não |
> | billingAccounts/billingProfiles/billingPermissions | Não |
> | billingAccounts/billingProfiles/billingRoleAssignments | Não |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Não |
> | billingAccounts/billingProfiles/billingSubscriptions | Não |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/clientes | Não |
> | billingAccounts/billingProfiles/faturas | Não |
> | billingAccounts/billingProfiles/faturas/pricesheet | Não |
> | billingAccounts/billingProfiles/invoiceSections | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Não |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não |
> | billingAccounts/BillingProfiles/patchOperations | Não |
> | billingAccounts/billingProfiles/paymentMethods | Não |
> | billingAccounts/billingProfiles/políticas | Não |
> | billingAccounts/billingProfiles/pricesheet | Não |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Não |
> | billingAccounts/billingProfiles/produtos | Não |
> | billingAccounts/billingProfiles/transações | Não |
> | billingAccounts/billingRoleAssignments | Não |
> | billingAccounts/billingRoleDefinitions | Não |
> | billingAccounts/billingSubscriptions | Não |
> | billingAccounts/createBillingRoleAssignment | Não |
> | billingAccounts/createInvoiceSectionOperations | Não |
> | billingAccounts/clientes | Não |
> | billingAccounts/clientes/billingSubscriptions | Não |
> | billingAccounts/clientes/initiateTransfer | Não |
> | billingAccounts/clientes/políticas | Não |
> | billingAccounts/clientes/produtos | Não |
> | billingAccounts/clientes/transações | Não |
> | billingAccounts/clientes/transferências | Não |
> | billingAccounts/departamentos | Não |
> | billingAccounts/enrollmentAccounts | Não |
> | billingAccounts/faturas | Não |
> | billingAccounts/invoiceSections | Não |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Não |
> | billingAccounts/invoiceSections/billingSubscriptions | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transferência | Não |
> | billingAccounts/invoiceSections/Elevate | Não |
> | billingAccounts/invoiceSections/initiateTransfer | Não |
> | billingAccounts/invoiceSections/patchOperations | Não |
> | billingAccounts/invoiceSections/productMoveOperations | Não |
> | billingAccounts/invoiceSections/produtos | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/invoiceSections/transações | Não |
> | billingAccounts/invoiceSections/transferências | Não |
> | billingAccounts/lineOfCredit | Não |
> | billingAccounts/patchOperations | Não |
> | billingAccounts/paymentMethods | Não |
> | billingAccounts/produtos | Não |
> | billingAccounts/transações | Não |
> | billingPeriods | Não |
> | billingPermissions | Não |
> | billproperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departamentos | Não |
> | enrollmentAccounts | Não |
> | faturas | Não |
> | transferir | Não |
> | transferências/acceptTransfer | Não |
> | transferências/declineTransfer | Não |
> | transferências/operationStatus | Não |
> | transferências/validateTransfer | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | blockchainMembers | Sim |
> | espectadores | Sim |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments/assignmentOperations | Não |
> | blueprintAssignments/operações | Não |
> | planos gráficos | Não |
> | plantas/artefatos | Não |
> | plantas/versões | Não |
> | plantas/versões/artefatos | Não |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices/canais | Não |
> | botServices/conexões | Não |
> | Idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Redis | Sim |
> | RedisConfigDefinition | Não |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | appliedReservations | Não |
> | calculateExchange | Não |
> | calculatePrice | Não |
> | calculatePurchasePrice | Não |
> | catálogos | Não |
> | commercialReservationOrders | Não |
> | exchange | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders/calculateRefund | Não |
> | reservationOrders/mesclagem | Não |
> | reservationOrders/reservas | Não |
> | reservationOrders/reservas/revisões | Não |
> | reservationOrders/retorno | Não |
> | reservationOrders/divisão | Não |
> | reservationOrders/permuta | Não |
> | Reservas | Não |
> | Os | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | perfis | Sim |
> | perfis/pontos de extremidade | Sim |
> | perfis/pontos de extremidade/customdomains | Não |
> | perfis/pontos de extremidade/origens | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | certificateOrders | Sim |
> | certificateOrders/certificados | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft. ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | recursos | Não |
> | domainNames | Sim |
> | nome_do_domínio/recursos | Não |
> | nome_do_domínio/internalLoadBalancers | Não |
> | DomainNames/certificados | Não |
> | nome_do_domínio/Slots | Não |
> | nome_do_domínio/Slots/funções | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não |
> | DomainNames/Slots/funções/métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | Cotas | Não |
> | resourceType | Não |
> | validateSubscriptionMoveAvailability | Não |
> | VirtualMachines | Sim |
> | virtualMachines/diagnosticSettings | Não |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | classicInfrastructureResources | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | recursos | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections/emparelhamentos | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | Sim |
> | Cotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks/virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | recursos | Não |
> | Discos | Não |
> | imagens | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | Cotas | Não |
> | storageAccounts | Sim |
> | storageAccounts/blobservices | Não |
> | storageAccounts/fileservices | Não |
> | storageAccounts/metricDefinitions | Não |
> | storageAccounts/métricas | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/diagnosticSettings | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/serviços/métricas | Não |
> | storageAccounts/tabelaservices | Não |
> | storageAccounts/vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitivaservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | RateCard | Não |
> | UsageAggregates | Não |

## <a name="microsoftcompute"></a>Microsoft. Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | availabilitySets | Sim |
> | diskEncryptionSets | Sim |
> | Discos | Sim |
> | galerias | Sim |
> | galerias/aplicativos | Não |
> | galerias/aplicativos/versões | Não |
> | galerias/imagens | Não |
> | galerias/imagens/versões | Não |
> | Hosts | Sim |
> | Hosts/hosts | Sim |
> | imagens | Sim |
> | proximityPlacementGroups | Sim |
> | restorePointCollections | Sim |
> | restorePointCollections/restorePoints | Não |
> | sharedVMImages | Sim |
> | sharedVMImages/versões | Não |
> | instantâneos | Sim |
> | VirtualMachines | Sim |
> | virtualMachines/extensões | Sim |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets/extensões | Não |
> | virtualMachineScaleSets/networkInterfaces | Não |
> | virtualMachineScaleSets/publicIPAddresses | Não |
> | virtualMachineScaleSets/virtualMachines | Não |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |

## <a name="microsoftconsumption"></a>Microsoft. consumo

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | AggregatedCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Contabiliza | Não |
> | CostTags | Não |
> | crédito | Não |
> | LostFocus | Não |
> | Previsão | Não |
> | muitas | Não |
> | Marketplaces | Não |
> | Pricesheets | Não |
> | produto | Não |
> | ReservationDetails | Não |
> | ReservationRecommendations | Não |
> | ReservationSummaries | Não |
> | ReservationTransactions | Não |
> | Marcas | Não |
> | Locatários | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | containerGroups | Sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | registros | Sim |
> | registros/compilações | Não |
> | registros/compilações/cancelar | Não |
> | registros/Builds/getLogLink | Não |
> | registros/buildTasks | Sim |
> | registros/buildTasks/etapas | Não |
> | registros/eventGridFilters | Não |
> | registros/getBuildSourceUploadUrl | Não |
> | registros/GetCredentials | Não |
> | registros/importImage | Não |
> | registros/queueBuild | Não |
> | registros/regenerateCredential | Não |
> | registros/regenerateCredentials | Não |
> | registros/replicações | Sim |
> | registros/execuções | Não |
> | registros/execuções/cancelamento | Não |
> | registros/scheduleRun | Não |
> | registros/tarefas | Sim |
> | registros/updatePolicies | Não |
> | registros/WebHooks | Sim |
> | registros/WebHooks/getCallbackConfig | Não |
> | registros/WebHooks/ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | contêinerservices | Sim |
> | managedClusters | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | aplicativos | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Alertas | Não |
> | billingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | departamentos | Não |
> | As | Não |
> | enrollmentAccounts | Não |
> | Port | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts/alertas | Não |
> | ExternalBillingAccounts/dimensões | Não |
> | ExternalBillingAccounts/previsão | Não |
> | ExternalBillingAccounts/consulta | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions/alertas | Não |
> | ExternalSubscriptions/dimensões | Não |
> | ExternalSubscriptions/previsão | Não |
> | ExternalSubscriptions/consulta | Não |
> | Visto | Não |
> | Consulta | Não |
> | Registr | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Configurações | Não |
> | showbackRules | Não |
> | Exibições | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Pedido | Não |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | SAS | Não |
> | resourceProviders | Sim |

## <a name="microsoftdatabox"></a>Microsoft. Data Box

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Sejam | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft. databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | espaços | Sim |
> | espaços de trabalho/virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | catálogos | Sim |
> | catálogos de | Sim |
> | datacatalogs/fontes de fonte | Não |
> | datacatalogs/fontes de pesquisa/verificações | Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não |
> | datacatalogs/fontes/exames/gatilhos | Não |
> | datacatalogs/scantargets | Não |
> | datacatalogs/scantargets/DataSets | Não |

## <a name="microsoftdatafactory"></a>Microsoft. datafactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | datafactorings/diagnosticSettings | Não |
> | datafactorings/metricDefinitions | Não |
> | dataFactorySchema | Não |
> | fábricas | Sim |
> | fábricas/integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |
> | contas/dataLakeStoreAccounts | Não |
> | contas/storageAccounts | Não |
> | contas/storageAccounts/contêineres | Não |
> | contas/transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |
> | contas/eventGridFilters | Não |
> | contas/firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft. datamigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Serviços | Sim |
> | serviços/projetos | Sim |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |
> | contas/compartilhamentos | Não |
> | contas/compartilhamentos/conjuntos de valores | Não |
> | contas/compartilhamentos/convites | Não |
> | contas/compartilhamentos/providersharesubscriptions | Não |
> | contas/compartilhamentos/synchronizationSettings | Não |
> | contas/sharesubscriptions | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não |
> | contas/sharesubscriptions/datasetmappings | Não |
> | contas/sharesubscriptions/gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | serverGroups | Sim |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |
> | serversv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | artifactSources | Sim |
> | distribuições | Sim |
> | pertopologias | Sim |
> | serviços e pertopologias | Sim |
> | pertopologias/serviços/unidades de serviço | Sim |
> | Tarefas | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | applicationgroups | Sim |
> | applicationgroups/aplicativos | Não |
> | applicationgroups/startmenuitems | Não |
> | hostpools | Sim |
> | hostpools/sessionhosts | Não |
> | hostpools/sessionhosts/usersessions | Não |
> | hostpools/usersessions | Não |
> | espaços | Sim |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | ElasticPools | Sim |
> | ElasticPools/IotHubTenants | Sim |
> | IotHubs | Sim |
> | IotHubs/eventGridFilters | Não |
> | ProvisioningServices | Sim |
> | usos | Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Pipelines | Sim |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | controladores | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | labcenters | Sim |
> | prático | Sim |
> | laboratórios/ambientes | Sim |
> | laboratórios/perrunners | Sim |
> | laboratórios/virtualMachines | Sim |
> | agendamento | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | databaseAccountNames | Não |
> | databaseAccounts | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios/domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects/clouddeployments | Não |
> | lcsprojects/conectores | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Serviços | Sim |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios/tópicos | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | Tópicos | Sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | clusters | Sim |
> | Namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/Eventhubs | Não |
> | namespaces/Eventhubs/authorizationrules | Não |
> | namespaces/Eventhubs/consumergroups | Não |
> | namespaces/networkrulesets | Não |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | elástico | Não |
> | providers | Não |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Registr | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | minhas áreas | Não |
> | myddds/áreas | Não |
> | myddds/áreas/áreas | Não |
> | myddds/áreas/áreas/galleryitems | Não |
> | myddds/áreas/galleryitems | Não |
> | myareas/galleryitems | Não |
> | Registr | Não |
> | Os | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft. genomas

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Sim |
> | guestConfigurationAssignments | Não |
> | antivírus | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitors | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | dedicatedHSMs | Sim |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/aplicativos | Não |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Serviços | Sim |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | maquina | Sim |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Gerenciadores de DataManager | Sim |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | QC | Sim |
> | networkScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Sejam | Sim |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | appTemplates | Não |
> | IoTApps | Sim |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Graph | Sim |

## <a name="microsoftkeyvault"></a>Microsoft. keyvault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | Sim |
> | Cofres | Sim |
> | cofres/accessPolicies | Não |
> | cofres/eventGridFilters | Não |
> | cofres/segredos | Não |

## <a name="microsoftkusto"></a>Microsoft. Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/attacheddatabaseconfigurations | Não |
> | clusters/bancos de dados | Não |
> | clusters/bancos de dados/conexões DataConnections | Não |
> | clusters/bancos de dados/eventhubconnections | Não |
> | clusters/sharedidentities | Não |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | labaccounts | Sim |
> | users | Não |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | hostingEnvironments | Sim |
> | integrationAccounts | Sim |
> | integrationServiceEnvironments | Sim |
> | integrationServiceEnvironments/managedApis | Sim |
> | isolatedEnvironments | Sim |
> | fluxos | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | commitmentPlans | Sim |
> | webServices | Sim |
> | Workspaces | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft. MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | espaços | Sim |
> | espaços de trabalho/computações | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Identities | Não |
> | userAssignedIdentities | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |
> | registrationAssignments | Não |
> | registrationDefinitions | Não |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | getentities | Não |
> | managementGroups | Não |
> | Os | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Sim |
> | contas/eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Ofereça | Não |
> | offerTypes | Não |
> | offerTypes/Publicadores | Não |
> | offerTypes/Publicadores/ofertas | Não |
> | offerTypes/Publicadores/ofertas/planos | Não |
> | offerTypes/Publicadores/ofertas/planos/contratos | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações/importImage | Não |
> | privategalleryitems | Não |
> | produto | Não |
> | publicado | Não |
> | editores/ofertas | Não |
> | editores/ofertas/emendas | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | classicDevServices | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | licenças | Não |
> | offertypes | Não |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | mediaservices | Sim |
> | mediaservices/accountFilters | Não |
> | mediaservices/ativos | Não |
> | mediaservices/ativos/assetFilters | Não |
> | mediaservices/contentKeyPolicies | Não |
> | mediaservices/eventGridFilters | Não |
> | mediaservices/liveEventOperations | Não |
> | mediaservices/liveEvents | Sim |
> | mediaservices/liveEvents/liveOutputs | Não |
> | mediaservices/liveOutputOperations | Não |
> | mediaservices/streamingEndpointOperations | Não |
> | mediaservices/streamingEndpoints | Sim |
> | mediaservices/streamingLocators | Não |
> | mediaservices/streamingPolicies | Não |
> | mediaservices/transformações | Não |
> | mídia/transformações/trabalhos | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft. migrar

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | assessmentProjects | Sim |
> | migrateprojects | Sim |
> | projeto | Sim |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sim |
> | objectUnderstandingAccounts | Sim |
> | remoteRenderingAccounts | Sim |
> | spatialAnchorsAccounts | Sim |
> | surfaceReconstructionAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts/backupPolicies | Sim |
> | netAppAccounts/capacityPools | Sim |
> | netAppAccounts/capacityPools/volumes | Sim |
> | netAppAccounts/capacityPools/volumes/backups | Não |
> | netAppAccounts/capacityPools/volumes/mountTargets | Sim |
> | netAppAccounts/capacityPools/volumes/instantâneos | Sim |
> | netAppAccounts/cofres | Não |
## <a name="microsoftnetwork"></a>Microsoft. Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | applicationGateways | Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim |
> | applicationSecurityGroups | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastionHosts | Sim |
> | bgpServiceCommunities | Não |
> | Conexões | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones/A | Não |
> | dnszones/AAAA | Não |
> | dnszones/tudo | Não |
> | dnszones/CAA | Não |
> | dnszones/CNAME | Não |
> | dnszones/MX | Não |
> | dnszones/NS | Não |
> | dnszones/PTR | Não |
> | dnszones/conjuntos de registros | Não |
> | dnszones/SOA | Não |
> | dnszones/SRV | Não |
> | dnszones/TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | Sim |
> | frontdoors | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | loadBalancers | Sim |
> | localNetworkGateways | Sim |
> | natGateways | Sim |
> | networkIntentPolicies | Sim |
> | networkInterfaces | Sim |
> | networkProfiles | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | networkWatchers/connectionMonitors | Sim |
> | networkWatchers/lentes | Sim |
> | networkWatchers/pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones/A | Não |
> | privateDnsZones/AAAA | Não |
> | privateDnsZones/tudo | Não |
> | privateDnsZones/CNAME | Não |
> | privateDnsZones/MX | Não |
> | privateDnsZones/PTR | Não |
> | privateDnsZones/SOA | Não |
> | privateDnsZones/SRV | Não |
> | privateDnsZones/TXT | Não |
> | privateDnsZones/virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixes | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | secureGateways | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | trafficmanagerprofiles/calor | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualHubs | Sim |
> | virtualNetworkGateways | Sim |
> | virtualNetworks | Sim |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | vpnGateways | Sim |
> | vpnSites | Sim |
> | webApplicationFirewallPolicies | Sim |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Namespaces | Sim |
> | namespaces/notificationHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | osNamespaces | Sim |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | HyperVSites | Sim |
> | ImportSites | Sim |
> | ServerSites | Sim |
> | VMwareSites | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | clusters | Sim |
> | Pseudodispositivos | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | espaços | Sim |
> | espaços de trabalho/fontes de fonte | Não |
> | espaços de trabalho/linksservices | Não |
> | espaços de trabalho/consulta | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | Soluções | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft. emparelhamento

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | emparelhamentos | Sim |
> | peeringServiceProviders | Não |
> | peeringServices | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | policyEvents | Não |
> | policyStates | Não |
> | policyTrackedResources | Não |
> | correções | Não |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | consoles | Não |
> | painéis | Sim |
> | UserSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | unidades | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | Cofres | Sim |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/hybridconnections | Não |
> | namespaces/hybridconnections/authorizationrules | Não |
> | namespaces/wcfrelays | Não |
> | namespaces/wcfrelays/authorizationrules | Não |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | As | Não |
> | Colec | Sim |
> | coleções/aplicativos | Não |
> | coleções/SecurityPrincipals | Não |
> | templateImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | consultas | Sim |
> | resourceChangeDetails | Não |
> | resourceChanges | Não |
> | Os | Não |
> | resourcesHistory | Não |
> | subscriptionsStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | availabilityStatuses | Não |
> | childAvailabilityStatuses | Não |
> | childResources | Não |
> | LostFocus | Não |
> | impactedResources | Não |
> | los | Não |
> | Notificações | Não |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Implantações | Não |
> | implantações/operações | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | Os | Não |
> | subscriptions | Não |
> | assinaturas/provedores | Não |
> | assinaturas/resourceGroups | Não |
> | assinaturas/resourcegroups/recursos | Não |
> | assinaturas/recursos | Não |
> | assinaturas/TagNames | Não |
> | assinaturas/tagNames/tagValues | Não |
> | Locatários | Não |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | aplicativos | Sim |
> | saasresources | Não |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | gratuitas | Sim |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | pesquisarservices | Sim |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alertas | Não |
> | allowedConnections | Não |
> | applicationWhitelistings | Não |
> | assessmentMetadata | Não |
> | Avaliações | Não |
> | AutoProvisioningSettings | Não |
> | Conformidades | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions/analyticsModels | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não |
> | jitNetworkAccessPolicies | Não |
> | networkData | Não |
> | playbookConfigurations | Sim |
> | Policie | Não |
> | preços | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | Configurações | Não |
> | subavaliações | Não |
> | Tarefa | Não |
> | Topologias | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | agregações | Não |
> | alertRules | Não |
> | alertRuleTemplates | Não |
> | indicadores | Não |
> | bolsas | Não |
> | dataconnecters | Não |
> | Contabilidade | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | Configurações | Não |

## <a name="microsoftservicebus"></a>Microsoft. ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventgridfilters | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/filas | Não |
> | namespaces/filas/authorizationrules | Não |
> | namespaces/tópicos | Não |
> | namespaces/tópicos/authorizationrules | Não |
> | namespaces/tópicos/assinaturas | Não |
> | namespaces/tópicos/assinaturas/regras | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft. infabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | aplicativos | Sim |
> | clusters | Sim |
> | clusters/aplicativos | Não |
> | containerGroups | Sim |
> | containerGroupSets | Sim |
> | edgeclusters | Sim |
> | edgeclusters/aplicativos | Não |
> | às | Sim |
> | secretstores | Sim |
> | secretstores/certificados | Não |
> | secretstores/segredos | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | aplicativos | Sim |
> | containerGroups | Sim |
> | gateways | Sim |
> | às | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations/resourceTypeRegistrations | Não |
> | distribuições | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | Sinalização/eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | hybridUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | applicationDefinitions | Sim |
> | aplicativos | Sim |
> | jitRequests | Sim |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | managedInstances/bancos de dados | Sim |
> | managedInstances/bancos de dados/backupShortTermRetentionPolicies | Não |
> | managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base | Não |
> | managedInstances/encryptionProtector | Não |
> | managedInstances/chaves | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não |
> | managedInstances/vulnerabilityAssessments | Não |
> | servidores | Sim |
> | servidores/administradores | Não |
> | servidores/communicationLinks | Não |
> | servidores/bancos de dados | Sim |
> | servidores/encryptionProtector | Não |
> | servidores/firewallRules | Não |
> | servidores/chaves | Não |
> | servidores/restorableDroppedDatabases | Não |
> | servidores/preobjetivos | Não |
> | servidores/tdeCertificates | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | storageAccounts | Sim |
> | storageAccounts/blobservices | Não |
> | storageAccounts/fileservices | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/tabelaservices | Não |
> | usos | Não |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches/storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | esses | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Cancelar | Não |
> | CreateSubscription | Não |
> | Desabilitar | Não |
> | Nome | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionOperations | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Sistemas | Sim |
> | ambientes/accessPolicies | Não |
> | ambientes/EventSources | Sim |
> | ambientes/referenceDataSets | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sim |
> | dedicatedCloudServices | Sim |
> | VirtualMachines | Sim |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts/apiAcls | Não |
> | apiManagementAccounts/APIs | Não |
> | apiManagementAccounts/APIs/apiAcls | Não |
> | apiManagementAccounts/APIs/connectionAcls | Não |
> | apiManagementAccounts/APIs/conexões | Não |
> | apiManagementAccounts/APIs/conexões/connectionAcls | Não |
> | apiManagementAccounts/APIs/localizedDefinitions | Não |
> | apiManagementAccounts/connectionAcls | Não |
> | apiManagementAccounts/conexões | Não |
> | billingMeters | Não |
> | Certificado | Sim |
> | connectionGateways | Sim |
> | Conexões | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | functions | Não |
> | hostingEnvironments | Sim |
> | hostingEnvironments/multiRolePools | Não |
> | hostingEnvironments/pools | Não |
> | publishingUsers | Não |
> | filmes | Não |
> | resourceHealthMetadata | Não |
> | tempos | Não |
> | serverFarms | Sim |
> | serverFarms/eventGridFilters | Não |
> | sites | Sim |
> | sites/configuração  | Não |
> | sites/eventGridFilters | Não |
> | sites/hostNameBindings | Não |
> | sites/networkConfig | Não |
> | sites/premieraddons | Sim |
> | sites/Slots | Sim |
> | sites/Slots/eventGridFilters | Não |
> | sites/Slots/hostNameBindings | Não |
> | sites/Slots/networkConfig | Não |
> | sourceControls | Não |
> | Verifica | Não |
> | verifyHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | Dispositivos | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão do modo completo |
> | ------------- | ----------- |
> | QC | Não |
> | componentsSummary | Não |
> | monitorInstances | Não |
> | monitorInstancesSummary | Não |
> | monitores | Não |
> | notificationSettings | Não |

## <a name="next-steps"></a>Próximos passos

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
