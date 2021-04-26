---
title: include dosyası
description: include dosyası
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401114"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Şemanın veri modeli

|Alan|Veri türü|Açıklama|
|----|----|----|
|**AlertDisplayName**|Dize|Uyarının görünen adı.|
|**AlertType**|Dize|Uyarı türü. Aynı türdeki uyarılar aynı değere sahip olmalıdır. Bu alan, uyarı örneği olmayan bir uyarı türünü temsil eden anahtarlı bir dizedir. Aynı algılama mantığı/analitik içindeki tüm uyarı örnekleri, uyarı türü için aynı değere sahip olmalıdır.|
|**Compromısedentity**|Dize|Bu uyarıyla ilgili olan kaynağın görünen adı.|
|**Açıklama**|Dize|Uyarının açıklaması.|
|**EndTimeUtc**|DateTime|Uyarıya dahil edilen son olay veya etkinliğin saati.  Alan, UTC saat dilimi bilgileri de dahil olmak üzere ıSO8601 biçimine uygun bir dize olmalıdır.|
|**Varlıklar**|IEnumerable (IEntity)|Uyarıyla ilgili varlıkların listesi. Bu liste, farklı türlerde varlıkların bir karışımını tutabilir. Varlıklar, varlıklar bölümünde tanımlanmış türlerden herhangi biri olabilir. Aşağıdaki listede yer alan varlıklar da gönderilebilir, ancak işlenebilecekleri garanti edilmez (uyarı, yeni varlık türleriyle doğrulanmayacak).|
|**ExtendedProperties**|Sözlük (dize, dize)|Sağlayıcılar, burada özel alanları dahil edebilir (isteğe bağlı olarak).|
|**Amaç**|Sabit listesi|Uyarının arkasındaki sonlandırma zinciri ile ilgili amaç. Desteklenen değerler listesi ve Azure Güvenlik Merkezi 'nin desteklenen sonlandırma zinciri amaçları hakkında açıklamalar için bkz. [amaçları](../articles/security-center/alerts-reference.md#intentions).<br/>Bu alan birden fazla değere sahip olabilir (virgülle ayrılmış olarak).|
|**Isıncident**|Bool|Bu alan, uyarının bir olay (çeşitli uyarıların bileşik gruplandırması) veya tek bir uyarı olup olmadığını belirler. Alan için varsayılan değer ' false ' (yani tek bir uyarıdır).|
|**ProcessingEndTime**|DateTime|Uyarının, uyarının tutulduğu orijinal üründeki Son Kullanıcı tarafından erişileme zamanı.|
|**ProductName**|Dize|Bu uyarıyı yayımlayan ürünün adı (Azure Güvenlik Merkezi, Azure ATP, Microsoft Defender ATP, MCAS, vb.).|
|**Düzeltme adımları**|Liste<String>|Uyarının düzeltilmesi için gerçekleştirilecek el ile eylem öğeleri.|
|**Resourcetanımlayıcılar**|Liste (kaynak tanımlayıcıları)|Bu uyarının, uyarıyı doğru ürün pozlaması grubuna (kiracı, çalışma alanı, abonelik vb.) yönlendirmek için kullanılabilecek kaynak tanımlayıcıları. Uyarı başına farklı türde birden fazla tanımlayıcı olabilir.|
|**Önem Derecesi**|Sabit listesi|Uyarının sağlayıcının bildirdiği şekilde önem derecesi. Olası değerler: bilgilendirici, düşük, orta ve yüksek.|
|**StartTimeUtc**|DateTime|Uyarıya dahil edilen ilk olay veya etkinliğin saati. Alan, UTC saat dilimi bilgileri de dahil olmak üzere ıSO8601 biçimine uygun bir dize olmalıdır.|
|**Durum**|Sabit listesi|Uyarının yaşam döngüsü durumu.<br/>Desteklenen durumlar: yeni, çözümlenmiş, kapatıldı, bilinmiyor.<br/>Desteklenen seçeneklerden başka bir değer belirten bir uyarı ' bilinmiyor ' durumuna atandı.<br/>Değer belirtmeyen bir uyarıya ' New ' durumu atanır.|
|**Systemalertıd**|Dize|Uyarı tanımlayıcısı.|
|**TimeGenerated**|DateTime|Uyarının uyarı sağlayıcısı tarafından oluşturulduğu zaman. İç uyarı sağlayıcılarına göre bildirilmezse, ürün tarafından işlenmek üzere alındığı zamanı atamayı tercih edebilirsiniz.  Alan, UTC saat dilimi bilgileri de dahil olmak üzere ıSO8601 biçimine uygun bir dize olmalıdır.|
|**SatıcıAdı**|Dize|Uyarıyı Başlatan satıcının adı.|
|||
