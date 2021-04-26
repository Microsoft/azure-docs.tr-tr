---
title: REST API kullanarak Azure Sentinel 'te Watchlists yönetme | Microsoft Docs
description: Bu makalede, Watchlists ve öğelerini oluşturmak, değiştirmek ve silmek için Azure Sentinel Watchlists 'in Log Analytics ' REST API kullanılarak nasıl yönetildiği açıklanır.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798499"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>REST API kullanarak Azure Sentinel 'te Watchlists yönetme

> [!IMPORTANT]
> Watchlists özelliği şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Azure 'da yerleşik olarak oluşturulan Azure Sentinel Log Analytics, Watchlists yönetmek için Log Analytics ' REST API kullanmanıza olanak sağlar. Bu belgede, REST API kullanarak Watchlists ve öğelerini oluşturma, değiştirme ve silme işlemlerinin nasıl yapılacağı gösterilir.  Bu şekilde oluşturulan Watchlists Azure Sentinel Kullanıcı arabiriminde görüntülenir.

## <a name="common-uri-parameters"></a>Ortak URI parametreleri

Tüm listem API komutları için ortak URI parametreleri aşağıda verilmiştir:

| Name | İçinde | Gerekli | Tür | Description |
|-|-|-|-|-|
| **SubscriptionID** | path | evet | GUID | Azure abonelik KIMLIĞI |
| **ResourceGroupName** | path | evet | string | abonelik içindeki kaynak grubunun adı |
| **WorkspaceName** | path | evet | string | Log Analytics çalışma alanının adı |
| **{watchlistAlias}** | path | Yes | string | belirli bir listem adı<br>\* Tüm Watchlists alınırken gerekli değildir |
| **{watchlistItemId}** | path | Evet * * | GUID | içinde oluşturulacak öğenin KIMLIĞI, listem öğesinden ekleme veya silme<br>\*\* Yalnızca listem öğesi komutları için gereklidir |
| **{api-Version}** | sorgu | evet | string | Bu isteği yapmak için kullanılan protokol sürümü. 29 Ekim 2020 itibariyle, listem API sürümü *2019-01-01-Preview* ' dır. |
| **{Yataertoken}** | yetkilendirme | evet | token | taşıyıcı yetkilendirme belirteci |
|  

## <a name="retrieve-all-watchlists"></a>Tüm Watchlists al

Bu komut, öğeleri olmadan bir çalışma alanıyla ilişkili tüm Watchlists alır.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | Mevcut Watchlists listesi veya hiçbir listem bulunmazsa boş |  |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
|

## <a name="look-up-a-watchlist-by-name"></a>Ada göre listem arama

Bu komut, öğeleri olmadan bir çalışma alanı ile ilişkili belirli bir listem alır.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | İstenen listem |  |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
| 404/bulunamadı |  | İstenen ada sahip listem bulunamadı |
|

## <a name="create-a-watchlist"></a>Listem oluşturma

Bu komut bir listem oluşturur ve ona öğe ekler. Listem ve öğelerini oluşturmak için bu uç noktaya iki çağrı alır: ilki listem (üst) öğesini oluşturur ve ikincisi öğeleri ekler.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>İstek gövdesi

Bir listem Create isteğinin istek gövdesinin bir örneği aşağıda verilmiştir:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | Öğeler olmadan, istek tarafından oluşturulan listem |  |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
| 409/çakışma |  | İşlem başarısız oldu, aynı diğer ada sahip bir listem var |
|

## <a name="delete-a-watchlist"></a>Bir listem silme

Bu komut, bir listem ve öğelerini siler.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | Boş yanıt gövdesi |  |
| 204/içerik yok | Boş yanıt gövdesi | Hiçbir şey silinmedi |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
|

## <a name="add-or-update-a-watchlist-item"></a>Listem öğesi ekleme veya güncelleştirme

Bu komut var olan bir *watchlisItemId* (bir GUID) üzerinde çalıştırıldığında, öğeyi istek gövdesinde belirtilen verilerle güncelleştirir. Aksi takdirde, yeni bir öğe oluşturulur.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>İstek gövdesi

Bir listem öğesi ekleme/güncelleştirme isteğine ait istek gövdesinin bir örneği aşağıda verilmiştir:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | İstek tarafından oluşturulan veya güncellenen listem öğesi |  |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
| 409/çakışma |  | İşlem başarısız oldu, aynı diğer ada sahip bir listem var |
|

## <a name="delete-a-watchlist-item"></a>Bir listem öğesini silme

Bu komut var olan bir listem öğesini siler.

### <a name="request-uri"></a>İstek URI'si
(URI tek bir satırdır, kolayca okunabilirlik için ayrılmıştır)

| Yöntem | İstek URI'si |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Yanıtlar

| Durum kodu | Yanıt gövdesi | Description |
|-|-|-|
| 200/TAMAM | Boş yanıt gövdesi |  |
| 204/içerik yok | Boş yanıt gövdesi | Hiçbir şey silinmedi |
| 400/hatalı istek |  | Hatalı oluşturulmuş istek sözdizimi, geçersiz istek parametresi... |
|

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Sentinel 'de Log Analytics API 'sini kullanarak Watchlists ve öğelerinin nasıl yönetileceğini öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Watchlists](watchlists.md) hakkında daha fazla bilgi edinin
- [Azure SENTINEL API](/rest/api/securityinsights/) 'sinin diğer kullanımlarını keşfet