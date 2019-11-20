---
title: Azure Veri Kataloğu 'nda veri kaynaklarına açıklama ekleme
description: Kolay adlar, Etiketler, açıklamalar ve uzmanlar dahil olmak üzere Azure Veri Kataloğu 'nda veri varlıklarına nasıl açıklama ekleneceğini vurgulayan nasıl yapılır makalesi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950255"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri kaynaklarına açıklama ekleme

## <a name="introduction"></a>Giriş

**Microsoft Azure Veri Kataloğu** , kurumsal veri kaynakları için bir kayıt sistemi ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, Veri Kataloğu, kullanıcıların veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olur ve kuruluşların mevcut verilerinden daha fazla değer almasını sağlar. Veri kaynağı veri kataloğuna kaydedildiğinde, meta verileri hizmet tarafından kopyalanıp dizinlenir, ancak hikaye burada sonlanmaz. Veri Kataloğu, kullanıcıların, veri kaynağından ayıklanan meta verileri tamamlayacak ve veri kaynağını daha fazla kişiye daha anlaşılır hale getirmek için açıklamalar ve Etiketler gibi kendi açıklayıcı meta verilerini sağlamasına olanak tanır.

## <a name="annotation-and-crowdsourcing"></a>Ek açıklama ve crowdkaynağı
Herkesin bir görüşi vardır. Bu iyi bir şeydir.
Veri Kataloğu, farklı kullanıcıların kurumsal veri kaynaklarında farklı perspektiflere sahip olduğunu ve bu perspektiflerin her birinin değerli olduğunu algılar. Aşağıdaki senaryoyu göz önünde bulundurun:

* Sistem Yöneticisi, veri kaynağını barındıran sunucular veya hizmetler için hizmet düzeyi anlaşmasını bilir.
* Veritabanı Yöneticisi, her bir veritabanı için Yedekleme zamanlamasını ve izin verilen ETL işleme pencerelerini bilir.
* Sistem sahibi, kullanıcıların veri kaynağına erişim istemesi işlemini bilir.
* Veriler veri kaynağındaki varlıkların ve özniteliklerin kurumsal veri modeliyle nasıl eşlendiğini bilir.
* Analist, verilerin destekledikleri iş süreçlerinin bağlamında nasıl kullanıldığını bilir.

Bu perspektiflerin her biri değerli ve Veri Kataloğu, kayıtlı veri kaynaklarının tamamen bir resmini sağlamak için her birinin yakalanmasına ve kullanılmasına izin veren meta veriler için bir crowdsourbir yaklaşım kullanır. Veri Kataloğu portalını kullanarak her Kullanıcı kendi ek açıklamalarını ekleyip düzenleyebilir ve diğer kullanıcılar tarafından sunulan ek açıklamaları görüntüleyebiliyor.

## <a name="different-types-of-annotations"></a>Farklı ek açıklama türleri
Veri Kataloğu aşağıdaki ek açıklama türlerini destekler:

