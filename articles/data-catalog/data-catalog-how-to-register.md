---
title: Azure Veri Kataloğu 'nda veri kaynaklarını kaydetme
description: Bu makalede, kayıt sırasında ayıklanan meta veri alanları da dahil olmak üzere Azure Veri Kataloğu 'nda veri kaynaklarının nasıl kaydedileceği vurgulanmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736314"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri kaynaklarını kaydetme
## <a name="introduction"></a>Giriş
Azure Veri Kataloğu, kurumsal veri kaynakları için kayıt ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, Veri Kataloğu, kullanıcıların veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olur ve kuruluşların mevcut verilerinden daha fazla değer almasını sağlar. Veri Kataloğu aracılığıyla bulunabilir hale getirmek için ilk adım, veri kaynağını kaydetmektir.

## <a name="register-data-sources"></a>Veri kaynaklarını kaydetme
Kayıt, veri kaynağından meta verileri ayıklama ve bu verileri veri kataloğu hizmetine kopyalama işlemidir. Veriler o anda bulunduğu yerde kalır ve geçerli sistemin yöneticilerinin ve ilkelerinin denetiminde olmaya devam eder.

Bir veri kaynağını kaydetmek için aşağıdakileri yapın:
1. Azure Veri Kataloğu portalında, Veri Kataloğu veri kaynağı kayıt aracını başlatın. 
2. Portalda oturum açmak için kullandığınız Azure Active Directory kimlik bilgileriyle İş veya okul hesabınızla oturum açın.
3. Kaydetmek istediğiniz veri kaynağını seçin.

Daha fazla adım adım ayrıntılar için bkz. [Azure Veri Kataloğu Ile çalışmaya başlama](data-catalog-get-started.md) öğreticisi.

Veri kaynağını kaydettikten sonra, katalog konumunu izler ve meta verilerini dizine ekler. Kullanıcılar veri kaynağını arayabilir, gözatabilir ve bulabilir ve sonra kendi tercih ettikleri uygulamayı veya aracını kullanarak bu konuma bağlanmak için konumunu kullanabilir.

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları
Şu anda desteklenen veri kaynaklarının listesi için bkz. [Veri Kataloğu DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Yapısal meta veriler
Bir veri kaynağını kaydettiğinizde, kayıt aracı seçtiğiniz nesnelerin yapısı hakkında bilgi ayıklar. Bu bilgilere yapısal meta veriler denir.

Tüm nesneler için, bu yapısal meta veriler nesnenin konumunu içerir, böylece verileri keşfettiğiniz kullanıcılar bu bilgileri istedikleri istemci araçlarındaki nesneye bağlamak için kullanabilir. Diğer yapısal meta veriler, nesne adı ve türü, öznitelik/sütun adı ve veri türü içerir.

## <a name="descriptive-metadata"></a>Açıklayıcı meta veriler
Veri kaynağından ayıklanan çekirdek yapısal meta verilere ek olarak, veri kaynağı kayıt aracı açıklayıcı meta verileri ayıklar. SQL Server Analysis Services ve SQL Server Reporting Services için bu meta veriler, bu hizmetler tarafından sunulan açıklama özelliklerinden alınmıştır. SQL Server için, MS\_Description Genişletilmiş özelliği kullanılarak girilen değerler ayıklanır. Oracle Database için veri kaynağı kayıt aracı, açıklamalar sütununu tüm\_sekme\_açıklamaları görünümünden ayıklar.

Veri kaynağından ayıklanan açıklayıcı meta verilere ek olarak, kullanıcılar veri kaynağı kayıt aracını kullanarak açıklayıcı meta verileri girebilir. Kullanıcılar Etiketler ekleyebilir ve kayıtlı nesneler için uzmanlar tanımlayabilir. Bu açıklayıcı meta veriler, veri kataloğu hizmetine yapısal meta verilerle birlikte kopyalanır.

## <a name="include-previews"></a>Önizlemeleri dahil et
Varsayılan olarak, veri kaynaklarından yalnızca meta veriler ayıklanır ve veri kataloğu hizmetine kopyalanır, ancak içerdiği verilerin bir örneğini görüntüleyebilmeniz durumunda veri kaynağını anlamak genellikle daha kolay hale getirilir.

Veri Kataloğu veri kaynağı kayıt aracı ' nı kullanarak, her tablo ve görünümdeki verilerin anlık görüntü önizlemesini ekleyebilirsiniz. Kayıt sırasında önizlemeleri eklemeyi seçerseniz, kayıt aracı her tablo ve görünümden en fazla 20 kayıt içerir. Bu anlık görüntü, yapısal ve açıklayıcı meta verilerle birlikte kataloğa kopyalanır.

> [!NOTE]
> Çok sayıda sütun içeren geniş tablolarda, önizlemelerinde 20 ' den az kayıt olabilir.
>
>

## <a name="include-data-profiles"></a>Veri profillerini dahil et
Önizlemeler dahil olmak üzere veri kataloğunda veri kaynakları arama yapan kullanıcılar için değerli bağlam sağlayabildiği gibi, veri profili de dahil olmak üzere, bulunan veri kaynaklarının anlaşılması kolaylaşır.

Veri Kataloğu veri kaynağı kayıt aracını kullanarak, kayıtlı her tablo ve görünüm için bir veri profili ekleyebilirsiniz. Kayıt sırasında bir veri profili eklemeyi tercih ederseniz, kayıt aracı her tablo ve görünümdeki verilerle ilgili toplam istatistikler içerir; örneğin:

* Nesnedeki verilerin satır ve boyut sayısı.
* Verilerin ve nesne şemasının en son güncelleştirmesine ilişkin tarih.
* Sütunlar için null kayıt sayısı ve ayrı değerler.
* Sütunlar için minimum, maksimum, ortalama ve standart sapma değerleri.

Bu istatistikler daha sonra, yapısal ve açıklayıcı meta verilerle birlikte kataloğa kopyalanır.

> [!NOTE]
> Metin ve Tarih sütunları, veri profilinde ortalama veya standart sapma istatistiklerini içermez.
>
>

## <a name="update-registrations"></a>Kayıtları güncelleştir
Veri kaynağı kaydı, kayıt sırasında ayıklanan meta verileri ve isteğe bağlı önizlemeyi kullandığınızda veri kataloğunda bulunabilir hale getirir. Veri kaynağının katalogda güncelleştirilmesi gerekiyorsa (örneğin, bir nesnenin şeması değiştiyse, ilk dışlanan tablolar dahil edilmelidir veya önizlemelerde yer alan verileri güncelleştirmek istiyorsanız), veri kaynağı kayıt aracı yeniden çalıştırılabilir.

Zaten kaydedilmiş bir veri kaynağının yeniden kaydedilmesi birleştirme "upsert" işlemi gerçekleştirir: varolan nesneler güncellenir ve yeni nesneler oluşturulur. Veri Kataloğu portalı aracılığıyla kullanıcılar tarafından sunulan tüm meta veriler korunur.

## <a name="summary"></a>Özet
Bir veri kaynağından Katalog hizmetine yapısal ve açıklayıcı meta verileri kopyalayan için veri kataloğunda veri kaynağını kaydetmek verilerin keşfedilmesine ve anlaşılmasını kolaylaştırır. Veri kaynağını kaydettikten sonra, veri kataloğu portalını kullanarak açıklama ekleyebilir, yönetebilir ve keşfedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Veri kaynaklarını kaydetme hakkında daha fazla bilgi için bkz. [Azure Veri Kataloğu Ile çalışmaya başlama](data-catalog-get-started.md) öğreticisi.
