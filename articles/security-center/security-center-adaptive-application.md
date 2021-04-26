---
title: Azure Güvenlik Merkezi'ndeki uyarlamalı uygulama denetimleri
description: Bu belge, Azure makinelerinde çalışan liste uygulamalarına izin vermek için Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi kullanmanıza yardımcı olur.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834633"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Makinelerinizin saldırı yüzeylerini azaltmak için uyarlamalı uygulama denetimleri kullanma

Azure Güvenlik Merkezi 'nin Uyarlamalı uygulama denetimlerinin avantajları ve bu veri tabanlı, akıllı özellik ile güvenliği nasıl geliştirebileceğinizi öğrenin.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Güvenlik Merkezi 'nin Uyarlamalı uygulama denetimleri nelerdir?

Uyarlamalı uygulama denetimleri, makineleriniz için bilinen güvenli uygulamalar listesine izin vermeyi tanımlamaya yönelik akıllı ve otomatikleştirilmiş bir çözümdür. 

Genellikle, kuruluşların aynı işlemlerin düzenli olarak çalıştırdığı makine koleksiyonları vardır. Güvenlik Merkezi, makinelerinizde çalışan uygulamaları çözümlemek ve bilinen güvenli yazılımın bir listesini oluşturmak için makine öğrenimini kullanır. Listelere izin ver, belirli Azure iş yüklerinize dayalıdır ve aşağıdaki yönergeleri kullanarak önerileri daha ayrıntılı bir şekilde özelleştirebilirsiniz.

Uyarlamalı uygulama denetimlerini etkinleştirdikten ve yapılandırdıktan sonra, güvenli olarak tanımladıklarından başka herhangi bir uygulama çalıştırıyorsa güvenlik uyarıları alırsınız.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Uyarlamalı uygulama denetimlerinin avantajları nelerdir?

Bilinen güvenli uygulamalar listesini tanımlayarak ve başka bir şey yürütüldüğünde uyarı ürettikten sonra, birden çok sağlamlaştırma hedefi elde edebilirsiniz:

- Kötü amaçlı yazılımdan koruma çözümleri tarafından kaçırılmış olabilecek olası kötü amaçlı yazılımları belirlemek
- Yalnızca lisanslı yazılımların kullanımını gösteren yerel güvenlik ilkeleriyle uyumluluğu geliştirme
- Eski veya desteklenmeyen uygulamalar çalıştırmaktan kaçının
- Kuruluşunuz tarafından yasaklanmış olan belirli yazılımları engelleyin
- Hassas verilere erişen uygulamalardan daha fazla bakış artışı

Şu anda kullanılabilir zorlama seçeneği yok. Uyarlamalı uygulama denetimleri, güvenli olarak tanımladıklarından başka herhangi bir uygulama çalıştırıyorsa güvenlik uyarıları sağlamaya yöneliktir.

## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|[Sunucular Için Azure Defender](defender-for-servers-introduction.md) gerekir|
|Desteklenen makineler:|![Evet ](./media/icons/yes-icon.png) Azure ve Windows ve Linux çalıştıran Azure dışı makineler<br>![Evet ](./media/icons/yes-icon.png) [Azure yay](../azure-arc/index.yml) makineleri|
|Gerekli roller ve izinler:|**Güvenlik okuyucu** ve **okuyucu** rolleri hem grupları hem de bilinen güvenli uygulamaların listesini görüntüleyebilir<br>**Katkıda bulunan** ve **Güvenlik yönetici** rolleri, grupları ve bilinen güvenli uygulamaların listesini düzenleyebilir|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Bir makine grubunda uygulama denetimlerini etkinleştir

Güvenlik Merkezi, aboneliklerinizde düzenli olarak benzer bir uygulama kümesini çalıştıran makine grupları tanımlamışsa aşağıdaki öneriyle karşılaşırsınız: **güvenli uygulamalar tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir**.

