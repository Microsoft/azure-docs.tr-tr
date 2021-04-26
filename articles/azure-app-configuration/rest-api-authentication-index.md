---
title: Azure uygulama yapılandırma REST API-kimlik doğrulama
description: Azure Uygulama yapılandırması 'nı kullanarak kimlik doğrulaması için başvuru sayfaları REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932702"
---
# <a name="authentication"></a>Kimlik Doğrulaması

Tüm HTTP isteklerinin kimliği doğrulanmalıdır. Aşağıdaki kimlik doğrulama şemaları desteklenir.

## <a name="hmac"></a>HMAC

[HMAC kimlik doğrulaması](./rest-api-authentication-hmac.md) , istek yüklerini imzalamak için rastgele oluşturulmuş bir gizli anahtar kullanır. Bu kimlik doğrulama yöntemini kullanan isteklerin nasıl yetkilendirildiği hakkındaki ayrıntılar, [HMAC yetkilendirme](./rest-api-authorization-hmac.md) bölümünde bulunabilir.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) kimlik doğrulaması](../active-directory/authentication/overview-authentication.md) , isteklerin kimliğini doğrulamak için Azure Active Directory alınan bir taşıyıcı belirtecini kullanır. Bu kimlik doğrulama yöntemini kullanan isteklerin [yetkilendirilmesine ilişkin ayrıntılar Azure AD yetkilendirme](./rest-api-authorization-azure-ad.md) bölümünde bulunabilir.