---
title: Azure Key Vault Azure Key Vault etkileyen bir Azure hizmet kesintisi durumunda yapılacaklar | Microsoft Docs
description: Azure Key Vault etkileyen bir Azure hizmet kesintisi durumunda yapılacaklar hakkında bilgi edinin.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: c0fed80f4ba9815cee49bc9968d542f168570986
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976366"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Kullanılabilirlik ve yedeklilik Azure Key Vault

, Tek tek hizmet bileşenleri başarısız olsa bile, anahtarlarınızın ve sırlarınızın uygulamanız için kullanılabilir kalmasını sağlamak için birden çok artıklık katmanı Azure Key Vault.

Anahtar kasanızın içeriği bölge içinde ve bir ikincil bölgeye en az 150 mil uzakta, ancak aynı coğrafya dahilinde çoğaltılır. Bu, anahtarlarınızın ve sırlarınızın yüksek dayanıklılığına sahip olmasını sağlar. Belirli bölge çiftleri hakkındaki ayrıntılar için bkz. [Azure eşleştirilmiş bölgeler](../best-practices-availability-paired-regions.md) belgesi.

Anahtar Kasası hizmetindeki tek tek bileşenler başarısız olursa, işlevselliğe bir azalma olmadığından emin olmak için, içindeki bölge adımındaki alternatif bileşenler. Bunu tetiklemek için herhangi bir işlem gerçekleştirmeniz gerekmez. Otomatik olarak gerçekleşir ve sizin için saydam hale gelir.

Tüm Azure bölgesinin kullanılamadığı nadir bir olayda, bu bölgedeki Azure Key Vault yaptığınız istekler otomatik olarak ikincil bir bölgeye yönlendirilir (*Yük devredildi*). Birincil bölge yeniden kullanılabilir olduğunda, istekler birincil bölgeye geri yönlendirilir (*başarısız*olur). Yine de, bu otomatik olarak gerçekleştiğinden herhangi bir işlem yapmanız gerekmez.

Bu yüksek kullanılabilirlik tasarımı aracılığıyla Azure Key Vault bakım etkinlikleri için kapalı kalma süresi gerektirmez.

Bilmeniz için bazı uyarılar vardır:

* Bölge yük devretmesi durumunda, hizmetin yük devretmesinin tamamlanması birkaç dakika sürebilir. Bu süre boyunca yapılan istekler yük devretme tamamlanana kadar başarısız olabilir.
* Yük devretme tamamlandıktan sonra, anahtar kasanızın salt okuma modunda olması gerekir. Bu modda desteklenen istekler şunlardır:
  * Anahtar kasalarını Listele
  * Anahtar kasalarının özelliklerini al
  * Gizli dizileri Listele
  * Gizli dizileri al
  * Anahtarları Listele
  * Get (özellikleri) anahtarları
  * Şifrele
  * Şifre Çöz
  * Ilacağını
  * Unwrap
  * Doğrula
  * İmzala
  * Yedekle
* Yük devretme işlemi geri alındıktan sonra, tüm istek türleri (okuma *ve* yazma istekleri dahil) kullanılabilir.

