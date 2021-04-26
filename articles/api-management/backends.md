---
title: Azure API Management arka uçları | Microsoft Docs
description: API Management 'de özel bacurlar hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813029"
---
# <a name="backends-in-api-management"></a>API Management backends

API Management *arka uç* (veya *API arka ucu*), ön uç API 'nizi ve işlemlerini uygulayan bir http hizmetidir.

Bazı API 'Leri içeri aktarırken API Management API arka ucunu otomatik olarak yapılandırır. Örneğin, API Management, bir [Openapı belirtimi](import-api-from-oas.md), [SOAP API 'si](import-soap-api.md)veya HTTP ile tetiklenen [Azure işlev uygulaması](import-function-app-as-api.md) veya [mantıksal uygulama](import-logic-app-as-api.md)gibi Azure kaynakları içeri aktarırken arka ucu yapılandırır.

API Management Ayrıca, API arka ucu olarak bir [Service Fabric kümesi](how-to-configure-service-fabric-backend.md) veya özel hizmet gibi diğer Azure kaynaklarının kullanılmasını da destekler. Bu özel arka uçların kullanılması için ek yapılandırma gerekir, örneğin, arka uç hizmetine isteklerin kimlik bilgilerini yetkilendirmek ve API işlemleri tanımlamak için. Bu arka uçları Azure portal veya Azure API 'Leri ya da araçlarını kullanarak yapılandırıp yönetirsiniz.

Arka uç oluşturduktan sonra API 'lerinizin arka uç URL 'sine başvurabilirsiniz. [`set-backend-service`](api-management-transformation-policies.md#SetBackendService)Bir gelen API isteğini, bu API 'nin varsayılan arka ucu yerine özel arka uca yönlendirmek için ilkeyi kullanın.

## <a name="benefits-of-backends"></a>Backenler 'in avantajları

Özel bir arka uçta aşağıdakiler de dahil olmak üzere çeşitli avantajları vardır:

* Arka uç hizmeti hakkında, API 'lerde yeniden kullanılabilirlik ve geliştirilmiş idare yükseltme bilgilerini soyutlar.  
* Varolan bir API 'de bir dönüştürme ilkesi yapılandırarak kolayca kullanılır
* Üstbilgi veya sorgu parametresi kimlik doğrulaması için [adlandırılmış değerler](api-management-howto-properties.md) yapılandırılmışsa Azure Key Vault gizli dizileri sürdürmek için API Management işlevlerinden yararlanır

## <a name="next-steps"></a>Sonraki adımlar

* Azure portal kullanarak [Service Fabric arka ucu](how-to-configure-service-fabric-backend.md) ayarlayın.
* Backends, API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)veya [Azure Resource Manager şablonları](../service-fabric/service-fabric-tutorial-deploy-api-management.md)kullanılarak da yapılandırılabilir.

