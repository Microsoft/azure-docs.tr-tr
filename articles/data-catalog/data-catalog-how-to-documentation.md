---
title: Azure Veri Kataloğu 'nda veri kaynaklarını belgeleme
description: Azure Veri Kataloğu 'nda veri varlıklarının nasıl belgeleneceğini vurgulayan nasıl yapılır makalesi.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950198"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri kaynaklarını belgeleme

## <a name="introduction"></a>Giriş
**Microsoft Azure Veri Kataloğu** , kurumsal veri kaynakları için bir kayıt sistemi ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, **Azure Veri Kataloğu** , kullanıcıların veri kaynaklarını bulmasına, *anlamasına*ve kullanmasına yardımcı olur ve kuruluşların mevcut verilerinden daha fazla değer almasını sağlar.

Bir veri kaynağı **Azure Veri Kataloğu 'na**kaydedildiğinde, meta verileri hizmet tarafından kopyalanıp dizinlenir, ancak hikaye burada sonlanmaz. **Azure Veri Kataloğu** , kullanıcıların veri kaynağı için kullanımı ve yaygın senaryoları betimleyen, kendi kendine ait tüm belgeleri sağlamasına de olanak tanır.

[Veri kaynaklarına açıklama ekleme](data-catalog-how-to-annotate.md)bölümünde, veri kaynağını bilen uzmanların etiketlere ve açıklamaya açıklama ekleyebileceğinizi öğrenirsiniz. **Azure Veri Kataloğu** portalı, kullanıcıların veri varlıklarını ve kapsayıcıları tam olarak belgeedebilmesi için zengin bir metin Düzenleyicisi içerir. Düzenleyici, başlıklar, metin biçimlendirme, madde işaretli listeler, numaralandırılmış listeler ve tablolar gibi paragraf biçimlendirme içerir.

Etiketler ve açıklamalar basit ek açıklamalar için harika. Bununla birlikte, veri tüketicilerinin kullanımını ve veri kaynağı için iş senaryolarını daha iyi anlamasına yardımcı olmak için uzman, ayrıntılı belgeler sağlayabilir. Bir veri kaynağını belgelemek kolaydır. Bir veri varlığı veya kapsayıcısı seçip **Belgeler**' i seçin.

![Veri kataloğunda belge sekmesi](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Veri varlıklarını belgeleme
**Azure Veri Kataloğu** belgelerinin avantajı, veri varlıklarınızın tamamını bir anlatım oluşturmak Için veri kataloğunuzu bir içerik deposu olarak kullanmanıza olanak tanır. Kapsayıcıları ve tabloları açıklayan ayrıntılı içeriği inceleyebilirsiniz. SharePoint veya dosya paylaşma gibi başka bir içerik deposunda içeriğiniz zaten varsa, bu var olan içeriğe başvurmak için varlık belgeleri bağlantılarına ekleyebilirsiniz. Bu özellik, mevcut belgelerinizi daha fazla bulunabilir hale getirir.

> [!NOTE]
> Belgeler arama dizinine dahil değildir.
>

![Belge sekmesi ve Web bağlantısına köprü](media/data-catalog-documentation/data-catalog-documentation2.png)

Belge düzeyi, bir veri varlığı kapsayıcısının özelliklerini ve değerini, bir kapsayıcı içindeki tablo şemasının ayrıntılı açıklamasıyla açıklayarak değişebilir. Sağlanan belgelerin düzeyi iş gereksinimlerinize göre yönlendirilmelidir. Ancak genel olarak, veri varlıklarının belgelenme ve diğer dezavantajları aşağıda verilmiştir:

* Yalnızca bir kapsayıcıyı belgeleyin: Tüm içerikler tek bir yerde bulunur, ancak kullanıcıların bilinçli bir karar vermesini sağlamak için gerekli ayrıntıların olması gerekebilir.
* Yalnızca tabloları belgeleyin: İçerik bu nesneye özeldir, ancak kullanıcılarınız belgeler için birden çok yere sahiptir.
* Belge kapsayıcıları ve tabloları: En kapsamlı yaklaşım, ancak belgelerin daha fazla bakımını gösterebilir.

## <a name="summary"></a>Özet
Veri kaynaklarını **Azure Veri Kataloğu** ile belgelemek, veri varlıklarınız hakkında, ihtiyacınız olan kadar ayrıntılı bir anlatım oluşturabilir.  Bağlantıları kullanarak, mevcut docs ve veri varlıklarınızı bir araya getiren varolan bir içerik deposunda depolanan içeriğe bağlanabilirsiniz. Kullanıcılarınız uygun veri varlıklarını bulduktan sonra, tüm belge kümesine sahip olabilirler.
