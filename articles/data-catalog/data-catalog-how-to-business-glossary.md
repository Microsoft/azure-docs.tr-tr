---
title: Azure Veri Kataloğu 'nda iş sözlüğünü ayarlama
description: Azure Veri Kataloğu 'nda iş sözlüğünü, kayıtlı veri varlıklarını etiketlemek için ortak bir iş sözlüğü tanımlamak ve kullanmak üzere vurgulayan nasıl yapılır makalesi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976803"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Yönetilen etiketleme için iş sözlüğünü ayarlama

## <a name="introduction"></a>Giriş

Azure Veri Kataloğu, veri kaynağı bulmayı mümkün hale getirir; bu sayede analiz yapmak ve kararlar almak için ihtiyaç duyduğunuz veri kaynaklarını kolayca bulabilir ve anlayabilirler. Bu yetenekler, en geniş kullanılabilir veri kaynaklarını bulabilir ve anlayabilmeniz için en büyük etkiyi yapar.

Varlıklar verilerinin daha fazla anlaşılmasına yönelik bir veri kataloğu özelliği etiketleniyor. Etiketleme kullanarak, anahtar sözcükleri bir varlıkla veya bir sütunla ilişkilendirebilirsiniz, bu da bir varlık veya göz atma aracılığıyla varlığı bulmayı kolaylaştırır. Etiketleme Ayrıca varlığın bağlamını ve amacını daha kolay anlamanıza yardımcı olur.

Ancak etiketleme bazen kendi sorunlarına neden olabilir. Etiketlemesinin tanıtılabileceği bazı sorunlar örnekleri şunlardır:

* Bazı varlıklarda kısaltmaların kullanımı ve başkaları üzerinde genişletilmiş metinler. Bu tutarsızlık, varlıkları aynı etiketle etiketleyebilse bile varlıkların bulunmasını sağlar.
* İçeriğe bağlı olarak anlamı olan olası Çeşitlemeler. Örneğin, müşteri veri kümesinde *gelir* adlı bir etiket müşteri tarafından gelir anlamına gelebilir, ancak üç aylık satış veri kümesindeki aynı etiket şirket için üç aylık gelir anlamına gelebilir.  

Veri Kataloğu, bu ve diğer benzer zorlukları ele almak için bir iş sözlüğü içerir.

Bir kuruluş, veri kataloğu iş sözlüğünü kullanarak, ortak bir iş sözlüğü oluşturmak için önemli iş şartlarını ve bunların tanımlarını belgeleyerek. Bu idare, kuruluş genelinde veri kullanımında tutarlılığı mümkün bir şekilde sunar. İş sözlükte bir terim tanımlandıktan sonra, bu, katalogdaki bir veri varlığına atanabilir. Bu yaklaşım, etiketlemeyle aynı yaklaşımla, *yönetilen etiketleniyor*.

## <a name="glossary-availability-and-privileges"></a>Sözlük kullanılabilirliği ve ayrıcalıkları

İş sözlüğü yalnızca Azure Veri Kataloğu 'nun standart sürümünde kullanılabilir. Veri Kataloğu 'nun ücretsiz sürümü bir sözlük içermez ve bu, yönetilen etiketlemeyle ilgili yetenekler sağlamaz.

Veri Kataloğu portalının gezinti menüsündeki **Sözlük** seçeneği aracılığıyla iş sözlüğü 'ne erişebilirsiniz.  

