---
title: Standart Sözleşme | Mavisi
description: Azure Market ve AppSource 'ta standart sözleşme
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819489"
---
# <a name="standard-contract"></a>Standart Sözleşme

Müşteriler için satın alma sürecini basitleştirmek ve yazılım satıcılarının yasal karmaşıklığını azaltmak için, Microsoft, Market 'teki bir işlemin kolaylaştırılmasına yardımcı olmak üzere standart bir sözleşme şablonu sunmaktadır. Azure Market yayımcıları, özel hüküm ve koşulları ortadan kaldırmaktansa, müşterilerine yalnızca bir kez ve kabul etmesi gereken standart sözleşme kapsamında yazılım sunmayı tercih edebilir. Standart Sözleşme şurada bulunabilir: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Bir teklifin hüküm ve koşulları, Bulut İş Ortağı Portalı bir teklif oluşturulurken Market sekmesinde tanımlanmıştır. Standart Sözleşme seçeneği, ayarı Evet olarak değiştirilerek etkinleştirilir.

![Standart Sözleşme seçeneğini etkinleştirme](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Standart sözleşmeyi kullanmayı seçerseniz, [bulut çözümü sağlayıcısı](./cloud-solution-providers.md) kanalı için ayrı hüküm ve koşullar hala gereklidir.

## <a name="standard-contract-amendments"></a>Standart Sözleşme düzeltme açıklamaları

Standart Sözleşme, yayımcıların basitlik için standart sözleşmeyi seçmesini ve ürün ya da işletmelerinin Özelleştirilmiş şartlarını seçmesine izin verir.  Müşterilerin, Microsoft standart sözleşmesini gözden geçirmiş ve kabul etmiş olmaları durumunda yalnızca sözleşmenin değişiklik yapması gerekir.

Azure Market yayımcılarına yönelik iki çeşit değişiklik vardır:

* Evrensel değişiklik: Bu değişiklik, tüm müşteriler için standart sözleşmeye evrensel olarak uygulanır. Evrensel değişiklik, satın alma akışındaki ürünün her müşterisi için gösterilir.

![Evrensel değişiklik 'Lar](media/marketplace-publishers-guide/universal-amendaments.png)

* Özel değişiklik açıklamaları: Azure Marketi, kiracılara hedeflenmiş özel değişiklik için bir sağlama de içerir. Bunlar, yalnızca belirli müşterileri hedefleyen standart sözleşmeye özel olarak değişiklik görürler. Yayımcılar hedeflemek istedikleri kiracıyı seçebilirler. Bu kiracının müşterileri, ürünü standart sözleşme ve hedeflenen değişiklik DTU 'ları altında satın alabilir.

![Özel değişiklik açıklamaları](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Özel değişiklik ile hedeflenen müşteriler Ayrıca satın alma sırasında standart koşullara evrensel değişiklik de sağlar.

>[!Note]
>Aşağıdaki teklif türleri standart sözleşme değişiklik türlerini destekler: Azure uygulamaları (çözüm şablonları ve yönetilen uygulamalar), sanal makineler, kapsayıcılar, kapsayıcı uygulamaları.

### <a name="customer-experience"></a>Müşteri deneyimi

Azure portal satın alma işlemi sırasında, müşteriler ürünle ilişkili koşulları Microsoft standart sözleşmesi ve değişiklik DTU 'ları olarak görebilir.

![Azure portal müşteri deneyimi.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Müşteriler bir teklif şartlarını almak ve kabul etmek için `Get-AzureRmMarketplaceTerms` kullanabilir. Standart Sözleşme ve ilişkili değişiklik, cmdlet 'in çıkışında döndürülür.

---
