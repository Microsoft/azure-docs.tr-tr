---
title: Azure kanıtlama denetim günlükleri
description: Azure kanıtlama için toplanan tüm denetim günlüklerini açıklar
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95759025"
---
# <a name="audit-logs-for-azure-attestation"></a>Azure kanıtlama için denetim günlükleri

Denetim günlükleri, zaman içinde gerçekleşen ayrı olayların güvenli, değişmez ve zaman damgalarına ait kayıtlardır. Bu Günlükler, kanıtlama örneğinizin işlevselliğini etkileyebilecek önemli olayları yakalar.

Azure kanıtlama, kanıtlama örneklerini ve bunlarla ilişkili ilkeleri yönetir. Örnek Yönetimi ve ilke değişiklikleriyle ilişkili eylemler denetlenir ve günlüğe kaydedilir.

Bu makale, günlüğe kaydedilen olaylar, toplanan bilgiler ve bu günlüklerin konumu hakkında bilgiler içerir.

## <a name="about-audit-logs"></a>Denetim günlükleri hakkında

Azure kanıtlama, kanıtlama gerçekleştirilme yöntemini etkileyen olaylara yönelik denetim günlükleri oluşturmak için kodu kullanır. Bu tipik olarak, kanıtlama Örneğinizde ilke değişikliklerinin yanı sıra bazı yönetim eylemlerine da nasıl veya ne zaman değişiklik yapıldığını da yavaşlatır.

### <a name="auditable-events"></a>Denetlenebilir olaylar
Topladığımız denetim günlüklerinden bazıları şunlardır:

|     Olay/API                              |     Olay Açıklaması                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Örnek Oluştur                        |     Bir kanıtlama hizmetinin yeni bir örneğini oluşturur. |
|     Örneği yok et                       |     Kanıtlama hizmeti örneğini yok eder. |
|     Ilke sertifikası Ekle                 |     Geçerli ilke yönetimi sertifikaları kümesine bir sertifika eklenmesi. |
|     Ilke sertifikasını kaldır              |     Geçerli ilke yönetimi sertifikaları kümesinden bir sertifikayı kaldırın. |
|     Geçerli Ilkeyi ayarla                     |     Belirli bir t türü için kanıtlama ilkesini ayarlar. |
|     Kanıtlama Ilkesini Sıfırla               |     Belirli bir t türü için kanıtlama ilkesini sıfırlar. |
|     Ilkeyi güncelleştirmeye hazırla               |     Belirli bir t türü için kanıtlama ilkesini güncelleştirmeye hazırlanın. |
|     Olağanüstü durumdan sonra kiracıları yeniden doldurma       |     Kanıtlama hizmeti 'nin bu örneğindeki tüm kanıtlama kiracılarını yeniden al. Bu, yalnızca kanıtlama hizmeti yöneticileri tarafından gerçekleştirilebilir. |

### <a name="collected--information"></a>Toplanan bilgiler
Azure kanıtlama, bu olayların her biri için aşağıdaki bilgileri toplar:

- İşlem adı
- İşlem başarısı
- İşlem çağıranı, aşağıdakilerden herhangi biri olabilir:
    - Azure AD UPN
    - Nesne kimliği
    - Sertifika
    - Azure AD Kiracı Kimliği
- İşlem hedefi, aşağıdakilerden herhangi biri olabilir:
    - Ortam
    - Hizmet bölgesi
    - Hizmet rolü
    - Hizmet rolü örneği
    - Kaynak kimliği
    - Kaynak bölgesi

### <a name="sample-audit-log"></a>Örnek denetim günlüğü

Denetim günlükleri JSON biçiminde sağlanır. Denetim günlüğünün nasıl görünebileceklerini aşağıda görebilirsiniz.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Denetim günlüklerine erişim

Bu Günlükler Azure 'da depolanır ve doğrudan erişilemez. Bu günlüklere erişmeniz gerekiyorsa bir destek bileti dosyası yapın. Daha fazla bilgi için bkz. [iletişim Microsoft desteği](https://azure.microsoft.com/support/options/). 

Destek bileti dosyalandığında Microsoft bu günlüklere erişmenizi sağlar.
