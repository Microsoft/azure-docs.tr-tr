---
title: IoT Hub cihaz güncelleştirmesinin IoT Tak ve Kullan nasıl kullandığını anlayın | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesi, kablosuz güncelleştirme yeteneğine sahip olan cihazları bulup yönetmek için kullanılır.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739539"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT Hub ve IoT Tak ve Kullan cihaz güncelleştirmesi

IoT Hub cihaz güncelleştirmesi, kablosuz güncelleştirme yeteneğine sahip olan cihazları bulup yönetmek için [ıot Tak ve kullan](../iot-pnp/index.yml) kullanır. Cihaz Güncelleştirme hizmeti, PnP arabirimlerini kullanarak cihazlara ve cihazlardan Özellikler ve iletiler gönderir ve alır. IoT Hub cihaz güncelleştirmesi, IoT cihazlarının aşağıdaki arabirimleri ve model kimliğini aşağıda açıklandığı gibi uygulamasını gerektirir.

## <a name="adu-core-interface"></a>ADU çekirdek arabirimi

' Aducoreınterface ' arabirimi, cihazlara güncelleştirme eylemleri ve meta veriler göndermek ve cihazlardan güncelleştirme durumu almak için kullanılır. ' ADU Core ' arabirimi iki nesne özelliklerine bölünür.

