---
title: Rapor sorguları al API 'SI
description: Ticari Market Analytics raporlarında kullanıma sunulan tüm sorguları almak için bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584229"
---
# <a name="get-report-queries-api"></a>Rapor sorguları al API 'SI

Rapor sorgularını al API 'SI, raporlarda kullanıma sunulan tüm sorguları alır. Tüm sistem ve Kullanıcı tanımlı sorguları varsayılan olarak alır.

**İstek sözdizimi**

| **Yöntem** | **İstek URI'si** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**İstek üst bilgisi**

| **Üst bilgi** | **Tür** | **Açıklama** |
| --- | --- | --- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |
||||

**Yol parametresi**

Yok

**Sorgu parametresi**

| **Parametre adı** | **Tür** | **Gerekli** | **Açıklama** |
| --- | --- | --- | --- |
| `queryId` | dize | No | Yalnızca bağımsız değişkende verilen KIMLIĞE sahip sorguların ayrıntılarını almak için filtrele |
| `queryName` | dize | No | Yalnızca bağımsız değişkende verilen ada sahip sorguların ayrıntılarını almak için filtrele |
| `IncludeSystemQueries` | boolean | No | Yanıta önceden tanımlanmış sistem sorgularını dahil et |
| `IncludeOnlySystemQueries` | boolean | No | Yanıta yalnızca sistem sorgularını dahil et |
|||||

**İstek yükü**

Yok

**Sözlük**

Yok

**Response**

Yanıt yükü aşağıdaki şekilde yapılandırılır:

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

Bu tabloda, yanıttaki öğelerin temel tanımları açıklanmaktadır.

| **Parametre** | **Açıklama** |
| --- | --- |
| `QueryId` | Sorgunun benzersiz UUID 'SI |
| `Name` | Sorgu oluşturma sırasında sorguya verilen ad |
| `Description` | Sorgunun oluşturulması sırasında verilen açıklama |
| `Query` | Rapor sorgu dizesi |
| `Type` | Kullanıcı tarafından `userDefined` oluşturulan sorgular ve `system` önceden tanımlanmış sistem sorguları için olarak ayarlayın |
| `User` | Sorguyu oluşturan kullanıcı KIMLIĞI |
| `CreatedTime` | Sorgu oluşturma zamanı |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç kodu. Olası değerler 200, 400, 401, 403, 500 |
|||
