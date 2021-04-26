---
title: İletişim Hizmetleri günlükleri
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim hizmetlerinde günlüğe kaydetme hakkında bilgi edinin
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0bf7213e5cfdc57194482c6914d4e25b9336832e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935883"
---
# <a name="communication-services-logs"></a>İletişim Hizmetleri günlükleri

Azure Iletişim Hizmetleri, Iletişim Hizmetleri çözümünüzü izlemek ve hatalarını ayıklamak için kullanabileceğiniz günlüğe kaydetme özellikleri sunar. Bu yetenekler Azure portal aracılığıyla yapılandırılabilir.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Kaynağınızın tanılama günlüklerini etkinleştirme

Bir kaynak oluşturulduğunda günlüğe kaydetme varsayılan olarak kapalıdır. Günlüğe kaydetmeyi etkinleştirmek için **izleme** bölümünün altındaki kaynak menüsünde **Tanılama ayarları** dikey penceresine gidin. Ardından **Tanılama ayarı Ekle**' ye tıklayın.

Sonra istediğiniz arşiv hedefini seçin. Şu anda, depolama hesaplarını ve arşiv hedefleri olarak Log Analytics destekliyoruz. Yakalamak istediğiniz günlüklerin türlerini seçtikten sonra, tanılama ayarlarını kaydedin.
 
Yeni ayarlar yaklaşık on dakika içinde etkili olur. Günlükler, Iletişim Hizmetleri kaynağınızın Günlükler bölmesinde yapılandırılan arşiv hedefinde görünmeye başlar.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="ACS Tanılama ayarları seçenekleri.":::

Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure Kaynak günlüklerine](../../azure-monitor/essentials/platform-logs-overview.md)genel bakış.

## <a name="resource-log-categories"></a>Kaynak günlüğü kategorileri

İletişim Hizmetleri, etkinleştirebileceğiniz üç tür günlük sunar:

* **Kullanım günlükleri** -her bir faturalandırılan hizmet teklifiyle ilişkili kullanım verilerini sağlar
* **Sohbet operasyonel Günlükler** -sohbet hizmeti ile ilgili temel bilgileri sağlar
* **SMS işletimsel Günlükler** -SMS hizmetiyle ilgili temel bilgileri sağlar
* **Kimlik doğrulama işlem günlükleri** -kimlik doğrulama hizmetiyle ilgili temel bilgileri sağlar

### <a name="usage-logs-schema"></a>Kullanım günlükleri şeması

| Özellik | Açıklama |
| -------- | ---------------|
| Timestamp | Günlüğün oluşturulduğu zaman damgası (UTC). |
| İşlem adı | Günlük kaydıyla ilişkili işlem. |
| İşlem sürümü | `api-version`OperationName BIR API kullanılarak gerçekleştirildiyse işlemle ilişkili. Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| Kategori | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. |
| Bağıntı Kimliği | Bağıntılı olayların KIMLIĞI. Birden çok tablo arasındaki bağıntılı olayları belirlemek için kullanılabilir. |
| Özellikler | Çeşitli Iletişim Hizmetleri modlarında geçerli olan diğer veriler. |
| Kayıt KIMLIĞI | Belirli bir kullanım kaydının benzersiz KIMLIĞI. |
| Kullanım Türü | Kullanım modu. (örneğin, sohbet, PSTN, NAT vb.) |
| Birim Türü | Belirli bir kullanım modu için, kullanımın dayandığı birimin türü. (örneğin, dakika, megabayt, ileti vb.). |
| Miktar | Bu kayıt için kullanılan veya tüketilen birim sayısı. |

### <a name="chat-operational-logs"></a>Sohbet işlem günlükleri

