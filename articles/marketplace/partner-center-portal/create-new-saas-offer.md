---
title: Ticari Market 'te yeni bir SaaS teklifi oluşturun
description: Azure Marketi, AppSource veya Microsoft Iş Ortağı Merkezi 'ndeki ticari Market portalını kullanan bulut çözümü sağlayıcısı (CSP) programı aracılığıyla listeleme veya satma için yeni bir hizmet olarak yazılım (SaaS) teklifi oluşturma.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a6694d094d185ad70cce8c546e12f48afb69f1ae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076953"
---
# <a name="create-a-new-saas-offer"></a>Yeni bir SaaS teklifi oluşturun

Hizmet olarak yazılım (SaaS) teklifi oluşturmaya başlamak için, ilk olarak [bir Iş Ortağı Merkezi hesabı](./create-account.md) oluşturduğunuzdan ve **genel bakış** sekmesi seçili olarak [ticari Market panosunu](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)açın.

![Iş Ortağı Merkezi 'nde ticari Market panosu](./media/new-offer-overview.png)

>[!Note]
> Bir teklif yayımlandıktan sonra, Iş Ortağı Merkezi 'nde yapılan teklifle ilgili düzenlemeler yalnızca sistem sırasında ve yeniden yayımlamadan sonra mağazaların ön yüzlerinin güncelleştirilmesini sağlar. Lütfen değişiklikleri yaptıktan sonra teklifi yayın için gönderdiğinizden emin olun.

\+ **Yeni teklifi seçin...** düğmesine tıklayın ve ardından **hizmet olarak yazılım** menü öğesini seçin. 

Başka bir teklif türü seçerseniz, eski [bulut iş ortağı portalı](https://cloudpartner.azure.com/)yönlendirilebilirsiniz. Yalnızca SaaS ve Dynamics 365 teklifleri Iş Ortağı Merkezi 'nde ticari Market portalında Şu anda kullanılabilir.

![Iş Ortağı Merkezi 'nde teklif penceresi oluştur](./media/new-offer-click.png)

**Yeni teklif** iletişim kutusu görüntülenir. 

![Yeni teklif iletişim kutusu](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Teklif KIMLIĞI ve diğer ad

- **TEKLIF kimliği**: hesabınızdaki her teklif için benzersiz tanımlayıcı. Bu KIMLIK, Market teklifi ve Azure Resource Manager şablonları (varsa) için URL adresindeki müşterilere görünür olacaktır. Teklif KIMLIĞI küçük harf, alfasayısal (kısa çizgiler ve alt çizgiler dahil ancak boşluk olmadan) olmalıdır. Bu 50 karakterle sınırlıdır ve *Oluştur*' u seçtikten sonra değiştirilemez.  
Örnek: test-teklif-1
<br>URL 'ye neden olur: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Teklif diğer adı**: Iş Ortağı Merkezi portalı içindeki teklifine başvurmak için kullanılan ad. Bu ad Market 'te kullanılmayacak ve *teklif adından* ve müşterilere gösterilecek diğer değerlerden farklı. Bu değer, *Oluştur*' u seçtikten sonra değiştirilemez.

<br>Örnek: test teklifi 1&#8482;

**Oluştur**'u seçin.  Bu teklif için bir **teklif genel bakış** sayfası oluşturulur.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Teklifin genel bakış

**Teklif genel bakış** sayfası şunları içerir: 

- **Yayımlama durumu** , bu teklifi yayımlamak için gereken adımların görsel bir gösterimini ve her adımın tamamlanması için ne kadar sürdüğünü gösterir. Tamamlanmamış yayımlama adımı simgeleri gri olacak. 

- **Teklif genel bakış** menüsü, bu teklif üzerinde işlem gerçekleştirmek için bağlantıların bir listesini içerir. Bu işlem listesi, teklifiniz için yaptığınız seçime göre değişecektir.  
    - Teklif bir taslak silise taslak silme 
    - Teklif canlı değilse, satış teklifini durdur 
    - Teklif önizleme sürümündedir-canlı 
    - Yayımcı oturumunu kapatma Işlemini tamamlamadıysanız, yayımlamayı Iptal edin

## <a name="offer-setup"></a>Teklif kurulumu

**Teklif kurulumu** sekmesi aşağıdaki bilgileri ister. Bu alanları tamamladıktan sonra **Kaydet** ' i seçin.

- **Microsoft üzerinden satımek istiyor musunuz?** (Evet/Hayır)
    - **Evet**, Microsoft 'un sizin adınıza Market işlemlerini barındırmakla Microsoft aracılığıyla teklifinizi satmak istiyorsunuz; veya 
    - **Hayır**, sizin teklifinizi, Microsoft 'tan bağımsız olarak tüm parasal işlemleri işleyerek yalnızca marketler aracılığıyla listelemek tercih edersiniz.    

### <a name="sell-through-microsoft"></a>Microsoft ile satış

Microsoft ile satış yapmak daha iyi müşteri bulma ve alma olanağı sağlar, Microsoft 'un Market işlemlerini sizin adınıza barındırmasına olanak tanır ve Microsoft 'un küresel olarak kullanılabilir ticari özellikleri avantajlarından yararlanır.

#### <a name="saas-offer-requirements"></a>SaaS teklif gereksinimleri

Iş Ortağı Merkezi 'nde ticari Market ile hizmet olarak yazılım (SaaS) teklifleri listelemek için aşağıdaki kriterlerin karşılanması gerekir:

- Teklifinizin kimlik yönetimi ve kimlik doğrulaması için [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) kullanması gerekir.
- Teklifinizin Azure Marketi ile tümleştirilecek [SaaS karşılama API 'lerini](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) kullanması gerekir.
- Daha kapsamlı gereksinimler için [SaaS teklifi Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)' na bakın.

#### <a name="saas-pricing-and-billing-options"></a>SaaS fiyatlandırma ve faturalandırma seçenekleri
Yayımcının Azure aboneliğinde çalışan SaaS çözümleri sayesinde, müşteriler tarafından ödenen lisans ücretleri, yazılımın dağıtıldığı altyapının maliyetini içerir. Azure altyapı kullanımı, iş ortağı tarafından doğrudan yönetilir ve size faturalandırılır. Gerçek altyapı kullanım ücretleri müşteri tarafından görülmez. Yayımcılar, Azure altyapı kullanım ücretlerini yazılım lisans fiyatlandırmasına paketlemelidir. 

SaaS, aylık veya yıllık faturalandırmaya, Kullanıcı başına veya ölçülen faturalandırma hizmeti kullanılarak sunulan tüketim ücretlerine göre destek sunuyor. Microsoft 'un ticari Market 'i, yayımcıların fiyatları, Microsoft ürün müşterileri ve Microsoft 'un bir kurum ücretini stopajına göre gelir ödediği bir kurum modeli üzerinde çalışır.

Aşağıdaki tabloda, kurum modelini göstermek için maliyetleri ve ödemlardan oluşan örnek bir bölme gösterilmektedir.

|**Lisans maliyetiniz**|**$100/ay**|
|:---|:---|
|Azure kullanım maliyeti (D1/1-çekirdek)|Müşteriye değil doğrudan yayımcıya faturalandırılır|
|Müşteri Microsoft tarafından faturalandırılır|aylık $100,00 (yayımcı, lisans ücretindeki herhangi bir tahakkuk eden veya geçiş altyapı maliyeti için hesap olmalıdır)|

|**Microsoft faturaları**|**$100/ay**|
|:---|:---|
|Microsoft, lisans maliyetinizi %80 oranında ödetir <br>*nitelikli SaaS uygulamaları için *, Microsoft Lisans maliyetlerinizin %90 ' ını öder*|$80,00/ay <br>Ayda 90,00 *$* *|

- Bu örnekte, Microsoft, yazılım lisansınızın $100,00 ' i müşteriye faturalandırır ve Yayımcı için $80,00 ' i ödeder.
- **Azaltılmış Market hizmet ücreti** için uygun olan iş ortakları, 2019 2020 Haziran 'a kadar Mayıs 'a kadar olan SaaS teklifleriyle daha düşük bir işlem ücreti görür. Bu senaryoda, Microsoft, yazılım lisansınızın $100,00 ' i faturalandırır ve Yayımcı için $90,00 ' i ödeder.

> [!NOTE]
> **Daha az Market hizmeti ücreti**: ticari Market 'te yayımladığınızdan belirli SaaS teklifleri için Microsoft, Market hizmet ücretini %20 ' den (Microsoft Publisher anlaşmasında açıklandığı gibi) %10 oranında azaltacak. Teklifinizin uygun olmasını sağlamak için, tekliflerinizin en az birinin, Microsoft tarafından IP ortak satışı veya IP ortak satışı önceliği atanmış olarak belirlenmiş olması gerekir.  Bu ay için daha düşük olan bu Market servis ücretini almak amacıyla her bir takvim ayının sonundan önce uygunluk en az beş (5) iş gününe ulaşılmalıdır.  Düşük Market hizmet ücreti, sanal makineler, yönetilen uygulamalar veya ticari Market 'ten sunulan diğer ürünlerle ilgili değildir.  Daha az Market hizmet ücreti yalnızca Microsoft tarafından 1 Mayıs 2019 ile 30 Haziran 2020 arasında toplanan lisans ücretleri için uygun tekliflerdir.  Bu süreden sonra Market hizmeti ücreti normal miktarına geri döner. 

### <a name="list-through-microsoft"></a>Microsoft ile Listele

Market listesi oluşturarak işletmenizi Microsoft ile yükseltin. Microsoft 'un doğrudan yazılım lisans işlemlerine katılmayacağı, teklifinizi yalnızca bir şekilde listelemek için seçin. İlişkili bir işlem ücreti yoktur ve Yayımcı, müşteriden toplanan tüm yazılım lisanslama ücretlerine %100 oranında devam eder. Ancak yayımcı, yazılım lisans işleminin, bunlarla sınırlı olmamak üzere tüm yönlerini desteklemekten sorumludur: sipariş karşılama, ölçüm, faturalandırma, faturalama, ödeme ve koleksiyon. 

- **Potansiyel müşterilerin bu liste teklifiyle nasıl etkileşime geçmesini istiyorsunuz?**

##### <a name="get-it-now-free"></a>Şimdi alın (ücretsiz)
Uygulamanıza erişebilecekleri geçerli bir URL ( *http* veya *https*ile başlayarak) sağlayarak müşterilerinizin teklifinizi ücretsiz olarak listeleyin.  Örneğin, `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Ücretsiz deneme (listeleme)
[Azure Active Directory (Azure AD) kullanarak tek tıklamayla kimlik doğrulama](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)aracılığıyla denemeyi alabileceğiniz, GEÇERLI bir URL ( *http* veya *https*ile başlayarak) sağlayarak, ücretsiz bir deneme bağlantısı olan müşterilere teklifinizi listeleyin.  Örneğin: `https://contoso.com/trial/saas-app`. Ücretsiz denemelerdeki teklif listesi, hizmetiniz tarafından oluşturulur, yönetilir ve yapılandırılır ve Microsoft tarafından yönetilen abonelikler içermez.

> [!NOTE]
> Deneme bağlantınız aracılığıyla uygulamanızın alacağı belirteçler yalnızca, uygulamanızda hesap oluşturmayı otomatikleştirmek için Azure AD aracılığıyla Kullanıcı bilgilerini almak üzere kullanılabilir. Bu belirteci kullanarak kimlik doğrulaması için Microsoft hesapları (MSA) desteklenmez.

##### <a name="contact-me"></a>Benimle iletişim kurun
Müşteri Ilişkileri yönetimi (CRM) sisteminizi bağlayarak müşteri iletişim bilgilerini toplayın. Müşterinin, bilgilerini paylaşması için izin istenir. Bu müşteri ayrıntıları, teklifinizin bulunduğu teklif adı, KIMLIĞI ve Market kaynağıyla birlikte, yapılandırdığınız CRM sistemine gönderilir. CRM 'nizi yapılandırma hakkında daha fazla bilgi için bkz. [Connect lider yönetimi](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Örnek Market teklif listesi

![Not ile örnek Market teklif listesi](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Sınama sürücüsünü etkinleştir

Test sürücüsü, bu kullanıcılara teklifinizi "satın almadan önce dene" seçeneği sunarak, daha fazla dönüştürmeye ve yüksek oranda nitelikli müşteri adaylarının oluşturulmasına neden olacak şekilde sergilemenin harika bir yoludur. [Sınama sürücüleri hakkında daha fazla bilgi edinin.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Sınama sürücüsünü etkinleştir** (onay kutusu)

Sınama sürücüsünü etkinleştirerek, müşterilerin teklifinizi sabit bir süre için denemesini sağlayacak bir tanıtım ortamı yapılandırmanız istenir. 

### <a name="type-of-test-drive"></a>Test sürücüsünün türü

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : çözümünüzü oluşturan tüm Azure kaynaklarını içeren bir dağıtım şablonu. Bu senaryoya uyan ürünler yalnızca Azure kaynaklarını kullanır.
- **[Iş Merkezi Için Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft, bir iş merkezi kurumsal kaynak planlama sistemi (Finans, işlemler, tedarik ZINCIRI, CRM vb.) için test sürücüsü hizmetini (sağlama ve dağıtım dahil) barındırır ve saklar.  
- **[Müşteri katılımı Için Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft, bir müşteri katılım sistemi (Sales, Service, Project Service, alan hizmeti vb.) için test sürücüsü hizmetini (sağlama ve dağıtım dahil) barındırır ve saklar.  
- **[Operations Için Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft, finans ve operasyon kurumsal kaynak planlama sistemi (Finans, işlemler, üretim, tedarik zinciri vb.) için test sürücüsü hizmetini (sağlama ve dağıtım dahil) barındırır ve saklar. 
- **[Mantıksal uygulama](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : tüm karmaşık çözüm mimarilerini çevreleyen bir dağıtım şablonu. Özel ürünlerin bu tür test sürücüsü kullanması gerekir.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : özel olarak oluşturulmuş bir panoya eklenmiş bir bağlantı. Etkileşimli bir Power BI görseli göstermek isteyen ürünlerin bu tür test sürücüsünü kullanması gerekir. Buraya yüklemeniz gereken, katıştırılmış Power BI URL 'sidir.

#### <a name="additional-test-drive-resources"></a>Ek test sürücüsü kaynakları
- [Test sürücüsü teknik En Iyi uygulamaları](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Test sürüşü Pazarlama En Iyi yöntemleri](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Test sürücüsüne genel bakış bir sayfalayıcı](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Müşteri adayı yönetimini bağlama

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Ek lider Yönetimi kaynakları
- [Müşteri adayı yönetimi SSS](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Ortak lider yapılandırma hataları](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Müşteri adayı yönetimine genel bakış bir sayfalayıcı](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

## <a name="properties"></a>Özellikler
**Özellikler** sekmesi, teklifinizi markette gruplandırmak için kullanılan kategorileri ve endüstrileri, teklifinizi destekleyen yasal sözleşmeleri ve uygulama sürümünüzü tanımlamanızı ister. 

Bu alanları tamamladıktan sonra **Kaydet** ' i seçin. 

### <a name="category"></a>Kategori
Teklifinizi uygun Market arama alanlarında gruplandırmak için en az bir (1) ve en fazla üç (3) kategori seçin. Teklifinizin teklif açıklamasında bu kategorileri nasıl desteklediğini lütfen unutmayın. 

### <a name="industry"></a>Sektör

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Uygulama sürümü
Bu, teklifinizin sürüm numarasını belirlemek için AppSource marketi 'nde kullanılan isteğe bağlı bir alandır. 

### <a name="standard-contract"></a>Standart Sözleşme

- **Standart Sözleşme mi kullanıyorsunuz?**

Müşteriler için satın alma sürecini basitleştirmek ve yazılım satıcılarının yasal karmaşıklığını azaltmak için, Microsoft, Market 'teki bir işlemin kolaylaştırılmasına yardımcı olmak üzere standart bir sözleşme şablonu sunmaktadır. 

Azure Market yayımcıları, özel hüküm ve koşulları ortadan kaldırmaktansa, müşterilerine yalnızca bir kez ve kabul etmesi gereken standart sözleşme kapsamında yazılım sunmayı tercih edebilir. 

Standart Sözleşme şurada bulunabilir: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Kullanım koşulları

Lisans koşullarınız standart sözleşmeden farklıysa, size ait yasal kullanım koşullarınızı buraya girmeye karar verebilirsiniz. Bunlar, düz metin olarak veya lisans koşullarınıza bağlanan tek bir URL olarak girilebilir.

Müşterilerinizin uygulamanızı deneyebilmeleri için önce bu koşulları kabul etmesi gerekir. 

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

## <a name="offer-listing"></a>Teklif listesi

Teklif listesi sekmesinde teklifinizin kullanılabildiği diller (ve pazarlar) görüntülenir. Şu anda Ingilizce (Birleşik Devletler) kullanılabilir tek konumdur. Ayrıca, Bu sayfa dile özgü listenin durumunu ve eklendiği tarih/saati görüntüler. Her dil/Pazar için Market ayrıntılarını (teklif adı, açıklama, arama terimleri vb.) tanımlamanız gerekir.

> [!NOTE]
> Teklif açıklaması (teklif açıklaması, belgeler, ekran görüntüleri, kullanım koşulları ve Gizlilik ilkesi gibi) sunan teklif listesi, "Bu uygulama yalnızca [Ingilizce olmayan dil] ' de kullanılabilir." Ayrıca, teklif listeleme içeriğinde kullanılandan farklı bir dilde içerik sunmak için *yararlı bir bağlantı URL 'si* sağlamak da kabul edilebilir.

### <a name="offer-listings"></a>Teklif listeleri

Teklif ve pazarlama varlıklarınızın açıklamaları dahil olmak üzere Market 'te görüntülenecek ayrıntıları sağlayın.

- **Ad** (gerekli): burada tanımlanan ad, seçtiğiniz Market (ler) de teklif listelerinizin başlığı olarak görünür. Ad, önceki **yeni teklif** girişinizin temel alınarak önceden doldurulur.  Bu, trademarked olabilir.  Bu, emojıs (ticari marka ve telif hakkı sembolleri olmadıkları müddetçe) içermemelidir ve 50 karakterle sınırlı olmalıdır.
- **Özet** (gerekli): market listeleme arama sonuçlarında kullanılacak teklifinizin kısa bir açıklamasını sağlayın. Bu alana en fazla 100 karakter metin girilebilir.
- **Açıklama** (gerekli): market dökümüne genel bakış bölümünde gösterilecek teklifin açıklamasını sağlayın. Bir değer teklifi, önemli avantajlar, herhangi bir kategori veya sektör ilişkilendirmesi, uygulama içi satın alma fırsatları, gerekli ön kapanışlar ve daha fazla bilgi edinmek için bir bağlantı dahil etmeyi göz önünde bulundurun.
Bu alana en fazla 3.000 karakter metin girilebilir. Ek ipuçları için [harika uygulama açıklaması yazma](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)makalesine bakın.
- **Anahtar sözcükleri ara**: müşterilerin Market 'te teklifinizi bulmak için kullanabileceği en fazla üç arama anahtar sözcüğü girin.
- Başlarken **yönergeleri** (gerekli): uygulamanızı nasıl yapılandıracağınızı ve potansiyel müşteriler için kullanmaya nasıl başlayacağınız açıklanmaktadır.  Bu hızlı başlangıç, daha ayrıntılı çevrimiçi belgelere bağlantılar içerebilir. Bu alana en fazla 3.000 karakter metin girilebilir. 

#### <a name="description"></a>**Açıklama**

Bu gerekli bir alandır. Açıklamaya eklenecek öğeler: 

* Tanımınızın ilk birkaç cümlede teklifinizin değer teklifini açık bir şekilde açıklamalıdır.  
* İlk birkaç tümcenin arama motoru sonuçlarında görüntülenebileceğini aklınızda bulundurun.  
* Ürününüzü satmaya yönelik özelliklere ve işlevlere güvenmeyin. Bunun yerine, teslim ettiğiniz değere odaklanın.  
* Sektöre özgü sözlük veya avantaj temelli bir ifade mümkün olduğunca yararlanın. 

Değer önerinizdeki çekirdek bileşenleri aşağıdaki bilgileri içermelidir: 

* Ürünün açıklaması. 
* Üründen faydalanan Kullanıcı türü. 
* Müşterinin ürün adresi olması veya bir sorun olması. 

Teklif açıklamanızı daha ilgi çekici hale getirmek için, açıklamayı biçimlendirmek için HTML etiketlerini kullanabilirsiniz. 

1. Paragraf oluşturmak istiyorsanız, metnin başına `<p>` ekleyin ve sonuna `</p>` ekleyin.

    **Örnek**: 

    ilk paragrafım `<p>`. `</p>` <br>
    `<p>` ikinci paragrafım. `</p>` <br>

    Yukarıdaki şöyle görünür:

    <p> İlk paragrafım. </p>
    <p> Bu, ikinci paragrafım. </p>

1. **Madde işaretli bir öğe listesi**eklemek istiyorsanız, metninizi aşağıdaki `<li>` etiketlere yerleştirin. `<ul>` ve `</ul>` etiketleri içinde daha fazla madde işaretli öğe (`<li>` ve `</li>` etiketleri arasında öğeler) kopyalayabilir ve yapıştırabilirsiniz. `<ul></ul>`eklediğinizden emin olun. 

    **Örnek**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    Yukarıdaki şöyle görünür:
    <ul> 
        <li>Buraya metin ekleyin</li> 
        <li> Buraya metin ekleyin </li> 
        <li> Buraya metin ekleyin </li> 
    </ul> 

1. **Kalın** içerik için, kalın yapmak istediğiniz metnin başına `<b>` ekleyin ve kalın olmasını istediğiniz metnin sonuna `</b>` ekleyin. 

    **Örnek**: `<b>` ücretsiz deneme `</b>`
    
    Yukarıdaki, ÜCRETSIZ deneme kelimesinin storefront içinde teklifin açıklamasına göre kalın olmasına neden olur. 

    **ÜCRETSIZ DENEME**

1. İçeriğiniz arasına **satır sonları** eklemek için, yeni bir satırda başlamasını istediğiniz içerikten önce `<br>` ekleyin. Bir boşluk bırakmak ve içeriğin yeni bir satırda başlamasını sağlamak istiyorsanız, içerikten önce `<br><br>` ekleyin. 

    **Örnek**:

    Bu bir metin çizgisi. `<br>` yeni bir satırda başlatılacak bir metin çizgisi. `<br><br>` aşağıda iki satırı başlatacak bir satırdır. 

    Yukarıdaki şöyle görünür:

    Bu bir metin çizgisi. <br> Bu, yeni bir satırda başlatılacak bir metin çizgisi. <br><br> Bu, aşağıdaki iki satırı başlatacak bir satırdır. 

1. **Metnin boyutunu artırmak**istiyorsanız, önce metnin ne kadar büyük olmasını istediğinizi seçin. Aşağıdaki örnekleri kullanın. Metnin boyutunu seçtikten sonra, metnin başına ve sonuna karşılık gelen `<H*></H*>` etiketlerini ekleyin. 

    **Örnek**:

    `<h1>`başlık 1`</h1>` <br>
    `<h2>`başlık 2 ' dir`</h2>` <br>
    `<h3>`başlık 3 '`</h3>` <br>
    `<h4>`başlık 4 '`</h4>` <br>
    `<h5>`başlık 5 ' tir`</h5>` <br>
    `<h6>`başlık 6 ' dır`</h6>` 

    Yukarıdaki şöyle görünür:

    ![Örnek başlıklar](./media/heading.png)

#### <a name="links"></a>Bağlantılar

- **Gizlilik ilkesi** (gerekli): kuruluşunuzun gizlilik ilkesine bağlayın. Uygulamanızın gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan ve geçerli bir gizlilik ilkesi sağlamaya yönelik siz sorumlusunuz.
- **CSP program pazarlama malzemeleri** (isteğe bağlı): teklifinizi [bulut çözümü sağlayıcısı (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programına genişletmeyi seçerseniz, pazarlama malzemeleri için bir bağlantı sağlamanız gerekir. CSP, CSP iş ortaklarının teklifinizi paketlemelerine, pazarlamaya ve satmasına olanak tanıyarak, teklifinizi daha geniş bir yelpazede nitelikli müşterilere genişletir. Bu satıcıların, teklifinizi pazarlama malzemelerinden sorumlu olması gerekir. Daha fazla bilgi için bkz. [Go-to-market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Faydalı bağlantılar** (isteğe bağlı): uygulamanız veya bir **başlık** ve **URL**sağlayarak listelenen ilgili hizmetlerinize ilişkin isteğe bağlı ek çevrimiçi belgeler. **+ URL Ekle**' ye tıklayarak ek yararlı bağlantılar ekleyin.

#### <a name="contact-information"></a>İletişim bilgileri

- **Kişiler**: her müşteri kişisi için, bir çalışan **adı** , **telefon numarası**ve **e-posta** adresi sağlayın.  (Bunlar *herkese açık olarak gösterilmez* ). **Destek kişi** grubu Için bir **Destek URL 'si** de gereklidir.  (Bu bilgiler genel *olarak* görüntülenecektir).

**Destek kişisi** (gerekli): genel destek soruları için.

**Mühendislik ilgili kişisi** (gerekli): Teknik sorular için.

**Channel Manager** ile ilgili iletişim (gerekli): CSP programıyla ilgili satıcı soruları için.

#### <a name="files-and-images"></a>Dosyalar ve görüntüler

- **Belgeler** (gerekli): teklif başına en az bir (1) ve en fazla üç (3) belge sağlayan, teklifiniz için ilgili pazarlama belgelerini ekleyin.
- **Görüntüler** (isteğe bağlı): teklifinizin logo görüntülerinin Market genelinde görünebileceği birden çok yer vardır; şu boyutlarda olması gerekir--küçük: 48 x 48 piksel _(gerekli),_ orta: 90 x 90 piksel, büyük: 216 x 216 piksel _( gerekli),_ geniş: 255 x 115 piksel ve Hero: 815 x 290 piksel. Tüm görüntülerin içinde olması gerekir. PNG biçimi.
- **Ekran görüntüleri** (gerekli): teklifinizi gösteren ekran görüntüleri ekleyin. En fazla beş (5) ekran görüntüsü eklenebilir ve 1280 x 720 piksel boyutunda olmalıdır. Tüm görüntülerin içinde olması gerekir. PNG biçimi.
- **Videolar** (isteğe bağlı): teklifinizi gösteren videoların bağlantılarını ekleyin. Müşteriler teklifinizle birlikte gösterilen YouTube ve/veya Vimeo videolarını içeren bağlantıları kullanabilirsiniz. Ayrıca, PNG biçiminde 1280 x 720 piksel boyutunda videonun küçük resmini de girmeniz gerekir. Her teklif için en fazla dört video görüntüleyebilirsiniz.

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

#### <a name="additional-marketplace-listing-resources"></a>Ek Market listeleme kaynakları

- [Market teklif listeleri için en iyi uygulamalar](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Önizleme

**Önizleme** sekmesi, teklifinizi daha geniş Market hedef kitlelerine yayımlamadan önce teklifinizi serbest bırakmak için sınırlı bir **Önizleme hedef kitlesi** tanımlamanızı sağlar.

> [!IMPORTANT]
> Teklifinizi önizlemede denetledikten sonra Market ortak hedef kitlesi için canlı **çalış** ' ı seçmeniz gerekir.

- **Bir önizleme hedef kitlesi tanımlayın: isteğe bağlı bir açıklama ile birlikte her satır için tek bir AAD/MSA hesap e-postası ekleyin.**

Canlı yayımlamadan önce teklifinizi doğrulamaya yardımcı olmak üzere mevcut Microsoft hesabı (MSA) veya Azure Active Directory hesapları için el ile on (10) e-posta adresi veya bir CSV dosyası karşıya yüklüyorsanız yirmi (20). Bu hesapları ekleyerek, Market 'te yayınlanmadan önce teklifiniz için önizlemeye erişim izni verilecek bir hedef kitle tanımlamanız gerekir. Teklifiniz zaten canlı ise, teklifiniz için herhangi bir değişikliği veya güncelleştirmeyi test etmek üzere bir önizleme hedef kitlesi de tanımlayabilirsiniz.

> [!NOTE]
> Önizleme hedef kitlesi, özel bir hedef kitleye göre farklılık gösterir. Bir önizleme hedef kitlesi, Market 'te canlı olarak yayımlanmadan _önce_ teklifinizin erişimine izin verilir. Ayrıca, bir plan oluşturmayı ve yalnızca özel bir hedef kitle için kullanılabilir hale getirebilirsiniz. **Plan listeleme** sekmesinde, **Bu özel bir plan** onay kutusu ile özel bir hedef kitle tanımlayabilirsiniz. Daha sonra, Azure kiracı kimliklerini kullanarak 20.000 adede kadar müşterinin özel bir kitlesini tanımlayabilirsiniz.

## <a name="technical-configuration"></a>Teknik yapılandırma

**Teknik yapılandırma** sekmesi, teklifinizi bağlamak için kullanılan teknik ayrıntıları (URL yolu, Web kancası, Kiracı kimliği ve uygulama kimliği) tanımlar. Bu bağlantı, teklifi edinmeyi tercih ettiklerinde, son müşteri için teklifinizi sağlamamızı sağlar. Toplanan alanların kullanımını açıklayan diyagramlar [SaaS karşılama API 'leri](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)için belgelerde bulunabilir.

- **Giriş sayfası URL 'si** (gerekli): müşterilerinizin teklifinizi marketten aldıktan sonra hangi site URL 'sini tanımlayın. Bu URL, bir müşteri sayfaya yönlendirildiğini belirteç alan uç nokta olur. Bu belirteç, karşılama API 'Lerinde çözümle kullanılarak sağlama ayrıntıları için takas edilebilir. Bu ayrıntılar ve topladığınız tüm diğerleri, kaydınızı tamamlayıp satın almasını etkinleştirmek için deneyiminizin yerleşik olduğu müşteri etkileşimli Web sayfasının bir parçası olarak kullanılabilir.

- **Bağlantı Web kancası** (gerekli): Microsoft 'un müşteri adına size gönderilmesi gereken tüm zaman uyumsuz olaylar için (örnek: SaaS aboneliği geçersiz oldu), bağlantı Web kancası sağlamanız gerekir. Zaten bir Web kancası sisteminiz yoksa, en basit yapılandırma, kendisine gönderilen olayları dinleyen bir HTTP uç noktası mantıksal uygulamasına sahip olmak ve bunları uygun şekilde (ör. https:\//prod-1westus.logic.azure.com:443/work) işleymelidir. Daha fazla bilgi için bkz. [Logic Apps 'TE HTTP uç noktaları Ile çağrı, tetikleyici veya iç içe iş akışları](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure AD KIRACı kimliği** (gerekli): Azure Portal içinde, iki hizmetimiz arasındaki bağlantının kimliği doğrulanmış bir iletişimin arkasında olduğunu doğrulayabilmemiz için [BIR Azure Active Directory (ad) uygulaması oluşturmanız](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) gerekir. [KIRACı kimliğini](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)bulmak için Azure Active Directory gidin ve **Özellikler**' i seçin, ardından listelenen **dizin kimliği** numarasını (ör. 50c464d3-4930-494c-963c-1e951d15360e) arayın.

- **Azure AD uygulama kimliği** (gerekli): Ayrıca, [Uygulama Kimliğiniz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) ve bir kimlik doğrulama anahtarınız olması gerekir. Bu değerleri almak için Azure Active Directory gidin ve **uygulama kayıtları**' i seçin, ardından LISTELENEN **uygulama kimliği** numarasını (ör. 50c464d3-4930-494c-963c-1e951d15360e) arayın. Kimlik doğrulama anahtarını bulmak için **Ayarlar** ' a gidin ve **anahtarlar**' ı seçin. Bir açıklama ve süre sağlamanız gerekir ve ardından bir sayı değeri sağlanacaktır.

 Azure uygulama KIMLIĞI 'nin yayımcı KIMLIĞINIZLE ilişkilendirildiğini unutmayın, bu nedenle tüm tekliflerinizi aynı uygulama KIMLIĞININ kullanıldığından emin olun.

## <a name="plan-overview"></a>Plana genel bakış

**Plana genel bakış** sekmesi, aynı teklif dahilinde çeşitli plan seçenekleri sağlamanıza olanak sağlar. Bu planlar (bazen SKU 'Lar olarak adlandırılır) sürüm, para veya hizmet katmanları bakımından farklılık gösterebilir. Teklifinizin Market 'te satılabilir olması için en az bir plan ayarlamanız gerekir.

Oluşturulduktan sonra plan adlarınızı, kimlikleri, fiyatlandırma modellerini, kullanılabilirliği (genel veya özel), geçerli yayımlama durumunu ve kullanılabilir eylemleri görürsünüz.

**Plana genel bakış** bakımından sunulan **Eylemler** planınızın geçerli durumuna bağlı olarak farklılık gösterir ve şunlar olabilir:

- Plan durumu **taslak** ise taslağı Sil
- Plan durumu **canlı** ise, bir planı satmayı veya özel izleyiciyi Eşitlemeyi Durdur

**Yeni plan oluştur** (Microsoft üzerinden satış için seçim yapan kişiler için en az bir plan)

- **Plan kimliği:** Bu teklifteki her plan için benzersiz bir plan KIMLIĞI oluşturun. Bu KIMLIK, ürün URL 'sindeki ve Azure Resource Manager şablonlarındaki (varsa) müşterilere görünür olacaktır. Yalnızca küçük harf, alfasayısal karakter, çizgi veya alt çizgi kullanın. Bu plan KIMLIĞI için en fazla 50 karakter kullanılabilir. Oluştur seçildikten sonra KIMLIğIN değiştirilemeyeceğini unutmayın.
- **Plan adı:** Tekliflerinizi hangi plana seçeceğinize karar verirken, müşteriler bu adı görür. Bu teklifteki her plan için benzersiz bir teklif adı oluşturun. Plan adı, aynı teklifin bir parçası olabilecek yazılım planlarını ayırt etmek için kullanılır (ör. Teklif adı: Windows Server; planlar: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Planı listeleme

**Plan listeleme** sekmesinde, planınızın kullanılabildiği diller (ve pazarlar) görüntülenir. Şu anda ingilizce (Birleşik Devletler) kullanılabilir tek konumdur. Ayrıca, Bu sayfa dile özgü listenin durumunu ve eklendiği tarih/saati görüntüler. Her dil/Pazar için Market ayrıntılarını (teklif adı, açıklama, arama terimleri vb.) tanımlamanız gerekir.

#### <a name="plan-listing-details"></a>Plan listeleme ayrıntıları

Plan dillerinden birini seçmek, **ad** ve açıklama dahil olmak üzere **Plan listesi** bilgilerini görüntüler **.**

- **Ad**: Önizleme **Yeni plan** girdinizi temel alarak önceden doldurulmuş ve teklifinizin Market 'te görüntülenen "yazılım planının" başlığı olarak görünür.
- **Açıklama:** Bu açıklama, bu yazılım planının ne olduğunu ve teklifinizdeki diğer yazılım planlarından herhangi bir farklılık yaptığını açıklayan bir fırsattır. En fazla 500 karakter içerebilir.

Bu alanları tamamladıktan sonra **Kaydet** ' i seçin.

#### <a name="plan-pricing-and-availability"></a>Fiyatlandırma ve kullanılabilirlik planlaması

**Fiyatlandırma ve kullanılabilirlik** sekmesi, bu planın kullanılabileceği pazarları, istenen ödeme modeli, Fiyat ve fatura terimini yapılandırmanıza olanak sağlar. Ayrıca, planın herkes için mi yoksa yalnızca belirli müşterilere mi (özel bir hedef kitle) görünür mı olacağını belirtebilirsiniz.

##### <a name="enabling-free-trials"></a>Ücretsiz denemeleri etkinleştirme

Ticari Market aracılığıyla sunulan SaaS teklifleri, Microsoft üzerinden satılırken bir aylık ücretsiz deneme olanağı sağlamanıza olanak tanır. Ölçümlü planlar hariç tüm faturalama modelleri ve koşulları için ücretsiz denemeler desteklenir. Bu seçenek, müşterilerin bir ay boyunca ücretsiz erişim için düşük bir engel olmasına olanak sağlar.  Teklifiniz dahilinde planlar için ücretsiz denemeyi etkinleştirmeyi seçerseniz, müşteri, ilk bir ay döneminin sonundan önce ücretli bir aboneliğe dönüştürülemez.  Bu süre boyunca, teklifinizi satın alan müşteriler ücretsiz deneme sürümü etkin olan ve aralarında dönüştürme desteklenen planların herhangi birini deneyebilir.  Ücretli aboneliğe dönüştürme işlemi, dönem sonunda otomatik olarak yapılır.

>[!Note]
>Müşteri, ücretsiz denemeler olmadan bir plana dönüştürmeyi seçerse, dönüştürme gerçekleşecektir, ancak ücretsiz deneme hemen kaybolacaktır.  Ayrıca, bir müşteri bir plan için ödeme başladıktan sonra, ücretsiz denemeleri destekleyen bir SKU 'ya dönüştürüseler bile, aynı abonelikte artık ücretsiz deneme alamaz.

Ücretsiz deneme yapılandırma özelliği, teklifinizin her bir planı için kullanılabilir. Her teklifin fiyatlandırmasına ve kullanılabilirliğine gitmeniz yeterlidir ve bir aylık denemeye izin vermek için kutuyu işaretleyin.

![Bir aylık ücretsiz deneme onay kutusu](./media/free-trial-enable.png)

>[!Note]
>Transactable teklifiniz ücretsiz bir deneme ile yayımlandıktan sonra bu plan için devre dışı bırakılamaz. Planı yeniden oluşturmak zorunda kalmamak için bu ayarın ilk yayımlama için doğru olduğundan emin olun.

Ücretsiz bir denemeye katılmış olan müşteri abonelikleri hakkında bilgi edinmek için, true veya false olarak işaretlenecek `isFreeTrial`yeni API özelliğini kullanın. Daha fazla bilgi için [SaaS Get abonelik API 'sine](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) bakın.

>[!Note]
>Market ölçüm hizmetinden yararlanan planlar için ücretsiz denemeler desteklenmez.

#### <a name="markets"></a>Pazar

- **Pazarları Düzenle** (isteğe bağlı)

Her plan en az bir pazarda kullanılabilir olmalıdır. Bu planı kullanılabilir yapmak istediğiniz tüm pazar konumları için onay kutusunu seçin. Microsoft remits Sales ve kullanım vergisinin sizin adınıza yardımına dahil olduğu "vergi havalesi" ülkelerinden oluşan bir arama kutusu ve düğmesi.

Planınız için Birleşik Devletler dolar (USD) cinsinden zaten fiyatlar ayarladıysanız ve başka bir pazar konumu eklerseniz, yeni pazar fiyatı geçerli döviz kurlarına göre hesaplanır. Yayımlamadan önce her bir pazar için ücreti her zaman gözden geçirmelisiniz. Fiyatlandırma, değişiklikleriniz kaydedildikten sonra "fiyatları dışarı aktar (xlsx)" bağlantısı kullanılarak incelenebilir.

#### <a name="pricing"></a>Fiyatlandırma

- **Fiyatlandırma modeli**: düz oran veya koltuk tabanlı

**Düz hız:** Tek bir aylık veya yıllık fiyat sabit fiyat fiyatı ile teklifinizin erişimini etkinleştirin. Bu, bazen site tabanlı fiyatlandırma olarak adlandırılır. Bu fiyatlandırma modeliyle isteğe bağlı olarak, müşterileri standart olmayan birimlere göre ücretlendirmesi için Market ölçüm hizmeti API 'sini kullanan tarifeli planlar tanımlayabilirsiniz.  Tarifeli faturalandırma hakkında daha fazla bilgi için bkz. [Market ölçüm hizmeti kullanılarak ölçülen faturalandırma](./saas-metered-billing.md).

**Kullanıcı başına:** Teklifle veya şirket dışı bilgisayarlara erişen kullanıcı sayısına göre fiyat ile teklifinizin erişimini etkinleştirin. Bu Kullanıcı tabanlı model, fiyata göre izin verilen en düşük ve en yüksek Kullanıcı sayısını ayarlamanıza olanak sağlar. Bu şekilde, farklı fiyat noktaları, birden çok plan yapılandırılarak kullanıcı sayısına göre yapılandırılabilir.  Bu alanlar isteğe bağlıdır. Sol tarafta yoksa, Kullanıcı sayısı bir sınıra sahip değildir (en az 1, en fazla sistem tarafından desteklenebilir). Bu alanlar, planınızdaki bir güncelleştirmenin parçası olarak düzenlenebilir.

Yayımlandıktan sonra faturalandırma fiyatlandırma modeli seçimi değiştirilemez. Ayrıca, aynı teklif için tüm planlar aynı fiyatlandırma modelini paylaşmalıdır.

- **Fatura dönemi**: aylık veya yıllık

Müşterilerin listelenen fiyatı ödemesi gereken sıklığı seçin. En az bir aylık veya yıllık fiyat belirtilmelidir ya da müşteriler için her iki seçenek de kullanılabilir hale getirilebilir.

- **Fiyat**: aylık ABD Doları veya yıl başına ABD Doları

Yerel para birimi cinsinden ayarlanan fiyatlar (USD = Birleşik Devletler dolar), kurulum sırasında kullanılabilir olan geçerli döviz kurlarını kullanarak tüm seçili piyasaların yerel para birimine dönüştürülür. Fiyatlandırma elektronik tablosunu dışarı aktarıp her bir pazardaki fiyatı inceleyerek yayımlamadan önce bu fiyatları doğrulayın. Tek bir pazarda özel fiyatlar ayarlamak isterseniz, fiyatlandırma elektronik tablosunu değiştirin ve içeri aktarın. Bu fiyatlandırmayı doğrulamak ve bu ayarların sahibi olmak sizin sorumluluğunuzdadır.
*\*Fiyatlandırma verilerinin dışarı aktarılmasını sağlamak için öncelikle fiyatlandırma değişikliklerinizi kaydetmeniz gerekir.*

Bir plan yayımlandıktan sonra nelerin değiştirebilecekleri hakkında bazı kısıtlamalar olduğundan, yayımlamadan önce fiyatlarınızı dikkatlice gözden geçirin:

- Bir plan yayımlandığında fiyatlandırma modeli değiştirilemez.
- Bir plan için faturalandırma dönemi yayımlandıktan sonra, daha sonra kaldırılamaz.
- Planınızdaki bir pazar bedeli yayımlandığında, daha sonra değiştirilemez.

### <a name="plan-audience"></a>Hedef kitle planlayın

Her planı herkese görünür olarak veya yalnızca seçtiğiniz belirli bir kitlele yapılandırma seçeneğiniz vardır. Azure AD kiracı kimliklerini kullanarak bu kısıtlanmış hedef kitleye üyelik atayabilirsiniz.

#### <a name="privacy"></a>Gizlilik

- **Bu özel bir plandır** (isteğe bağlı onay kutusu)

Planınızı özel ve yalnızca seçtiğiniz kısıtlanmış kitlele görünür hale getirmek için bu kutuyu işaretleyin. Özel bir plan olarak yayımlandıktan sonra izleyiciyi güncelleştirebilir veya planı herkes için kullanılabilir hale getirebilirsiniz. Bir plan herkese görünür olarak yayımlandıktan sonra herkese görünür kalması gerekir. (Plan bir özel plan olarak yeniden yapılandırılamaz).

- **Kısıtlanmış hedef kitle (kiracı kimlikleri)**

Bu özel plana erişimi olacak hedef kitleyi atayın. Atanan her kiracı KIMLIĞININ açıklamasını ekleme seçeneğiyle kiracı kimlikleri kullanılarak erişim atanır. Bir. csv elektronik tablo dosyası içeri aktarıldıysanız, en fazla 10 Kiracı kimliği eklenebilir veya 20.000 müşteri Kiracı kimliği olabilir.

Kiracı, bir GUID (genel benzersiz tanımlayıcı, kaynakları tanımlamak için kullanılan 128 bitlik bir tamsayı numarası) olarak temsil edilen KIMLIĞE sahip bir kuruluşun gösterimidir. Kuruluş veya uygulama geliştirici Microsoft'la bir ilişki oluşturduğunda, örneğin Azure'a, Microsoft Intune'a veya Microsoft 365'e kaydolduğunda kuruluşun veya uygulama geliştiricinin aldığı özel bir Azure AD örneğidir. Her Azure AD kiracısı benzersizdir ve diğer Azure AD kiracılarından ayrıdır. Kiracıyı denetlemek için, uygulamanızı yönetmek üzere kullanmak istediğiniz hesapla Azure portal oturum açın. Bir kiracınız varsa, otomatik olarak bu kiracıda oturum açar ve kiracı adını doğrudan hesap adınızın altında görebilirsiniz. Adınızı, e-postanızı, dizininizi/kiracı kimliğinizi (GUID) ve etki alanınızı görmek için, Azure portalının sağ üst kısmında bulunan hesap adınızın üzerine gelin. Hesabınız birden çok kiracıyla ilişkiliyse, kiracılar arasında geçiş yapabileceğiniz bir menüyü açmak için hesap adınızı seçebilirsiniz. Her kiracının kendi kiracı kimliği vardır. Ayrıca, şu adreste bir etki alanı adı URL 'SI kullanarak kuruluşunuzun kiracı KIMLIĞINI de arayabilirsiniz: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

SaaS, özel bir hedef kitle tanımlamak için kiracı kimlikleri kullanır, diğer teklif türleri de Azure abonelik kimliklerini (GUID 'Ler olarak da temsil edilir) kullanabilir.

> [!NOTE]
> Özel hedef kitle (veya kısıtlı kitleci), önizleme kitlemesinden farklıdır. **[Önizleme](#preview)** sekmesinde bir önizleme hedef kitlesi tanımlayabilirsiniz. Bir önizleme hedef kitlesi, Market 'te canlı olarak yayımlanmadan *önce* teklifinizin erişimine izin verilir. Özel hedef kitle ataması yalnızca belirli bir plana uygulansa da, önizleme hedef kitlesi tüm planları (özel veya değil) görüntüleyebilir, ancak yalnızca plan test edilirken ve doğrulandığında sınırlı önizleme döneminde bu süre boyunca yalnızca sınırlı önizleme dönemi boyunca görüntülenebilir.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Market teklifi içindeki planların örnek listesi

![Notlardaki örnek Market planı listesi](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test sürüşü

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Bulut çözümü sağlayıcısı (CSP) satıcı hedef kitlesi

, Teklifinizin CSP programında kullanılabilmesini sağlamak için, bulut çözümü sağlayıcılarının, müşterilerine paketlenmiş bir çözümün parçası olarak ürününüzü satmasına olanak sağlar. Daha fazla bilgi için bkz. [bulut çözümü sağlayıcısı (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers.md).

## <a name="publish"></a>Yayımlama

Teklifin tüm gerekli bölümlerini tamamladıktan sonra portalın sağ üst köşesinde **Yayımla** ' yı seçin. **İnceleme ve yayımlama** sayfasına yeniden yönlendirilirsiniz.

#### <a name="submit-offer-to-preview"></a>Önizlemeye teklif Gönder

Bu teklifi ilk kez yayınlıyorsanız, şunları yapabilirsiniz:

- Teklifin her bölümü için tamamlanma durumuna bakın.
    - *Başlatılmamış* -bölüm dokunulmamış ve tamamlanması gereken anlamına gelir.
    - *Tamamlanmamış* -bölümde düzeltilmesi gereken hatalar olduğu veya daha fazla bilgi sağlanması gerekir. Lütfen bölüm (ler) e geri dönün ve güncelleştirin.
    - *Tam* -bölümün tamamlandığı, tüm gerekli verilerin sağlandığı ve hata olmadığı anlamına gelir. Teklifi gönderebilmeniz için teklifin tüm bölümlerinin tamamen bir durumda olması gerekir.
- Uygulamanızı anlamak için yararlı olan tüm ek notlara ek olarak, uygulamanızın doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın.
- **Gönder**' i seçerek teklifi yayımlamaya gönderebilirsiniz. Size, gözden geçirmeniz ve onaylamanız için teklifin bir önizleme sürümünün ne zaman kullanılabileceğini bilmenizi sağlayacak bir e-posta göndereceğiz. Teklifinizi ortak (veya özel bir teklif, özel hedef kitleye) yayımlamak için Iş Ortağı Merkezi 'ne dönmeniz ve teklif için **Go-Live** ' ı seçmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te mevcut bir teklifi güncelleştirme](./update-existing-offer.md)
