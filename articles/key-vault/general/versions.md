---
title: Azure Key Vault sürümleri
description: Azure Key Vault çeşitli sürümleri
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792387"
---
# <a name="key-vault-versions"></a>Key Vault sürümleri

Azure Key Vault yenilikleri aşağıda verilmiştir. [Azure updates Key Vault kanalında](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)yeni özellikler ve geliştirmeler de duyurulur.

## <a name="june-2020"></a>Haziran 2020

Key Vault için Azure Izleyici artık önizlemededir.  Azure Izleyici, Key Vault isteklerinizin, performanlarınızın ve gecikmelerinden oluşan Birleşik bir görünüm sunarak anahtar kasalarınızın kapsamlı bir şekilde izlenmesini sağlar. Daha fazla bilgi için bkz [. Key Vault Için Azure izleyici (Önizleme).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Mayıs 2020

"Kendi anahtarını getir" (BYOK), artık genel kullanıma sunuldu. Key Vault [Azure Key Vault bYok belirtimine](../keys/byok-specification.md)BAKıN ve [HSM korumalı anahtarları Key Vault (bYok) öğesine nasıl içeri aktaracağınızı](../keys/hsm-protected-keys-byok.md)öğrenin.

## <a name="march-2020"></a>Mart 2020

Özel uç noktalar artık önizlemede kullanılabilir. Azure özel bağlantı hizmeti, sanal ağınızdaki özel bir uç nokta üzerinden Azure Key Vault ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişmenizi sağlar.  [Key Vault Azure özel bağlantısı Ile tümleştirmeyi](private-link-service.md)öğrenin.

## <a name="2019"></a>2019

- Yeni nesil Azure Key Vault SDK 'Ları sürümü. Kullanımları örnekleri için bkz. [Python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)ve [Node.js](../secrets/quick-create-node.md) için Azure Key Vault gizli hızlı başlangıç
- Anahtar Kasası sertifikalarını yönetmek için yeni Azure ilkeleri. [Key Vault Için Azure ilkesi yerleşik tanımlarına](../policy-reference.md)bakın.
- Azure Key Vault sanal makine uzantısı artık genel olarak kullanılabilir.  Bkz. Windows için [Linux için sanal makine uzantısı](../../virtual-machines/extensions/key-vault-linux.md) ve [Key Vault sanal makine uzantısı](../../virtual-machines/extensions/key-vault-windows.md)Key Vault.
- Azure Key Vault için olay odaklı gizli dizi yönetimi artık Azure Event Grid sunulmaktadır. Daha fazla bilgi için bkz. [Azure Key Vault olaylar için Event Grid şeması] (.. /.. /Event-Grid/Event-Schema-Key-Vault.exe) ve [Azure Event Grid ile Anahtar Kasası bildirimlerini nasıl alabileceğinizi ve yanıtlayacağınızı](event-grid-tutorial.md)öğrenin.

## <a name="2018"></a>2018

Bu yıldan yayınlanan yeni özellikler ve tümleştirmeler:

- Azure Işlevleri ile tümleştirme. Anahtar Kasası işlemlerine yönelik [Azure işlevleri](../../azure-functions/index.yml) ile ilgili örnek bir senaryo için bkz. [gizli dizi döndürmesini otomatikleştirme](../secrets/tutorial-rotation.md). 
- [Azure Databricks Ile tümleştirme](/azure/databricks/scenarios/store-secrets-azure-key-vault). Bu, Azure Databricks artık iki tür gizli kapsam desteklemektedir: Azure Key Vault-desteklenen ve Databricks-desteklenen. Daha fazla bilgi için bkz. [Azure Key Vault ile desteklenen gizli dizi kapsamı oluşturma](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault Için sanal ağ hizmeti uç noktaları](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Bu yıl yayımlanan yeni özellikler:

- Yönetilen depolama hesabı anahtarları. Depolama hesabı anahtarları özelliği, Azure depolama ile daha kolay tümleştirme ekledi. Daha fazla bilgi için bkz. genel bakış konusu, [yönetilen depolama hesabı anahtarlarına genel bakış](../secrets/overview-storage-keys.md).
- Geçici silme. Geçici silme özelliği, Anahtar kasaları ve Anahtar Kasası nesnelerinizin veri korumasını geliştirir. Daha fazla bilgi için bkz. genel bakış konusu, [geçici silme genel bakış](./soft-delete-overview.md).

## <a name="2015"></a>2015

Bu yıl yayımlanan yeni özellikler:
- Sertifika yönetimi. 26 Eylül 2016 tarihinde GA sürüm 2015-06-01 ' e bir özellik olarak eklenmiştir.

Genel kullanılabilirlik (sürüm 2015-06-01), 24 Haziran 2015 tarihinde duyuruldu. Bu sürümde aşağıdaki değişiklikler yapılmıştır: 
- Anahtar "Use" alanını silme kaldırıldı.
- Anahtar "kullanım" alanı kaldırılmış şekilde bilgi alın.
- "Use" alanı kaldırılmış bir anahtarı içeri aktarın.
- Anahtar "kullanım" alanı kaldırılmış şekilde geri yükleyin.     
- RSA algoritmaları için "RSA_OAEP", "RSA-OAEP" olarak değiştirildi. [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-certificates.md)bilgi için bkz..    
 
İkinci önizleme sürümü (sürüm 2015-02-01-Önizleme), 20 Nisan 2015 ' de duyuruldu. Daha fazla bilgi için bkz. [REST API Update](/archive/blogs/kv/rest-api-update) blog gönderisi. Aşağıdaki görevler güncelleştirildi:
 
- Anahtara kasaya eklenen ve işleme için bir sayfalama desteği olan anahtarları listeleyin.
- Anahtarın sürümlerini listelemek için anahtar ekleme işleminin sürümlerini listeleyin.  
- Kasalardan oluşan bir sayfalama desteğiyle gizli dizileri listeleyin.
- Gizli dizi sürümlerini listelemek için gizli-ekleme işleminin sürümlerini listeleyin.  
- Tüm işlemler-özniteliklere oluşturulan/güncellenen zaman damgaları eklendi.  
- Gizli dizileri için gizli bir Içerik türü oluşturun.
- İsteğe bağlı bilgiler olarak anahtar eklenmiş etiketler oluşturun.
- İsteğe bağlı bilgiler olarak gizli eklenen Etiketler oluşturun.
- Anahtar eklenmiş bir etiketi isteğe bağlı bilgiler olarak güncelleştirin.
- Gizli eklenen bir etiketi isteğe bağlı bilgiler olarak güncelleştirin.
- 10 K ile 25 K bayt arasındaki gizli dizileri için maksimum boyut değişti. [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-certificates.md)bilgi için bkz..    
 
## <a name="2014"></a>2014
 
İlk önizleme sürümü (sürüm 2014-12-08-önizleme), 8 Ocak 2015 tarihinde duyuruldu.  
 
## <a name="next-steps"></a>Sonraki adımlar

- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-certificates.md)
- [Anahtarlar](../keys/index.yml)
- [Gizli Diziler](../secrets/index.yml)
- [Sertifikalar](../certificates/index.yml)