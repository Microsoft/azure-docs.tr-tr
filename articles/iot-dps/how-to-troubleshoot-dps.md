---
title: Azure IoT Hub DPS ile bağlantıyı Tanıla ve sorunlarını giderme
description: Azure IoT Hub cihaz sağlama hizmeti (DPS) için cihaz bağlantısıyla sık karşılaşılan hataları tanılamayı ve sorun gidermeyi öğrenin
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: fd7d4407f7ea552409599946d1c13a7e9d1d7268
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062548"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub cihaz sağlama hizmeti ile sorun giderme

Kanıtlama hataları, kayıt hataları vb. gibi birçok olası başarısızlık noktası olduğundan, IoT cihazlarına yönelik bağlantı sorunlarının giderilmesi zor olabilir. Bu makalede, [Azure izleyici](../azure-monitor/overview.md)aracılığıyla cihaz bağlantısı sorunlarını algılamaya ve gidermeye yönelik yönergeler sunulmaktadır.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Ölçümleri görüntülemek ve uyarıları ayarlamak için Azure Izleyici 'yi kullanma

Aşağıdaki yordamda IoT Hub cihaz sağlama hizmeti ölçümünde uyarının nasıl görüntüleneceği ve ayarlanacağı açıklanır. 

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. IoT Hub cihaz sağlama hizmetinize gidin.

3. **Ölçümler**’i seçin.

4. İstediğiniz ölçümü seçin. 
   <br />Şu anda DPS için üç ölçüm vardır:

    | Ölçüm Adı | Description |
    |-------|------------|
    | Kanıtlama denemeleri | Cihaz sağlama hizmeti ile kimlik doğrulamaya çalıştı cihaz sayısı|
    | Kayıt denemeleri | Başarılı kimlik doğrulamasından sonra IoT Hub kaydolmaya çalışılan cihazların sayısı|
    | Cihaz atandı | IoT Hub başarıyla atanan cihazların sayısı|

5. Ölçümün görsel görünümünü oluşturmak için istenen toplama yöntemini seçin. 

6. Ölçüm uyarısı ayarlamak için, ölçüm dikey penceresinin sağ üst köşesindeki **Yeni uyarı kuralları** ' nı seçin, benzer şekilde **Uyarı** dikey penceresine gidebilir ve **Yeni uyarı kuralları**' nı seçebilirsiniz.

7. **Koşul Ekle**' yi seçin, ardından komut istemlerini izleyerek istediğiniz ölçüm ve eşiği seçin.

Daha fazla bilgi için bkz. [Azure izleyici 'de uyarılar](../azure-monitor/alerts/alerts-overview.md).

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Hataları görüntülemek ve çözmek için log analitik kullanma

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. IoT Hub 'ınıza gidin.

3. **Tanılama ayarları**' nı seçin.

4. **Tanılamayı aç '** ı seçin.

5. İstenen günlüklerin toplanmasını sağlar.

    | Günlük Adı | Description |
    |-------|------------|
    | DeviceOperations Işlemleri | Cihaz bağlantı olaylarıyla ilgili Günlükler |
    | ServiceOperations | Service SDK kullanımıyla ilgili olay günlükleri (ör. kayıt grupları oluşturma veya güncelleştirme)|

6. **Log Analytics gönder** ' i açın ([bkz. fiyatlandırma](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Cihaz sağlama hizmeti kaynağı altındaki Azure portal **Günlükler** sekmesine gidin.

8. Son olayları görüntülemek için **Çalıştır** ' a tıklayın.

9. Sonuçlar varsa, `OperationName` `ResultType` `ResultSignature` `ResultDescription` hata hakkında daha fazla ayrıntı almak için,, ve (hata iletisi) bölümüne bakın.


## <a name="common-error-codes"></a>Genel hata kodları
Sık karşılaşılan hataları anlamak ve çözmek için bu tabloyu kullanın.

| Hata Kodu| Description | HTTP durum kodu |
|-------|------------|------------|
| 400 | İsteğin gövdesi geçerli değil; Örneğin, ayrıştırılamıyor veya nesne doğrulanamıyor.| 400 Hatalı biçim |
| 401 | Yetkilendirme belirteci doğrulanamıyor; Örneğin, zaman aşımına uğradı veya isteğin URI 'SI için uygulanmaz. Bu hata kodu, TPM kanıtlama akışının bir parçası olarak cihazlara de döndürülür. | 401 Yetkisiz|
| 404 | Cihaz sağlama hizmeti örneği veya bir kaynak (örn. bir kayıt) yok. |404 Bulunamadı |
| 412 | İstekteki ETag, RFC7232 başına mevcut kaynağın ETag 'i ile eşleşmiyor. | 412 Önkoşul başarısız oldu |
| 429 | İşlemler, hizmet tarafından kısıtlanıyor. Belirli hizmet limitleri için bkz. [cihaz sağlama hizmeti sınırlarını IoT Hub](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | 429 çok fazla istek |
| 500 | Bir iç hata oluştu. | 500 İç Sunucu Hatası|