---
title: Azure Sentinel 'de verileri sınıflandırmak ve analiz etmek için varlıkları kullanma | Microsoft Docs
description: Azure Sentinel 'deki veri öğelerine varlık sınıflandırmaları (kullanıcılar, ana bilgisayar adları, IP adresleri) atayın ve bunları birden çok kaynaktaki verileri karşılaştırmak, analiz etmek ve ilişkilendirmek için kullanın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456338"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Azure Sentinel 'de varlıkları kullanarak verileri sınıflandırma ve çözümleme

## <a name="what-are-entities"></a>Varlıklar nelerdir?

Uyarılar Azure Sentinel 'e gönderildiğinde veya oluşturulduğunda, Sentinel 'in kategori olarak kategorilere göre tanıyabileceği ve sınıflandırıp sınıflandırtıkları veri öğelerini içerirler **.** Azure Sentinel belirli bir veri öğesinin ne tür bir varlık temsil ettiğini anladığı zaman, sorun hakkında doğru soruları bilir ve daha sonra bu öğeyle ilgili öngörüleri tüm veri kaynakları genelinde karşılaştırabilir ve daha sonra tüm Sentinel deneyim-analiz, araştırma, düzeltme, arama, arama, arama, arama, arama, arama, arama, arama ve benzerlerini ele alabilir. Bazı yaygın varlık örnekleri şunlardır; kullanıcılar, konaklar, dosyalar, süreçler, IP adresleri ve URL 'Ler.

### <a name="entity-identifiers"></a>Varlık tanımlayıcıları

Azure Sentinel, çok çeşitli varlık türlerini destekler. Her türün, belirli bir varlığı tanımlamak için kullanılabilecek bir de dahil olmak üzere kendi benzersiz öznitelikleri vardır. Bu öznitelikler varlıktaki alanlar olarak temsil edilir ve **tanımlayıcılar** olarak adlandırılır. Desteklenen varlıkların tam listesini ve bunların tanımlayıcılarını aşağıda görebilirsiniz.

#### <a name="strong-and-weak-identifiers"></a>Güçlü ve zayıf tanımlayıcılar

Yukarıda belirtildiği gibi, her varlık türü için, alan veya alan kümeleri için, bunu tanımlayabilirler. Bu alanlara veya alan kümelerine, herhangi bir belirsizlik olmadan bir varlığı benzersiz bir şekilde tanımlayabilmeleri veya bazı durumlarda bir varlığı **benzersiz olarak tanımlamak** için garanti etmeseler, bu alanlar veya alan kümeleri **tanımlayıcı tanımlayıcılar** olarak adlandırılabilir. Çoğu durumda, bir güçlü tanımlayıcı oluşturmak için zayıf tanımlayıcıların bir seçimi birleştirilebilir.

Örneğin, Kullanıcı hesapları farklı bir şekilde **Hesap** varlıkları olarak tanımlanabilir: BIR Azure AD hesabının sayısal tanımlayıcısı ( **GUID** alanı) veya **Kullanıcı asıl adı (UPN)** değeri gibi tek bir **güçlü** tanımlayıcı veya alternatif olarak, **ad** ve **ntdomain** alanları gibi **zayıf tanımlayıcıların** bir birleşimini kullanarak. Farklı veri kaynakları aynı kullanıcıyı farklı yollarla tanımlayabilir. Azure Sentinel, tanımlayıcılarına göre aynı varlık olarak tanıyabileceği iki varlıkla karşılaştığında, doğru ve tutarlı bir şekilde işlenebilmesi için iki varlığı tek bir varlıkta birleştirir.

Ancak, kaynak sağlayıcılarınızın biri bir varlığın yeterince tanımlanmadığından bir uyarı oluşturursa (örneğin, etki alanı adı bağlamı olmadan Kullanıcı adı gibi tek bir **zayıf tanımlayıcıyı** kullanarak), kullanıcı varlığı aynı kullanıcı hesabının diğer örnekleriyle birleştirilemez. Bu diğer örnekler ayrı bir varlık olarak tanımlanır ve bu iki varlık Birleşik yerine ayrı olarak kalır.

