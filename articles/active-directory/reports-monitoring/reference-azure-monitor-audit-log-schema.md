---
title: Azure Izleyici 'de Azure Active Directory Denetim günlüğü şemasını yorumlama | Microsoft Docs
description: Azure Izleyici 'de kullanılmak üzere Azure AD denetim günlüğü şemasını açıkla
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f75af14e388626a9ebbb54d43079f30dcfdd98a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987951"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Azure Izleyici 'de Azure AD denetim günlükleri şemasını yorumlama (Önizleme)

Bu makalede, Azure Izleyici 'de Azure Active Directory (Azure AD) denetim günlüğü şeması açıklanmaktadır. Her bir günlük girişi, aşağıdaki iki örnekte gösterildiği gibi, metin olarak depolanır ve JSON blobu olarak biçimlendirilir: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Alan ve özellik açıklamaları

| Alan adı | Açıklama |
|------------|-------------|
| time       | Tarih ve saat (UTC). |
| operationName | İşlemin adı. |
| operationVersion | İstemci tarafından istenen REST API sürümü. |
| category | Şu anda, *Denetim* desteklenen tek değerdir. |
| tenantId | Günlüklerle ilişkili kiracı GUID 'SI. |
| resultType | İşlemin sonucu. Sonuç, *başarılı* veya *başarısız*olabilir. |
| resultSignature |  Bu alan eşlenmeyecek ve güvenle yoksayabilirsiniz. | 
| resultDescription | Sonucun ek açıklaması (kullanılabiliyorsa). | 
| durationMs |  Bu alan eşlenmeyecek ve güvenle yoksayabilirsiniz. |
| callerIpAddress | İsteği yapan istemcinin IP adresi. | 
| correlationId | İstemci tarafından geçirilen isteğe bağlı bir GUID. İstemci tarafı işlemlerini sunucu tarafı işlemleriyle ilişkilendirmenize yardımcı olabilir ve hizmetleri kapsayan günlükleri izlerken yararlıdır. |
| identity | İsteği yaptığınızda sunulan belirtecin kimliği. Kimlik bir kullanıcı hesabı, sistem hesabı veya hizmet sorumlusu olabilir. |
| level | İleti türü. Denetim günlükleri için, düzey her zaman *bilgilendirme amaçlıdır*. |
| location | Veri merkezinin konumu. |
| properties | Bir denetim günlüğü ile ilişkili desteklenen özellikleri listeler. Daha fazla bilgi için sonraki tabloya bakın. | 

<br>

| Özellik adı | Açıklama |
|---------------|-------------|
| AuditEventCategory | Denetim olayının türü. *Kullanıcı yönetimi*, *uygulama yönetimi*veya başka bir tür olabilir.|
| Kimlik türü | Tür *uygulama* veya *Kullanıcı*olabilir. |
| İşlem Türü | Tür *ekleme*, *güncelleştirme*, *silme*olabilir. ya da *diğer*. |
| Hedef Kaynak Türü | İşlemin gerçekleştirildiği hedef kaynak türünü belirtir. Tür *uygulama*, *Kullanıcı*, *rol*, *ilke* olabilir | 
| Hedef kaynak adı | Hedef kaynağın adı. Bir uygulama adı, rol adı, Kullanıcı asıl adı veya hizmet asıl adı olabilir. |
| additionalTargets | Belirli işlemler için ek özellikleri listeler. Örneğin, bir güncelleştirme işlemi için, eski değerler ve yeni değerler *Targetupdatedproperties*altında listelenir. | 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici oturum açma günlükleri şemasını yorumlama](reference-azure-monitor-sign-ins-log-schema.md)
* [Azure tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Sık sorulan sorular ve bilinen sorunlar](concept-activity-logs-azure-monitor.md#frequently-asked-questions)