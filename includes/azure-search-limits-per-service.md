---
title: include dosyası
description: include dosyası
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188751"
---
Depolama, disk alanı veya *en fazla* Dizin, belge veya diğer üst düzey kaynak sayısı üzerinde (hangisi önce geliyorsa) sabit bir sınıra göre kısıtlanmıştır. Aşağıdaki tablo, depolama sınırlarını belgelemektedir. Dizinler, belgeler ve diğer nesneler üzerinde en fazla sınır için bkz. [kaynağa göre sınırlar](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Boş | Temel<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Hizmet düzeyi sözleşmesi (SLA)<sup>3</sup>  |Hayır |Evet |Evet |Evet |Evet |Evet |Evet |Evet |
| Bölüm başına depolama |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Hizmet başına bölüm |Yok |1\. |12 |12 |12 |3 |12 |12 |
| Bölüm boyutu |Yok |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Çoğaltmalar |Yok |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> temel, sabit bir bölüme sahiptir. Bu katmanda, daha fazla sorgu iş yükleri için daha fazla çoğaltma ayırmak üzere ek arama birimleri kullanılır.

<sup>2</sup> S3 HD 'nin sabit sınırı üç bölümden oluşur ve bu, S3 için bölüm sınırından daha düşüktür. S3 HD dizin sınırı çok daha yüksek olduğu için daha düşük bir bölüm sınırı uygulanmaktadır. Hem işlem kaynakları (depolama ve işleme) hem de içerik (dizinler ve belgeler) için hizmet sınırları mevcut olduğu için içerik sınırına ilk önce ulaşılır.

<sup>3</sup> hizmet düzeyi sözleşmeleri, ayrılmış kaynaklar üzerinde faturalandırılabilir hizmetler için sunulmaktadır. Ücretsiz hizmetler ve Önizleme özelliklerinin SLA 'Sı yoktur. Faturalanabilir hizmetler için, hizmet için yeterli artıklık sağladığınızda SLA 'Lar devreye girer. Sorgu (Read) SLA 'Lar için iki veya daha fazla çoğaltma gerekiyor. Sorgu ve dizin oluşturma (okuma-yazma) SLA 'Lar için üç veya daha fazla çoğaltma gereklidir. Bölüm sayısı bir SLA değerlendirmesi değildir. 