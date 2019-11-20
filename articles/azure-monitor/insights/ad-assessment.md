---
title: Azure Izleyici ile Active Directory ortamınızı iyileştirin | Microsoft Docs
description: Düzenli aralıklarla ortamlarınızın riskini ve sistem durumunu değerlendirmek için Active Directory sistem durumu denetimi çözümünü kullanabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/10/2019
ms.openlocfilehash: bdc84a9213bd98981040775d3fec90f45edac54f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899198"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Azure Izleyici 'de Active Directory sistem durumu denetimi çözümüyle Active Directory ortamınızı iyileştirin

![AD sistem durumu denetim simgesi](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Düzenli aralıklarla sunucu ortamlarınızın riskini ve sistem durumunu değerlendirmek için Active Directory sistem durumu denetimi çözümünü kullanabilirsiniz. Bu makale, çözümü yüklemenize ve kullanmanıza yardımcı olur, böylece olası sorunlar için düzeltici eylemler gerçekleştirebilirsiniz.

Bu çözüm, dağıtılan sunucu altyapınıza özgü önerilerin öncelikli bir listesini sağlar. Öneriler, riski hızla anlamanıza ve işlem yapmanıza yardımcı olan dört odak alanına göre kategorize edilir.

Öneriler, binlerce müşteri ziyaretinden Microsoft mühendisleri tarafından elde edilen bilgi ve deneyime dayanır. Her öneri, bir sorunun neden bir sorun ve önerilen değişikliklerin nasıl uygulanacağı hakkında rehberlik sağlar.

Kuruluşunuz için en önemli odak alanını seçebilir ve risk ücretsiz ve sağlıklı bir ortamı çalıştırmaya yönelik ilerlemenizi izleyebilirsiniz.

Çözümü ekledikten ve bir onay tamamlandıktan sonra, ortamınızdaki altyapı için **ad sistem durumu denetimi** panosunda odak alanlarının Özet bilgileri gösterilir. Aşağıdaki bölümlerde, Active Directory sunucu altyapınız için önerilen eylemleri görüntüleyip uygulayabileceğiniz **ad sistem durumu denetimi** panosundaki bilgilerin nasıl kullanılacağı açıklanır.  

![AD sistem durumu denetimi kutucuğunun görüntüsü](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![AD sistem durumu denetimi panosunun görüntüsü](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Önkoşullar

* Active Directory sistem durumu denetimi çözümü, Windows için Log Analytics aracısına sahip olan (Microsoft Monitoring Agent (MMA) olarak da bilinir) yüklü .NET Framework 4.5.2 veya üzeri bir sürümü gerektirir.  Aracı System Center 2016-Operations Manager, Operations Manager 2012 R2 ve Azure Izleyici tarafından kullanılır.
* Bu çözüm, Windows Server 2008 ve 2008 R2, Windows Server 2012 ve 2012 R2 ve Windows Server 2016 çalıştıran etki alanı denetleyicilerini destekler.
* Azure portal Azure Marketi 'nden Active Directory sistem durumu denetimi çözümünü eklemek için bir Log Analytics çalışma alanı. Ek yapılandırma gerekmez.

  > [!NOTE]
  > Çözümü ekledikten sonra, danışmanlı Assessment. exe dosyası aracıları olan sunuculara eklenir. Yapılandırma verileri okuyup işlenmek üzere Bulutta Azure Izleyici 'ye gönderilir. Alınan verilere mantık uygulanır ve bulut hizmeti verileri kaydeder.
  >
  >

Değerlendirilecek etki alanının üyesi olan etki alanı denetleyicileriniz için sistem durumu denetimini gerçekleştirmek üzere, bu etki alanındaki her etki alanı denetleyicisi, aşağıdaki desteklenen yöntemlerden birini kullanarak bir aracı ve Azure Izleyici bağlantısı gerektirir:

1. Etki alanı denetleyicisi System Center 2016-Operations Manager veya Operations Manager 2012 R2 tarafından zaten izlenmediği takdirde [Windows için Log Analytics aracısını](../../azure-monitor/platform/agent-windows.md) yükler.
2. System Center 2016-Operations Manager veya Operations Manager 2012 R2 ile izleniyorsa ve yönetim grubu Azure Izleyici ile tümleşikse, etki alanı denetleyicisi, veri toplamak ve hizmete iletmek için Azure Izleyici ile çok daha fazla erişilebilir olabilir ve yine de Operations Manager tarafından izlenir.  
3. Aksi takdirde, Operations Manager yönetim grubunuz hizmetle tümleşikse, çözümü etkinleştirmek üzere [Aracı tarafından yönetilen bilgisayarlar ekleme](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) altındaki adımları izleyerek, hizmet tarafından veri toplamaya yönelik etki alanı denetleyicilerini eklemeniz gerekir. alanında.  

Etki alanı denetleyicinizde bir Operations Manager yönetim grubuna rapor veren, verileri toplayan, atanan yönetim sunucusuna ileten ve sonra doğrudan bir yönetim sunucusundan Azure Izleyici 'ye gönderilen aracı.  Veriler Operations Manager veritabanlarına yazılmaz.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory sistem durumu denetimi verileri toplama ayrıntıları

Active Directory sistem durumu denetimi, etkinleştirdiğiniz aracıyı kullanarak aşağıdaki kaynaklardan veri toplar:

- Kayıt Defteri
- LDAP
- .NET Framework
- Olay günlüğü
- Active Directory Hizmet Arabirimleri (ADSI)
- Windows PowerShell
- Dosya verileri
- Windows Yönetim Araçları (WMI)
- DCDIAG aracı API 'SI
- Dosya Çoğaltma hizmeti (NTFRS) API 'SI
- Özel C# kod

Veriler etki alanı denetleyicisinde toplanır ve yedi günde bir Azure Izleyici 'ye iletilir.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Önerilerin nasıl önceliklendirildiğini anlama

Yapılan her öneriye, önerinin göreli önemini tanımlayan bir ağırlık değeri verilir. Yalnızca en önemli 10 öneri gösterilmektedir.

### <a name="how-weights-are-calculated"></a>Ağırlıklar nasıl hesaplanır

Ağırlıklı değerler üç ana etkene göre toplam değerlerdir:

* Bir sorunu tespit eden *olasılık* sorunlara neden olur. Daha yüksek bir olasılık, öneriye ilişkin daha büyük bir genel puanı elde eden bir olasılıktır.
* Soruna neden olursa sorunun kuruluşunuza *etkisi* . Daha yüksek bir etkisi, önerinin daha büyük bir genel puanı ile aynıdır.
* Öneriyi uygulamak için gereken *çaba* . Daha yüksek bir çaba, önerinin daha küçük bir genel puanı ile aynıdır.

Her öneri ağırlığı, her bir odak alanı için kullanılabilen toplam puanların yüzdesi olarak ifade edilir. Örneğin, güvenlik ve uyumluluk odağında bir önerinin %5 puanı varsa, bu öneriyi uygulamak genel güvenlik ve uyumluluk puanınızı %5 oranında artırır.

### <a name="focus-areas"></a>Odak alanı

**Güvenlik ve uyumluluk** -bu odak alanı, olası güvenlik tehditleri ve ihlal, kurumsal ilkeler ve teknik, yasal ve yasal uyumluluk gereksinimlerine yönelik öneriler gösterir.

**Kullanılabilirlik ve Iş sürekliliği** -bu odak alanı hizmet kullanılabilirliği, altyapınızın esnekliği ve iş koruma önerilerini gösterir.

**Performans ve ölçeklenebilirlik** -bu odak alanı, kuruluşunuzun BT altyapısının BÜYÜMESI, BT ortamınızın geçerli performans gereksinimlerini karşıladığından emin olmak ve değişen altyapı ihtiyaçlarına yanıt verebilmeleri için öneriler gösterir.

**Yükseltme, geçiş ve dağıtım** -bu odak alanı, mevcut altyapınıza Active Directory yükseltmenize, geçirmenize ve dağıtmanıza yardımcı olacak öneriler gösterir.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Her odak alanında %100 puan elde etmeniz gerekir mi?

Olmayabilir. Öneriler, binlerce müşteri ziyaretinde Microsoft mühendisleri tarafından kazanılan bilgi ve deneyimlere dayalıdır. Ancak, iki sunucu altyapısı aynı değildir ve belirli öneriler sizinle daha fazla veya daha az olabilir. Örneğin, sanal makineleriniz Internet 'e açık değilse bazı güvenlik önerileri daha az ilgili olabilir. Bazı kullanılabilirlik önerileri düşük öncelikli geçici veri toplama ve raporlama sağlayan hizmetlerle daha az ilgili olabilir. Yetişkin iş açısından önemli olan sorunlar, başlangıç için daha az önemli olabilir. Önceliklerinizin hangi odak alanlarından olduğunu belirlemek ve sonra puanlarınızın zaman içinde nasıl değişdiklerini görmek isteyebilirsiniz.

Her öneri, neden önemli olduğuna ilişkin bir kılavuz içerir. BT hizmetlerinizin doğası ve kuruluşunuzun iş ihtiyaçları göz önüne alındığında, öneriyi uygulamanız için uygun olup olmadığını değerlendirmek için bu kılavuzu kullanmanız gerekir.

## <a name="use-health-check-focus-area-recommendations"></a>Sistem durumu denetimi odak alanı önerilerini kullan

Yüklendikten sonra, Azure portal çözüm sayfasında sistem durumu denetimi kutucuğunu kullanarak önerilerin özetini görüntüleyebilirsiniz.

Altyapınız için özetlenen uyumluluk değerlendirmelerini görüntüleyin ve sonra öneriler ' e gidin.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Bir odak alanı önerilerini görüntülemek ve düzeltici eylemi gerçekleştirmek için

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. **Genel bakış** sayfasında, **Active Directory sistem durumu denetim** kutucuğuna tıklayın.

2. **Sistem durumu denetimi** sayfasında, odak alanı Dikey penceresinde bulunan Özet bilgilerini gözden geçirin ve ardından bu odak alanı önerilerini görüntülemek için bir tane tıklatın.

3. Odak alanı sayfalarında, ortamınız için yapılan öncelikli önerileri görüntüleyebilirsiniz. Önerinin neden yapıldığına ilişkin ayrıntıları görüntülemek için **etkilenen nesneler** altında bir öneriye tıklayın.

    ![Sistem durumu denetimi önerilerinin görüntüsü](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. **Önerilen eylemlerde**önerilen düzeltici eylemler gerçekleştirebilirsiniz. Öğe ne zaman giderildiği, daha sonraki değerlendirmeler önerilen eylemlerin alındığını ve uyumluluk puanınız artacaktır. Düzeltilen öğeler **geçirilmiş nesneler**olarak görünür.

## <a name="ignore-recommendations"></a>Önerileri yoksay

Yok saymak istediğiniz önerileriniz varsa, Azure Izleyici 'nin değerlendirme sonuçlarınızda görünmesini engellemek için kullanacağı bir metin dosyası oluşturabilirsiniz.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Yok sayılacak önerileri belirlemek için

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Ortamınızdaki bilgisayarlarda başarısız olan önerileri listelemek için aşağıdaki sorguyu kullanın.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Günlük sorgusunun gösterildiği bir ekran görüntüsü aşağıda verilmiştir: <

![başarısız öneriler](media/ad-assessment/ad-failed-recommendations.png)

Yoksaymak istediğiniz önerileri seçin. Sonraki yordamda RecommendationId için değerleri kullanacaksınız.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Bir ıgnorereyorum geçişleri. txt metin dosyası oluşturmak ve kullanmak için

1. Ignorereyorumgeçişleri. txt adlı bir dosya oluşturun.

2. Azure Izleyici 'nin ayrı bir satırda yok saymasını istediğiniz her öneri için her bir RecommendationId yapıştırın veya yazın, sonra dosyayı kaydedip kapatın.

3. Azure Izleyici 'nin önerileri yoksaymasını istediğiniz her bilgisayarda dosyayı aşağıdaki klasöre yerleştirin.

   * Microsoft Monitoring Agent olan bilgisayarlarda (doğrudan veya Operations Manager üzerinden bağlı)- *systemdrive*: \Program Files\Microsoft Monitoring Tors t\agent
   * Operations Manager 2012 R2 Management Server- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations manager\server
   * Operations Manager 2016 yönetim sunucusu- *systemdrive*: \Program Files\Microsoft System Center 2016 \ Operations manager\server

### <a name="to-verify-that-recommendations-are-ignored"></a>Önerilerin yoksayıldığını doğrulamak için

Zamanlanan bir sonraki sistem durumu denetimi çalıştıktan sonra, varsayılan olarak yedi günde bir, belirtilen öneri *yoksayıldı* olarak işaretlenir ve panoda görünmez.

1. Tüm yoksayılan önerileri listelemek için aşağıdaki günlük sorgularını kullanabilirsiniz.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Daha sonra yoksayılan önerilere bakmak istediğinize karar verirseniz, tüm ıgnorereyorum. txt dosyalarını kaldırın veya RecommendationIDs kaldırabilir.

## <a name="ad-health-check-solutions-faq"></a>AD sistem durumu denetimi çözümleri SSS

*Bir sistem durumu denetimi ne sıklıkta çalıştırılır?*

* Onay her yedi günde bir çalıştırılır.

*Sistem durumu denetiminin ne sıklıkta çalışacağını yapılandırmak için bir yol var mı?*

* Şimdilik hayır.

*Bir sistem durumu denetimi çözümünü ekledikten sonra başka bir sunucu bulunursa, denetlenecek*

* Evet, bu, her yedi günde bir üzerinde denetlenir.

*Sunucu kullanımdan çıkarılmışsa, sistem durumu denetiminden kaldırılır?*

* Bir sunucu 3 hafta boyunca veri göndermezse, kaldırılır.

*Veri toplamayı yapan işlemin adı nedir?*

* Danışmanorassessment. exe

*Verilerin toplanması ne kadar sürer?*

* Sunucu üzerindeki gerçek veri koleksiyonu yaklaşık 1 saat sürer. Çok sayıda Active Directory sunucusu olan sunucularda daha uzun sürebilir.

*Verilerin toplanması sırasında yapılandırmak için bir yol var mı?*

* Şimdilik hayır.

*Neden yalnızca ilk 10 öneriyi görüntülersin?*

* Size ayrıntılı bir görev listesi vermek yerine öncelikle öncelikli önerileri gidermeye odaklanmanız önerilir. Bunları adresledikten sonra ek öneriler kullanılabilir hale gelir. Ayrıntılı listeyi görmeyi tercih ediyorsanız, bir günlük sorgusu kullanarak tüm önerileri görüntüleyebilirsiniz.

*Öneriyi yoksaymak için bir yol var mı?*

* Evet, bkz. Yukarıdaki [önerileri yoksay](#ignore-recommendations) bölümü.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıntılı AD sistem durumu denetimi verilerini ve önerilerini çözümlemeyi öğrenmek için [Azure izleyici günlük sorgularını](../log-query/log-query-overview.md) kullanın.
