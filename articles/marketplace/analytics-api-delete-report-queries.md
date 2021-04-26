---
title: Rapor sorguları API 'sini Sil
description: Ticari Market analizlerinin Kullanıcı tanımlı sorgularını silmek için bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584257"
---
# <a name="delete-report-queries-api"></a>Rapor sorguları API 'sini Sil

Bu API Kullanıcı tanımlı sorguları siler.

**İstek sözdizimi**

| **Yöntem** | **İstek URI'si** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**İstek üst bilgisi**

| **Üst bilgi** | **Tür** | **Açıklama** |
| --- | --- | --- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |

**Yol parametresi**

| **Parametre adı** | **Tür** | **Açıklama** |
| --- | --- | --- |
| `queryId` | string | Yalnızca bu bağımsız değişkende verilen KIMLIĞE sahip sorguların ayrıntılarını almak için filtrele |

**Sorgu parametresi**

Yok

**İstek yükü**

Yok

**Sözlük**

Yok

**Response**

Yanıt yükü JSON biçiminde aşağıdaki şekilde yapılandırılır.

Yanıt kodu: 200, 400, 401, 403, 404, 500

Yanıt yükü:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
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
| `QueryId` | Silinen sorgunun benzersiz UUID 'SI. |
| `Name` | Silinen sorgunun adı |
| `Description` | Silinen sorgunun açıklaması |
| `Query` | Silinen sorgunun rapor sorgu dizesi |
| `Type` | Kullanıcı tanımlı |
| `User` | Sorguyu oluşturan kullanıcı KIMLIĞI |
| `CreatedTime` | Sorgunun oluşturulduğu zaman |
| `ModifiedTime` | Null |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç kodu. Olası değerler 200, 400, 401, 403, 500 |
