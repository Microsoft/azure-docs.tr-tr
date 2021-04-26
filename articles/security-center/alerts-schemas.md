---
title: Azure Güvenlik Merkezi uyarıları şemaları
description: Bu makalede, Azure Güvenlik Merkezi tarafından güvenlik uyarıları için kullanılan farklı şemalar açıklanmaktadır.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558007"
---
# <a name="security-alerts-schemas"></a>Güvenlik uyarıları şemaları

Aboneliğiniz Azure Defender 'ı etkinleştirmişse Güvenlik Merkezi, kaynakları için tehditler algıladığında güvenlik uyarıları alırsınız.

Bu güvenlik uyarılarını Azure Güvenlik Merkezi 'nin **tehdit koruması** sayfalarında veya gibi dış araçlarla görüntüleyebilirsiniz:

- [Azure Sentinel](../sentinel/index.yml) -Microsoft 'un bulutu-NATIVE SIEM. Sentinel Bağlayıcısı, Azure Güvenlik Merkezi 'ndeki uyarıları alır ve Azure Sentinel için [Log Analytics çalışma alanına](../azure-monitor/logs/quick-create-workspace.md) gönderir.
- Üçüncü taraf Sıems- [Azure Event Hubs](../event-hubs/index.yml)veri gönderme. Ardından, Olay Hub 'ınızı verilerinizi bir üçüncü taraf SıEM ile tümleştirin. [SıEM, SOAR veya BT hizmet yönetimi çözümüne akış uyarıları](export-to-siem.md)hakkında daha fazla bilgi edinin.
- [REST API](/rest/api/securitycenter/) -uyarılara erişmek için REST API kullanıyorsanız, [çevrimiçi uyarılar API 'si belgelerine](/rest/api/securitycenter/alerts)bakın.

Uyarıları tüketmek için herhangi bir programlama yöntemi kullanıyorsanız, sizin için uygun olan alanları bulmak için doğru şemaya ihtiyacınız olacaktır. Ayrıca, bir olay hub 'ına veriyorsanız veya genel HTTP bağlayıcılarıyla Iş akışı Otomasyonu tetiklemeye çalışıyorsanız, JSON nesnelerini doğru şekilde ayrıştırmak için şemaları kullanın.

>[!IMPORTANT]
> Şema bu senaryoların her biri için biraz farklıdır, bu nedenle aşağıdaki ilgili sekmeyi seçtiğinizden emin olun.


## <a name="the-schemas"></a>Şemalar 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Olay Hub 'ına iş akışı otomasyonu ve sürekli dışarı aktarma](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Logic Apps, Olay Hub 'ına ve üçüncü taraf Sıems 'ye gönderilen uyarılar için örnek JSON

Aşağıda, iletilen uyarı olaylarının şemasını bulacaksınız:

- Güvenlik Merkezi 'nin iş akışı Otomasyonu 'nda yapılandırılmış Azure Logic App örnekleri
- Güvenlik Merkezi 'nin sürekli dışarı aktarma özelliğini kullanan Azure Olay Hub 'ı

İş akışı Otomasyonu özelliği hakkında daha fazla bilgi için bkz. [Güvenlik Merkezi tetikleyicilerine yanıtları otomatikleştirme](workflow-automation.md).

Sürekli dışa aktarma hakkında daha fazla bilgi için bkz. [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel ve Log Analytics çalışma alanları](#tab/schema-sentinel)

Sentinel Bağlayıcısı, Azure Güvenlik Merkezi 'ndeki uyarıları alır ve Azure Sentinel için Log Analytics çalışma alanına gönderir. 

Güvenlik Merkezi uyarılarını kullanarak bir Sentinel Case veya olay oluşturmak için aşağıda gösterilen uyarıların şemasına ihtiyacınız vardır. 

Azure Sentinel hakkında daha fazla bilgi için [belgelerine](../sentinel/index.yml)bakın.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure Etkinlik Günlüğü](#tab/schema-activitylog)

Azure Güvenlik Merkezi, Azure etkinlik günlüğünde oluşturulan güvenlik uyarılarını olaylar olarak denetler.

Uyarı etkinleştir olayını gösterildiği gibi arayarak etkinlik günlüğündeki güvenlik uyarıları olaylarını görüntüleyebilirsiniz:

[![Uyarı etkinleştir olayının etkinlik günlüğünde aranıyor](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Azure etkinlik günlüğüne gönderilen uyarılar için örnek JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Şemanın veri modeli

|Alan|Açıklama|
|----|----|
|**lardan**|Sabit, "Işlem"|
|**ID**|Azure Güvenlik Merkezi uyarı KIMLIĞI|
|**açıklaması**|Uyarının açıklaması|
|**Eventdataıd**|Bkz. CorrelationId|
|**eventName**|Value ve localizedValue alt alanları, uyarı görünen adını içerir|
|**alan**|Value ve localizedValue alt alanları sabittir-"güvenlik"|
|**eventTimestamp**|Uyarının oluşturulduğu zamana ilişkin UTC zaman damgası|
|**id**|Tam olarak nitelenmiş uyarı KIMLIĞI|
|**düzey**|Sabit, "bilgilendirme"|
|**operationId**|Bkz. CorrelationId|
|**operationName**|Değer alanı sabittir-"Microsoft. Security/Locations/Alerts/Activate/Action" ve yerelleştirilmiş değer "uyarıyı etkinleştir" (büyük olasılıkla Kullanıcı yerel ayarı için yerelleştirilmiş olabilir) olacaktır|
|**resourceGroupName**|Kaynak grubu adını dahil eder|
|**resourceProviderName**|Value ve localizedValue alt alanları sabittir-"Microsoft. Security"|
|**Kaynak**|Value ve localizedValue alt alanları sabittir-"Microsoft. Security/Locations/Alerts"|
|**RESOURCEID**|Tam Azure Kaynak KIMLIĞI|
|**durumlarına**|Value ve localizedValue alt alanları sabittir-"etkin"|
|**Dosya**|Value ve localizedValue alt alanları boş|
|**submissionTimestamp**|Etkinlik günlüğüne olay gönderimi UTC zaman damgası|
|**subscriptionId**|Güvenliği aşılmış kaynağın abonelik KIMLIĞI|
|**özelliklerinin**|Uyarıyla ilgili ek özelliklerin JSON paketi. Bunlar bir uyarıdan diğerine değişebilir, ancak aşağıdaki alanlar tüm uyarılarda görünür:<br>-önem derecesi: saldırının önem derecesi<br>-Compromısedentity: güvenliği aşılmış kaynağın adı<br>-Düzeltmelere Ationsteps: gerçekleştirilecek düzeltme adımları dizisi<br>-Amaç: uyarının Kill zinciri hedefi. Olası amaçlar, [amaçları tablosunda](alerts-reference.md#intentions) belgelenmiştir|
|**relatedEvents**|Sabit boş dizi|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph, Microsoft 365 veri ve zeka ağ geçidindir. Microsoft 365, Windows 10 ve Enterprise Mobility + Security çok fazla miktarda veriye erişmek için kullanabileceğiniz Birleşik bir programlama modeli sağlar. Milyonlarca kullanıcıyla etkileşime geçen kuruluşlar ve tüketiciler için uygulama derlemek üzere Microsoft Graph içindeki çok sayıda veriyi kullanın.

MS Graph 'e gönderilen güvenlik uyarıları için şema ve JSON temsili [Microsoft Graph belgelerinde](/graph/api/resources/alert)bulunabilir.

---


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Güvenlik Merkezi 'nin tehdit koruması araçlarının güvenlik uyarısı bilgilerini gönderirken kullandığı şemalar açıklanmaktadır.

Dışındaki güvenlik merkezi 'nden güvenlik uyarılarına erişme yolları hakkında daha fazla bilgi için aşağıdaki sayfalara bakın:

- [Azure Sentinel](../sentinel/index.yml) -Microsoft 'un bulutu-NATIVE SIEM
- [Azure Event Hubs](../event-hubs/index.yml) -Microsoft 'un tam olarak yönetilen, gerçek zamanlı veri alma hizmeti
- [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)
- [Log Analytics çalışma alanları](../azure-monitor/logs/quick-create-workspace.md) -Azure izleyici, günlük verilerini, veri ve yapılandırma bilgilerini içeren bir kapsayıcı olan bir Log Analytics çalışma alanında depolar