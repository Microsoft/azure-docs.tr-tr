---
title: include dosyası
description: include dosyası
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 0614e6fb1df13332cb05dcb7bb9b0f07a1764d23
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251304"
---
| Kaynak | Sınır |
| ---------------------------------------------------------------------- | -------------------------- |
| En fazla ölçek birimi sayısı | Bölge başına 10<sup>1</sup> |
| Önbellek boyutu | Birim<sup>2</sup> başına 5 gib |
| HTTP yetkilisi başına eşzamanlı arka uç bağlantıları<sup>3</sup> | <sup>4</sup> birim başına 2.048 |
| Önbelleğe alınan en büyük yanıt boyutu | 2 MIB |
| En fazla ilke belgesi boyutu | 256 KiB<sup>5</sup> |
| Hizmet örneği başına en fazla özel ağ geçidi etki alanı<sup>6</sup> | 20 |
| Hizmet örneği başına en fazla CA sertifikası sayısı<sup>7</sup> | 10 |
| Abonelik başına en fazla hizmet örneği sayısı<sup>8</sup> | 20 |
| Hizmet örneği başına en fazla abonelik sayısı<sup>8</sup> | 500 |
| Hizmet örneği başına en fazla istemci sertifikası sayısı<sup>8</sup> | 50 |
| Hizmet örneği başına en fazla API sayısı<sup>8</sup> | 50 |
| Hizmet örneği başına en fazla API Yönetimi işlemi sayısı<sup>8</sup> | 1.000 |
| En fazla toplam istek süresi<sup>8</sup> | 30 saniye |
| En fazla arabelleğe alınmış yük boyutu<sup>8</sup> | 2 MIB |
| En yüksek istek URL 'SI boyutu<sup>9</sup> | 4096 bayt |
| URL yol kesiminin en fazla uzunluğu<sup>10</sup> | 260 karakter |
| [Doğrulama ilkesi](../articles/api-management/validation-policies.md)tarafından kullanılan en büyük API şeması boyutu<sup>10</sup> | 4 MB |
| [Doğrulama-içerik ilkesinde](../articles/api-management/validation-policies.md#validate-content) istek veya Yanıt gövdesinin en büyük boyutu | 100 KB |
| Şirket içinde barındırılan ağ geçitlerinin maksimum sayısı<sup>11</sup> | 25 |

<sup>1</sup> Ölçek sınırları, fiyatlandırma katmanına bağlıdır. Fiyatlandırma katmanları ve ölçek limitleriyle ilgili ayrıntılar için bkz. [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Birim başına önbellek boyutu fiyatlandırma katmanına bağlıdır. Fiyatlandırma katmanlarını ve ölçekleme sınırlarını görmek için bkz. [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Bağlantılar havuza alınır ve arka uç tarafından açıkça kapanmadığı takdirde yeniden kullanılır.<br/>
<sup>4</sup> Bu sınır, temel, standart ve Premium katmanların birimi başına olur. Geliştirici katmanı 1.024 ile sınırlıdır. Bu sınır, tüketim katmanına uygulanmaz.<br/>
<sup>5</sup> Bu sınır, temel, standart ve Premium katmanları için geçerlidir. Tüketim katmanında, ilke belgesi boyutu 16 KiB ile sınırlıdır.<br/>
<sup>6</sup> Yalnızca geliştirici ve Premium katmanlarında birden çok özel etki alanı desteklenir.<br/>
<sup>7</sup> CA sertifikaları, tüketim katmanında desteklenmez.<br/>
<sup>8</sup> Bu sınır yalnızca tüketim katmanı için geçerlidir. Bu kategorilerde diğer katmanlara yönelik bir sınır yoktur.<br/>
<sup>9</sup> Yalnızca tüketim katmanı için geçerlidir. En fazla 2048 bayt uzunluğunda sorgu dizesi içerir.<br/>
<sup>10</sup> bu sınırı artırmak için lütfen [desteğe](https://azure.microsoft.com/support/options/)başvurun.<br/>
<sup>11</sup> Şirket içinde barındırılan ağ geçitleri yalnızca geliştirici ve Premium katmanlarda desteklenir. Sınır, [kendinden konak ağ geçidi kaynaklarının](/rest/api/apimanagement/2019-12-01/gateway)sayısı için geçerlidir. Bu sınırı artırmak için lütfen [desteğe](https://azure.microsoft.com/support/options/)başvurun. Şirket içinde barındırılan bir ağ geçidi kaynağıyla ilişkili düğümlerin (veya çoğaltmaların) sayısının Premium katmanda sınırsız olduğunu ve geliştirici katmanındaki tek bir düğümde bulunduğunu unutmayın.
