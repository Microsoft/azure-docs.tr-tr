---
title: Azure Veri Kataloğu terminolojisi
description: Bu makalede, Azure Veri Kataloğu belgelerinde kullanılan kavramlar ve koşullara bir giriş sunulmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736283"
---
# <a name="azure-data-catalog-terminology"></a>Azure Veri Kataloğu terminolojisi

Bu makalede, Azure Veri Kataloğu belgelerinde kullanılan kavramlar ve koşullara bir giriş sunulmaktadır.

## <a name="catalog"></a>Katalog

Azure Veri Kataloğu, veri kaynakları ve veri varlıklarının kaydedileceği bulut tabanlı bir meta veri deposudur. Katalog, veri kaynaklarından ayıklanan yapısal meta veriler için ve kullanıcılar tarafından eklenen açıklayıcı meta veriler için merkezi bir depolama konumu görevi görür.

## <a name="data-source"></a>Veri kaynağı

Veri kaynağı, veri varlıklarını yöneten bir sistem veya kapsayıcıdır. Örnekler arasında SQL Server veritabanları, Oracle veritabanları, SQL Server Analysis Services veritabanları (Tablolu veya çok boyutlu) ve SQL Server Reporting Services sunucuları verilebilir.

## <a name="data-asset"></a>Veri varlığı

Veri varlıkları, katalog ile kaydedilenebilir veri kaynakları içinde yer alan nesnelerdir. SQL Server tabloları ve görünümleri, Oracle tabloları ve görünümleri, SQL Server Analysis Services ölçüleri, boyutları ve KPI 'Leri ve SQL Server Reporting Services raporları sayılabilir.

## <a name="data-asset-location"></a>Veri varlık konumu

Katalog, bir veri kaynağının veya veri varlığının konumunu depolar ve bu, bir istemci uygulaması kullanılarak kaynağa bağlanmak için kullanılabilir. Konumun biçimi ve ayrıntıları, veri kaynağı türüne göre farklılık gösterir. Örneğin, bir SQL Server tablosu, dört bölüm adı – sunucu adı, veritabanı adı, şema adı, nesne adı ile tanımlanabilir, ancak bir SQL Server Reporting Services raporu URL 'SI ile tanımlanabilir.

## <a name="structural-metadata"></a>Yapısal meta veriler

Yapısal meta veriler, bir veri varlığının yapısını açıklayan bir veri kaynağından ayıklanan meta verilerdir. Bu, varlık konumunu, nesne adını ve türünü ve türe özgü ek özellikleri içerir. Örneğin, tablolar ve görünümler için yapısal meta veriler, nesne sütunlarının adlarını ve veri türlerini içerir.

## <a name="descriptive-metadata"></a>Açıklayıcı meta veriler