Öneriyi seçin ya da Uyarlamalı uygulama denetimleri sayfasını açarak önerilen bilinen güvenli uygulamalar ve makine gruplarının listesini görüntüleyin.

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **Uyarlamalı uygulama denetimleri**' ni seçin.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Azure panosundan Uyarlamalı uygulama denetimleri açma" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    **Uyarlamalı uygulama denetimleri** sayfası, VM 'leriniz aşağıdaki sekmelerde gruplandırılarak açılır:

    - **Yapılandırılmış** -zaten tanımlı bir uygulama listesi olan makine grupları. Her grup için, yapılandırılan sekmede şunları gösterir:
        - gruptaki makinelerin sayısı
        - Son Uyarılar

    - Aynı uygulamaları tutarlı bir şekilde çalıştıran ve izin verilenler listesine sahip olmayan, **Önerilen** ve olmayan gruplar. Bu gruplar için uyarlamalı uygulama denetimlerini etkinleştirmenizi öneririz.
    
      > [!TIP]
      > "BELGEADı grubu" önekiyle bir grup adı görürseniz, bu, kısmen tutarlı bir uygulamalar listesi olan makineler içerir. Güvenlik Merkezi bir düzen göremez, ancak [grubun Uyarlamalı uygulama denetimleri kuralını düzenlemede](#edit-a-groups-adaptive-application-controls-rule)açıklanan şekilde, bazı Uyarlamalı uygulama denetimleri kurallarını el ile tanımlayıp _tanımlayamayacağını görmek_ için bu grubun gözden geçirilmesini öneriyor.
      >
      > Ayrıca, [makineyi bir gruptan diğerine taşıma](#move-a-machine-from-one-group-to-another)bölümünde açıklandığı gibi bu gruptaki makineleri diğer gruplara taşıyabilirsiniz.

    - **Öneri yok** -tanımlı bir uygulama listesi olmayan ve özelliği desteklemeyen makineler. Makineniz aşağıdaki nedenlerle bu sekmede olabilir:
      - Log Analytics Aracısı eksik
      - Log Analytics Aracısı olayları göndermiyor
      - Bu, bir GPO veya yerel güvenlik ilkesi tarafından etkinleştirilen önceden var olan [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) ilkesi olan bir Windows makinesidir

      > [!TIP]
      > Güvenlik Merkezi 'nin makine grubu başına benzersiz önerilerini tanımlamak için en az iki haftalık veri olması gerekir. Kısa süre önce oluşturulmuş olan veya Azure Defender ile yalnızca son etkinleştirilen aboneliklere ait olan makineler, **öneri yok** sekmesi altında görünür.


1. **Önerilen** sekmeyi açın. Önerilen izin verilenler listelerine sahip makine grupları görüntülenir.

   ![Önerilen sekme](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Grup seçin. 

1. Yeni kuralınızı yapılandırmak için bu **uygulama denetim kurallarını Yapılandır** sayfasının çeşitli bölümlerini gözden geçirin ve bu belirli makine grubu için benzersiz olacak içerikler:

   ![Yeni bir kural yapılandırın](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Makineleri seçin** -varsayılan olarak, tanımlanan gruptaki tüm makineler seçilidir. Bu kuraldan kaldırılan her türlü seçimi kaldırın.
   
   1. **Önerilen uygulamalar** -bu gruptaki makineler için ortak olan ve çalışmasına izin verilmesi önerilen uygulamaların listesini gözden geçirin.
   
   1. **Daha fazla uygulama** -bu gruptaki makinelerde daha az görülen veya yararlanılıdüğü bilinen uygulamalar listesini gözden geçirin. Bir uyarı simgesi, bir saldırgan tarafından uygulama izin verilenler listesini atlamak için kullanılabilecek belirli bir uygulama olduğunu gösterir. Bu uygulamaları dikkatle incelemenizi öneririz.

      > [!TIP]
      > Her iki uygulama listesi, belirli bir uygulamayı belirli kullanıcılara kısıtlama seçeneğini içerir. Mümkün olan her durumda en az ayrıcalık ilkesini benimseyin.
      > 
      > Uygulamalar yayımcıları tarafından tanımlanmıştır, bir uygulamanın yayımcı bilgileri yoksa (imzasız), belirli bir uygulamanın tam yolu için bir yol kuralı oluşturulur.

   1. Kuralı uygulamak için **Denetim**' i seçin. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Grubun Uyarlamalı uygulama denetimleri kuralını düzenleme

Kuruluşunuzdaki bilinen değişiklikler nedeniyle bir makine grubu için izin verilenler listesini düzenlemeye karar verebilirsiniz. 

Bir makine grubunun kurallarını düzenlemek için:

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **Uyarlamalı uygulama denetimleri**' ni seçin.

1. **Yapılandırılan** sekmesinden, düzenlemek istediğiniz kurala sahip grubu seçin.

1. [Bir makine grubunda Uyarlamalı uygulama denetimlerini etkinleştirme](#enable-application-controls-on-a-group-of-machines)bölümünde açıklandığı gibi **uygulama denetim kurallarını Yapılandır** sayfasının çeşitli bölümlerini gözden geçirin.

1. İsteğe bağlı olarak, bir veya daha fazla özel kural ekleyin:

   1. **Kural Ekle**' yi seçin.

      ![Özel kural ekle](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Bilinen bir güvenli yol tanımlıyorsanız, **kural türünü** ' Path ' olarak değiştirin ve tek bir yol girin. Joker karakterleri yola ekleyebilirsiniz.
   
      > [!TIP]
      > Bir yoldaki Joker karakterlere yönelik bazı senaryolar yararlı olabilir:
      > 
      > * Bu klasör ve alt klasörlerdeki tüm yürütülebilir dosyalara izin vermek için yolun sonunda bir joker karakter kullanma.
      > * Bilinen bir yürütülebilir adı, değişen klasör adı (örneğin, bilinen bir yürütülebiliri içeren kişisel Kullanıcı klasörleri, otomatik olarak oluşturulan klasör adları vb.) sağlamak için yolun ortasında bir joker karakter kullanma.
  
   1. İzin verilen kullanıcıları ve korumalı dosya türlerini tanımlayın.

   1. Kuralı tanımlamayı bitirdiğinizde **Ekle**' yi seçin.

1. Değişiklikleri uygulamak için **Kaydet**' i seçin.


## <a name="review-and-edit-a-groups-settings"></a>Grubun ayarlarını gözden geçirme ve düzenleme

1. Grubunuzun ayrıntılarını ve ayarlarını görüntülemek için **Grup ayarları** ' nı seçin.

    Bu bölmede grubun adı (değiştirilebilir), işletim sistemi türü, konum ve ilgili diğer ayrıntılar gösterilir.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Uyarlamalı uygulama denetimleri için Grup ayarları sayfası" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. İsteğe bağlı olarak, grubun adını veya dosya türü koruma modlarını değiştirin.

1. **Uygula** ve **Kaydet**' i seçin.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>"Uyarlamalı uygulama denetim ilkenizde izin verilen Allowlist Rules" önerisini yanıtlayın

Güvenlik Merkezi 'nin makine öğrenimi, daha önceden izin verilmeyen yasal davranış belirlediğinde, bu öneriyi görürsünüz. Öneri, hatalı pozitif uyarıların sayısını azaltmak için mevcut tanımlarınız için yeni kurallar önerir.

Sorunları düzeltmek için:

1. Öneriler sayfasında, **Uyarlamalı uygulama denetim ilkenizde bulunan Allowlist kurallarını** seçerek, yeni tanımlanan, olası yasal davranışa sahip grupları görmek için önerilir.

1. Düzenlemek istediğiniz kurala sahip grubu seçin.

1. [Bir makine grubunda Uyarlamalı uygulama denetimlerini etkinleştirme](#enable-application-controls-on-a-group-of-machines)bölümünde açıklandığı gibi **uygulama denetim kurallarını Yapılandır** sayfasının çeşitli bölümlerini gözden geçirin.

1. Değişiklikleri uygulamak için **Denetim**' i seçin.




## <a name="audit-alerts-and-violations"></a>Denetim uyarıları ve ihlalleri

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **Uyarlamalı uygulama denetimleri**' ni seçin.

1. Son uyarıları olan makineleri içeren grupları görmek için, **yapılandırılan** sekmede listelenen grupları gözden geçirin.

1. Daha fazla araştırmak için bir grup seçin.

   ![Son Uyarılar](./media/security-center-adaptive-application/recent-alerts.png)

1. Daha fazla ayrıntı ve etkilenen makinelerin listesi için bir uyarı seçin.

    Uyarılar sayfası uyarıların daha ayrıntılı ayrıntılarını gösterir ve tehdidi hafifletmek için öneriler içeren bir **eylem al** bağlantısı sağlar.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Uyarlamalı uygulama denetimleri uyarılarının başlangıç saati ":::

    > [!NOTE]
    > Uyarlamalı uygulama denetimleri her on iki saatte bir olay hesaplar. Uyarılar sayfasında gösterilen "etkinlik başlangıç saati", uyarlamalı uygulama denetimlerinin şüpheli işlemin etkin olduğu zamandan **değil** , uyarı oluşturduğu süredir.


## <a name="move-a-machine-from-one-group-to-another"></a>Bir makineyi bir gruptan diğerine taşıma

Bir makineyi bir gruptan diğerine taşıdığınızda, uygulanan uygulama denetim ilkesi, onu taşıdığınız grubun ayarlarına değişir. Ayrıca, bir makineyi yapılandırılmış bir gruptan yapılandırılmamış bir gruba taşıyabilirsiniz, böylece makineye uygulanan uygulama denetim kuralları da kaldırılır.

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **Uyarlamalı uygulama denetimleri**' ni seçin.

1. **Uyarlamalı uygulama denetimleri** sayfasında, **yapılandırılan** sekmesinden, taşınacak makineyi içeren grubu seçin.

1. **Yapılandırılmış makinelerin** listesini açın.

1. Satırın sonundaki üç noktadan makinenin menüsünü açın ve **Taşı**' yı seçin. **Makineyi farklı bir grup bölmesine taşı** açılır.

1. Hedef grubunu seçin ve **makineyi taşı**' yı seçin.

1. Yaptığınız değişiklikleri kaydetmek için **Kaydet**'i seçin.





## <a name="manage-application-controls-via-the-rest-api"></a>Uygulama denetimlerini REST API aracılığıyla yönetme 

Uyarlamalı uygulama denetimlerinizi program aracılığıyla 'yi yönetmek için REST API kullanın. 

İlgili API belgeleri, [Güvenlik Merkezi 'nın API belgelerinin Uyarlamalı uygulama denetimleri bölümünde](/rest/api/securitycenter/adaptiveapplicationcontrols)bulunur.

REST API kullanılabilen işlevlerden bazıları:

* **Liste** tüm grup önerilerinizi alır ve her grup için bir NESNEYLE bir JSON sağlar.

* **Get** , JSON 'u tam öneri verileriyle (diğer bir deyişle, makine listesi, yayımcı/yol kuralları vb.) alır.

* **PUT** , kuralınızı yapılandırır (Bu isteğin gövdesi olarak **Al** ile aldığınız JSON 'ı kullanın).
 
   > [!IMPORTANT]
   > **PUT** işlevi get komutu tarafından döndürülen JSON 'dan daha az parametre bekler.
   >
   > PUT isteğinde JSON kullanmadan önce şu özellikleri kaldırın: recommendationStatus, configurationStatus, sorunlar, konum ve dir.


## <a name="faq---adaptive-application-controls"></a>SSS-Uyarlamalı uygulama denetimleri

- [Uygulama denetimlerini zorlamak için herhangi bir seçenek var mı?](#are-there-any-options-to-enforce-the-application-controls)
- [Recommendeded uygulamamda neden bir Qualys uygulaması görüyorum?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Uygulama denetimlerini zorlamak için herhangi bir seçenek var mı?
Şu anda kullanılabilir zorlama seçeneği yok. Uyarlamalı uygulama denetimleri, güvenli olarak tanımladıklarından başka herhangi bir uygulama çalıştırıyorsa **güvenlik uyarıları** sağlamaya yöneliktir. Bunlar, bir dizi avantaja sahiptir ([Uyarlamalı uygulama denetimlerinin avantajları nelerdir?](#what-are-the-benefits-of-adaptive-application-controls)) ve bu sayfada gösterildiği gibi son derece özelleştirilebilir.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Recommendeded uygulamamda neden bir Qualys uygulaması görüyorum?
[Sunucular Için Azure Defender](defender-for-servers-introduction.md) , ek ücret ödemeden makineleriniz için güvenlik açığı taraması içerir. Bir Qualys lisansına veya hatta bir Qualys hesabına ihtiyacınız yoktur. her şey güvenlik merkezi 'nin içinde sorunsuz bir şekilde işlenir. Bu tarayıcının ayrıntıları ve nasıl dağıtılacağı hakkında yönergeler için bkz. [Defender 'ın tümleşik güvenlik açığı değerlendirme çözümü](deploy-vulnerability-assessment-vm.md).

Güvenlik Merkezi tarayıcıyı dağıttığında hiçbir uyarının oluşturulmemesini sağlamak için, uyarlamalı uygulama denetim önerilen izin listesi, tüm makineler için tarayıcıyı içerir. 


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi 'ni kullanarak Azure 'da ve Azure dışı makinelerde çalışan uygulamaların izin verilenler listesini nasıl tanımlayacağınızı öğrendiniz. Bazı güvenlik merkezi iş yükü koruma özellikleri hakkında daha fazla bilgi edinmek için bkz.:

* [Tam zamanında (JIT) VM erişimini anlama](just-in-time-explained.md)
* [Azure Kubernetes kümelerinizin güvenliğini sağlama](defender-for-kubernetes-introduction.md)