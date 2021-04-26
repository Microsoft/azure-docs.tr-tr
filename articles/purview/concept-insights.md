---
title: Azure purview 'da Öngörüler raporlarını anlama
description: Bu makalede, Azure purview 'daki Öngörüler açıklanmaktadır.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555456"
---
# <a name="understand-insights-in-azure-purview"></a>Azure Purview'da İçgörüleri Anlama

Bu makalede, Azure purview 'daki Öngörüler özelliğine bir genel bakış sunulmaktadır.

Içgörüler, takip eden önemli nesnelerin biridir. Bu özellik, müşterileri ve veri kaynağı yöneticileri, iş kullanıcıları, veri özellikleri, veri müdürü ve güvenlik yöneticileri için belirli Öngörüler sağlamak üzere, kendi kataloğuna bir cam görünümü bölmesi ve daha fazla anlık görüntü sağlar. Şu anda, takip eden müşterilerin genel önizlemede kullanılabilecek aşağıdaki Içgörüler raporları vardır.

## <a name="asset-insights"></a>Varlık öngörüleri

Bu rapor, verilerinize ait bir kuşbakışı görünümünü ve kaynak türüne göre dağılımını, sınıflandırmadan ve dosya boyutuna göre boyutlara göre sağlar. Bu rapor, kuruluşunuzun veri Emlak dahilinde belirli bir sınıflandırmayla kaç varlık olduğunu öğrenmek isteyen farklı türlerdeki kullanıcıları, takip etme hesabı ve çalışan taramalar ya da iş kullanıcıları gibi farklı türlerde kullanıcılara karşı farklılık gösterebilir. 

Rapor, grafikler ve KPI 'ler ve daha sonra da yanlış yerleştirilmiş dosyalar gibi belirli anormallere kapsamlı öngörüler sağlar. Bu rapor ayrıca, müşterinin belirli bir sınıflandırmayla varlık sayısını görüntüleyebileceği, bilgilerin kaynak türlerine ve en üst klasörlere göre dökümünü görüntüleyebileceği ve ayrıca daha fazla araştırma için varlıkların listesini görüntüleyebileceği uçtan uca müşteri deneyimini destekler.

## <a name="scan-insights"></a>Öngörüleri Tara

Rapor, yöneticilerin taramanın genel durumunu (kaç başarılı, kaç tane başarısız, kaç tane iptal edildiğini) anlamasına olanak sağlar. Bu rapor, son yedi gün veya son 30 günlük bir süre içinde, purview hesabında yürütülen taramalarda bir durum güncelleştirmesine izin verir.

Bu rapor ayrıca yöneticilerin hangi taramaların başarısız olduğunu ve belirli kaynak türlerini keşfederek ve araştırmalarını sağlar. Kullanıcıların araştırmasını daha fazla sağlamak için, rapor, "kaynaklar" deneyiminin içindeki tarama geçmişi sayfasına gitmelerine yardımcı olur.

## <a name="glossary-insights"></a>Sözlük öngörüleri

Bu rapor, iş kullanıcılarına ve verilerine sözlük üzerinde bir durum raporu kazandırır. Kullanıcılar, sözlük koşullarına durum dağılımını anlamak için bu raporu görüntüleyebilir, varlıklara kaç tane sözlük terimi ekleneceğini ve ne kadar çok varlık iliştirilemez olduğunu öğrenin. İş kullanıcıları, sözlük koşullarının eksiksiz bir şekilde olduğunu da öğrenebilir. 

Bu rapor, bir iş kullanıcısının veya verilerinin odaklanmak için ihtiyacı olan en üst öğeleri özetler ve kendi kuruluşu için bir bütün ve kullanılabilir sözlük oluşturur. Kullanıcılar ayrıca, belirli bir sözlük terimi üzerinde değişiklik yapmak için "Sözlük öngörüleri" deneyiminden "Sözlük" deneyimine gidebilir.

## <a name="classification-insights"></a>Sınıflandırma öngörüleri

Bu rapor, sınıflandırılan verilerin nerede bulunduğu, tarama sırasında bulunan sınıflandırmaların ve sınıflandırılan dosyaların kendileri için ayrıntıya gitme hakkında ayrıntılar sağlar. Güvenlik yöneticilerinin, kuruluşun veri Emlak bölümünde bulunan bilgi türlerini anlamasına olanak sağlar. 

Azure purview ' de, sınıflandırmalar konu etiketlerine benzerdir ve veri Emlak içindeki belirli bir türün içeriğini işaretlemek ve tanımlamak için kullanılır.

Sınıflandırma öngörülerini, belirli sınıflandırmalarla içerik belirlemek ve depolar için ek güvenlik ekleme ya da içeriği daha güvenli bir konuma taşıma gibi gerekli eylemleri anlamak için kullanın.

Daha fazla bilgi için bkz. [Azure purview 'daki verileriniz hakkında sınıflandırma öngörüleri](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Duyarlılık etiketleme öngörüleri

Bu rapor, bir tarama sırasında bulunan duyarlılık etiketleri ve etiketli dosyaların kendileri için ayrıntıya gitme hakkında ayrıntılar sağlar. Güvenlik yöneticilerinin, kuruluşun veri Emlak bölümünde bulunan bilgilerin güvenliğini sağlamanıza olanak sağlar. 

Azure purview 'da, her kategoriye uygulamak istediğiniz grup güvenlik ilkelerinin yanı sıra sınıflandırma türü kategorilerini belirlemek için duyarlılık etiketleri kullanılır.

İçeriklerinizde bulunan duyarlılık etiketlerini tanımlamak ve belirli depolara veya dosyalara erişimi yönetme gibi gerekli eylemleri anlamak için etiketleme öngörüleri raporunu kullanın.

Daha fazla bilgi için bkz. [Azure purview 'daki verileriniz hakkında duyarlılık etiketi öngörüleri](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Dosya Uzantısı öngörüleri

Bu rapor, bir tarama sırasında bulunan dosya uzantıları veya dosya türleri hakkında ayrıntılar ve dosyaların kendileri için detaya gitme bilgilerini sağlar. 

Her seferinde kaç dosya olduğunu, bu dosyaların nerede olduğunu ve hassas veriler için tarama yapılıp yapılmayacağını anlamak için dosya uzantısı öngörüleri raporunu kullanın.

Daha fazla bilgi için bkz. [Azure purview 'daki verileriniz hakkında dosya uzantısı öngörüleri](file-extension-insights.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Sözlük öngörüleri](glossary-insights.md)
* [Öngörüleri Tara](scan-insights.md)
* [Sınıflandırma öngörüleri](./classification-insights.md)