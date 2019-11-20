---
title: Azure Cloud Shell kaynaklar için Yönetilen kimlikler kullanma
description: Azure Cloud Shell 'de MSI ile kod doğrulama
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328709"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Azure Cloud Shell 'de Azure kaynakları için Yönetilen kimlikler kullanma

Azure Cloud Shell, Azure kaynakları için yönetilen kimliklerle yetkilendirmeyi destekler. Azure hizmetleriyle güvenli iletişim kurmak üzere erişim belirteçlerini almak için bunu kullanın.

## <a name="about-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikler hakkında
Bulut uygulamaları derlerken ortak bir zorluk, bulut hizmetlerinde kimlik doğrulaması için kodunuzda olması gereken kimlik bilgilerini güvenli bir şekilde yönetmektir. Cloud Shell, bir betiğin gerekebilecek bir kimlik bilgisi için Key Vault alma kimliğini doğrulamanız gerekebilir.

Azure kaynaklarına yönelik yönetilen kimlikler, Azure hizmetlerine Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik vererek bu sorunu daha kolay bir şekilde çözmenizi sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri bulunmasına gerek kalmadan kimlik doğrulaması yapabilirsiniz.

## <a name="acquire-access-token-in-cloud-shell"></a>Cloud Shell erişim belirteci al

MSI erişim belirtecinizi bir ortam değişkeni olarak ayarlamak için aşağıdaki komutları yürütün `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Belirteç süre sonu işleme

Yerel MSI alt sistemi belirteçleri önbelleğe alır. Bu nedenle, bunu istediğiniz sıklıkta ve Azure AD sonuçlarına yalnızca şu durumlarda bir hat üzeri çağrı yapabilirsiniz:
- önbellekte belirteç olmaması nedeniyle önbellek isabetsizliği meydana geldi
- belirtecin geçerliliği zaman aşımına uğradı

Kodunuzda belirteci önbelleğe alırsanız, kaynağın belirtecin dolduğunu gösterdiği senaryoları işlemeye hazır olmanız gerekir.

Belirteç hatalarını işlemek için, MSI [erişim belirteçlerini seçmekte olan MSI sayfasını](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar
[MSI hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[MSI VM 'lerinden erişim belirteçleri alınıyor](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
