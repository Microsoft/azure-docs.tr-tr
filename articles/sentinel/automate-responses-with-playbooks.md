---
title: Azure Sentinel 'de PlayBook 'ları ile tehdit yanıtını otomatikleştirin | Microsoft Docs
description: Bu makalede, Azure Sentinel 'de Otomasyon açıklanmakta ve PlayBook 'ları 'ın tehdit engellemesini ve yanıtı otomatikleştirmek için nasıl kullanılacağı gösterilmektedir.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610098"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Azure Sentinel 'de PlayBook 'ları ile tehdit yanıtını otomatikleştirin

Bu makalede, Azure Sentinel PlayBook 'ları ve bunların güvenlik düzenleme, otomasyon ve yanıt (SOAR) işlemlerini uygulamak için nasıl kullanılacağı açıklanmakta ve zaman ve kaynakları kaydederken daha iyi sonuçlar elde edilir.

## <a name="what-is-a-playbook"></a>PlayBook nedir?

SıEM/SOC ekipleri genellikle, kullanılabilir personelin çok büyük olması için birimlerde düzenli olarak güvenlik uyarıları ve olayları açığa kaldırmalardır. Bu, çok sayıda uyarının yoksayıldığı ve çok sayıda olayın incelenemediği durumlarda, kuruluşun fark edilmemiş saldırılara karşı savunmasız kalmasına neden olur.

Çoğu, bu uyarıların ve olayların çoğu, belirli ve tanımlı düzeltme eylemleri kümeleri tarafından giderilebildiğiniz yineleme desenlerine uygundur.

PlayBook, Azure Sentinel 'ten bir yordam olarak çalıştırılabilen Bu düzeltme eylemlerinin bir koleksiyonudur. Bir PlayBook, tehdit yanıtınızı otomatikleştirmenize ve düzenlemenize yardımcı olabilir; Bu, el ile çalıştırılabilir veya bir analiz kuralı veya bir Otomasyon kuralı tarafından tetiklendiğinde belirli uyarılara veya olaylara yanıt olarak otomatik olarak çalışacak şekilde ayarlanabilir.

PlayBook 'lar oluşturulur ve abonelik düzeyinde uygulanır, ancak **PlayBook 'ları** sekmesi (yeni **Otomasyon** dikey penceresinde) seçili aboneliklerde bulunan tüm PlayBook 'ları görüntüler.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps temel kavramlar

Azure Sentinel 'de playbooks, kurumsal bir sistem genelinde sistemler arasında görev ve iş akışlarını zamanlamanıza, otomatikleştirmenize ve ayarlamanıza yardımcı olan [Azure Logic Apps](../logic-apps/logic-apps-overview.md)yerleşik iş akışlarını temel alır. Bu, PlayBook 'ları 'ın Logic Apps ' yerleşik şablonlarının tüm gücünden ve özelleştirme avantajlarından yararlanabilme anlamına gelir.

> [!NOTE]
> Azure Logic Apps ayrı bir kaynak olduğundan, ek ücretler uygulanabilir. Daha fazla ayrıntı için [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) fiyatlandırma sayfasını ziyaret edin.

Azure Logic Apps bağlayıcıları kullanarak diğer sistemlerle ve hizmetlerle iletişim kurar. Aşağıda bağlayıcılar ve bazı önemli öznitelikleri hakkında kısa bir açıklama verilmiştir:

- **Yönetilen bağlayıcı:** Belirli bir ürün veya hizmete yönelik API çağrıları etrafında sarmalama yapılan eylemler ve Tetikleyiciler kümesi. Azure Logic Apps hem Microsoft hem de Microsoft dışı hizmetlerle iletişim kurmak için yüzlerce bağlayıcı sunar.
  - [Tüm Logic Apps bağlayıcıların ve belgelerinin listesi](/connectors/connector-reference/)

