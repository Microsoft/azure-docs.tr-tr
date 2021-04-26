---
title: IoT Hub şema ve diğer bilgiler için güncelleştirmeleri cihaz güncelleştirmesine aktarma | Microsoft Docs
description: IoT Hub cihaz güncelleştirmesine güncelleştirmeler aktarılırken kullanılan şema ve diğer ilgili bilgiler (nesneler dahil).
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 989535d0bd6f514e63c7cea9e5fd71912f8fb08b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780166"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>IoT Hub şema ve diğer bilgiler için güncelleştirmeleri cihaz güncelleştirmesine aktarma
IoT Hub için bir güncelleştirmeyi cihaz güncelleştirmesine aktarmak istiyorsanız, öncelikle [kavramları](import-concepts.md) ve [nasıl yapılır Kılavuzu](import-update.md) ' nu gözden geçirdiğinizden emin olun. İçeri aktarma bildirimi oluştururken kullanılan şemanın ayrıntıları ile ilgileniyorsanız ve ilgili nesneler hakkında bilgi için aşağıya bakın.

## <a name="import-manifest-schema"></a>Bildirim şemasını içeri aktar

| Ad | Tür | Description | Kısıtlamalar |
| --------- | --------- | --------- | --------- |
| UpdateID | `UpdateId` nesne | Kimliği güncelleştirin. |
| Güncelleştirme türü | string | Güncelleştirme türü: <br/><br/> * `microsoft/apt:1` Başvuru Aracısı 'nı kullanarak paket tabanlı güncelleştirme gerçekleştirirken belirtin.<br/> * `microsoft/swupdate:1` Başvuru Aracısı 'nı kullanarak görüntü tabanlı bir güncelleştirme gerçekleştirirken belirtin.<br/> * `microsoft/simulator:1` Örnek aracı simülatörü kullanılırken belirtin.<br/> * Özel bir aracı geliştirilirken özel bir tür belirtin. | Biçim: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> En fazla 32 karakter toplamı |
| Instalınstalbu ölçüt | string | Güncelleştirmenin başarıyla uygulanıp uygulanmadığını belirleme aracı tarafından yorumlanan dize:  <br/> * Güncelleştirme türü için SWVersion **değerini** belirtin `microsoft/swupdate:1` .<br/> * `{name}-{version}` `microsoft/apt:1` Apt dosyasından ad ve sürümün alındığı güncelleştirme türünü belirtin.<br/> * Özel bir aracı geliştirilirken özel bir dize belirtin.<br/> | En fazla 64 karakter |
| Uyumluluk | `CompatibilityInfo` [Nesne](#compatibilityinfo-object) dizisi | Bu güncelleştirmeyle uyumlu cihaz uyumluluk bilgileri. | En fazla 10 öğe |
| CreatedDateTime | Tarih/saat | Güncelleştirmenin oluşturulduğu tarih ve saat. | Sınırlandırılmış ISO 8601 tarih ve saat biçimi, UTC olarak |
| ManifestVersion | string | Bildirim şeması sürümünü içeri aktarın. `2.0`Arabirim ve arabirimle uyumlu olacak şekilde belirtin `urn:azureiot:AzureDeviceUpdateCore:1` `urn:azureiot:AzureDeviceUpdateCore:4` . | Olmalıdır `2.0` |
| Dosyalar | Nesne dizisi `File` | Yük dosyalarını Güncelleştir | En fazla 5 dosya |

## <a name="updateid-object"></a>UpdateID nesnesi

| Ad | Tür | Description | Kısıtlamalar |
| --------- | --------- | --------- | --------- |
| Sağlayıcı | string | Güncelleştirme kimliğinin sağlayıcı parçası. | 1-64 karakter, alfasayısal, nokta ve tire. |
| Name | string | Güncelleştirme kimliğinin bir bölümünü adlandırın. | 1-64 karakter, alfasayısal, nokta ve tire. |
| Sürüm | sürüm | Güncelleştirme kimliğinin sürüm bölümü. | 2 ila 4 bölüm, noktayla ayrılmış sürüm numarası 0 ve 2147483647. Öndeki sıfırlar bırakılacak. |

## <a name="file-object"></a>Dosya nesnesi

| Ad | Tür | Description | Kısıtlamalar |
| --------- | --------- | --------- | --------- |
| Kısaltın | string | Dosyanın adı | Güncelleştirme içinde benzersiz olmalıdır |
| SizeInBytes | Int64 | Dosyanın bayt cinsinden boyutu. | Ayrı dosya başına en fazla 800 MB veya güncelleştirme başına 800 MB |
| Karmalar | `Hashes` nesne | Dosyanın karmasını içeren JSON nesnesi |

## <a name="compatibilityinfo-object"></a>Compatibilityınfo nesnesi

| Ad | Tür | Description | Kısıtlamalar |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Güncelleştirmenin uyumlu olduğu cihazın üreticisi. | 1-64 karakter, alfasayısal, nokta ve tire. |
| DeviceModel | string | Güncelleştirmenin uyumlu olduğu cihaz modeli. | 1-64 karakter, alfasayısal, nokta ve tire. |

## <a name="hashes-object"></a>Karma nesnesi

| Name | Gerekli | Tür | Description |
| --------- | --------- | --------- | --------- |
| SHA256 | True | string | SHA-256 algoritmasını kullanarak dosyanın Base64 ile kodlanmış karması. |

## <a name="next-steps"></a>Sonraki adımlar

[İçeri aktarma kavramları](./import-concepts.md)hakkında daha fazla bilgi edinin.

Hazır olduğunuzda Içeri aktarma işlemi adım adım yönergeler için size yol gösterecek [Içeri aktarma How-To kılavuzunu](./import-update.md)deneyin.