![Veri Kataloğu-iş sözlüğü 'ne erişin](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Veri Kataloğu yöneticileri ve sözlük yöneticileri rolünün üyeleri iş sözlükte Sözlük terimleri oluşturabilir, düzenleyebilir ve silebilir. Tüm veri kataloğu kullanıcıları terim tanımlarını görüntüleyebilir ve varlıkları sözlük koşullarına göre etiketleyebilir.

![Veri Kataloğu-yeni bir sözlük terimi ekleyin](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Sözlük terimleri oluşturma

Veri Kataloğu yöneticileri ve sözlük yöneticileri, **Yeni terim** düğmesine tıklayarak Sözlük terimleri oluşturabilir. Her sözlük terimi aşağıdaki alanları içerir:

* Terim için bir iş tanımı
* Varlık veya sütun için amaçlanan kullanım veya iş kurallarını yakalayan bir açıklama
* Terimi en iyi şekilde bilen paydaşların listesi
* Terimin düzenlendiği hiyerarşiyi tanımlayan üst terim

## <a name="glossary-term-hierarchies"></a>Sözlük terimi hiyerarşileri

Bir kuruluş, veri kataloğu iş sözlüğünü kullanarak iş sözlüğünü bir terim hiyerarşisi olarak tanımlayabilir ve iş taksonomisini daha iyi temsil eden bir terim sınıflandırması oluşturabilir.

Bir terim, belirli bir hiyerarşi düzeyinde benzersiz olmalıdır. Yinelenen adlara izin verilmez. Bir hiyerarşideki düzey sayısı için bir sınır yoktur, ancak üç düzey veya daha az sayıda olduğunda hiyerarşi daha kolay anlaşılır.

İş sözlükte hiyerarşilerin kullanımı isteğe bağlıdır. Sözlük terimleri için üst terim alanını boş bırakmak, sözlükte koşulların düz (hiyerarşik olmayan) bir listesini oluşturur.  

## <a name="tagging-assets-with-glossary-terms"></a>Varlıkları Sözlük terimleri ile etiketleme

Katalog içinde Sözlük terimleri tanımlandıktan sonra, varlıkların etiketlenmesi deneyimi, bir kullanıcı etiketi yazarken sözlük aramak için en iyi duruma getirilmiştir. Veri Kataloğu portalı, aralarından seçim yapabileceğiniz eşleşen sözlük koşullarının bir listesini görüntüler. Kullanıcı listeden bir sözlük terimi seçerse, terim varlığa bir etiket (sözlük etiketi olarak da adlandırılır) olarak eklenir. Kullanıcı, sözlükte olmayan bir terim (Kullanıcı etiketi olarak da bilinir) yazarak yeni bir etiket oluşturmayı da tercih edebilir.

![Bir Kullanıcı etiketiyle etiketlendirilmiş veri varlığı ve iki sözlük etiketi](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Kullanıcı etiketleri, veri kataloğu 'nun ücretsiz sürümünde desteklenen tek etiket türüdür.

### <a name="hover-behavior-on-tags"></a>Etiketlerde üzerine gelme davranışı

Veri Kataloğu portalında, iki etiket türü görsel olarak farklıdır ve farklı vurgulu davranışlar sunar. Bir Kullanıcı etiketinin üzerine geldiğinizde, etiket metnini ve etiketi eklemiş olan kullanıcı veya kullanıcıları görebilirsiniz. Sözlük etiketinin üzerine geldiğinizde, terimin tam tanımını görüntülemek için sözlük döneminin tanımını ve iş sözlüğü açma bağlantısını da görürsünüz.

### <a name="search-filters-for-tags"></a>Etiketler için arama filtreleri

Sözlük etiketleri ve Kullanıcı etiketleri hem aranabilir hem de bir aramada filtre olarak uygulanabilir.

## <a name="summary"></a>Özet

Azure Veri Kataloğu ' nda iş sözlüğü ve bunun desteklediği yönetilen etiketleme kullanarak, veri varlıklarını tutarlı bir şekilde tanımlayabilir, yönetebilir ve keşfedebilirsiniz. İş sözlüğü, kuruluş üyelerine iş sözlüğü öğrenmesini yükseltebilir. Sözlük ayrıca varlık bulmayı ve anlalamayı kolaylaştıran anlamlı meta verileri yakalamayı da destekler.

## <a name="next-steps"></a>Sonraki adımlar

* [İş sözlüğü işlemlerine yönelik REST API belgeleri](/rest/api/datacatalog/data-catalog-glossary)