- **Özel bağlayıcı:** Önceden oluşturulmuş bağlayıcılar olarak kullanılamayan hizmetlerle iletişim kurmak isteyebilirsiniz. Özel Bağlayıcılar, bir bağlayıcıyı oluşturma (ve hatta paylaşma) ve kendi tetiklerini ve eylemlerini tanımlamanızı sağlayarak bu ihtiyacı ele geçirir.
  - [Kendi özel Logic Apps bağlayıcılarınızı oluşturun](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure Sentinel Bağlayıcısı:** Azure Sentinel ile etkileşime geçen PlayBook 'lar oluşturmak için Azure Sentinel bağlayıcısını kullanın.
  - [Azure Sentinel bağlayıcı belgeleri](/connectors/azuresentinel/)

- **Tetikleyici:** PlayBook 'u Başlatan bağlayıcı bileşeni. Bu, PlayBook 'un tetiklendiğinde almasını beklediği şemayı tanımlar. Azure Sentinel bağlayıcısının Şu anda iki tetikleyici vardır:
  - [Uyarı tetikleyicisi](/connectors/azuresentinel/#triggers): PlayBook, giriş olarak uyarıyı alır.
  - [Olay tetikleyicisi](/connectors/azuresentinel/#triggers): PlayBook, dahil edilen tüm uyarıları ve varlıklarıyla birlikte olayı giriş olarak alır.

    > [!IMPORTANT]
    >
    > - PlayBook 'ları için **olay tetikleyicisi** özelliği şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

- **Eylemler:** Eylemler, tetikleyiciden sonra gerçekleşen tüm adımlardır. Bunlar ardışık olarak, paralel veya karmaşık koşullar matrisine göre düzenlenebilirler.

- **Dinamik alanlar:** Tetikleyici ve eylemlerin çıkış şemasına göre belirlenen ve gerçek çıktılarına göre belirlenen, izleyen eylemlerde kullanılabilen geçici alanlar.

### <a name="permissions-required"></a>Gerekli izinler

 SecOps ekibinize güvenlik düzenlemesi, otomasyon ve yanıt (SOAR) işlemleri için Logic Apps kullanabilme (yani, PlayBook 'lar oluşturmak ve çalıştırmak için) Azure Sentinel 'de, Azure rollerini, güvenlik işlemleri ekibinizin belirli üyelerine veya tüm ekibe atayabilirsiniz. Aşağıda farklı kullanılabilir roller ve atanması gereken görevler açıklanmaktadır:

#### <a name="azure-roles-for-logic-apps"></a>Logic Apps için Azure rolleri

- **Mantıksal uygulama katılımcısı** , mantıksal uygulamaları yönetmenizi ve PlayBook 'ları çalıştırmanızı sağlar, ancak bunlara erişimi değiştiremezsiniz ( **sahip** rolü gereklidir).
- **Logic App operatörü**  Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremezsiniz.

#### <a name="azure-roles-for-sentinel"></a>Sentinel için Azure rolleri

- **Azure Sentinel katılımcısı** rolü, bir analiz kuralına PlayBook eklemenize olanak tanır.
- **Azure Sentinel Yanıtlayıcı** rolü, bir PlayBook 'u el ile çalıştırmanızı sağlar.
- **Azure Sentinel Automation katılımcısı** , Otomasyon kurallarının PlayBook 'ları çalıştırmasına izin verir. Başka bir amaçla kullanılmaz.

#### <a name="learn-more"></a>Daha fazla bilgi edinin

- [Azure Logic Apps 'de Azure rolleri hakkında daha fazla bilgi edinin](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- Azure [Sentinel 'de Azure rolleri hakkında daha fazla bilgi edinin](roles.md).

## <a name="steps-for-creating-a-playbook"></a>PlayBook oluşturma adımları

- [Otomasyon senaryosunu tanımlayın](#use-cases-for-playbooks).

- [Azure mantıksal uygulamasını oluşturun](tutorial-respond-threats-playbook.md).

- [Mantıksal uygulamanızı test edin](#run-a-playbook-manually-on-an-alert).

- PlayBook 'u bir [Otomasyon kuralına](#incident-creation-automated-response) veya [analiz kuralına](#alert-creation-automated-response)ekleyin veya [gerektiğinde el ile çalıştırın](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>PlayBook 'lar için kullanım örnekleri

Azure Logic Apps platformu yüzlerce eylem ve tetikleyici sunarak neredeyse tüm otomasyon senaryosu oluşturulabilir. Azure Sentinel, aşağıdaki SOC senaryolarıyla başlamasını önerir:

#### <a name="enrichment"></a>Zenginleştirme

**Daha akıllı kararlar almak için veri toplayın ve olaya bağlayın.**

Örnek:

Bir uyarıdan Azure Sentinel olayı, IP adresi varlıkları üreten bir analiz kuralına göre oluşturuldu.

Olay, aşağıdaki adımlarla bir PlayBook çalıştıran bir Otomasyon kuralını tetikler:

- [Yeni bir Azure Sentinel olayı oluşturulduğunda](/connectors/azuresentinel/#triggers)başlayın. Olayda temsil edilen varlıklar, Olay tetikleyicisinin dinamik alanlarında depolanır.

- Her IP adresi için, daha fazla veri almak üzere [virüs toplamı](https://www.virustotal.com/)gibi bir dış tehdit bilgileri sağlayıcısını sorgulayın.

- Döndürülen verileri ve öngörüleri olay açıklaması olarak ekleyin.

#### <a name="bi-directional-sync"></a>İki yönlü eşitleme

**Playbooks 'lar, Azure Sentinel olaylarınızı diğer anahtar oluşturma sistemleriyle eşitlemek için kullanılabilir.**

Örnek:

Tüm olay oluşturma için bir Otomasyon kuralı oluşturun ve ServiceNow içinde bir bilet açan bir PlayBook ekleyin:

- [Yeni bir Azure Sentinel olayı oluşturulduğunda](/connectors/azuresentinel/#triggers)başlayın.

- [ServiceNow](/connectors/service-now/)içinde yeni bir bilet oluşturun.

- Kolay Özet için olay adını, önemli alanları ve Azure Sentinel olayına bir URL 'YI ekleyin.

#### <a name="orchestration"></a>Düzenleme

**Olaylar kuyruğunu daha iyi denetlemek için SOC sohbet platformunu kullanın.**

Örnek:

Bir uyarıdan Kullanıcı adı ve IP adresi varlıkları üreten bir analiz kuralıyla bir Azure Sentinel olayı oluşturuldu.

Olay, aşağıdaki adımlarla bir PlayBook çalıştıran bir Otomasyon kuralını tetikler:

- [Yeni bir Azure Sentinel olayı oluşturulduğunda](/connectors/azuresentinel/#triggers)başlayın.

- Güvenlik analistlerinizin olaydan haberdar olduğundan emin olmak için [Microsoft ekiplerinde](/connectors/teams/) veya [bolluk](/connectors/slack/) 'de güvenlik işlemleri kanalınıza bir ileti gönderin.

- Uyarı içindeki tüm bilgileri, üst düzey ağ yöneticinize ve güvenlik yöneticinize e-posta ile gönderin. E-posta iletisi, **Engelle** ve **Yoksay** Kullanıcı seçeneği düğmelerini içerecektir.

- Yöneticilerden bir yanıt alınana kadar bekleyip çalışmaya devam edin.

- Yöneticiler **blok**' ı seçtiyseniz, UYARıDAKI IP adresini engellemek için güvenlik duvarına bir komut gönderin ve kullanıcıyı devre dışı bırakmak IÇIN Azure AD ' a bir tane yapın.

#### <a name="response"></a>Yanıt

**En az insan bağımlılıklarıyla tehditleri hemen yanıtlayın.**

İki örnek:

**Örnek 1:** [Azure AD kimlik koruması](../active-directory/identity-protection/overview-identity-protection.md)tarafından keşfedilen, güvenliği aşılmış bir kullanıcıyı gösteren bir analiz kuralına yanıt verir:

   - [Yeni bir Azure Sentinel olayı oluşturulduğunda](/connectors/azuresentinel/#triggers)başlayın.

   - Olaydaki her bir Kullanıcı varlığının tehlikede olduğu şüpheli:

     - Kullanıcıya bir takımlar iletisi göndererek kullanıcının şüpheli eylemi gerçekleştirdiğine ilişkin bir onay isteği gönderin.

     - [Kullanıcının durumunu tehlikeye atıldığından emin](/connectors/azureadip/#confirm-a-risky-user-as-compromised)olmak için Azure AD kimlik koruması başvurun. Azure AD Kimlik Koruması, kullanıcıyı **riskli** olarak etiketleyip daha önce yapılandırılmış herhangi bir zorlama ilkesi (örneğin, kullanıcının bir sonraki oturum açma sırasında MFA kullanmasını gerektirmek için) uygular.

        > [!NOTE]
        > PlayBook, Kullanıcı üzerinde herhangi bir zorlama eylemi başlatmaz veya herhangi bir zorlama ilkesi yapılandırması başlatmaz. Yalnızca Azure AD Kimlik Koruması, zaten tanımlanmış olan ilkeleri uygun şekilde uygulamasını söyler. Herhangi bir zorlama, tamamen Azure AD Kimlik Koruması tanımlanmakta olan uygun ilkelere bağlıdır.

**Örnek 2:** [Uç nokta Için Microsoft Defender](/windows/security/threat-protection/)tarafından keşfedilen, güvenliği aşılmış bir makineyi gösteren bir analiz kuralına yanıt verme:

   - [Yeni bir Azure Sentinel olayı oluşturulduğunda](/connectors/azuresentinel/#triggers)başlayın.

   - Olay varlıklarına dahil edilen şüpheli makineleri ayrıştırmak için Azure Sentinel 'de **varlıklar-Konakları al** eylemini kullanın.

   - Uyarıdaki [makineleri yalıtmak](/connectors/wdatp/#actions---isolate-machine) Için uç nokta Için Microsoft Defender 'a bir komut verin.

## <a name="how-to-run-a-playbook"></a>PlayBook çalıştırma

Playbooks **el ile** ya da **otomatik olarak** çalıştırılabilir.

Bunları el ile çalıştırmak, bir uyarı aldığınızda, seçili uyarıya yanıt olarak isteğe bağlı bir PlayBook çalıştırmayı tercih edebilirsiniz. Şu anda bu özellik yalnızca uyarılar için desteklenir, olaylar için desteklenmez.

Onları otomatik olarak çalıştırmak bir analiz kuralında (Uyarılar için) otomatik bir yanıt olarak ya da bir Otomasyon kuralında (olaylar için) bir eylem olarak ayarlanmasıdır. [Otomasyon kuralları hakkında daha fazla bilgi edinin](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Otomatik Yanıt ayarlama

Güvenlik işlemleri ekipleri yinelenen olay ve uyarı türlerine yönelik rutin yanıtları tamamen otomatikleştirerek iş yükünü önemli ölçüde azaltarak, benzersiz olaylar ve uyarılar üzerinde daha fazla odaklanmanızı, desenleri, tehdit arayanları ve daha fazlasını analiz etmenize olanak tanır.

Otomatik Yanıt ayarlama, bir analiz kuralının her tetiklenişinde, bir uyarı oluşturmanın yanı sıra kural tarafından oluşturulan uyarının giriş olarak alacağı bir PlayBook çalıştıracaktır.

Uyarı bir olay oluşturursa olay, uyarı tarafından oluşturulan olayın bir girişi olarak alacak bir PlayBook çalıştıran bir Otomasyon kuralı tetikleyecektir.

#### <a name="alert-creation-automated-response"></a>Uyarı oluşturma otomatik yanıtı

Uyarı oluşturma tarafından tetiklenen ve girdileri (ilk adım "bir Azure Sentinel uyarısı tetiklendiğinde") olarak bildiren PlayBook 'ları 'lar için, PlayBook 'u bir analiz kuralına ekleyin:

1. İçin otomatik yanıt tanımlamak istediğiniz uyarıyı üreten [analitik kuralını](tutorial-detect-threats-custom.md) düzenleyin.

1. **Otomatik Yanıt** sekmesinde **Uyarı Otomasyonu** altında, bu analiz kuralının bir uyarı oluşturulduğunda tetikleyeceği PlayBook veya PlayBook 'ları seçin.

#### <a name="incident-creation-automated-response"></a>Olay oluşturma otomatik yanıtı

Olay oluşturma tarafından tetiklenen ve girdileri olarak (ilk adım "bir Azure Sentinel olayı tetiklendiğinde") olayları alan PlayBook 'lar için, bir Otomasyon kuralı oluşturup bunun içinde bir **PlayBook Çalıştır** eylemi tanımlayın. Bu, 2 şekilde yapılabilir:

- İçin otomatik yanıt tanımlamak istediğiniz olayı üreten analitik kuralını düzenleyin. **Otomatik Yanıt** sekmesindeki **olay Otomasyonu** altında bir Otomasyon kuralı oluşturun. Bu, yalnızca bu analiz kuralı için otomatik bir yanıt oluşturacak.

- **Otomasyon dikey penceresindeki** **Otomasyon kuralları** sekmesinde yeni bir Otomasyon kuralı oluşturun ve uygun koşulları ve istenen eylemleri belirtin. Bu Otomasyon kuralı, belirtilen koşulları yerine getiren tüm analiz kuralları için geçerli olacaktır.

    > [!NOTE]
    > Bir PlayBook 'u bir Otomasyon kuralından çalıştırmak için, Azure Sentinel, özel olarak yetkili bir hizmet hesabı kullanır. Bu hesabın kullanımı (Kullanıcı hesabınıza karşılık olarak), hizmetin güvenlik düzeyini artırır ve otomasyon kuralları API 'sinin CI/CD kullanım durumlarını desteklemesini sağlar.
    >
    > Bu hesaba, PlayBook 'un bulunduğu kaynak grubuna açık izinler verilmelidir. Bu noktada, herhangi bir Otomasyon kuralı bu kaynak grubunda herhangi bir PlayBook 'u çalıştırabilecektir.
    >
    > Bir Otomasyon kuralına **PlayBook 'u Çalıştır** eylemini eklediğinizde, PlayBook 'lar açılır listesi görüntülenir. Azure Sentinel 'in izinleri olmayan PlayBook 'lar kullanılamıyor ("gri") olarak görünür. **PlayBook Izinlerini Yönet** bağlantısını seçerek, noktadan Azure Sentinel 'e izin verebilirsiniz.
    >
    > Çok[kiracılı (açık](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) bir senaryoda, PlayBook 'u çağıran Otomasyon kuralı farklı bir kiracıda olsa bile, PlayBook 'un yaşadığı kiracı üzerindeki izinleri tanımlamanız gerekir. Bunu yapmak için, PlayBook 'un kaynak grubunda **sahip** izninizin olması gerekir.

[Otomasyon kuralları oluşturmaya yönelik tüm yönergelere](tutorial-respond-threats-playbook.md#respond-to-incidents)bakın.

### <a name="run-a-playbook-manually-on-an-alert"></a>Bir uyarı üzerinde el ile bir PlayBook çalıştırma

El ile tetikleme, Azure Sentinel portalından aşağıdaki dikey pencerelerde bulunur:

- **Olaylar** görünümü ' nde, belirli bir olayı seçin, **uyarıları** sekmesini açın ve bir uyarı seçin.

- **Araştırma** bölümünde belirli bir uyarı seçin.

1. Seçilen uyarı için **PlayBook 'Ları görüntüle** ' ye tıklayın. Bir **Azure Sentinel uyarısı tetiklendiğinde** ve erişiminiz olduğunda, ile başlayan tüm PlayBook 'ları içeren bir liste alacaksınız.

1. Belirli bir PlayBook 'un tetiklenmesi için bu satırdaki **Çalıştır** ' a tıklayın.

1. Bu uyarı üzerinde herhangi bir PlayBook 'un çalıştırıldığı sürelerin bir listesini görüntülemek için **çalıştırmalar** sekmesini seçin. Yalnızca tamamlanmış çalıştırmanın bu listede görünmesi birkaç saniye sürebilir.

1. Belirli bir çalıştırmaya tıkladığınızda, Logic Apps tüm çalıştırma günlüğü açılır.

### <a name="run-a-playbook-manually-on-an-incident"></a>Bir olay üzerinde el ile bir PlayBook çalıştırma

Henüz desteklenmiyor. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>PlayBook 'ları 'ları yönetme

**PlayBook 'ları** sekmesinde, erişiminiz olan tüm PlayBook 'ları, Azure 'da Şu anda görüntülenen abonelikler tarafından filtrelenmiş bir liste görüntülenir. Abonelikler filtresi, genel sayfa üstbilgisindeki **Dizin + abonelik** menüsünden kullanılabilir.

Bir PlayBook adına tıkladığınızda, sizi Logic Apps PlayBook ana sayfasına yönlendirir. **Durum** sütunu, etkin mi yoksa devre dışı mı olduğunu gösterir.

**Tetikleyici türü** , bu PlayBook 'u Başlatan Logic Apps tetikleyiciyi temsil eder.

| Tetikleyici türü | PlayBook içindeki bileşen türlerini belirtir |
|-|-|
| **Azure Sentinel olay/uyarı** | PlayBook, Sentinel tetikleyicilerden biri ile başlatılır (uyarı, olay) |
| **Azure Sentinel eylemini kullanma** | PlayBook, Sentinel olmayan bir tetikleyici ile başlatılır, ancak bir Azure Sentinel eylemi kullanır |
| **Diğer** | PlayBook, Sentinel bileşeni içermiyor |
| **Başlatılmadı** | PlayBook oluşturuldu, ancak hiçbir bileşen içermiyor (Tetikleyiciler veya eylemler). |
|

PlayBook 'un Logic App sayfasında, çalıştırdığı tüm sürelerin günlüğü, sonuç (başarı veya başarısızlık ve diğer ayrıntılar) dahil olmak üzere PlayBook hakkında daha fazla bilgi görebilirsiniz. Ayrıca, uygun izinleriniz varsa Logic Apps tasarımcısını girebilir ve PlayBook 'u doğrudan düzenleyebilirsiniz.

### <a name="api-connections"></a>API bağlantıları

API bağlantıları, Logic Apps diğer hizmetlere bağlanmak için kullanılır. Logic Apps bağlayıcısında yeni bir kimlik doğrulaması yapıldığında, **API bağlantısı** türünde yeni bir kaynak oluşturulur ve hizmete erişim yapılandırılırken belirtilen bilgileri içerir.

Tüm API bağlantılarını görmek için Azure portal üst bilgi arama kutusuna *API bağlantıları* girin. İlgilendiğiniz sütunları aklınızda edin:

- Görünen ad-bağlantı oluşturduğunuz her seferinde "kolay" adı verin.
- Durum-bağlantı durumunu gösterir: hata, bağlı.
- Kaynak grubu-API bağlantıları, PlayBook (Logic Apps) kaynağının kaynak grubunda oluşturulur.

API bağlantılarını görüntülemenin bir başka yolu da **tüm kaynaklar** dikey penceresine gidip *API bağlantısı* türüne göre filtrelenebilir. Bu şekilde birden çok bağlantının tek seferde seçilmesi, etiketlenmesi ve silinmesinin yapılmasına izin verir.

Var olan bir bağlantının yetkilendirmesini değiştirmek için bağlantı kaynağını girin ve **API bağlantısını Düzenle**' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure Sentinel 'de tehdit yanıtlarını otomatikleştirmek için PlayBook 'ları kullanma](tutorial-respond-threats-playbook.md)
