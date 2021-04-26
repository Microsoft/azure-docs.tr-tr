---
title: Rapor sorguları API 'sini deneyin
description: Ticari Market Analytics raporları için bir rapor sorgusu yürütmek üzere bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584174"
---
# <a name="try-report-queries-api"></a>Rapor sorguları API 'sini deneyin

Bu API bir rapor sorgu ekstresi yürütür. API, verilerin beklediğiniz gibi olup olmadığını doğrulamak için yalnızca iş ortağı olarak kullandığınız 10 kayıt döndürür.

> [!IMPORTANT]
> Bu API, 100 saniyelik bir sorgu yürütme zaman aşımına uğradı. API 'nin 100 saniyeden daha fazla sürdüğünü fark ederseniz, sorgu sözdizimsel olarak yanlış olabilir veya 200 dışında bir hata kodu almış olursunuz. Sorgu söz dizimi doğruysa gerçek rapor oluşturma işlemi geçer.

**İstek sözdizimi**

| **Yöntem** | **İstek URI'si** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**İstek üst bilgisi**

| **Üst bilgi** | **Tür** | **Açıklama** |
| --- | --- | --- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |
|||

**QueryParameter**

| **Parametre adı** | **Tür** | **Açıklama** |
| --- | --- | --- |
| `exportQuery` | string | Yürütülmesi gereken rapor sorgu dizesi |
| `queryId` | string | Geçerli bir var olan sorgu KIMLIĞI |
|||

**Yol**  **parametresi**

Yok

**İstek yükü**

Yok

**Sözlük**

Yok

**Response**

Yanıt yükü aşağıdaki şekilde yapılandırılır:

Yanıt kodu: 200, 400, 401, 403, 404, 500

Yanıt yükü: Ilk 10 sorgu yürütme satırı
