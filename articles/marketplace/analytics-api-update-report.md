---
title: Rapor API 'sini Güncelleştir
description: Ticari Market Analytics raporlarının parametresini raporlamak için bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584173"
---
# <a name="update-report-api"></a>Rapor API 'sini Güncelleştir

Bu API, bir rapor parametresini değiştirmenize yardımcı olur.

**İstek sözdizimi**

| Yöntem | İstek URI'si |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**İstek yükü**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Sözlük**

Bu tabloda, istek yükünde öğelerin temel tanımları listelenmektedir.

| Parametre | Gerekli | Açıklama | İzin verilen değerler |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Rapora atanacak ad | dize |
| `Description` | No | Oluşturulan raporun açıklaması | string |
| `StartTime` | Yes | Rapor oluşturma işlemi başlamadan sonra zaman damgası | dize |
| `RecurrenceInterval` | No | Raporun saat cinsinden oluşturulması gereken sıklık. En küçük değer 4 ' dir | tamsayı |
| `RecurrenceCount` | No | Oluşturulacak rapor sayısı. Varsayılan değer sonsuzdir | tamsayı |
| `Format` | Yes | İçe aktarılmış dosyanın dosya biçimi. Varsayılan CSV 'dir. | CSV/TSV |
| `CallbackUrl` | Yes | rapor oluşturma sırasında çağrılacak https geri çağırma URL 'SI | string |
|||||

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
| `ReportId` | Güncelleştirilmekte olan raporun evrensel benzersiz tanımlayıcısı (UUID) |
| `ReportName` | İstek yükünde rapora verilen ad |
| `Description` | Raporun oluşturulması sırasında verilen açıklama |
| `QueryId` | Rapor oluşturulduğu sırada geçirilen sorgu KIMLIĞI |
| `Query` | Bu rapor için yürütülecek sorgu metni |
| `User` | Raporu oluşturmak için kullanılan Kullanıcı KIMLIĞI |
| `CreatedTime` | Raporun oluşturulduğu zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ModifiedTime` | Raporun son değiştirilme zamanı. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `StartTime` | Rapor yürütmenin başlayacağı zaman. Saat biçimi yyyy-MM-ddTHH: mm: ssZ şeklindedir |
| `ReportStatus` | Rapor yürütmenin durumu. Olası değerler duraklatıldı, etkin ve etkin değildir. |
| `RecurrenceInterval` | Rapor oluşturma sırasında belirtilen yinelenme aralığı |
| `RecurrenceCount` | Rapor oluşturma sırasında belirtilen yinelenme sayısı |
| `CallbackUrl` | İstekte belirtilen geri çağırma URL 'SI |
| `Format` | Rapor dosyalarının biçimi |
|||
