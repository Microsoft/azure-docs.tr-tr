---
title: Rapor yürütmeleri API 'sini Duraklat
description: Bir ticari Market Analytics raporunun zamanlanan yürütmesini duraklatmak için bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584205"
---
# <a name="pause-report-executions-api"></a>Rapor yürütmeleri API 'sini Duraklat

Bu API yürütülürken, raporların zamanlanan yürütmesi duraklatılır.

**İstek sözdizimi**

| Yöntem | İstek URI'si |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**İstek üst bilgisi**

| Üst bilgi | Tür | Description |
| ------------ | ------------- | ------------- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |
||||

**Yol parametresi**

Yok

**Sorgu parametresi**

| Parametre adı | Gerekli | Tür | Açıklama |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Evet | string | Değiştirilen raporun KIMLIĞI |
|||||

**Sözlük**

Yok

**Response**

Yanıt yükü JSON biçiminde aşağıdaki şekilde yapılandırılır:

Yanıt kodu: 200, 400, 401, 403, 404, 500 yanıt yükü:

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
      "RecurrenceCount": 0,
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

| Parametre | Açıklama |
| ------------ | ------------- |
| `ReportId` | Silinen raporun evrensel benzersiz tanımlayıcısı (UUID) |
| `ReportName` | Oluşturma sırasında rapora verilen ad |
| `Description` | Raporun oluşturulması sırasında verilen açıklama |
| `QueryId` | Rapor oluşturulduğu sırada geçirilen sorgu KIMLIĞI |
| `Query` | Bu rapor için yürütülecek sorgu metni |
| `User` | Rapor oluşturmak için kullanılan Kullanıcı KIMLIĞI |
| `CreatedTime` | Raporun oluşturulduğu zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ModifiedTime` | Raporun son değiştirilme zamanı. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `StartTime` | Rapor yürütmenin başlayacağı zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ReportStatus` | Rapor yürütmenin durumu. Olası değerler duraklatıldı, etkin ve etkin değildir. |
| `RecurrenceInterval` | Rapor oluşturma sırasında belirtilen yinelenme aralığı |
| `RecurrenceCount` | Rapor oluşturma sırasında belirtilen yinelenme sayısı |
| `CallbackUrl` | İstekte belirtilen geri çağırma URL 'SI |
| `Format` | Rapor dosyalarının biçimi |
|||
