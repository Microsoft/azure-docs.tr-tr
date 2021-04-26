---
title: Rapor API 'SI al
description: Iş Ortağı Merkezi 'nde zamanlanan analiz raporlarını almak için bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584212"
---
# <a name="get-report-api"></a>Rapor API 'SI al

Bu API, zamanlanmış tüm raporları alır.

**İstek sözdizimi**

| **Yöntem** | **İstek URI'si** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**İstek üst bilgisi**

| **Üst bilgi** | **Tür** | **Açıklama** |
| --- | --- | --- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |

**Yol parametresi**

Yok

**Sorgu parametresi**

| **Parametre adı** | **Gerekli** | **Tür** | **Açıklama** |
| --- | --- | --- | --- |
| `reportId` | No | string | Yalnızca `reportId` Bu bağımsız değişkende verilen raporların ayrıntılarını almak için filtrele |
| `reportName` | No | string | Yalnızca `reportName` Bu bağımsız değişkende verilen raporların ayrıntılarını almak için filtrele |
| `queryId` | No | boolean | Yanıta önceden tanımlanmış sistem sorgularını dahil et |

**Sözlük**

Yok

**Response**

Yanıt yükü JSON biçiminde aşağıdaki şekilde yapılandırılır:

Yanıt kodu: 200, 400, 401, 403, 404, 500

Yanıt yükü:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Sözlük**

Bu tabloda, yanıttaki öğelerin temel tanımları listelenmiştir.

| **Parametre** | **Açıklama** |
| --- | --- |
| `ReportId` | Oluşturulan raporun benzersiz UUID 'SI |
| `ReportName` | İstek yükünde rapora verilen ad |
| `Description` | Rapor oluşturulduğunda verilen açıklama |
| `QueryId` | Rapor oluşturulduğu sırada geçirilen sorgu KIMLIĞI |
| `Query` | Bu rapor için yürütülecek sorgu metni |
| `User` | Raporu oluşturmak için kullanılan Kullanıcı KIMLIĞI |
| `CreatedTime` | Raporun oluşturulduğu zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ModifiedTime` | Raporun son değiştirilme zamanı. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `StartTime` | Yürütmenin başlayacağı zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ReportStatus` | Rapor yürütmenin durumu. Olası değerler duraklatıldı, etkin ve etkin değildir. |
| `RecurrenceInterval` | Rapor oluşturma sırasında belirtilen yinelenme aralığı |
| `RecurrenceCount` | Rapor oluşturma sırasında belirtilen yinelenme sayısı |
| `CallbackUrl` | İstekte belirtilen geri çağırma URL 'SI |
| `Format` | Rapor dosyalarının biçimi |
