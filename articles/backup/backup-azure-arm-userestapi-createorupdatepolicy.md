---
title: REST API kullanarak yedekleme ilkeleri oluşturma
description: Bu makalede, REST API kullanarak yedekleme ilkeleri (zamanlama ve bekletme) oluşturmayı ve yönetmeyi öğreneceksiniz.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: b6e665b5c71dc5f2e8ebc22e00e1a71237f48bfc
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173430"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>REST API kullanarak Azure Kurtarma Hizmetleri yedekleme ilkeleri oluşturma

Azure kurtarma hizmetleri Kasası için yedekleme ilkesi oluşturma adımları, [ilke REST API belgesinde](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate)özetlenmiştir. Bu belgeyi, Azure VM yedeklemesi için bir ilke oluşturmak üzere bir başvuru olarak kullanmamıza izin verin.

## <a name="backup-policy-essentials"></a>Yedekleme ilkesi temelleri

- Her kasa için bir yedekleme ilkesi oluşturulur.
- Aşağıdaki iş yüklerinin yedeklenmesi için bir yedekleme ilkesi oluşturulabilir
  - Azure VM
  - Azure VM 'de SQL
  - Azure Dosya Paylaşımı
- Bir ilke, birçok kaynağa atanabilir. Azure VM yedekleme ilkesi, birçok Azure VM 'yi korumak için kullanılabilir.
- Bir ilke iki bileşenden oluşur
  - Zamanlama: yedeklemenin ne zaman ele
  - Bekletme: her yedeklemenin ne kadar süreyle saklanması gerekir.
- Zamanlama, belirli bir zaman noktasıyla "günlük" veya "haftalık" olarak tanımlanabilir.
- Bekletme "günlük", "haftalık", "aylık", "yıllık" yedekleme noktaları için tanımlanabilir.
- "haftalık" haftanın belirli bir gününde bir yedekleme anlamına gelir, "aylık" bir yedekleme, ayın belirli bir gününde, "yıllık" ise yılın belirli bir gününde bir yedeği ifade eder.
- "Aylık", "yıllık" yedekleme noktaları için bekletme "Longtermbekletme" olarak adlandırılır.
- Bir kasa oluşturulduğunda, Azure VM yedeklemeleri için "DefaultPolicy" adlı bir ilke de oluşturulur ve Azure VM 'Leri yedeklemek için kullanılabilir.

Azure Backup ilkesi oluşturmak veya güncelleştirmek için aşağıdaki *PUT* işlemini kullanın

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

`{policyName}` ve `{vaultName}` URI 'de verilmiştir. Ek bilgiler istek gövdesinde verilmiştir.

## <a name="create-the-request-body"></a>İstek gövdesini oluşturma

Örneğin, Azure VM yedeklemesi için bir ilke oluşturmak üzere, istek gövdesinin bileşenleri aşağıda verilmiştir.

|Ad  |Gerekli  |Tür  |Açıklama  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource özellikleri        |
|etiketler     |         | Nesne        |  Kaynak etiketleri       |

İstek gövdesindeki tanımlarının tüm listesi için, [yedekleme ilkesi REST API belgesine](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate)bakın.

### <a name="example-request-body"></a>Örnek istek gövdesi

Aşağıdaki istek gövdesi, Azure VM yedeklemeleri için bir yedekleme ilkesi tanımlar.

İlke şöyle diyor:

- Her Pazartesi, Çarşamba, Salı, Pasifik standart saati 10:00 ' de haftalık bir yedekleme gerçekleştirin.
- Her Pazartesi, Çarşamba, bir haftada Perşembe için alınan yedeklemeleri koruyun.
- Her Çarşamba ve ayın üçüncü Perşembe günü iki ay boyunca gerçekleştirilen yedeklemeleri koruyun (varsa önceki bekletme koşullarını geçersiz kılar).
- Şubat ve Kasım ayının dört yıl içinde dördüncü Pazartesi ve dördüncü Perşembe üzerinde gerçekleştirilen yedeklemeleri koruyun (varsa önceki bekletme koşullarını geçersiz kılar).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Zamanlama ve bekletme için zaman biçimleri yalnızca TarihSaat ' i destekler. Yalnızca zaman biçimini desteklemez.

## <a name="responses"></a>Responses

Yedekleme ilkesi oluşturma/güncelleştirme [zaman uyumsuz bir işlemdir](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Bu işlemin Ayrıca izlenmesi gereken başka bir işlem oluşturduğu anlamına gelir.

Başka bir işlem oluşturulduğunda 202 (kabul edildi) ve ardından bu işlem tamamlandığında 200 (Tamam) iki yanıt döndürür.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|200 TAMAM     |    [Koruma PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#protectionpolicyresource)     |  Tamam       |
|202 kabul edildi     |         |     Eden    |

### <a name="example-responses"></a>Örnek yanıtlar

İlke oluşturma veya güncelleştirme için *PUT* isteğini gönderdikten sonra, ilk yanıt bir konum üst bilgisi veya Azure-Async-header ile 202 (kabul edilir) olur.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ardından, bir basit *Get* komutuyla konum üstbilgisini veya Azure-AsyncOperation üst bilgisini kullanarak elde edilen işlemi izleyin.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

İşlem tamamlandıktan sonra, yanıt gövdesinde ilke içeriğiyle 200 (Tamam) döndürür.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Bir ilke bir öğeyi korumak için zaten kullanılıyorsa, ilkedeki tüm güncelleştirmeler, ilişkili tüm öğeler için [korumayı değiştirmeye](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) neden olur.

## <a name="next-steps"></a>Sonraki adımlar

[Korumasız bir Azure VM için korumayı etkinleştirin](backup-azure-arm-userestapi-backupazurevms.md).

Azure Backup REST API 'Leri hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

- [Azure kurtarma hizmetleri sağlayıcısı REST API](/rest/api/recoveryservices/)
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)
