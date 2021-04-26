---
title: Azure Veri Kataloğu 'nda veri profili oluşturma veri kaynaklarını kullanma
description: Nasıl yapılır makalesi Azure Veri Kataloğu 'nda veri kaynaklarını kaydederken tablo ve sütun düzeyi veri profillerinin nasıl ekleneceğini ve veri kaynaklarını anlamak için veri profillerinin nasıl kullanılacağını vurgulamaktır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 473ba69fa30542dfa41dfc5f19002ac36b56b482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674963"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri profili veri kaynakları

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Giriş

**Microsoft Azure Veri Kataloğu** , kurumsal veri kaynakları için bir kayıt sistemi ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, **Azure Veri Kataloğu** , kullanıcıların veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olur ve kuruluşların mevcut verilerinden daha fazla değer almasını sağlar. Bir veri kaynağı **Azure Veri Kataloğu 'na** kaydedildiğinde, meta verileri hizmet tarafından kopyalanıp dizinlenir, ancak hikaye burada sonlanmaz.

**Azure Veri Kataloğu** 'Nun **veri profili oluşturma** özelliği, kataloğunuzdaki desteklenen veri kaynaklarından verileri inceler ve bu verilerle ilgili istatistikleri ve bilgileri toplar. Veri varlıklarınızın profilini eklemek kolaydır. Bir veri varlığını kaydettiğinizde veri kaynağı kayıt aracında **veri profilini dahil et** ' i seçin.

## <a name="what-is-data-profiling"></a>Veri profili oluşturma nedir?

Veri profili kaydı yapılan veri kaynağındaki verileri inceler ve bu verilerle ilgili istatistikleri ve bilgileri toplar. Veri kaynağı bulma sırasında, bu istatistikler iş sorununu çözmek üzere verilerin uygunluğunu belirlemenize yardımcı olabilir.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Aşağıdaki veri kaynakları, veri profilini oluşturmayı destekler:

* SQL Server (Azure SQL DB ve Azure SYNAPSE Analytics dahil) tabloları ve görünümleri
* Oracle tabloları ve görünümleri
* Teradata tabloları ve görünümleri
* Hive tabloları

Veri varlıklarını kaydederken veri profillerinin dahil edilmesi, kullanıcıların aşağıdakiler de dahil olmak üzere veri kaynaklarıyla ilgili sorularınızı yanıtlamasını sağlar:

* İş sorunumu çözmek için kullanılabilir mi?
* Veriler belirli standartlara veya desenlere uygun mı?
* Veri kaynağının bazı bozukluklar nelerdir?
* Bu verileri uygulamamda tümleştirmeyle ilgili olası sorunlar nelerdir?

> [!NOTE]
> Ayrıca, verilerin bir uygulamayla nasıl tümleştirilebileceğini betimleyen bir varlığa belgeler ekleyebilirsiniz. Bkz. [veri kaynaklarını belgeleme](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Veri kaynağı kaydedilirken veri profili ekleme

Veri kaynağınızın bir profilini eklemek kolaydır. Veri kaynağını kaydettiğinizde, veri kaynağı kayıt aracının **kaydedileceği nesneler** panelinde **veri profilini dahil et**' i seçin.

![Veri profilini dahil et onay kutusu](media/data-catalog-data-profile/data-catalog-register-profile.png)

Veri kaynaklarını kaydetme hakkında daha fazla bilgi edinmek için bkz. [veri kaynaklarını kaydetme](data-catalog-how-to-register.md) ve [Azure Veri Kataloğu ile çalışmaya başlama](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Veri profillerini içeren veri varlıklarının filtrelenmesi

Bir veri profili içeren veri varlıklarını bulmak için `has:tableDataProfiles` `has:columnsDataProfiles` arama koşullarınızdan birini ekleyebilirsiniz.

> [!NOTE]
> Veri kaynağı kayıt aracında **veri profili Ekle** ' nin seçilmesi, hem tablo hem de sütun düzeyi profil bilgilerini içerir. Ancak, veri kataloğu API 'SI, veri varlıklarının yalnızca tek bir profil bilgileri kümesiyle kaydedilmesini sağlar.
>

## <a name="viewing-data-profile-information"></a>Veri profili bilgilerini görüntüleme

Bir profille uygun bir veri kaynağı bulduktan sonra, veri profili ayrıntılarını görüntüleyebilirsiniz. Veri profilini görüntülemek için bir veri varlığı seçin ve veri kataloğu portalı penceresinde **veri profili** ' ni seçin.

![Veri profili sekmesi](media/data-catalog-data-profile/data-catalog-view.png)

**Azure Veri Kataloğu** 'ndaki bir veri profili, aşağıdakiler dahil olmak üzere tablo ve sütun profili bilgilerini gösterir:

### <a name="object-data-profile"></a>Nesne veri profili

* Satır sayısı
* Tablo boyutu
* Nesne son güncelleştirildiği zaman

### <a name="column-data-profile"></a>Sütun veri profili

* Sütun veri türü
* Farklı değer sayısı
* NULL değerli satır sayısı
* Sütun değerleri için minimum, maksimum, ortalama ve standart sapma

## <a name="summary"></a>Özet

Veri profili oluşturma, iş sorunlarını çözmeye yönelik verilerin uygunluğunu belirlemenize yardımcı olmak üzere kayıtlı veri varlıkları hakkında istatistikler ve bilgiler sağlar. Veri profillerini açıklama ekleme ve belgeleme ile birlikte, veri Profilleri kullanıcılara verilerinizi daha ayrıntılı bir şekilde anlayabilirler.

## <a name="see-also"></a>Ayrıca Bkz.

* [Veri kaynaklarını kaydetme](data-catalog-how-to-register.md)
* [Azure Veri Kataloğu ile çalışmaya başlama](data-catalog-get-started.md)
