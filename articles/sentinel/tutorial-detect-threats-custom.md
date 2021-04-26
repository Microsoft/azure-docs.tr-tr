---
title: Azure Sentinel ile tehditleri algılamak için özel analiz kuralları oluşturma | Microsoft Docs
description: Azure Sentinel ile güvenlik tehditlerini algılamak üzere özel analiz kuralları oluşturmayı öğrenmek için bu öğreticiyi kullanın. Olay gruplaması, uyarı gruplama ve uyarı zenginleştirme özelliğinden yararlanarak OTOMATIK devre DıŞı seçeneğini anlayın.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814865"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Öğretici: tehditleri algılamak için özel analiz kuralları oluşturma

Veri kaynaklarınızı Azure Sentinel 'e [bağladığınıza](quickstart-onboard.md) göre, ortamınızda bulunan tehditleri ve anormal davranışları keşfetmenize yardımcı olması için özel analiz kuralları oluşturabilirsiniz. Bu kurallar ortamınızda belirli olayları veya olay kümelerini arar, belirli olay eşiklerine veya koşullara ulaşıldığında sizi uyarır, değerlendirmenize ve araştırmaya yönelik olayları oluşturur ve otomatik izleme ve düzeltme işlemleriyle tehditleri yanıtlar. 

Bu öğretici, Azure Sentinel ile tehditleri algılamak için özel kurallar oluşturmanıza yardımcı olur.

Bu Öğreticiyi tamamladıktan sonra şunları yapabilirsiniz:
> [!div class="checklist"]
> * Analiz kuralları oluşturma
> * Olayların ve uyarıların işlenme biçimini tanımlayın
> * Uyarıların ve olayların nasıl oluşturulduğunu tanımlayın
> * Kurallarınız için otomatik tehdit yanıtları seçin

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Zamanlanan sorgu ile özel bir analiz kuralı oluşturma

1. Azure Sentinel gezinti menüsünde **analiz**' ı seçin.

1. Üstteki eylem çubuğunda **+ Oluştur** ' u seçin ve **zamanlanan sorgu kuralı**' nı seçin. Bu, **analiz Kuralı sihirbazını** açar.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Zamanlanmış sorgu oluştur" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Analiz Kuralı Sihirbazı-Genel sekmesi

- Benzersiz bir **ad** ve **Açıklama** sağlayın. 

