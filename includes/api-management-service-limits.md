---
title: include dosyası
description: include dosyası
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188774"
---
| Resource | Sınır |
| --- | --- |
| En fazla ölçek birimi sayısı | Bölge başına 10<sup>1</sup> |
| Önbellek boyutu | <sup>2</sup> birim BAŞıNA 5 GB |
| HTTP yetkilisi başına eşzamanlı arka uç bağlantıları<sup>3</sup> | <sup>4</sup> birim başına 2.048 |
| Önbelleğe alınan en büyük yanıt boyutu | 2 MB |
| En fazla ilke belgesi boyutu | 256 KB<sup>5</sup> | 
| Hizmet örneği başına en fazla özel ağ geçidi etki alanı<sup>6</sup> | 20 |
| Hizmet örneği başına en fazla CA sertifikası sayısı | 10 | 
| Abonelik başına en fazla hizmet örneği sayısı<sup>7</sup> | 20 | 
| Hizmet örneği başına en fazla abonelik sayısı<sup>7</sup> | 500 |
| Hizmet örneği başına en fazla istemci sertifikası sayısı<sup>7</sup> | 50 | 
| Hizmet örneği başına en fazla API sayısı<sup>7</sup> | 50 | 
| Hizmet örneği başına en fazla API işlemi sayısı<sup>7</sup> | 1000 | 
| En fazla toplam istek süresi<sup>7</sup> | 30 saniye | 
| En fazla arabelleğe alınmış yük boyutu<sup>7</sup> | 2 MB | 


<sup>1</sup> Ölçek sınırları, fiyatlandırma katmanına bağlıdır. Fiyatlandırma katmanlarını ve ölçekleme sınırlarını görmek için bkz. [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Birim başına önbellek boyutu fiyatlandırma katmanına bağlıdır. Fiyatlandırma katmanlarını ve ölçekleme sınırlarını görmek için bkz. [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Bağlantılar havuza alınır ve arka uç tarafından açıkça kapanmadığı takdirde yeniden kullanılır.<br/>
<sup>4</sup> Bu sınır, temel, standart ve Premium katmanların birimi başına olur. Geliştirici katmanı 1.024 ile sınırlıdır. Bu sınır, tüketim katmanına uygulanmaz.<br/> 
<sup>5</sup> Bu sınır, temel, standart ve Premium katmanları için geçerlidir. Tüketim katmanında, ilke belgesi boyutu 4 KB ile sınırlıdır.<br/>
<sup>6</sup> Bu kaynak yalnızca Premium katmanda kullanılabilir.<br/>
<sup>7</sup> Bu kaynak yalnızca tüketim katmanı için geçerlidir.<br/>