| Özellik | Açıklama |
| -------- | ---------------|
| TimeGenerated | Günlüğün oluşturulduğu zaman damgası (UTC). |
| OperationName | Günlük kaydıyla ilişkili işlem. |
| ID | Bağıntılı olayların KIMLIĞI. Birden çok tablo arasındaki bağıntılı olayları belirlemek için kullanılabilir. |
| OperationVersion | OperationName bir API kullanılarak gerçekleştirildiyse, işlemle ilişkili api sürümü. Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| Kategori | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. |
| ResultType | İşlemin durumu. |
| ResultSignature | İşlemin alt durumu. Bu işlem bir REST API çağrısına karşılık geliyorsa, bu alan karşılık gelen REST çağrısının HTTP durum kodudur. |
| ResultDescription | Bu işlemin statik metin açıklaması. |
| DurationMs | İşlem süresi (milisaniye cinsinden). |
| Callerıpaddress | İşlem, genel olarak kullanılabilir IP adresine sahip bir varlıktan gelen bir API çağrısına karşılık geliyorsa, çağıran IP adresi. |
| Level | Etkinliğin önem düzeyi. |
| URI | İsteğin URI 'SI. |
| UserId | İstek gönderenin kullanıcı KIMLIĞI. |
| ChatThreadId | İstekle ilişkili sohbet iş parçacığı KIMLIĞI. |
| ChatMessageId | İstekle ilişkili sohbet iletisi KIMLIĞI. |
| SDK türü | İstekte kullanılan SDK türü. |
| PlatformType | İstekte kullanılan platform türü. |

### <a name="sms-operational-logs"></a>SMS işletimsel günlükleri

| Özellik | Açıklama |
| -------- | ---------------|
| TimeGenerated | Günlüğün oluşturulduğu zaman damgası (UTC). |
| OperationName | Günlük kaydıyla ilişkili işlem. |
| ID | Bağıntılı olayların KIMLIĞI. Birden çok tablo arasındaki bağıntılı olayları belirlemek için kullanılabilir. |
| OperationVersion | OperationName bir API kullanılarak gerçekleştirildiyse, işlemle ilişkili api sürümü. Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| Kategori | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. |
| ResultType | İşlemin durumu. |
| ResultSignature | İşlemin alt durumu. Bu işlem bir REST API çağrısına karşılık geliyorsa, bu alan karşılık gelen REST çağrısının HTTP durum kodudur. |
| ResultDescription | Bu işlemin statik metin açıklaması. |
| DurationMs | İşlem süresi (milisaniye cinsinden). |
| Callerıpaddress | İşlem, genel olarak kullanılabilir IP adresine sahip bir varlıktan gelen bir API çağrısına karşılık geliyorsa, çağıran IP adresi. |
| Level | Etkinliğin önem düzeyi. |
| URI | İsteğin URI 'SI. |
| OutgoingMessageLength | Giden iletideki karakterlerin sayısı. |
| Incomingmessagelength | Gelen iletideki karakterlerin sayısı. |
| Teslimat Yattaları | Bu iletiyi teslim etmek için yapılan deneme sayısı. |
| PhoneNumber | SMS iletisinin gönderildiği telefon numarası. |
| SDK türü | İstekte kullanılan SDK türü. |
| PlatformType | İstekte kullanılan platform türü. |
| Yöntem | İstekte kullanılan yöntem. |

### <a name="authentication-operational-logs"></a>Kimlik doğrulama işlem günlükleri

| Özellik | Açıklama |
| -------- | ---------------|
| TimeGenerated | Günlüğün oluşturulduğu zaman damgası (UTC). |
| OperationName | Günlük kaydıyla ilişkili işlem. |
| ID | Bağıntılı olayların KIMLIĞI. Birden çok tablo arasındaki bağıntılı olayları belirlemek için kullanılabilir. |
| OperationVersion | `api-version` `operationName` Bir API kullanılarak gerçekleştirildiyse, işlemle ilişkili. Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| Kategori | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. |
| ResultType | İşlemin durumu. |
| ResultSignature | İşlemin alt durumu. Bu işlem bir REST API çağrısına karşılık geliyorsa, bu alan karşılık gelen REST çağrısının HTTP durum kodudur. |
| DurationMs | İşlem süresi (milisaniye cinsinden). |
| Callerıpaddress | İşlem, genel olarak kullanılabilir IP adresine sahip bir varlıktan gelen bir API çağrısına karşılık geliyorsa, çağıran IP adresi. |
| Level | Etkinliğin önem düzeyi. |
| URI | İsteğin URI 'SI. |
| SDK türü | İstekte kullanılan SDK türü. |
| PlatformType | İstekte kullanılan platform türü. |
| Kimlik | İşlemle ilgili Iletişim Hizmetleri kimliği. |
| Kapsamlar | Erişim belirtecinde Iletişim Hizmetleri kapsamları var. |