- **Tacler** alanında, kuralın sınıflandırılacağı saldırı kategorileri arasından seçim yapabilirsiniz. Bunlar, [Mitre ATT&CK](https://attack.mitre.org/) çerçevesinin tacetiği temel alır.

- Uyarı **önem derecesini** uygun şekilde ayarlayın. 

- Kuralı oluşturduğunuzda, **durumu** varsayılan olarak **etkindir** ve oluşturma işlemi tamamlandıktan sonra hemen çalışacağı anlamına gelir. Hemen çalışmasını istemiyorsanız, **devre dışı**' yı seçin ve kural **etkin kurallar** sekmesine eklenir ve ihtiyacınız olduğunda bu seçeneği etkinleştirebilirsiniz.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Özel analiz kuralı oluşturmaya başlayın":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Kural sorgu mantığını tanımlama ve ayarları yapılandırma

**Kural mantığını ayarla** sekmesinde, doğrudan **kural sorgu** alanına bir sorgu yazabilir veya sorguyu Log Analytics oluşturup buraya kopyalayabilir ve yapıştırabilirsiniz.

- Sorgular Kusto Sorgu Dili (KQL) ile yazılır. KQL [kavramları](/azure/data-explorer/kusto/concepts/) ve [sorguları](/azure/data-explorer/kusto/query/)hakkında daha fazla bilgi edinin ve bu kullanışlı [hızlı başvuru kılavuzuna](/azure/data-explorer/kql-quick-reference)bakın.

- Bu ekran görüntüsünde gösterilen örnek, [başarısız bir Windows oturum açma olayları](/windows/security/threat-protection/auditing/event-4625)türünü göstermek Için *securityevent* tablosunu sorgular.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Sorgu kuralı mantığını ve ayarlarını yapılandırma" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- [Azure etkinliğinde](../azure-monitor/essentials/activity-log.md)anormal sayıda kaynak oluşturulduğunda sizi uyaran bir örnek sorgu aşağıda verilmiştir.

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Kural sorgusu en iyi yöntemleri
    > - Sorgu uzunluğu 1 ile 10.000 karakter arasında olmalıdır ve " `search *` " veya "" karakterlerini içeremez `union *` . Sorgu uzunluğu sınırlamasını aşmak için [Kullanıcı tanımlı işlevleri](/azure/data-explorer/kusto/query/functions/user-defined-functions) kullanabilirsiniz.
    >
    > - Log Analytics sorgu penceresinde Azure Veri Gezgini sorguları oluşturmak için ADX işlevlerinin kullanılması **desteklenmez**.
    >
    > - **`bag_unpack`** Bir sorgudaki işlevi kullanırken, sütunları "" kullanarak alanlar olarak `project field1` ve sütun yoksa, sorgu başarısız olur. Bu sorunla karşı koruma sağlamak için, sütunu aşağıdaki gibi proje oluşturmanız gerekir:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Uyarı zenginleştirme

> [!IMPORTANT]
> Uyarı zenginleştirme özellikleri şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
    
- Sorgu sonuçlarınızdan parametreleri Azure Sentinel tarafından tanınan varlıklara eşlemek için **varlık eşleme** yapılandırması bölümünü kullanın. Varlıklar, herhangi bir araştırma işleminin yapı taşları olarak hizmet veren ve takip eden eylemleri sunan önemli bilgilerle kuralları çıktıyı (Uyarılar ve olaylar) zenginleştirmektedir. Ayrıca, uyarıları **olay ayarları** sekmesindeki olaylara birlikte gruplandırmanız gereken ölçütlerdir.

    [Azure Sentinel 'de varlıklar](entities-in-azure-sentinel.md)hakkında daha fazla bilgi edinin.

    Tüm varlık eşleme yönergeleri ve [geriye dönük uyumluluk](map-data-fields-to-entities.md#notes-on-the-new-version)hakkında önemli bilgiler için bkz. [Azure Sentinel 'de veri alanlarını eşleme](map-data-fields-to-entities.md) .

- Sorguınızdan olay verileri öğelerini ayıklamak ve bu kural tarafından oluşturulan uyarılarda, uyarı ve olaylarınıza anında olay içeriği görünürlüğü sağlamak için **özel ayrıntılar** yapılandırma bölümünü kullanın.

    Uyarılarla ilgili özel ayrıntılar hakkında daha fazla bilgi edinin ve [tüm yönergeleri](surface-custom-details-in-alerts.md)görüntüleyin.

### <a name="query-scheduling-and-alert-threshold"></a>Sorgu zamanlama ve uyarı eşiği

- **Sorgu zamanlaması** bölümünde aşağıdaki parametreleri ayarlayın:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Sorgu zamanlamasını ve olay gruplamayı ayarlama" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Sorgunun her 5 dakikada bir veya yaklaşık 14 günde bir sıklıkta ne sıklıkla çalıştırılacağını kontrol etmek için bir kez **Çalıştır** 'ı ayarlayın.

    - Sorgunun kapsadığı verilerin zaman dilimini öğrenmek için **en son ' dan arama verileri** ayarlama-Örneğin, son 10 dakikalık verileri veya son 6 saatlik veriyi sorgulayabilir. En büyük değer 14 gündür.

        > [!NOTE]
        > **Sorgu aralıkları ve geriye doğru süre**
        >
        >  Bu iki ayar, bir noktaya kadar birbirinden bağımsızdır. Bir sorguyu, zaman aralığından daha uzun bir süre (çakışan sorgulara sahip olan) kapsayan kısa bir aralıkta çalıştırabilirsiniz, ancak kapsam süresini aşan bir aralıkta bir sorgu çalıştıramazsınız, aksi takdirde, genel sorgu kapsamında boşluklar olur.
        >
        > **Alma gecikmesi**
        >
        > Kaynaktaki bir olayın oluşturulması ve Azure Sentinel 'e alımı arasında oluşabilecek **gecikme süresini** hesaba katmak ve veri çoğaltma olmadan tam kapsama sağlamak Için, Azure Sentinel zamanlanan analiz kurallarını zamanlanan zamanlarından **beş dakikalık bir gecikmeyle** çalıştırır.
        >
        > Bu gecikmenin neden gerekli olduğuna ve bu sorunun nasıl çözülediğinin ayrıntılı bir teknik açıklaması için, "[Azure Sentinel zamanlanmış uyarı kurallarında alma gecikmesini işleme](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)" konusunun harika Marsiano 'ın mükemmel blog gönderisine bakın.

- Kuralın duyarlılık düzeyini tanımlamak için **uyarı eşiği** bölümünü kullanın. Örneğin, **sorgu sonucu sayısı şundan büyük olduğunda uyarı üret** ' i  ayarlayın ve kuralın yalnızca sorgu her çalıştığında 1000 ' den fazla sonuç döndürürse bir uyarı oluşturmasını istiyorsanız 1000 numarasını girin. Bu gerekli bir alandır. bu nedenle, bir eşik ayarlamak istemiyorsanız, uyarının her olayı kaydetmesini istiyorsanız, sayı alanına 0 girin.
    
### <a name="results-simulation"></a>Sonuç benzetimi

**Sonuç simülasyonu** alanında, sihirbazın sağ tarafındaki **geçerli verilerle test** ' i seçin ve Azure Sentinel, o sırada tanımlanmış olan zamanlamaya göre, sorgunun çalıştırıldığı son 50 defa üzerinde oluşturulan sonuçların (günlük olayları) bir grafiğini gösterir. Sorguyu değiştirirseniz, grafiği güncelleştirmek için **geçerli verilerle test et** ' i seçin. Grafik, **sorgu zamanlama** bölümündeki ayarlar tarafından belirlenen, tanımlanan süre içindeki sonuçların sayısını gösterir.
  
Sonuç simülasyonu, yukarıdaki ekran görüntüsünde sorgu için nasıl görünebileceğini aşağıda bulabilirsiniz. Sol taraf varsayılan görünümdedir ve grafik üzerinde zaman içindeki bir noktanın üzerine geldiğinizde doğru kenar görüntülenir.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Sonuç benzetimi ekran görüntüleri":::

Sorgunuzun çok fazla veya çok sık uyarı tetikleyeceğini görürseniz, **sorgu zamanlama** ve **uyarı eşiği** [bölümlerindeki](#query-scheduling-and-alert-threshold) ayarlarla denemeler yapabilir ve **geçerli verilerle test** ' i seçebilirsiniz.

### <a name="event-grouping-and-rule-suppression"></a>Olay gruplama ve kural gizleme

> [!IMPORTANT]
> Olay gruplama Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
    
- **Olay gruplandırması** altında, **olayların** gruplandırılmasına yönelik iki yönden birini **seçin:** 

    - **Tüm olayları tek bir uyarıya gruplandırın** (varsayılan ayar). Sorgu, yukarıda belirtilen **uyarı eşiğine** göre daha fazla sonuç döndürdüğü sürece kural her çalıştığında tek bir uyarı oluşturur. Uyarı, sonuçlarda döndürülen tüm olayların bir özetini içerir. 

    - **Her olay için bir uyarı tetikle**. Kural, sorgu tarafından döndürülen her olay için benzersiz bir uyarı oluşturur. Olayların tek tek görüntülenmesini istiyorsanız veya kullanıcıları, ana bilgisayar adı ya da başka bir şeye göre belirli parametrelere göre gruplandırmak istiyorsanız bu kullanışlıdır. Bu parametreleri sorguda tanımlayabilirsiniz.
    
        Şu anda bir kuralın oluşturabileceği uyarı sayısı üst sınırı 20'dir. Belirli bir kuralda **olay gruplama** , **her olay Için bir uyarı tetikleyecek** şekilde ayarlanır ve kuralın sorgusu 20 ' den fazla olay döndürürse, ilk 19 olayın her biri benzersiz bir uyarı oluşturur ve 20. uyarı döndürülen tüm olay kümesini özetler. Diğer bir deyişle, 20. Uyarı **tüm olayları Grup altında tek bir uyarı** seçeneğinde üretilme şeydir.

    > [!NOTE]
    > **Olaylar** ve **Uyarılar** arasındaki fark nedir?
    >
    > - **Olay** , bir eylemin tek bir oluşumunun açıklamasıdır. Örneğin, bir günlük dosyasındaki tek bir girdi olay olarak sayabilir. Bu bağlamda bir olay, bir analiz kuralındaki sorgu tarafından döndürülen tek bir sonuca başvurur.
    >
    > - **Uyarı** , bir güvenlik açısından önemli olan, bir araya gelen olaylar koleksiyonudur. Olayda önemli güvenlik etkileri varsa, bir uyarı tek bir olay içerebilir; Örneğin, bir yabancı ülkeden ofis saatleri dışında bir yönetim oturum açma.
    >
    > - Bu şekilde, **Olaylar** nelerdir? Azure Sentinel 'in iç mantığı, **uyarılardan** veya uyarı gruplarından **Olaylar** oluşturur. Olaylar sırası, SOC analistlerinin iş önceliklendirme, araştırma ve düzeltme için odak noktasıdır.
    > 
    > Azure Sentinel, bazı veri kaynaklarından gelen ham olayları ve diğer kaynaklardan zaten işlenmiş uyarıları alır. İstediğiniz zaman hangi bir ilgilendiğiniz hakkında dikkat etmeniz önemlidir.

- **Gizleme** bölümünde, uyarı **oluşturulduktan sonra sorguyu çalıştırmayı durdur** **ayarı,** bir uyarı alındıktan sonra, bu kuralın işlemini sorgu aralığını aşan bir süre için askıya almak istediğinizde kullanabilirsiniz. Bunu açarsanız, 24 saate kadar, sorgunun çalışmayı durdurması gereken süre kadar olan **sorguyu durdur** olarak ayarlamanız gerekir.

## <a name="configure-the-incident-creation-settings"></a>Olay oluşturma ayarlarını yapılandırma

**Olay ayarları** sekmesinde, Azure Sentinel 'in uyarıları eyleme dönüştürülebilir olaylara nasıl dönüşdiğini ve ne yapılacağını seçebilirsiniz. Bu sekme tek başına bırakılırsa, Azure Sentinel her uyarıdan tek ve ayrı bir olay oluşturur. Bu sekmedeki ayarları değiştirerek, herhangi bir olayın oluşturulmasını veya tek bir olayda birden çok uyarıyı gruplandırmadığının tercih edilebilir.

> [!IMPORTANT]
> Olay ayarları sekmesi şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Olay oluşturma ve uyarı gruplama ayarlarını tanımlayın":::

### <a name="incident-settings"></a>Güvenlik olayı ayarları

**Olay ayarları** bölümünde, **Bu analiz kuralı tarafından tetiklenen uyarılardan olaylar oluşturun** , varsayılan olarak **etkin** olarak ayarlanır, yani Azure Sentinel, her biri ve kural tarafından tetiklenen her uyarıdan tek ve ayrı bir olay oluşturur.
    
- Bu kuralın herhangi bir olay oluşturmaya neden olmasını istemiyorsanız (örneğin, bu kural sonraki Analize ilişkin bilgileri toplamak için), bunu **devre dışı** olarak ayarlayın.

- Her tek uyarı için bir uyarı grubundan tek bir olayın oluşturulmasını istiyorsanız bir sonraki bölüme bakın.

### <a name="alert-grouping"></a>Uyarı gruplama

**Uyarı gruplama** bölümünde, tek bir olayın 150 benzer veya yinelenen uyarılarla (bkz. nota) oluşturulmasını istiyorsanız, **Bu analiz kuralı tarafından tetiklenen, grup ile ilgili uyarıları** ve aşağıdaki parametreleri ayarlayın. 

- **Grubu seçilen zaman çerçevesinde oluşturulan uyarılarla sınırlayın**: benzer veya yinelenen uyarıların birlikte gruplandırılacağı zaman çerçevesini belirleme. Bu zaman çerçevesinde karşılık gelen tüm uyarılar toplu olarak bir olay veya bir olay kümesi oluşturur (aşağıdaki gruplandırma ayarlarına bağlı olarak). Bu zaman dilimi dışındaki uyarılar, ayrı bir olay veya olay kümesi oluşturur.

- **Bu analiz kuralı tarafından tetiklenen uyarıları tek bir olaya göre Gruplandır**: uyarıların birlikte gruplandırılacağı temeli seçin:

    - **Tüm varlıkların eşleşmesi durumunda uyarıları tek bir olaya gruplayın**: her eşlenmiş varlıkların (Yukarıdaki kural mantığını ayarla sekmesinde tanımlanan) her biri için aynı değerleri paylaşıyorsa, uyarılar birlikte gruplandırılır. Önerilen ayar budur.

    - **Bu kural tarafından tetiklenen tüm uyarıları tek bir olaya grupla**: Bu kural tarafından oluşturulan tüm uyarılar, özdeş değerler paylaşmasa bile birlikte gruplandırılır.

    - **Seçili varlıkların eşleşmesi durumunda uyarıları tek bir olaya gruplayın**: uyarılar, eşlenmiş varlıkların bazıları için özdeş değerleri paylaşıyorsa (açılan listeden seçim yapabilirsiniz). Örneğin, kaynak veya hedef IP adreslerine göre ayrı olaylar oluşturmak istiyorsanız bu ayarı kullanmak isteyebilirsiniz.

- **Kapalı eşleşen olayları yeniden aç**: bir olay çözümlenirse ve kapatılmışsa ve daha sonra bu olaya ait olması gereken başka bir uyarı varsa, bu ayarı devre dışı bırakmak istiyorsanız **etkin** olarak ayarlayın ve uyarının yeni bir olay oluşturmasını istiyorsanız **devre dışı** bırakın.
    
    > [!NOTE]
    > **En çok 150 uyarı** tek bir olay halinde gruplandırılabilir. Tek bir olaya gruplandıran bir kural tarafından 150 ' den fazla uyarı oluşturulursa, orijinalle aynı olay ayrıntıları ile yeni bir olay oluşturulur ve fazlalık uyarılar yeni olaya göre gruplandırılır.

## <a name="set-automated-responses-and-create-the-rule"></a>Otomatikleştirilmiş yanıtları ayarlama ve kuralı oluşturma

1. **Otomatik yanıtlar** sekmesinde, özel kural tarafından bir uyarı oluşturulduğunda otomatik olarak çalıştırmak istediğiniz tüm PlayBook 'ları seçin. PlayBook 'ları oluşturma ve otomatikleştirme hakkında daha fazla bilgi için bkz. [tehditlere yanıt verme](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Otomatik yanıt ayarlarını tanımlama":::

1. Yeni uyarı kuralınız için tüm ayarları gözden geçirmek için **gözden geçir ve Oluştur '** u seçin. "Doğrulama başarılı oldu" iletisi göründüğünde, uyarı kuralınızı başlatmak için **Oluştur** ' u seçin.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Tüm ayarları gözden geçirin ve kuralı oluşturun":::

## <a name="view-the-rule-and-its-output"></a>Kuralı ve çıktısını görüntüleme
  
- Yeni oluşturulan özel kuralınızı ("zamanlanmış" türünde), ana **analiz** ekranındaki **etkin kurallar** sekmesinin altındaki tabloda bulabilirsiniz. Bu listeden her kuralı etkinleştirebilir, devre dışı bırakabilir veya silebilirsiniz.

- Oluşturduğunuz uyarı kurallarının sonuçlarını görüntülemek için **Olaylar** sayfasına gidin; burada, olayları önceliklendirebilirsiniz, [araştırın](tutorial-investigate-cases.md)ve tehditleri düzeltin.

> [!NOTE]
> Azure Sentinel 'de oluşturulan uyarılar [Microsoft Graph güvenliği](/graph/security-concept-overview)aracılığıyla kullanılabilir. Daha fazla bilgi için [Microsoft Graph güvenlik uyarıları belgelerine](/graph/api/resources/security-api-overview)bakın.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="issue-no-events-appear-in-query-results"></a>Sorun: sorgu sonuçlarında hiçbir olay görünmüyor

**Olay gruplama** , **her olay için bir uyarı tetikleyecek** şekilde ayarlandıysa, bazı senaryolarda sorgu sonuçlarını daha sonra (örneğin, bir olaydan uyarılara özetleme gibi) görüntülerken, hiçbir sorgu sonucu görünmeyecektir. Bunun nedeni, olayın uyarı bağlantısının, belirli olay bilgilerinin karmasında ve karmayı sorguya dahil edilmesi nedeniyle yapılır. Uyarı oluşturulduktan sonra sorgu sonuçları değiştiyse, karma artık geçerli olmaz ve hiçbir sonuç görüntülenmez. 

Olayları görmek için, kuralın sorgusundan karmayı içeren satırı el ile kaldırın ve sorguyu çalıştırın.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Sorun: zamanlanmış bir kural yürütülemedi veya OTOMATIK devre DıŞı olarak ada eklenmiş şekilde görünür

Zamanlanmış bir sorgu kuralının çalışamamasından kaynaklanabilir, ancak bu durum oluşabilir. Azure Sentinel, hatanın belirli türüne ve kendisine yönelik koşullara göre geçici ya da kalıcı olarak, hataları ortaya çıkarak sınıflandırır.

#### <a name="transient-failure"></a>Geçici hata

Geçici olan ve yakında normal olarak döndürülen bir durum nedeniyle geçici bir hata oluşur. bu noktada kural yürütme başarılı olur. Azure Sentinel 'in geçici olarak sınıflandırdığı hatalara ilişkin bazı örnekler şunlardır:

- Kural sorgusunun çalıştırılması ve zaman aşımına uğramadan çok uzun sürüyor.
- Veri kaynakları ile Log Analytics veya Log Analytics ile Azure Sentinel arasında bağlantı sorunları.
- Diğer tüm yeni ve bilinmeyen hatalar geçici olarak değerlendirilir.

Geçici bir hata durumunda Azure Sentinel, bir noktaya kadar önceden belirlenmiş ve sürekli artan aralıklar sonrasında kuralı yeniden yürütmeye devam eder. Bundan sonra, kural yalnızca bir sonraki zamanlandığı zamanda yeniden çalıştırılır. Bir kural geçici bir hata nedeniyle hiçbir şekilde otomatik olarak devre dışı bırakılamaz.

#### <a name="permanent-failure---rule-auto-disabled"></a>Kalıcı hata-kural otomatik devre dışı

Kuralın çalışmasına izin veren koşullardaki bir değişiklikten dolayı kalıcı bir hata oluşur. Bu, insan müdahalesine gerek kalmadan eski durumuna dönmeyecektir. Kalıcı olarak sınıflandırılan hatalara ilişkin bazı örnekler aşağıda verilmiştir:

- Hedef çalışma alanı (kural sorgusunun işletilme) silinmiş.
- Hedef tablo (kural sorgusunun üzerinde işletilebilir) silinmiş.
- Azure Sentinel, hedef çalışma alanından kaldırılmıştır.
- Kural sorgusu tarafından kullanılan bir işlev artık geçerli değil; değiştirilmiş ya da kaldırılmış.
- Kural sorgusunun veri kaynaklarından birinin izinleri değiştirildi.
- Kural sorgusunun veri kaynaklarından biri silinmiş veya bağlantısı kesildi.

**Ardışık kalıcı hatalardan oluşan, aynı türde ve aynı kurala sahip bir olayda,** Azure Sentinel, kuralı yürütmeyi denemeyi durduruyor ve ayrıca aşağıdaki adımları gerçekleştirir:

- Kuralı devre dışı bırakır.
- **"OTOMATIK devre dışı"** sözcüklerini kural adının başlangıcına ekler.
- Hatanın nedenini (ve devre dışı bırakmayı) kuralın açıklamasına ekler.

Kural listesini ada göre sıralayarak otomatik devre dışı kuralların varlığını kolayca belirleyebilirsiniz. Otomatik devre dışı bırakılan kurallar listenin en üstünde veya yakınında olacaktır.

SOC yöneticileri, otomatik olarak devre dışı bırakılan kuralların varlığı için kural listesini düzenli olarak denetlediğinizden emin olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Sentinel kullanarak tehditleri algılamaya nasıl başlacağınız hakkında daha fazla öğrenirsiniz.

- [Azure Sentinel 'de olayları araştırmaya](tutorial-investigate-cases.md)nasıl bilgi alabileceğinizi öğrenin.
- [Azure Sentinel 'de varlıklar](entities-in-azure-sentinel.md)hakkında bilgi edinin.
- [Azure Sentinel 'de otomatik tehdit yanıtlarını ayarlamayı](tutorial-respond-threats-playbook.md)öğrenin.
