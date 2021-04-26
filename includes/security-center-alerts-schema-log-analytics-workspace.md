---
title: include dosyası
description: include dosyası
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "79538837"
---
### <a name="the-data-model-of-the-schema"></a>Şemanın veri modeli

|Alan|Açıklama|
|----|----|
|**AlertName**|Uyarı görünen adı|
|**AlertType**|benzersiz uyarı tanımlayıcısı|
|**Yapılandırma**|Seçim Bu uyarının güvenilirlik düzeyi (yüksek/düşük)|
|**ConfidenceScore**|Seçim Güvenlik uyarısının sayısal güvenilirlik göstergesi|
|**Açıklama**|Uyarı için açıklama metni|
|**DisplayName**|Uyarının görünen adı|
|**EndTime**|Uyarının etki bitiş saati (uyarıya katkıda bulunan son olay saati)|
|**Varlıklar**|Uyarıyla ilgili varlıkların listesi. Bu liste, farklı türlerde varlıkların bir karışımını tutabilir|
|**ExtendedLinks**|Seçim Uyarıyla ilgili tüm bağlantılar için bir paket. Bu paket, farklı türlerde bağlantıların bir karışımını tutabilir|
|**ExtendedProperties**|Uyarıyla ilgili ek alanların bir paketi|
|**Isıncident**|Uyarının bir olay mu yoksa düzenli bir uyarı mı olduğunu belirler. Olay, birden çok uyarıyı tek bir güvenlik olayında toplayan bir güvenlik uyarısıdır|
|**ProcessingEndTime**|Uyarının oluşturulduğu UTC zaman damgası|
|**ProductComponentName**|Seçim Uyarının oluşturulduğu ürün içindeki bir bileşen adı.|
|**ProductName**|sabit (' Azure Güvenlik Merkezi ')|
|**Adı**|kullanılmayan|
|**Düzeltme adımları**|Güvenlik tehdidi düzeltileceği için gerçekleştirilecek el ile eylem öğeleri|
|**ResourceId**|Etkilenen kaynağın tam tanımlayıcısı|
|**Önem Derecesi**|Uyarı önem derecesi (yüksek/orta/düşük/bilgilendirici)|
|**SourceComputerId**|Etkilenen sunucu için benzersiz bir GUID (uyarı sunucuda oluşturulduysa)|
|**SourceSystem**|kullanılmayan|
|**StartTime**|Uyarının etki başlangıç saati (uyarıya katkıda bulunan ilk olayın zamanı)|
|**Systemalertıd**|Bu güvenlik uyarısı örneğinin benzersiz tanımlayıcısı|
|**Değerine**|Taranan kaynağın bulunduğu aboneliğin üst Azure Active Directory kiracısı tanımlayıcısı|
|**TimeGenerated**|Değerlendirmenin gerçekleştiği UTC zaman damgası (Güvenlik Merkezi 'nin Tarama süresi) (DiscoveredTimeUTC ile aynı)|
|**Tür**|sabit (' SecurityAlert ')|
|**SatıcıAdı**|Uyarıyı sağlayan satıcının adı (ör. ' Microsoft ')|
|**Vendororiginalıd**|kullanılmayan|
|**WorkspaceResourceGroup**|bir VM, sunucu, sanal makine ölçek kümesi veya bir çalışma alanına rapor veren App Service örneği üzerinde uyarının oluşturulması durumunda, o çalışma alanı kaynak grubu adını içerir|
|**WorkspaceSubscriptionId**|bir VM, sunucu, sanal makine ölçek kümesi veya bir çalışma alanına rapor veren App Service örneği üzerinde uyarının oluşturulması durumunda bu çalışma alanı SubscriptionID 'si bulunur|
|||
