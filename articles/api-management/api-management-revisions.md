---
title: Azure API Management 'de düzeltmeler | Microsoft Docs
description: Azure API Management düzeltmelerin kavramı hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812147"
---
# <a name="revisions-in-azure-api-management"></a>Azure API Management 'de düzeltmeler

Düzeltmeler, API 'leriniz üzerinde denetimli ve güvenli bir şekilde değişiklik yapmanızı sağlar. Değişiklik yapmak istediğinizde yeni bir düzeltme oluşturun. Daha sonra API tüketicilerinizi etkilemeden API 'yi düzenleyebilir ve test edebilirsiniz. Hazırsanız, düzeltinizi güncel hale getirebilirsiniz. Aynı zamanda, değişiklik günlüğüne isteğe bağlı olarak bir giriş gönderebilir, bu da API tüketicilerinizi değiştiği gibi güncel tutmaya devam edebilir. Değişiklik günlüğü geliştirici portalınıza yayımlanır.

> [!NOTE]
> Geliştirici portalı, tüketim katmanında kullanılamaz.

Düzeltmeler ile şunları yapabilirsiniz:

- Üretim API 'nizi bozmadan API tanımlarınızda ve ilkeleriniz üzerinde güvenli bir şekilde değişiklikler yapın.
- Değişiklikleri yayımlamadan önce deneyin.
- Yaptığınız değişiklikleri belgeleyin, böylece geliştiricileriniz nelerin yeni olduğunu anlayabilirler.
- Sorunları bulursanız geri alın.

[Kılavuzumuzu izleyerek düzeltmeler ile çalışmaya başlayın.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Belirli düzeltmelere erişme

API 'nize yapılan her düzeltmeye, özel olarak oluşturulmuş bir URL kullanılarak erişilebilir. API `;rev={revisionNumber}` URL 'nizin sonuna, bu API 'nin belirli bir düzeltmesine erişmek için sorgu dizesinden önce ekleyin. Örneğin, API 'nin düzeltme 3 ' e erişmek için bu URL 'YI kullanabilirsiniz `customers` :

`https://apis.contoso.com/customers;rev=3?customerId=123`

Varsayılan olarak, her bir düzeltme geçerli düzeltme ile aynı güvenlik ayarlarına sahiptir. Her düzeltme için farklı güvenlik uygulanmasını istiyorsanız, belirli bir düzeltme için ilkeleri kasıtlı olarak değiştirebilirsiniz. Örneğin, dış çağıranların hala geliştirme aşamasında olan bir düzeltmeye erişmesini engellemek için bir [IP filtreleme ilkesi](./api-management-access-restriction-policies.md#RestrictCallerIPs) eklemek isteyebilirsiniz.

Bir düzeltme çevrimdışı hale getirilir ve bu, düzeltme, URL 'SI aracılığıyla düzeltmeye çalıştıklarında bile çağıranların erişilmez olmasını sağlar. Azure portal kullanarak bir düzeltmeyi çevrimdışı olarak işaretleyebilirsiniz. PowerShell kullanıyorsanız, `Set-AzApiManagementApiRevision` cmdlet 'ini kullanabilir ve `Path` bağımsız değişkenini olarak ayarlayabilirsiniz `$null` .

> [!NOTE]
> Testleri test için kullanmadığınız durumlarda çevrimdışı olarak bir değişiklik yapmanız önerilir.

## <a name="current-revision"></a>Geçerli düzeltme

Tek bir düzeltme *geçerli* düzeltme olarak ayarlanabilir. Bu düzeltme, URL 'de açık bir düzeltme numarası belirtmeyen tüm API istekleri için kullanılan bir değişiklik olacaktır. Bu düzeltmeyi güncel olarak ayarlayarak önceki bir düzeltmeye geri dönebilirsiniz.

Azure portal kullanarak bir düzeltmeyi güncel olarak ayarlayabilirsiniz. PowerShell kullanıyorsanız `New-AzApiManagementApiRelease` cmdlet 'ini kullanabilirsiniz.

## <a name="revision-descriptions"></a>Düzeltme açıklamaları

Bir düzeltme oluşturduğunuzda, kendi izleme amaçlarınız için bir açıklama ayarlayabilirsiniz. Açıklamalar API kullanıcılarınıza çalınmıyor.

Bir düzeltmeyi güncel olarak belirlediğinizde, isteğe bağlı olarak bir genel değişiklik günlüğü notunun de belirtebilirsiniz. Değişiklik günlüğü, API kullanıcılarınızın görüntülemesi için geliştirici portalına dahildir. PowerShell cmdlet 'ini kullanarak değişiklik günlüğü notunuzun üzerinde değişiklik yapabilirsiniz `Update-AzApiManagementApiRelease` .

## <a name="versions-and-revisions"></a>Sürümler ve düzeltmeler

Sürümler ve düzeltmeler farklı özelliklerdir. Her sürüm, sürümü olmayan bir API gibi birden çok düzeltmelere sahip olabilir. Sürümler veya başka bir şekilde sürümlerini kullanmadan düzeltmeler kullanabilirsiniz. Genellikle sürümler, API sürümlerini son değişikliklerle ayırmak için kullanılır, ancak düzeltmeler bir API 'de küçük ve olmayan değişiklikler için kullanılabilir.

Düzeltmede önemli değişiklikler olduğunu bulmalı veya düzeltmeyi bir beta/test sürümüne resmi olarak dönüştürmek istiyorsanız, düzeltmeden bir sürüm oluşturabilirsiniz. Azure portal kullanarak, düzeltmeler sekmesinin düzeltme bağlamı menüsünde ' düzeltmeden sürüm oluştur ' düğmesine tıklayın.
