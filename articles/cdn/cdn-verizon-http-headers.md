---
title: Verizon'a özgü HTTP üst bilgilerini Azure CDN kurallar altyapısı için | Microsoft Docs
description: Bu makalede, Azure CDN ile kurallar altyapısı Verizon'a özgü HTTP üst bilgilerini kullanmayı açıklar.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593256"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN kurallar altyapısı Verizon'a özgü HTTP üstbilgileri

İçin **verizon'dan Azure CDN Premium** ürünleri, bir HTTP isteği, kaynak sunucunun bulunma noktası (POP) sunucusu ekleyebilirsiniz bir veya daha fazla ayrılmış üstbilgileri (veya proxy özel üst bilgiler) istemci isteğinde POP'a gönderilir. Bu üst alınan üstbilgileri iletme standart ek bilgilerdir. Standart istek üst bilgileri hakkında daha fazla bilgi için bkz. [isteği alanları](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Bu ayrılmış üstbilgileri biri Azure CDN (Content Delivery Network) POP isteğinde kaynak sunucuya eklenmesini önlemek istiyorsanız, bir kural oluşturmanız gerekir [Proxy özel üst bilgileri özelliği](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) kurallar altyapısı. Bu kuralda üstbilgileri üstbilgi alanında varsayılan listesinden kaldırmak istediğiniz üstbilgi hariç tutun. Etkinleştirdiyseniz [önbellek yanıt üst bilgileri hata ayıklama özelliği](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers), gerekli eklediğinizden emin olun `X-EC-Debug` üstbilgileri. 

Örneğin, kaldırmak için `Via` üst bilgi, kuralın üstbilgi alanı, aşağıdaki listede yer alan üst bilgileri içermelidir: *X-iletilen-için X-iletilen-Proto, X-ana bilgisayar, X-Midgress X ağ geçidi listesini, X-EC-Name, ana bilgisayar*. 

![Proxy özel üst bilgileri kuralı](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Aşağıdaki tabloda, Verizon CDN POP ' isteğindeki ekleyen üstbilgileri açıklanmaktadır:

İstek üstbilgisi | Açıklama | Örnek
---------------|-------------|--------
[aracılığıyla](#via-request-header) | Bu proxy POP sunucu tanımlayan bir kaynak sunucuya gönderilen istek. | HTTP/1.1 ECS (dca/1A2B)
X-iletilen-için | İstek sahibinin IP adresini gösterir.| 10.10.10.10
X iletilen Proto | İsteğin protokol gösterir. | http
X-Host | İsteğin ana bilgisayar adını belirtir. | CDN.mydomain.com
X-Midgress | İstek proxy ek bir CDN sunucu olup olmadığını gösterir. Örneğin, POP Sunucusu kaynağı kalkan sunucu veya POP server ADN ağ geçidi sunucusu. <br />Yalnızca midgress trafiği gerçekleştiğinde bu üst bilgi isteği eklendi. Bu durumda, üst bilgi isteği ek bir CDN sunucu proxy olduğunu belirtmek için 1 olarak ayarlanır.| 1\.
[Ana Bilgisayar](#host-request-header) | Konak ve istenen içeriğin bulunduğu bağlantı noktasını tanımlar. | Marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Bir müşteri kaynağa atanmış ADN ağ geçidi sunucusu yük devretme listesini tanımlar. <br />Kaynak koruma: Bir müşteri kaynağa atanmış kaynak kalkan sunucuları kümesini gösterir. | `icn1,hhp1,hnd1`
X-EC - _&lt;adı&gt;_ | İle başlar ve istek üstbilgileri *X-EC* (örneğin, X-EC-etiketi, [X-EC-Debug](cdn-http-debug-headers.md)) CDN tarafından kullanım için ayrılmıştır.| waf üretim

## <a name="via-request-header"></a>İstek üstbilgisi
Bir biçime `Via` üstbilgisi tanımlar POP sunucu isteği aşağıdaki sözdizimi tarafından belirtilir:

`Via: Protocol from Platform (POP/ID)` 

Sözdiziminde kullanılan terimler şu şekilde tanımlanır:
- Protokol: İstek için proxy kullanılan protokol (örneğin, HTTP/1.1) sürümü gösterir. 

- Platform: İçerik istendi platform gösterir. Aşağıdaki kodu, bu alan için geçerlidir: 

    Kod | Platform
    -----|---------
    ECAcc | HTTP büyük
    ECS   | HTTP küçük
    ECD   | Uygulama teslim ağı (ADN)

- POP: Gösterir [POP](cdn-pop-abbreviations.md) , işlenen istek. 

- KİMLİĞİ: Yalnızca iç kullanım içindir.

### <a name="example-via-request-header"></a>Örnek aracılığıyla istek üstbilgisi

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Konak istek üstbilgisi
POP sunucuları üzerine yazar `Host` aşağıdaki koşulların her ikisinin de doğru olduğunda üst bilgi:
- İstenen içerik için kaynak, bir müşteri kaynak sunucusudur.
- Karşılık gelen müşteri kaynağın HTTP ana bilgisayar üstbilgisi seçeneği boş değil.

`Host` İstek üst bilgisi, HTTP ana bilgisayar üstbilgisi seçeneği tanımlanan değerini yansıtmak üzere yazılır.
Ardından, boş olarak müşteri kaynağın HTTP ana bilgisayar üstbilgisi seçenek ayarlanırsa `Host` istek sahibi tarafından gönderilen istek üst bilgisi, müşterinin kaynak sunucuya iletilir.

## <a name="x-gateway-list-request-header"></a>X ağ geçidi listesini istek üstbilgisi
POP sunucu ekleme/üzerine yazar ' aşağıdaki koşullardan biri karşılandığında X ağ geçidi listesini istek üst bilgisi:
- İstek ADN platforma işaret eder.
- İstek kaynak kalkan özelliği tarafından korunan bir müşteri kaynak sunucuya iletilir.