Açıklayıcı meta veriler, bir veri varlığının amacını veya amacını tanımlayan meta verileridir. Genellikle, Azure Veri Kataloğu Portalı kullanılarak Katalog kullanıcıları tarafından açıklayıcı meta veriler eklenir, ancak kayıt sırasında veri kaynağından da ayıklanamaz. Örneğin, Azure Veri Kataloğu kayıt aracı, SQL Server Analysis Services ve SQL Server Reporting Services içindeki Description özelliğinden ve SQL Server veritabanlarındaki [MS_Description Extended özelliğinden](https://technet.microsoft.com/library/ms190243.aspx) açıklamaları ayıklar, ancak Bu özellikler değerlerle doldurulmuştur.

## <a name="request-access"></a>Erişim izni iste

Veri varlığının açıklayıcı meta verileri, veri varlığına veya veri kaynağına erişim isteme hakkında bilgi içerebilir. Bu bilgiler, veri varlık konumuyla birlikte sunulur ve aşağıdaki seçeneklerden birini veya daha fazlasını içerebilir:

* Veri kaynağına erişim vermekten sorumlu kullanıcı veya takımın e-posta adresi.
* Veri kaynağına erişim kazanmak için kullanıcıların izlemesi gereken belgelenmiş işlemin URL 'SI.
* Veri kaynağına erişim kazanmak için kullanılabilen bir kimlik ve erişim yönetimi aracının URL 'SI (örneğin Microsoft Identity Manager).
* Kullanıcıların veri kaynağına nasıl erişim kazanabileceğinizi açıklayan bir serbest metin girişi.

## <a name="preview"></a>Önizleme

Azure Veri Kataloğu 'ndaki önizleme, kayıt sırasında veri kaynağından Ayıklanacak ve veri varlık meta verileri ile katalogda depolanabilen 20 ' ye kadar kaydın bir anlık görüntüsüdür. Önizleme, bir veri varlığını keşfettiğiniz kullanıcıların işlevini ve amacını daha iyi anlamasına yardımcı olabilir. Diğer bir deyişle, örnek verileri görmek yalnızca sütun adlarını ve veri türlerini görmekten daha değerli olabilir.
Önizlemeler yalnızca tablolar ve görünümler için desteklenir ve kayıt sırasında Kullanıcı tarafından açıkça seçilmelidir.

## <a name="data-profile"></a>Veri Profili

Azure Veri Kataloğu 'ndaki bir veri profili, kayıt sırasında veri kaynağından ayıklanabilen ve veri varlığı meta verileriyle Katalogda depolanan kayıtlı bir veri varlığı hakkındaki tablo düzeyi ve sütun düzeyi meta verilerin bir anlık görüntüsüdür. Veri profili, bir veri varlığını keşfettiğiniz kullanıcıların işlevini ve amacını daha iyi anlamasına yardımcı olabilir. Önizlemelere benzer şekilde, veri profillerinin kayıt sırasında Kullanıcı tarafından açıkça seçilmesi gerekir.

> [!NOTE]
> Veri profilini ayıklamak büyük tablolar ve görünümler için maliyetli bir işlem olabilir ve bir veri kaynağını kaydetmek için gereken süreyi önemli ölçüde artırabilir.


## <a name="user-perspective"></a>Kullanıcı perspektifi

Azure veri kataloğunda, tüm kullanıcılar kayıtlı bir veri varlığı için açıklayıcı meta veriler sağlayabilir. Her kullanıcının verileri ve kullanımı üzerinde ayrı bir perspektifi vardır. Örneğin, bir sunucudan sorumlu yönetici, hizmet düzeyi sözleşmesi (SLA) veya yedekleme pencerelerinin ayrıntılarını sağlayabilir; veri, verilerin desteklediği iş süreçlerine yönelik belgelere bağlantılar verebilir; Analist, diğer analistlerle ilgili şartlar için bir açıklama sağlayabilir ve bu, verileri bulması ve anlaması gereken kullanıcılara en değerli olabilir.

Bu perspektiflerin her biri, doğal olarak değerlidir ve Azure Veri Kataloğu ile her bir Kullanıcı, verileri ve amacını anlamak için bu bilgileri kullanabilir.

## <a name="expert"></a>Uzman

Uzman, bir veri varlığı için bilinçli "uzman" perspektifine sahip olarak tanımlanmış bir kullanıcı. Herhangi bir Kullanıcı, kendisini veya başka bir kullanıcıyı bir varlık için uzman olarak ekleyebilir. Uzman olarak listelenmekte olan Azure Veri Kataloğu 'nda hiçbir ek ayrıcalık iletmiyor; Kullanıcıların, bir varlığın açıklayıcı meta verilerini gözden geçirirken yararlı olması gereken perspektifleri kolayca bulmasını sağlar.

## <a name="owner"></a>Sahip

Sahip, Azure Veri Kataloğu 'nda bir veri varlığını yönetmek için ek ayrıcalıklara sahip bir kullanıcı. Kullanıcılar kayıtlı veri varlıklarının sahipliğini alabilir ve sahipler diğer kullanıcıları ikincil sahipler olarak ekleyebilir. Daha fazla bilgi için bkz. [veri varlıklarını yönetme](data-catalog-how-to-manage.md)  

> [!NOTE]
> Sahiplik ve yönetim yalnızca Azure Veri Kataloğu 'nun standart sürümünde kullanılabilir.

## <a name="registration"></a>Kayıt

Kayıt, veri varlığı meta verilerini bir veri kaynağından ayıklayarak Azure Veri Kataloğu hizmetine kopyalamadır. Daha sonra kayıtlı olan veri varlıkları açıklaneklenebilir ve bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı Başlangıç: Azure Veri Kataloğu oluşturma](data-catalog-get-started.md) 