Bu meydana gelme riskini en aza indirmek için, tüm uyarı sağlayıcılarınızın oluşturdukları uyarılarda varlıkları doğru şekilde tanımladıklarından emin olmalısınız. Ayrıca, Kullanıcı hesabı varlıklarının Azure Active Directory ile eşitlenmesi, Kullanıcı hesabı varlıklarını birleştirebilecek bir dizin oluşturamayacak.

#### <a name="supported-entities"></a>Desteklenen varlıklar

Aşağıdaki varlık türleri şu anda Azure Sentinel 'de tanımlanmıştır:

- Kullanıcı hesabı
- Ana bilgisayar
- IP Adresi
- Kötü Amaçlı Yazılımlar
- Dosya
- İşleme
- Bulut uygulaması
- Etki alanı adı
- Azure kaynağı
- Dosya karması
- Kayıt defteri anahtarı
- Kayıt defteri değeri
- Güvenlik grubu
- URL
- IoT cihazı
- Mailbox
- Posta kümesi
- Posta iletisi
- Gönderim postası

[Varlıklar başvurusunda](entities-reference.md)bu varlıkların tanımlayıcılarını ve diğer ilgili bilgileri görüntüleyebilirsiniz.

## <a name="entity-mapping"></a>Varlık eşleme

Azure Sentinel bir uyarı içindeki bir veri parçasını bir varlığı tanımlayarak nasıl tanır?

Azure Sentinel 'de veri işlemenin nasıl yapıldığına göz atalım. Veriler, hizmet-hizmet, aracı tabanlı veya bir Syslog hizmeti ve bir günlük ileticisi kullanılarak, [Bağlayıcılar](connect-data-sources.md)aracılığıyla çeşitli kaynaklardan alınır. Veriler Log Analytics çalışma alanınızdaki tablolarda depolanır. Daha sonra bu tablolar, tanımladığınız ve etkinleştirdiğiniz analiz kuralları tarafından düzenli olarak zamanlanmış aralıklarla sorgulanır. Bu analiz kuralları tarafından gerçekleştirilen birçok eylemden biri, tablolardaki veri alanlarının Azure Sentinel tarafından tanınan varlıklara eşlenmesinin bir sayısıdır. Analiz kurallarında tanımladığınız eşlemelere göre, Azure Sentinel, sorgunuz tarafından döndürülen sonuçlardan alanları alır, bunları her varlık türü için belirttiğiniz tanımlayıcılara göre tanır ve bunlara bu tanımlayıcılar tarafından tanımlanan varlık türüne uygulanır.

Bunun noktası nedir?

Azure Sentinel, farklı türlerdeki veri kaynaklarından gelen uyarılarda varlıkları tanımlayabiliyor ve özellikle de her bir veri kaynağı veya üçüncü bir şema için ortak olan tanımlayıcı tanımlayıcıları kullanıyorsa, bu uyarıların ve veri kaynaklarının tümü arasında kolayca ilişki kurabilir. Bu bağıntılar, güvenlik işlemleriniz için size katı bir temel sunarak, varlıklar üzerinde zengin bilgi ve Öngörüler oluşturmanıza yardımcı olur.

[Veri alanlarını varlıklara nasıl eşleyeceğinizi](map-data-fields-to-entities.md)öğrenin.

[Hangi tanımlayıcıların bir varlığı kesin olarak tanımlacağınızı](entities-reference.md)öğrenin.

## <a name="entity-pages"></a>Varlık sayfaları

Bir aramada, bir uyarıda veya bir araştırmada herhangi bir varlık (Şu anda kullanıcılarla ve konaklarla sınırlı) ile karşılaşırsanız, varlığı seçebilir ve bu varlıkla ilgili yararlı bilgiler içeren bir veri sayfası olan varlık **sayfasına** yönlendirilirsiniz. Bu sayfada bulacağınız bilgi türleri, varlıkla ilgili temel gerçekleri, bu varlıkla ilgili önemli olayların bir zaman çizelgesini ve varlığın davranışı hakkında öngörüleri içerir.

Varlık sayfaları üç bölümden oluşur:

- Sol taraftaki panel, varlığın Azure Active Directory, Azure Izleyici, Azure Güvenlik Merkezi ve Microsoft Defender gibi veri kaynaklarından toplanan tanımlama bilgilerini içerir.

- Orta bölmede, varlıkla ilgili, uyarılar, yer işaretleri ve etkinlikler gibi önemli olayların bir grafik ve metin zaman çizelgesi gösterilmektedir. Etkinlikler Log Analytics 'den önemli olaylar toplamalarına sahiptir. Bu etkinlikleri algılayan sorgular Microsoft güvenlik araştırma ekipleri tarafından geliştirilir.

- Sağ taraftaki panel, varlık hakkındaki davranış öngörülerini sunar. Bu Öngörüler, anomali ve güvenlik tehditlerini hızlı bir şekilde belirlemenize yardımcı olur. Öngörüler, Microsoft Security Research ekipleri tarafından geliştirilmiştir ve anomali algılama modellerini temel alır.

### <a name="the-timeline"></a>Zaman çizelgesi

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Varlık sayfaları zaman çizelgesi":::

Zaman çizelgesi, varlık sayfasının Azure Sentinel 'de davranış analizinin katkılarının önemli bir parçasıdır. Varlıkla ilgili olaylar hakkında bir hikaye sunar ve varlığın etkinliğini belirli bir zaman çerçevesinde anlamanıza yardımcı olur.

Birkaç önceden ayarlanmış seçenek arasından ( *son 24 saat*) veya özel olarak tanımlanmış zaman dilimine ayarlanmış **zaman aralığını** seçebilirsiniz. Ayrıca, zaman çizelgesindeki bilgileri belirli olay veya uyarı türleriyle sınırlayan filtreler ayarlayabilirsiniz.

Aşağıdaki öğe türleri zaman çizelgesine dahildir:

- Uyarılar-varlığın **eşlenmiş bir varlık** olarak tanımlandığı tüm uyarılar. Kuruluşunuzun [analiz kurallarını kullanarak özel uyarılar](./tutorial-detect-threats-custom.md)oluşturduğuna sahip olması durumunda kuralların varlık eşlemesinin düzgün şekilde yapıldığından emin olun.

- Yer işaretleri-sayfada gösterilen belirli varlığı içeren tüm yer işaretleri.

- Etkinlikler-varlıkla ilgili önemli olaylarının toplamı.

### <a name="entity-insights"></a>Varlık öngörüleri

Varlık öngörüleri, analistlerinizin daha verimli ve etkili bir şekilde araştırılması için Microsoft güvenlik araştırmacıları tarafından tanımlanan sorgulardır Öngörüler, varlık sayfasının bir parçası olarak sunulur ve ana bilgisayarlar ve kullanıcılar hakkında tablo verileri ve grafikler biçiminde değerli güvenlik bilgileri sağlar. Burada bilgilere sahip olmanız, Log Analytics için gezinti yapmanız gerekmediği anlamına gelir. Öngörüler, oturum açma işlemleri, Grup eklemeleri, anormal etkinlikler ve daha fazlası ile ilgili verileri içerir ve anormal davranışları algılamak için gelişmiş ML algoritmaları içerir.

Öngörüler aşağıdaki veri kaynaklarına dayalıdır:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- Signınlogs (Azure AD)
- Officeetkinliği (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Sinyal (Azure Izleyici Aracısı)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Varlık sayfalarını kullanma

Varlık sayfaları birden çok kullanım senaryosunun parçası olacak şekilde tasarlanmıştır ve olay yönetimi, araştırma grafiği, yer işaretleri veya doğrudan Azure Sentinel ana menüsündeki **varlık davranışı Analizi** altındaki varlık arama sayfasından erişilebilir.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Varlık sayfası kullanım örnekleri":::

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'de varlıklarla çalışma hakkında bilgi edindiniz. Uygulamayla ilgili pratik yönergeler için ve elde ettiğiniz öngörüleri kullanmak için aşağıdaki makalelere bakın:

- Azure Sentinel 'de [varlık davranışı analizlerini etkinleştirin](./enable-entity-behavior-analytics.md) .
- [Güvenlik tehditleri Için araya](./hunting.md).
