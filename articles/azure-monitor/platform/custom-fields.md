---
title: Azure Izleyici 'de özel alanlar | Microsoft Docs
description: Azure Izleyici 'nin özel alanlar özelliği, toplanan bir kaydın özelliklerine eklenen bir Log Analytics çalışma alanındaki kayıtlardan kendi aranabilir alanlarını oluşturmanızı sağlar.  Bu makalede özel bir alan oluşturma ve örnek olayla ayrıntılı bir anlatım sağlayan işlem açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: 1fa8fb8ee944103626966839def358e68a55d8ac
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932602"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics çalışma alanında özel alanlar oluşturma

> [!NOTE]
> Bu makalede, Log Analytics çalışma alanındaki metin verilerinin toplanarak nasıl ayrıştırılacağını açıklanmaktadır. Bir sorgu filtresinde, [Azure izleyici 'de metin verileri ayrıştırma](../log-query/parse-text.md)bölümünde açıklanan yönergelerin toplanmasından sonra metin verilerini ayrıştırmayı öneririz. Özel alanları kullanarak çeşitli avantajlar sağlar.

Azure Izleyici 'nin **özel alanlar** özelliği, kendi aranabilir alanlarınızı ekleyerek Log Analytics çalışma alanınızdaki mevcut kayıtları genişletmenizi sağlar.  Özel alanlar, aynı kayıttaki diğer özelliklerden ayıklanan verilerden otomatik olarak doldurulur.

![Genel Bakış](media/custom-fields/overview.png)

Örneğin, aşağıdaki örnek kayıt, olay açıklamasında bulunan yararlı verilere sahiptir. Bu verilerin ayrı bir özelliğe ayıklanması, bu işlemleri sıralama ve filtreleme gibi işlemler için kullanılabilir hale getirir.

![Örnek Ayıkla](media/custom-fields/sample-extract.png)

> [!NOTE]
> Önizlemede, çalışma alanınızda 100 özel alan ile sınırlı olursunuz.  Bu özellik genel kullanıma ulaştığında, bu sınır genişletilir.

## <a name="creating-a-custom-field"></a>Özel alan oluşturma
Özel bir alan oluşturduğunuzda, Log Analytics değerini doldurmak için hangi verilerin kullanılacağını anlamalıdır.  Bu verileri hızlıca tanımlamak için bir Microsoft Research tarafından FlashExtract adlı bir teknoloji kullanır.  Açık yönergeler sağlamanızı gerektirmek yerine, Azure Izleyici sağladığınız örneklerden ayıklamak istediğiniz verileri öğreniyor.

Aşağıdaki bölümlerde özel alan oluşturma yordamı sağlanmaktadır.  Bu makalenin alt kısmında bir örnek ayıklamanın izlenecek yolu yer aldığı bir örnektir.

> [!NOTE]
> Özel alan, belirtilen ölçütlere uyan kayıtlar Log Analytics çalışma alanına eklendiğinden, bu nedenle yalnızca özel alan oluşturulduktan sonra toplanan kayıtlarda yer alacak şekilde doldurulur.  Özel alan, oluşturulduğu sırada zaten veri deposunda olan kayıtlara eklenmez.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1\. adım – özel alana sahip olacak kayıtları tanımla
İlk adım, özel alanı alacak kayıtları tanımlamaktır.  [Standart bir günlük sorgusuyla](../log-query/log-query-overview.md) başlayıp Azure izleyicisinin öğrenolacağı model olarak davranacak bir kayıt seçersiniz.  Verileri özel bir alana çıkaracağınızı belirttiğinizde, **alan ayıklama Sihirbazı** , ölçütleri doğruladığınızda ve iyileştirmeniz durumunda açılır.

1. Özel alana sahip olacak [kayıtları almak Için](../log-query/log-query-overview.md) **günlüklere** gidin ve bir sorgu kullanın.
2. Log Analytics, özel alanı doldurmak için veri ayıklama modeli olarak görev yapmak üzere kullanacağı bir kayıt seçin.  Bu kayıttan ayıklamak istediğiniz verileri tanımlayacaksınız ve Log Analytics tüm benzer kayıtlar için özel alanı doldurma mantığını belirlemek için bu bilgileri kullanır.
3. Kayıt özelliklerini genişletin, kaydın üstteki özelliğinin solundaki elipsi tıklatın ve **alanları Ayıkla**' yı seçin.
4. **Alan ayıklama Sihirbazı** açılır ve seçtiğiniz kayıt **ana örnek** sütununda görüntülenir.  Özel alan, seçilen özelliklerde aynı değerlere sahip olan kayıtlar için tanımlanır.  
5. Seçim tam olarak istediğiniz şekilde değilse, ölçütleri daraltmak için ek alanlar ' ı seçin.  Ölçütlerin alan değerlerini değiştirmek için, istediğiniz ölçütlerle eşleşen farklı bir kayıt iptal etmeli ve seçmeniz gerekir.

### <a name="step-2---perform-initial-extract"></a>2\. adım-ilk ayıklamayı gerçekleştirin.
Özel alana sahip olacak kayıtları tanımladıktan sonra, ayıklamak istediğiniz verileri belirlersiniz.  Log Analytics, benzer kayıtlardaki benzer desenleri belirlemek için bu bilgileri kullanacaktır.  Bundan sonraki adımda, sonuçları doğrulayabilir ve Log Analytics analizinde kullanmak üzere daha fazla ayrıntı sağlayabilirsiniz.

1. Özel alanı doldurmak istediğiniz örnek kayıttaki metni vurgulayın.  Daha sonra alan için bir ad ve veri türü sağlamak ve ilk ayıklamayı gerçekleştirmek için bir iletişim kutusu görüntülenir.  **\_CF** karakterleri otomatik olarak eklenir.
2. Toplanan kayıtların analizini gerçekleştirmek için **Ayıkla** ' ya tıklayın.  
3. **Özet** ve **Arama sonuçları** bölümleri, ayıklamayı doğru bir şekilde incelemenize olanak sağlayacak sonuçları görüntüler.  **Özet** , kayıtları tanımlamak için kullanılan ölçütü ve tanımlanan veri değerlerinin her biri için bir sayıyı görüntüler.  **Arama sonuçları** ölçütlere uyan kayıtların ayrıntılı bir listesini sağlar.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3\. adım – Ayıkla ve oluştur özel alanının doğruluğunu doğrulayın
İlk ayıklamayı gerçekleştirdikten sonra Log Analytics, daha önce toplanan verileri temel alarak sonuçlarını görüntüleyecektir.  Sonuçlar doğru görünemediğinde, başka bir iş olmadan özel alan oluşturabilirsiniz.  Aksi takdirde, Log Analytics mantığını iyileştirebilmek için sonuçları iyileştirebilirsiniz.

1. İlk ayıklama içindeki herhangi bir değer doğru değilse, yanlış bir kaydın yanındaki **Düzenle** simgesine tıklayın ve seçimi değiştirmek için **Bu vurgulamayı Değiştir** ' i seçin.
2. Giriş, **ana örneğin**altındaki **ek örnekler** bölümüne kopyalanır.  Daha sonra, yaptığı seçimi anlamak Log Analytics yardımcı olması için vurguyu burada ayarlayabilirsiniz.
3. Var olan tüm kayıtları değerlendirmek için bu yeni bilgileri kullanmak üzere **Ayıkla** ' ya tıklayın.  Sonuçlar, bu yeni zeka göre yeni değiştirdiğiniz bir kayıt için değiştirilebilir.
4. Ayıklama içindeki tüm kayıtlar yeni özel alanı doldurmak üzere verileri doğru şekilde tanımlaana kadar düzeltmeler eklemeye devam edin.
5. Sonuçlardan memnun kaldığınızda **ayıklamayı kaydet** ' e tıklayın.  Özel alan artık tanımlanmış, ancak henüz hiçbir kayda eklenmeyecek.
6. Belirtilen ölçütlerle eşleşen yeni kayıtların toplanmasını bekleyip günlük aramasını yeniden çalıştırın. Yeni kayıtlar özel alana sahip olmalıdır.
7. Özel alanı başka bir kayıt özelliği gibi kullanın.  Bunu kullanarak verileri toplayabilir ve gruplandırabilir, hatta yeni Öngörüler oluşturmak için kullanabilirsiniz.

## <a name="viewing-custom-fields"></a>Özel alanları görüntüleme
Yönetim grubunuzdaki tüm özel alanların bir listesini, Azure portal Log Analytics çalışma alanınızın **Gelişmiş ayarlar** menüsünden görebilirsiniz.  Çalışma alanınızdaki tüm özel alanların listesi için **veri** ve ardından **özel alanlar** ' ı seçin.  

![Özel alanlar](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Özel bir alanı kaldırma
Özel bir alanı kaldırmanın iki yolu vardır.  İlki, yukarıda açıklandığı gibi tüm liste görüntülenirken her bir alan için **Kaldır** seçeneğidir.  Diğer yöntem, bir kaydı almak ve alanın solunda bulunan düğmeye tıklaklarıdır.  Menü, özel alanı kaldırmak için bir seçeneğe sahip olur.

## <a name="sample-walkthrough"></a>Örnek kılavuz
Aşağıdaki bölümde, özel bir alan oluşturma işleminin tamamı gösterilmektedir.  Bu örnek, hizmet değiştirme durumunu gösteren Windows olaylarında hizmet adını ayıklar.  Bu, Windows bilgisayarlarda sistem başlatma sırasında Service Control Manager tarafından oluşturulan olayları kullanır.  Bu örneği izlemek istiyorsanız [Sistem günlüğü Için bilgi olayları toplamalısınız](data-sources-windows-events.md).

Hizmet denetimi Yöneticisi 'nden, bir hizmetin başlamasını veya durdurulmasını belirten olay olan 7036 olay KIMLIĞINE sahip tüm olayları döndürmek için aşağıdaki sorguyu girin.

![Sorgu](media/custom-fields/query.png)

Daha sonra olay KIMLIĞI 7036 olan kayıtları seçip genişlettik.

![Kaynak kaydı](media/custom-fields/source-record.png)

Üstteki özelliğin yanındaki elipsi tıklayarak özel alanlar tanımlayacağız.

![Alanları Ayıkla](media/custom-fields/extract-fields.png)

**Alan ayıklama Sihirbazı** açılır ve **olay günlüğü** ve **EventID** alanları **ana örnek** sütununda seçilir.  Bu, özel alanın olay KIMLIĞI 7036 olan sistem günlüğünden olaylar için tanımlandığını gösterir.  Bu, başka bir alan seçmemiz gerekmemesi için yeterlidir.

![Ana örnek](media/custom-fields/main-example.png)

Hizmetin adını **Rendereddescription** özelliğinde vurgularız ve hizmet adını belirlemek için **hizmeti** kullanın.  Özel alan **Service_CF**olarak adlandırılacaktır. Bu örnekte yer alan tür bir dizedir, bu nedenle bu şekilde değişmeden bırakabiliriz.

![Alan başlığı](media/custom-fields/field-title.png)

Hizmet adının bazı kayıtlar için doğru tanımlandığını, ancak başkaları için değil olduğunu görüyoruz.   **Arama sonuçları** , **WMI performans bağdaştırıcısının** adının bir bölümünün seçilmediyse gösterir.  **Özet** , bir kaydın **Windows modülleri yükleyicisi**yerine bir **Modül yükleyicisinin** tanımlı olduğunu gösterir.  

![Arama sonuçları](media/custom-fields/search-results-01.png)

**WMI performans bağdaştırıcısı** kaydıyla başlayacağız.  Düzenleme simgesine tıkladık ve sonra **Bu vurgulamayı değiştirirsiniz**.  

![Vurgulamayı Değiştir](media/custom-fields/modify-highlight.png)

Vurgu sözcüğünü, **WMI** 'yi içerecek şekilde artırdık ve sonra ayıklamayı yeniden çalıştıracağız.  

![Ek örnek](media/custom-fields/additional-example-01.png)

**WMI performans bağdaştırıcısı** girişlerinin düzeltilmiş olduğunu ve Log Analytics Ayrıca bu bilgileri **Windows Modül Yükleyicisi**kayıtlarını düzeltmek için kullanıldığını görebiliriz.

![Arama sonuçları](media/custom-fields/search-results-02.png)

Artık **Service_CF** oluşturulduğunu doğrulayan ancak henüz hiçbir kayda eklenmemiş bir sorgu çalıştırabiliriz. Bunun nedeni, özel alanın mevcut kayıtlara karşı çalışmamaları, bu nedenle yeni kayıtların toplanmasını beklemek istiyoruz.

![Başlangıç sayısı](media/custom-fields/initial-count.png)

Bir süre geçtikten sonra yeni olaylar toplandıktan sonra, **Service_CF** alanının artık ölçütünüzle eşleşen kayıtlara eklendiğini görebiliriz.

![Nihai sonuçlar](media/custom-fields/final-results.png)

Artık özel alanı başka bir kayıt özelliği gibi kullanabiliriz.  Bunu göstermek için, en etkin Hizmetleri denetlemek üzere yeni **Service_CF** alanına göre gruplandıran bir sorgu oluşturacağız.

![Sorguya göre Gruplandır](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Sonraki adımlar
* Ölçütler için özel alanlar kullanarak sorgular oluşturmaya yönelik [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.
* Özel alanlar kullanarak ayrıştırdığınızda [özel günlük dosyalarını](data-sources-custom-logs.md) izleyin.