Modelinizde beklenen bileşen adı, bu arabirimi uygularken **"azureDeviceUpdateAgent"** . [Azure IoT PnP bileşenleri hakkında daha fazla bilgi edinin](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Aracı meta verileri

Aracı meta verileri, cihaz veya cihaz güncelleştirme aracısının cihaz güncelleştirme hizmetlerine bilgi ve durum göndermek için kullandığı alanları içerir.

|Name|Şema|Yön|Açıklama|Örnek|
|----|------|---------|-----------|-----------|
|resultCode|tamsayı|cihazdan buluta|Son Güncelleştirme eyleminin sonucu hakkında bilgi içeren bir kod. , Başarı veya başarısızlık için doldurulabilir ve [http durum kodu belirtimini](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)izlemelidir.|500|
|extendedResultCode|tamsayı|cihazdan buluta|Sonuç hakkında ek bilgiler içeren bir kod. , Başarı veya başarısızlık için doldurulabilir.|0x80004005|
|state|tamsayı|cihazdan buluta|Bu, cihaz güncelleştirme aracısının geçerli durumunu gösteren bir tamsayıdır. Ayrıntılar için aşağıya bakın |Boş|
|ınstalınstalupdateıd|string|cihazdan buluta|Şu anda yüklü olan güncelleştirmenin KIMLIĞI (cihaz güncelleştirmesi aracılığıyla). Bu değer, cihaz güncelleştirmesi aracılığıyla hiçbir güncelleştirme gerçekleştirmamış bir cihaz için null olacaktır.|Null|
|`deviceProperties`|Harita|cihazdan buluta|Üreticisini ve modeli içeren özellikler kümesi.|Ayrıntılar için aşağıya bakın

#### <a name="state"></a>Durum

Cihaz güncelleştirme hizmetinden bir eylem alındıktan sonra cihaz Güncelleştirme Aracısı tarafından bildirilen durumudur. `State` Cihaz Güncelleştirme `Action` `Actions` hizmetinden cihaz güncelleştirme aracısına gönderilen (aşağıya bakın) yanıt olarak bildirilir. Cihaz Güncelleştirme hizmeti ile Cihaz Güncelleştirme Aracısı arasında akan istekler için [genel bakış iş akışına](understand-device-update.md#device-update-agent) bakın.

|Name|Değer|Açıklama|
|---------|-----|-----------|
|Boş|0|Cihaz, cihaz güncelleştirme hizmetinden bir eylem almaya hazırlanıyor. Başarılı bir güncelleştirmeden sonra durum `Idle` duruma döndürülür.|
|DownloadSucceeded|2|Başarılı bir indirme.|
|Installsucceeded|4|Başarılı bir yüklemesi.|
|Başarısız|255|Güncelleştirme sırasında bir hata oluştu.|

#### <a name="device-properties"></a>Cihaz Özellikleri

Üreticisi ve modeli içeren özellikler kümesidir.

|Name|Şema|Yön|Description|
|----|------|---------|-----------|
|üretici|string|cihazdan buluta|Tarafından bildirilen cihaz üreticisi `deviceProperties` . Bu özellik iki yerden okunurdur-' AzureDeviceUpdateCore ' arabirimi ilk olarak [yapılandırma dosyası](device-update-configuration-file.md) dosyasından ' aduc_manufacturer ' değerini okumaya çalışır.  Değer yapılandırma dosyasında doldurulmamışsa, varsayılan olarak ADUC_DEVICEPROPERTIES_MANUFACTURER için derleme zamanı tanımını raporlamak olacaktır. Bu özellik yalnızca önyükleme zamanında raporlanır.|
|model|string|cihazdan buluta|İle bildirilen cihazın cihaz modeli `deviceProperties` . Bu özellik iki yerden okunurdur. AzureDeviceUpdateCore arabirimi önce [yapılandırma dosyası](device-update-configuration-file.md) dosyasından ' aduc_model ' değerini okumaya çalışacaktır.  Değer yapılandırma dosyasında doldurulmamışsa, varsayılan olarak ADUC_DEVICEPROPERTIES_MODEL için derleme zamanı tanımını raporlamak olacaktır. Bu özellik yalnızca önyükleme zamanında raporlanır.|
|aduVer|string|cihazdan buluta|Cihazda çalışan cihaz güncelleştirme aracısının sürümü. Bu değer derlemeden yalnızca derleme zamanı ENABLE_ADU_TELEMETRY_REPORTING, 1 (true) olarak ayarlandığında okunurdur. Müşteriler, değeri 0 (false) olarak ayarlayarak sürüm raporlamayı devre dışı bırakabilirsiniz. [Cihaz Güncelleştirme Aracısı özelliklerini özelleştirme](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|string|cihazdan buluta|Cihazda çalışan teslim Iyileştirme aracısının sürümü. Değer, derleme sırasında yalnızca derleme zamanı ENABLE_ADU_TELEMETRY_REPORTING 1 (doğru) olarak ayarlandığında, derlemeden okunurdur. Müşteriler, değeri 0 (false) olarak ayarlayarak sürüm raporlamayı devre dışı bırakabilirsiniz. [Teslim iyileştirme Aracısı özelliklerini özelleştirme](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Hizmet meta verileri

Hizmet meta verileri, cihaz güncelleştirme hizmetlerinin eylem ve verileri cihaz güncelleştirme aracısına iletmek için kullandığı alanları içerir.

|Name|Şema|Yön|Description|
|----|------|---------|-----------|
|action|tamsayı|buluttan cihaza|Aracının gerçekleştirmesi gereken bir eyleme karşılık gelen bir tamsayıdır. Aşağıda listelenen değerler.|
|updateManifest|string|buluttan cihaza|Bir güncelleştirmenin içeriğini tanımlamakta kullanılır. [Içeri aktarma bildiriminden](import-update.md#create-a-device-update-import-manifest) üretildi|
|updateManifestSignature|JSON Nesnesi|buluttan cihaza|Kaynak doğrulaması için kullanılan JSON Web anahtarlarına sahip bir JSON Web Imzası (JWS).|
|Dosya URL 'leri|Harita|buluttan cihaza|' `FileHash` E eşleyin `DownloadUri` . Aracıya, hangi dosyaların indirileceğini ve dosyaların doğru şekilde indirildiğini doğrulamak için kullanılacak karmayı söyler.|

#### <a name="action"></a>Eylem

`Actions` Aşağıda cihaz güncelleştirme hizmeti tarafından belirtildiği gibi cihaz Güncelleştirme Aracısı tarafından gerçekleştirilen eylemler temsil etmektedir. Cihaz Güncelleştirme Aracısı, `State` `State` alındı işlemini (yukarıdaki bölüme bakın) rapor eder `Action` . Cihaz Güncelleştirme hizmeti ile Cihaz Güncelleştirme Aracısı arasında akan istekler için [genel bakış iş akışına](understand-device-update.md#device-update-agent) bakın.

|Name|Değer|Açıklama|
|---------|-----|-----------|
|İndir|0|Yayımlanan içeriği veya güncelleştirmeyi ve gereken diğer içerikleri indirin|
|Yükleme|1|İçeriği veya güncelleştirmeyi yükler. Genellikle bu, içerik veya güncelleştirme için yükleyicinin çağrılması anlamına gelir.|
|Uygula|2|Güncelleştirmeyi sonlandırın. Gerektiğinde sistemi yeniden başlatmayı bildirir.|
|İptal|255|Geçerli eylemi işlemeyi durdurun ve geri dönün `Idle` . , Aracıdaki aracıya geri dönmesini söylemek için de kullanılır `Failed` `Idle` .|

## <a name="device-information-interface"></a>Cihaz bilgileri arabirimi

Cihaz bilgileri arabirimi [ıot Tak ve Kullan mimarisi](../iot-pnp/overview-iot-plug-and-play.md)içinde kullanılan bir kavramdır. Aygıtın donanımı ve işletim sistemi hakkında bilgi sağlayan, bulut özelliklerine cihaz içerir. IoT Hub cihaz güncelleştirmesi; telemetri ve Tanılamalar için Deviceınformation. manufacturer ve Deviceınformation. model özelliklerini kullanır. Cihaz bilgileri arabirimi hakkında daha fazla bilgi için bu [örneğe](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)bakın.

Modelinizde beklenen bileşen adı bu arabirimi uygularken **Deviceınformation** . [Azure IoT PnP bileşenleri hakkında bilgi edinin](../iot-pnp/concepts-components.md)

|Ad|Tür|Şema|Yön|Açıklama|Örnek|
|----|----|------|---------|-----------|-----------|
|üretici|Özellik|string|cihazdan buluta|Cihaz üreticisinin şirket adı. Bu, özgün donanım üreticisi (OEM) adı ile aynı olabilir.|Contoso|
|model|Özellik|string|cihazdan buluta|Cihaz modeli adı veya KIMLIĞI.|IoT Edge cihaz|
|swVersion|Özellik|string|cihazdan buluta|Cihazınızdaki yazılımın sürümü. swVersion, belleniminin sürümü olabilir.|4.15.0-122.368|
|osName|Özellik|string|cihazdan buluta|Cihazdaki işletim sisteminin adı.|Ubuntu Server 18.04|
|processorArchitecture|Özellik|string|cihazdan buluta|Cihazdaki işlemcinin mimarisi.|ARM64|
|processorManufacturer|Özellik|string|cihazdan buluta|Cihazdaki işlemcinin üreticisinin adı.|Microsoft|
|totalStorage|Özellik|string|cihazdan buluta|Cihazdaki toplam kullanılabilir depolama alanı kilobayt cinsinden.|2048|
|Toplam bellek|Özellik|string|cihazdan buluta|Cihazdaki toplam kullanılabilir bellek (kilobayt cinsinden).|256|

## <a name="model-id"></a>Model Kimliği 

Model KIMLIĞI, akıllı cihazların IoT Tak ve Play.To ile Azure IoT uygulamalarına yeteneklerini nasıl duyurmasına ilişkin özellikleri Azure IoT uygulamalarına tanıtmak için akıllı cihaz oluşturma hakkında daha fazla bilgi edinin [ıot Tak ve kullan cihaz Geliştirici Kılavuzu ' nu](../iot-pnp/concepts-developer-guide-device.md)ziyaret edin.

IoT Hub cihaz güncelleştirmesi, IoT Tak ve Kullan akıllı cihazının, cihaz bağlantısının bir parçası olarak **"dtmı: AzureDeviceUpdate; 1"** değerine sahip BIR model kimliği bildirmesini gerektirir. [Bir model kimliğini nasıl duyurduğunu öğrenin](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).