| Ek Açıklama | Notlar |
| --- | --- |
| Kolay ad |Veri varlıklarının daha kolay anlaşılır olması için veri varlık düzeyinde kolay adlar sağlanabilir. Kolay adlar en çok, temel alınan nesne adı şifreli olduğunda, kısaltılmıştır ve kullanıcılara anlamlı değildir. |
| Açıklama |Açıklamalar veri varlığı ve öznitelik/sütun düzeylerinde sağlanabilir. Açıklamalar, kullanıcının veri varlığı veya kullanımı üzerinde perspektifi tanımlayan serbest biçimli kısa metin açıklamalarıdır. |
| Etiketler (Kullanıcı etiketleri) |Etiketler veri varlığı ve öznitelik/sütun düzeylerinde sağlanabilir. Kullanıcı etiketleri, veri varlıklarını veya özniteliklerini sınıflandırmak için kullanılabilecek Kullanıcı tanımlı etiketlerdir. |
| Etiketler (sözlük etiketleri) |Etiketler veri varlığı ve öznitelik/sütun düzeylerinde sağlanabilir. Sözlük etiketleri, genel bir iş sınıflandırması kullanarak veri varlıklarını veya özniteliklerini sınıflandırmak için kullanılabilen, merkezi olarak tanımlanmış sözlük koşullardır. Daha fazla bilgi için bkz. [İş Sözlüğünü Yönetilen Etiketleme için ayarlama](data-catalog-how-to-business-glossary.md) |
| Uzmanı |Uzmanlar, veri varlık düzeyinde sağlanabilir. Uzmanlar, veri üzerinde uzman perspektifleri olan kullanıcıları veya grupları belirler ve kayıtlı veri kaynaklarını bulacak ve mevcut ek açıklamalar tarafından yanıtlanmayan soruların bulunduğu kullanıcılar için iletişim noktası olarak görev yapabilir. |
| Erişim izni iste |Veri varlık düzeyinde istek erişim bilgileri sağlanabilir. Bu bilgiler, henüz erişim izinleri olmayan bir veri kaynağını bulacak kullanıcılar içindir. Kullanıcılar, erişim izni veren kullanıcı veya grubun e-posta adresini, kullanıcıların erişim kazanmak için ihtiyaç duyduğu işlemin veya aracın URL 'sini girebilir veya işlemin kendisini metin olarak girebilirler. |
| Belgeler |Belgeler veri varlık düzeyinde sağlanabilir. Varlık belgeleri, bağlantılar ve görüntüler içerebilen ve açıklamalar ve Etiketler aracılığıyla hiçbir bilginin sağlayabileceği zengin metin bilgileri içerir. |

## <a name="annotating-multiple-assets"></a>Birden çok kıymete açıklama ekleme
Veri Kataloğu portalında birden çok veri varlığı seçerken, kullanıcılar tüm seçili varlıklara tek bir işlemde açıklama ekleyebilir. Ek açıklamalar, tüm seçili varlıklar için geçerlidir. Bu, ilgili veri varlıkları için tutarlı bir açıklama ve etiket ve uzmanlar kümesi seçmenizi ve sağlanması kolaylaşır.

> [!NOTE]
> Veri Kataloğu veri kaynağı kayıt aracı kullanılarak veri varlıkları kaydettirilirken Etiketler ve uzmanlar de belirtilebilir.
>
>

Birden çok tablo ve görünüm seçerken, veri kataloğu portalında yalnızca seçili olan tüm veri varlıklarının ortak bulunduğu sütunlar görüntülenir. Bu, kullanıcıların tüm seçili varlıklar için aynı ada sahip tüm sütunlar için Etiketler ve açıklamalar sağlamasına izin verir.

## <a name="annotations-and-discovery"></a>Ek açıklamalar ve bulma
Kayıt sırasında veri kaynağından ayıklanan meta veriler veri kataloğu arama dizinine eklendikçe, Kullanıcı tarafından sağlanan meta verilerin de dizini oluşturulur. Bu, yalnızca ek açıklamaların, bulduğu verileri daha kolay anlamasına olanak tanıyan, ek açıklamaların de kullanıcılara anlamlı olan koşullara göre arayarak açıklamalı veri varlıklarını bulmasını kolaylaştıran anlamına gelir.

## <a name="summary"></a>Özet
Veri Kataloğu ile veri kaynağını kaydetme, veri kaynağından Katalog hizmetine yapısal ve açıklayıcı meta verileri kopyalayarak bu verilerin keşfedilmesini sağlar. Veri kaynağı kaydedildikten sonra kullanıcılar, veri kataloğu portalı içinden bulmayı ve anlamayı kolaylaştırmak için ek açıklamalar sağlayabilir.

## <a name="see-also"></a>Ayrıca bkz.
* Veri kaynaklarına açıklama ekleme hakkında adım adım ayrıntılar için [Azure Veri Kataloğu](data-catalog-get-started.md) öğreticisini kullanmaya başlayın.
