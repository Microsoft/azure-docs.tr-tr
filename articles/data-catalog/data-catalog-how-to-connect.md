---
title: Azure Veri Kataloğu 'nda veri kaynaklarına bağlanma
description: Nasıl yapılır makalesi Azure Veri Kataloğu ile keşfedilen veri kaynaklarına nasıl bağlanılacağını vurgulayın.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976779"
---
# <a name="how-to-connect-to-data-sources"></a>Veri kaynaklarına bağlanma
## <a name="introduction"></a>Giriş
**Microsoft Azure Veri Kataloğu** , kurumsal veri kaynakları için bir kayıt sistemi ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, **Azure Veri Kataloğu** , kullanıcıların veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olur ve kuruluşların mevcut verilerinden daha fazla değer almasını sağlar. Bu senaryonun önemli bir yönü, verileri kullanır. Kullanıcı bir veri kaynağını bulduktan ve amacını anladıktan sonra, bir sonraki adım veri kaynağına bağlanmak için veri kaynağına bağlanıyacaktır.

## <a name="data-source-locations"></a>Veri kaynağı konumları
Veri kaynağı kaydı sırasında, **Azure Veri Kataloğu** veri kaynağıyla ilgili meta verileri alır. Bu meta veriler, veri kaynağının konumunun ayrıntılarını içerir. Konumun ayrıntıları veri kaynağından veri kaynağına farklılık gösterecektir, ancak her zaman bağlanmak için gereken bilgileri içerir. Örneğin, bir SQL Server tablosunun konumu sunucu adı, veritabanı adı, şema adı ve tablo adını içerir, ancak bir SQL Server Reporting Services raporunun konumu sunucu adını ve raporun yolunu içerir. Diğer veri kaynağı türlerinde, kaynak sistemin yapısını ve yeteneklerini yansıtan konumlar olacaktır.

## <a name="integrated-client-tools"></a>Tümleşik istemci araçları
Bir veri kaynağına bağlanmak için en basit yol, "içinde aç..." öğesini kullanmaktır. **Azure Veri Kataloğu** portalındaki menü. Bu menü, seçili veri varlığına bağlanma seçeneklerinin listesini görüntüler.
Varsayılan kutucuk görünümü kullanılırken, bu menü her kutucukta kullanılabilir.

 ![Veri varlık kutucuğunda Excel 'de bir SQL Server tablosu açma](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Liste görünümü kullanılırken, menü, Portal penceresinin üst kısmındaki arama çubuğunda bulunur.

 ![Rapor Yöneticisi bir SQL Server Reporting Services raporu açma](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Desteklenen Istemci uygulamaları
"İçinde aç..." Azure Veri Kataloğu portalındaki veri kaynakları için menü, istemci bilgisayarda doğru istemci uygulaması yüklü olmalıdır.

| Uygulamada aç | Dosya Uzantısı/protokol | Desteklenen uygulama sürümleri |
| --- | --- | --- |
| Excel |. odc |Excel 2010 veya üzeri |
| Excel (En Iyi 1000) |. odc |Excel 2010 veya üzeri |
| Power Query |. xlsx |Excel 2016 veya Excel 2010 veya Excel 2013, Power Query Excel eklentisi yüklü |
| Power BI Desktop |. pbix |2016 Temmuz veya üzeri Power BI Desktop |
| SQL Server Veri Araçları |vsweb:// |SQL Server araçları 'ın yüklü olduğu Visual Studio 2013 güncelleştirme 4 veya üzeri |
| Rapor Yöneticisi |http:// |[SQL Server Reporting Services için tarayıcı gereksinimlerini](https://technet.microsoft.com/library/ms156511.aspx) inceleyin |

## <a name="your-data-your-tools"></a>Verileriniz, araçlarınız
Menüdeki seçenekler şu anda seçili olan veri varlığının türüne bağlıdır. Kuşkusuz, olası tüm araçlar "aç..." da dahil edilmez. , ancak herhangi bir istemci Aracı kullanılarak veri kaynağına bağlanmak hala kolaydır. **Azure Veri Kataloğu** portalında bir veri varlığı seçildiğinde, tüm konum Özellikler bölmesinde görüntülenir.

 ![SQL Server tablosu için bağlantı bilgileri](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Bağlantı bilgisi ayrıntıları veri kaynağı türünden veri kaynağı türüne göre farklılık gösterir, ancak portalda yer alan bilgiler, herhangi bir istemci aracında veri kaynağına bağlanmak için ihtiyacınız olan her şeyi verecektir. Kullanıcılar, **Azure Veri Kataloğu**' nu kullanarak bulduğu veri kaynakları için bağlantı ayrıntılarını kopyalayabilir ve bu kişilerin istedikleri aracında verilerle çalışmasını sağlar.

## <a name="connecting-and-data-source-permissions"></a>Bağlama ve veri kaynağı izinleri
**Azure Veri Kataloğu** veri kaynaklarını bulunabilir hale getiriyor olsa da veri erişimi veri kaynağı sahibinin veya yöneticisinin denetiminde kalır. **Azure Veri Kataloğu** 'nda bir veri kaynağını keşfetmek, kullanıcıya veri kaynağının kendine erişim izinleri vermez.

Bir veri kaynağını keşfettiğiniz ancak verilerine erişim izni olmayan kullanıcıların, bir veri kaynağına açıklama eklerken Istek erişimi özelliğinde bilgi sağlayabilmesi için. Burada verilen bilgiler – veri kaynağı erişimi kazanmak için işlem veya iletişim noktası bağlantıları dahil olmak üzere, portaldaki veri kaynağı konumu bilgileri ile birlikte sunulur.

 ![Girilen istek erişimi yönergelerine sahip bağlantı bilgileri](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Özet
Veri kaynağını **Azure Veri Kataloğu** ile kaydetmek, veri kaynağından Katalog hizmetine yapısal ve açıklayıcı meta verileri kopyalayarak bu verilerin keşfedilmesini sağlar. Bir veri kaynağı kaydedildikten ve keşfedildiğinde, kullanıcılar **Azure Veri Kataloğu** portalından "aç..." ' dan veri kaynağına bağlanabilir. menü veya tercih ettiğiniz veri araçlarını kullanma.

## <a name="see-also"></a>Ayrıca bkz.
* Veri kaynaklarına bağlanma hakkında adım adım ayrıntılar için [Azure Veri Kataloğu](data-catalog-get-started.md) öğreticisini kullanmaya başlayın.
