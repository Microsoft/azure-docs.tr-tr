---
title: Ticari Market 'te yeni bir Dynamics 365 Iş Merkezi teklifi oluşturun
description: Azure Marketi, AppSource veya Microsoft Iş Ortağı Merkezi 'ndeki ticari Market portalını kullanan bulut çözümü sağlayıcısı (CSP) programı aracılığıyla listeleme veya satma için yeni bir Dynamics 365 Iş Merkezi teklifi oluşturma.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: b3d66cab8cf43d862bbf17d5783f0469e3e1a5f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825646"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Yeni bir Dynamics 365 Iş Merkezi teklifi oluşturma

Bu konuda, yeni bir Dynamics 365 Iş Merkezi teklifinin nasıl oluşturulacağı açıklanmaktadır. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) , finans, işlemler, tedarik ZINCIRI, CRM ve proje yönetimi ve elektronik ticaret dahil olmak üzere çok çeşitli iş süreçlerini işleyen bir kurumsal kaynak planlama (ERP) sistemidir. Premium paketler klasik dağıtım modelini ve üretimi de destekler. Dynamics 365 Business Central için tüm teklifler, sertifika sürecimize gitmelidir.

Dynamics 365 Iş Merkezi teklifleri oluşturmaya başlamak için önce [bir Iş Ortağı Merkezi hesabı](./create-account.md) oluşturduğunuzdan ve **genel bakış** sayfası seçili olarak [ticari Market panosunu](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)seçtiğinizden emin olun.

![Iş Ortağı Merkezi 'nde ticari Market panosu](./media/new-offer-overview.png)

>[!Note]
> Bir teklif yayımlandıktan sonra, Iş Ortağı Merkezi 'nde yapılan teklifle ilgili düzenlemeler yalnızca sistem sırasında ve yeniden yayımlamadan sonra mağazaların ön yüzlerinin güncelleştirilmesini sağlar. Lütfen değişiklikleri yaptıktan sonra teklifi yayın için gönderdiğinizden emin olun.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

**+ Yeni teklif** düğmesini seçin ve ardından **Dynamics 365 İş Merkezi** menü öğesini seçin. **Yeni teklif** iletişim kutusu görüntülenir.

### <a name="offer-id-and-alias"></a>Teklif KIMLIĞI ve diğer ad

- **TEKLIF kimliği**: hesabınızdaki her teklif için benzersiz tanımlayıcı. Bu KIMLIK, Market teklifi ve Azure Resource Manager şablonları (varsa) için URL adresindeki müşterilere görünür olacaktır. Teklif KIMLIĞI küçük harf alfasayısal karakterler (tireler ve alt çizgiler dahil ancak boşluk yok), 50 karakterle sınırlı ve **Oluştur**' u seçtikten sonra değiştirilemez.  Örneğin, burada *Test-teklif-1* girerseniz, teklif URL 'si `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`olur.

- **Teklif diğer adı**: Iş Ortağı Merkezi içindeki teklifine başvurmak için kullanılan ad. Bu ad Market 'te kullanılmayacak ve teklif adından ve müşterilere gösterilecek diğer değerlerden farklı. Bu değer, **Oluştur**' u seçtikten sonra değiştirilemez.

**TEKLIF kimliği** ve **teklif diğer adınızı**girdikten sonra **Oluştur**' u seçin. Daha sonra teklifinizin farklı bölümlerinin tümünde çalışabileceksiniz.

## <a name="offer-setup"></a>Teklif kurulumu

**Teklif kurulum** sayfası, aşağıdaki bilgileri ister. Bu alanları tamamladıktan sonra **Kaydet** ' i seçtiğinizden emin olun.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Potansiyel müşterilerin bu liste teklifiyle nasıl etkileşime geçmesini istiyorsunuz?

Bu teklif için kullanmak istediğiniz seçeneği belirleyin.

#### <a name="get-it-now-free"></a>Şimdi alın (ücretsiz)

Uygulamanıza erişebilecekleri geçerli bir URL ( *http* veya *https*ile başlayarak) sağlayarak müşterilerinizin teklifinizi ücretsiz olarak listeleyin.  Örneğin, `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Ücretsiz deneme (listeleme)

Ücretsiz bir deneme bağlantısı olan müşterilere teklifinizi, bir deneme sunabileceği geçerli bir URL ( *http* veya *https*ile başlayarak) sunarak listeleyin.  Örneğin: `https://contoso.com/trial/my-app`. Ücretsiz denemelerdeki teklif listesi, hizmetiniz tarafından oluşturulur, yönetilir ve yapılandırılır ve Microsoft tarafından yönetilen abonelikler içermez.

> [!NOTE]
> Uygulamanızın deneme bağlantınız aracılığıyla alacağı belirteçler, uygulamanızda hesap oluşturmayı otomatikleştirmek için yalnızca Azure Active Directory (Azure AD) aracılığıyla Kullanıcı bilgilerini almak üzere kullanılabilir. Bu belirteci kullanarak kimlik doğrulaması için Microsoft hesapları desteklenmez.

#### <a name="contact-me"></a>Benimle iletişim kurun

Müşteri Ilişkileri yönetimi (CRM) sisteminizi bağlayarak müşteri iletişim bilgilerini toplayın. Müşterinin, bilgilerini paylaşması için izin istenir. Bu müşteri ayrıntıları, teklifinizin bulunduğu teklif adı, KIMLIĞI ve Market kaynağıyla birlikte, yapılandırdığınız CRM sistemine gönderilir. CRM 'nizi yapılandırma hakkında daha fazla bilgi için bkz. [Connect lider yönetimi](#connect-lead-management). 

### <a name="test-drive"></a>Test sürüşü

Test sürücüsü, bu kullanıcılara teklifinizi "satın almadan önce dene" seçeneği sunarak, daha fazla dönüştürmeye ve yüksek oranda nitelikli müşteri adaylarının oluşturulmasına neden olacak şekilde sergilemenin harika bir yoludur. [Sınama sürücüleri hakkında daha fazla bilgi edinin.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Bir sınama sürücüsünü etkinleştirmek için, **sınama sürücüsünü etkinleştir** kutusunu işaretleyin. Daha sonra, müşterilerin teklifinizi sabit bir süre için denemesini sağlamak üzere teknik yapılandırma Yapılandır ' da [Test sürücüsünde](#test-drive-technical-configuration) bir tanıtım ortamı yapılandırmanız gerekir. 

#### <a name="type-of-test-drive"></a>Test sürücüsünün türü

Aşağıdaki seçeneklerden seçim yapın:

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

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Daha fazla bilgi için bkz. [müşteri adayı yönetimine genel bakış](./commercial-marketplace-get-customer-leads.md).

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

## <a name="properties"></a>Özellikler

**Özellikler** sayfası, teklifinizi Market 'te, uygulama sürümünüzde ve teklifinizi destekleyen yasal sözleşmelerde gruplandırmak için kullanılan kategorileri ve endüstrileri tanımlamanızı sağlar. Bu sayfayı tamamladıktan sonra **Kaydet** ' i seçin.

### <a name="category"></a>Kategori

Teklifinizi uygun Market arama alanına yerleştirmek için kullanılacak en az bir ve en fazla üç kategori seçin. Teklifinizin teklif açıklamasında bu kategorileri nasıl desteklediğine dikkat edin. 

### <a name="industry"></a>Sektör

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Uygulama sürümü

Teklifinizin sürüm numarasını girin. Müşteriler bu sürümü teklifin ayrıntı sayfasında listelenmiş olarak görür.

### <a name="standard-contract"></a>Standart Sözleşme

Müşteriler için satın alma sürecini basitleştirmek ve yazılım satıcılarının yasal karmaşıklığını azaltmak için, Microsoft, Market 'teki bir işlemin kolaylaştırılmasına yardımcı olmak üzere standart bir sözleşme şablonu sunmaktadır.

Özel hüküm ve koşulları ortadan kaldırmaktansa, yazılımınızı standart sözleşme altında sunmayı tercih edebilirsiniz; bu da müşterilerin yalnızca VET ve bir kez kabul etmesi gerekir.

Standart Sözleşme şurada bulunabilir: https://go.microsoft.com/fwlink/?linkid=2041178

Standart sözleşmeyi kullanmak için **Standart sözleşmeyi kullan?** kutusunu işaretleyin.

#### <a name="terms-of-use"></a>Kullanım koşulları

**Standart sözleşmeyi kullan?** kutusunu denetlemeyin, **kullanım koşulları** alanında kendi yasal kullanım koşullarınızı sağlamanız gerekir. En fazla 10.000 karakter girin veya kullanım koşullarınız daha uzun bir açıklama gerektiriyorsa, ek lisans koşullarınızın bulunabileceği URL 'YI sağlayın. Müşterilerinizin uygulamanızı deneyebilmeleri için önce bu koşulları kabul etmesi gerekir.

## <a name="offer-listing"></a>Teklif listesi

Teklif listesi sayfası, teklifinizin listelenecek dilleri görüntüler. Şu anda **İngilizce (Birleşik Devletler)** kullanılabilir tek seçenektir.

Her dil/Pazar için Market ayrıntılarını (teklif adı, açıklama, görüntüler vb.) tanımlamanız gerekir. Bu bilgileri sağlamak için dil/Pazar adını seçin.

> [!NOTE]
> Teklif açıklaması (örneğin, açıklama, belgeler, ekran görüntüleri, kullanım koşulları vb.) için teklif listesi içeriği, "Bu uygulama yalnızca [Ingilizce olmayan dil] içinde kullanılabilir" olarak, Ingilizce olması gerekmez. Ayrıca, teklif listeleme içeriğinde kullanılandan farklı bir dilde içerik sunmak için *yararlı bir bağlantı URL 'si* sağlamak da kabul edilebilir.

### <a name="name"></a>Ad

Buraya girdiğiniz ad, müşteriler tarafından teklif listelerinizin başlığı olarak gösterilir. Bu alan, teklifi oluştururken **teklif diğer adı** için girdiğiniz metin ile önceden doldurulur, ancak bu değeri değiştirebilirsiniz. Bu ad trademarked olabilir (ve ticari marka veya telif hakkı sembolleri dahil edebilirsiniz). Ad 50 karakterden uzun olamaz ve herhangi bir emojıs içeremez.

### <a name="short-description"></a>Kısa açıklama

Sunabileceğiniz (en fazla 100 karakter) kısa bir açıklama sağlayın ve bu işlem Market arama sonuçlarında kullanılabilir.

### <a name="description"></a>Açıklama

Teklifiniz için daha uzun bir açıklama sağlayın (en fazla 3.000 karakter). Bu açıklama Market dökümüne Genel Bakış bölümündeki müşterilere görüntülenecektir. Teklifinizin değer teklifini, anahtar avantajlarını, kategori ve/veya sektör ilişkilendirmelerini, uygulama içi satın alma fırsatlarını ve gerekli tüm teklifleri dahil edin. 

Tanımlarınızı yazmak için bazı ipuçları:  

- Tanımınızın ilk birkaç cümlede teklifinizin değer teklifini açık bir şekilde açıklamalıdır. Değer teklifinde aşağıdaki öğeleri ekleyin:
  - Ürünün açıklaması
  - Üründen faydalanan Kullanıcı türü
  - Müşteri ihtiyaçları veya ürün adresleriyle ilgili bir sorun
- İlk birkaç tümcenin arama motoru sonuçlarında görüntülenebileceğini aklınızda bulundurun.  
- Ürününüzü satmaya yönelik özelliklere ve işlevlere güvenmeyin. Bunun yerine, teslim ettiğiniz değere odaklanın.  
- Sektöre özgü sözlük veya avantaj temelli bir ifade mümkün olduğunca yararlanın. 
- Açıklamanızı biçimlendirmek ve daha ilgi çekici hale getirmek için HTML etiketleri kullanmayı düşünün.

### <a name="search-keywords"></a>Anahtar sözcüklerde ara

Müşterilerin teklifinizi Market 'te bulmasına yardımcı olmak için isteğe bağlı en fazla üç arama anahtar sözcüğü girebilirsiniz. En iyi sonuçlar için, tanımlarınızda bu anahtar sözcükleri de kullanmayı deneyin.

### <a name="products-your-app-works-with"></a>Uygulamanızın ile birlikte çalışarak ürünler

Müşterilerinizin uygulamanızın belirli ürünlerle birlikte çalışıp çalışmadığını bilmesini istiyorsanız buraya en fazla üç ürün adı girin.

### <a name="support-urls"></a>Destek URL 'Leri

Bu bölüm, müşterilerinizin teklifiniz hakkında daha fazla bilgi sağlamasına yardımcı olacak bağlantılar sağlamanıza olanak tanır.

#### <a name="help-link"></a>Yardım bağlantısı

Müşterilerinizin teklifiniz hakkında daha fazla bilgi sağlayabilecekleri URL 'YI girin.

#### <a name="privacy-policy-url"></a>Gizlilik ilkesi URL 'SI

Kuruluşunuzun gizlilik ilkesinin URL 'sini girin. Uygulamanızın gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan ve geçerli bir gizlilik ilkesi sağlamaya yönelik siz sorumlusunuz.

### <a name="contacts"></a>Kişiler

Bu bölümde, bir **destek kişisi** ve **mühendislik ilgili kişisi**için ad, e-posta ve telefon numarası sağlamanız gerekir. Bu bilgiler müşterilere gösterilmez, ancak Microsoft tarafından kullanılabilir ve CSP iş ortakları için de sağlanıyor olabilir.

**Destek kişisi** bölümünde, CSP iş ortaklarının teklifiniz için destek BULABILECEĞI **Destek URL** 'sini de sağlamanız gerekir.

### <a name="supporting-documents"></a>Destekleyici belgeler

Burada, teknik incelemeler, broşürler, denetim listeleri veya sunular gibi en az bir (en fazla üç) ilgili pazarlama belgesi sağlayın. Bu belgeler. PDF biçiminde olmalıdır.

### <a name="marketplace-images"></a>Market görüntüleri

Bu bölümde, müşteri teklifinizi gösterirken kullanılacak logo ve görüntüler sağlayabilirsiniz. Tüm görüntülerin. png biçiminde olması gerekir.

#### <a name="store-logos"></a>Mağaza logoları

Teklifinizin logosunu iki boyutta sağlayın: **küçük (48 x 48)** ve **büyük (216 x 216)** .

#### <a name="hero"></a>Hero

Hero görüntüsü isteğe bağlıdır. Bir tane sağlarsanız, 815 x 290 pikseli ölçmelidir.

#### <a name="screenshots"></a>Ekran görüntüleri

Teklifinizin nasıl çalıştığını gösteren ekran görüntüleri ekleyin. En az üç ekran görüntüsü gerekir ve beş adede kadar ekleyebilirsiniz. Tüm ekran görüntüleri 1280 x 720 piksel olmalıdır.

#### <a name="videos"></a>Videolar

İsteğe bağlı olarak, teklifinizi gösteren en fazla beş video ekleyebilirsiniz. Bu videoların YouTube ve/veya Vimeo 'da barındırılması gerekir. Her biri için videonun adını, URL 'sini ve videonun küçük bir görüntüsünü girin (1280 x 720 piksel)

#### <a name="additional-marketplace-listing-resources"></a>Ek Market listeleme kaynakları

- [Market teklif listeleri için en iyi uygulamalar](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Kullanılabilirlik

**Kullanılabilirlik** sayfası, teklifinizin nerede ve nasıl kullanılabilir hale sunulabileceği hakkında seçenekler sağlar.

### <a name="markets"></a>Pazar

Bu bölüm, teklifinizin kullanılabilir olması gereken pazarları belirtmenize olanak tanır. Bunu yapmak için **Pazar seçimi** açılır penceresini görüntüleyen **pazarları Düzenle**' yi seçin.

Varsayılan olarak, bir pazar seçili değildir, ancak teklifinizi yayımlamak için en az bir pazar seçmeniz gerekir. Teklifinizin her olası pazarda kullanılabilmesini sağlamak için **Tümünü Seç** ' e tıklayın veya eklemek istediğiniz belirli pazarları seçin. İşiniz bittiğinde **Kaydet**' i seçin.

Burada yaptığınız seçimler yalnızca yeni alımlar için geçerlidir; zaten belirli bir pazar ortamında uygulamanız varsa ve daha sonra bu pazarı kaldırırsanız, bu pazarda zaten sunulan bir pazara sahip kişiler bunu kullanmaya devam edebilir, ancak bu pazardaki yeni müşteriler teklifinizi alabilir.

> [!IMPORTANT]
> Bu gereksinimler burada veya Iş Ortağı Merkezi 'nde listelenmese de, yerel yasal gereksinimleri karşılamak sizin sorumluluğunuzdadır.

Tüm pazarlar, yerel yasalar ve kısıtlamalar ' ı seçtiğinizde ve diğer faktörler bazı ülkelerde ve bölgelerde belirli tekliflerin listelenmesini engelleyebilse de aklınızda bulundurun.

### <a name="preview-audience"></a>İzleyiciyi Önizle

Teklifinizi daha geniş Market teklifiyle yayımlamadan önce, önce onu sınırlı bir **Önizleme hedef kitlesi**için kullanılabilir hale getirmeniz gerekir. Burada bir **gizleme anahtarı** (yalnızca küçük harf ve/veya sayı kullanan herhangi bir dize) girin. Önizleme hedef kitlenizin üyeleri, Market 'te teklifinizin önizlemesini görüntülemek için bu anahtarı bir belirteç olarak kullanabilir.

Daha sonra, teklifinizi kullanılabilir duruma getirmek ve önizleme kısıtlamasını kaldırmak için hazır olduğunuzda, **gizleme anahtarını** kaldırmanız ve yeniden yayımlamanız gerekir.

## <a name="technical-configuration"></a>Teknik yapılandırma

**Teknik yapılandırma** sayfası, teklifiniz ile bağlantı kurmak için kullanılan teknik ayrıntıları tanımlar. Bu bağlantı, teklifi edinmeyi tercih ettiklerinde, son müşteri için teklifinizi sağlamamızı sağlar.

### <a name="package-type"></a>Paket türü

Teklifiniz için geçerli olan seçeneği belirleyin:

- **Ekleme tarihi**: bir eklenti uygulaması, Dynamics 365 Business Central 'ın deneyimini ve mevcut işlevselliğini genişletir. Daha fazla bilgi için bkz. [eklenti uygulamaları](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Bağlan**: bir Connect uygulaması, Dynamics 365 İş Merkezi ile bir üçüncü taraf çözüm veya hizmet arasında noktadan noktaya bağlantı kurulması gereken senaryolarda kullanılabilir. Daha fazla bilgi için bkz. [bağlanma](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Karşıya dosya yükleme

Yukarıdaki **Ekle** ' yi seçtiyseniz, teklifin paket dosyasını, bağımlılıkları olan herhangi bir uzantıya ait paket dosyaları ile birlikte karşıya yükleyeceksiniz.

#### <a name="extensions-package-file"></a>Uzantılar paket dosyası

Teklifiniz için Uzantı paketi dosyası (. app) dosyasını karşıya yükleyin.

#### <a name="library-package-file"></a>Kitaplık paketi dosyası

Teklifinizin Market 'e yayımlanmayacak başka bir uzantıyla birlikte yüklenmesi gerekiyorsa gerekir. Öyleyse. app dosyasını buraya yükleyin.

#### <a name="dependency-package-file"></a>Bağımlılık paketi dosyası

Teklifinizin Market 'te zaten yayımlanmış başka bir uzantıyla birlikte yüklenmesi gerekiyorsa gereklidir. Bu durumda, `.app` veya `.zip` dosyasını buraya yükleyin.

### <a name="url-to-app-installation"></a>Uygulama yüklemesinin URL 'SI

Yukarıdaki **Bağlan** ' ı seçtiyseniz uygulama yüklemenizin URL 'sini burada belirtin.

## <a name="test-drive-technical-configuration"></a>Sınama sürücüsü teknik yapılandırma

[Teklif kurulumu](#offer-setup) sayfasında **bir test sürücüsünü etkinleştir** ' i seçtiyseniz, müşterilerinizin teklifinizin bir test sürücüsü ile karşılaşmasına izin vermek için buraya Ayrıntılar sağlamanız gerekir.

**Sınama sürücüsü** sayfası, müşterilerin satın alma işlemine geçmeden önce teklifinizi denemesini sağlayacak bir tanıtım (veya "test sürücüsü") ayarlamanıza olanak sağlar. [Test sürücüsü nedir?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)makalesinde daha fazla bilgi edinin. Teklifiniz için artık bir test sürücüsü sağlamak istemiyorsanız **[teklif kurulumu](#offer-setup)** sayfasına dönün ve **Test sürücüsünü etkinleştir**seçeneğinin işaretini kaldırın.

Aşağıdaki test sürücüsü türleri, her biri kendi teknik yapılandırma gereksinimlerine sahip kullanılabilir.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Mantıksal uygulama](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Teknik yapılandırma gerekli değil)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager test sürücüsü için teknik yapılandırma

Çözümünüzü oluşturan tüm Azure kaynaklarını içeren bir dağıtım şablonu. Bu senaryoya uyan ürünler yalnızca Azure kaynaklarını kullanır. [Azure Resource Manager test sürücüsü](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)ayarlama hakkında daha fazla bilgi edinin.

- **Bölgeler** (gerekli): Şu anda, test sürücünüzün kullanılabilir hale getirilbileceği 26 Azure tarafından desteklenen bölge vardır. Genellikle, en iyi performans için en yakın bölgeyi seçebilmeniz için, en fazla sayıda müşteriyi tahmin ettiğiniz bölgelerde test sürücünüzü kullanılabilir hale getirmek isteyeceksiniz. Aboneliğinizin seçtiğiniz her bölgede gereken tüm kaynakları dağıtmasına izin verildiğinden emin olmanız gerekir.

- **Örnekler**: bir türü (sıcak veya soğuk) ve kullanılabilir örneklerin sayısını seçin; Bu değer teklifinizin kullanılabildiği bölge sayısıyla çarpılacak.

**Sık**erişimli: Bu örnek türü dağıtılır ve seçilen bölgeye göre erişim bekleniyor. Müşteriler, bir dağıtımı beklemek yerine bir test sürücüsünün *sık* erişimli örneklerine anında erişebilir. Zorunluluğunu getirir, bu örneklerin her zaman Azure aboneliğinizde çalışıyor olması, bu nedenle daha büyük bir çalışma süresi maliyeti doğuracaktır. En az bir *sıcak* örnek olması önemle önerilir, çünkü çoğu müşteri tam dağıtımları beklemek Istemelidir ve *etkin* örnek yoksa, müşteri kullanımındaki bir bırakmaya neden olur.

**Soğuk**: Bu örnek türü, bölge başına büyük olasılıkla dağıtılabilecek örneklerin toplam sayısını temsil eder. Soğuk örnekler, bir müşteri test sürücüsünü istediğinde tüm test sürücüsü Kaynak Yöneticisi şablonun dağıtılmasını gerektirir, böylece *soğuk* örnekler, *etkin* örneklere göre yüklenmeye çok daha yavaştır. Zorunluluğunu getirir, yalnızca test sürücüsünün süresi için ödeme yapmanız gereken *bir deyişle, Azure aboneliğinizde her zaman* bir *sıcak* örnekle birlikte çalışmıyor.

- **Test sürücüsü Azure Resource Manager şablonu**: Azure Resource Manager şablonunuzu içeren. zip ' i yükleyin.  Hızlı başlangıç makalesinde Azure Resource Manager şablonu oluşturma hakkında daha fazla bilgi edinin [Azure Portal kullanarak Azure Resource Manager şablonları oluşturun ve dağıtın](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Sınama sürücüsü süresi** (zorunlu): test sürücüsünün etkin kalacağı sürenin uzunluğunu saat cinsinden belirtin. Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer. Bu süre yalnızca birkaç saat kadar ayarlanabilir (örneğin, "2" saat, "1,5" geçerli değildir).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 test sürücüsü için teknik yapılandırma

Microsoft, bu test sürücüsü türünü kullanarak hizmet sağlamayı ve dağıtımını barındırarak ve tutarak bir test sürücüsü ayarlamanın karmaşıklığını ortadan kaldırabilir. Bu barındırılan test sürücüsü türünün yapılandırması, test sürücüsünün bir Iş Merkezi, müşteri katılımı veya Işlem kitlesini hedeflediğinden bağımsız olarak aynıdır.

- **En fazla eşzamanlı test sürücüsü** (gerekli): test sürücünüzü tek seferde kullanabilen en fazla müşteri sayısını ayarlayın. Her eşzamanlı kullanıcı, test sürücüsü etkinken bir Dynamics 365 lisansını kullanır, bu nedenle maksimum sınır kümesini destekleyecek yeterli lisansa sahip olduğunuzdan emin olmanız gerekir. Önerilen 3-5 değeri.

- **Sınama sürücüsü süresi** (zorunlu): saat sayısını tanımlayarak test sürücüsünün etkin kalacağı sürenin uzunluğunu girin. Bu süre dolduktan sonra, oturum sona erdir ve artık lisanslarınızdan birini tüketmez. Teklifinizin karmaşıklığına bağlı olarak 2-24 saat arasında bir değer öneririz. Bu süre yalnızca birkaç saat kadar ayarlanabilir (örneğin, "2" saat, "1,5" geçerli değildir).  Kullanıcı zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde yeni bir oturum isteyebilir.

- **Örnek URL** (gerekli): müşterinin test sürücüsüne başlayacağı URL. Genellikle uygulamanızı örnek verilerle çalıştıran Dynamics 365 örneğinizin URL 'SI (örneğin, https://testdrive.crm.dynamics.com).

- **Örnek Web API URL 'si** (gerekli): Microsoft 365 hesabınızda oturum açarak ve **Ayarlar** ' a giderek, Dynamics 365 ÖRNEĞINIZ IÇIN Web apı URL 'sini alın \&gt; **Özelleştirme** \&gt; **Geliştirici kaynakları** \&gt; **Örnek Web API 'si (hizmet kök URL 'si)** , burada bulunan URL 'yi kopyalayın (örneğin, https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Rol adı** (gerekli): özel Dynamics 365 test sürücünüzde tanımladığınız güvenlik rolü adını sağlayın, bu kullanıcı sınama sürücüleri sırasında kullanıcıya atanır (örneğin, test-sürücü-rolü).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Mantıksal uygulama sınama sürücüsü için teknik yapılandırma

Herhangi bir özel ürün, çeşitli karmaşık çözüm mimarilerini kapsayan bu tür test sürücüsü Dağıtım şablonunu kullanmalıdır. Mantıksal uygulama test sürücüleri ayarlama hakkında daha fazla bilgi için bkz. GitHub üzerinde [işlemler](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) ve [müşteri katılımı](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Bölge** (gerekli, tek seçimli açılan liste): Şu anda, test sürücünüzün kullanılabilir hale getirilbileceği 26 Azure tarafından desteklenen bölge vardır. Mantıksal uygulamanızın kaynakları seçtiğiniz bölgede dağıtılır. Mantıksal uygulamanızda belirli bir bölgede depolanan özel kaynaklar varsa bölgenin burada seçildiğinden emin olun. En iyi yöntem, mantıksal uygulamanızı portalda Azure aboneliğinizde yerel olarak dağıtmaktır ve bu seçimi yapmadan önce doğru çalıştığını doğrular.

- **En fazla eşzamanlı test sürücüsü** (gerekli): test sürücünüzü tek seferde kullanabilen en fazla müşteri sayısını ayarlayın. Bu test sürücüleri zaten dağıtılır ve müşterilerin bir dağıtımı beklemeden bunlara anında erişmelerini sağlar.

- **Sınama sürücüsü süresi** (zorunlu): test sürücüsünün etkin kalacağı sürenin uzunluğunu saat cinsinden belirtin. Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer.

- **Azure Kaynak grubu adı** (gerekli): mantıksal uygulama test sürücünüzün kaydedildiği [Azure Kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) adını girin.

- **Azure mantıksal uygulama adı** (gerekli): kullanıcıya test sürücüsünü atayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

- **Mantıksal uygulama adı** (gerekli) sağlamasını kaldırma: müşteri bittikten sonra test sürücüsünü sağlayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI test sürücüleri için teknik yapılandırma gerekli değildir

Etkileşimli bir Power BI görseli göstermek isteyen ürünler, özel olarak oluşturulmuş bir panoyu test sürücüleri olarak paylaşmak için, daha fazla teknik yapılandırma gerekmeden gömülü bir bağlantı kullanabilir. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) şablonu uygulamalarını ayarlama hakkında daha fazla bilgi edinin.

### <a name="deployment-subscription-details"></a>Dağıtım aboneliği ayrıntıları

Test sürücüsünü sizin adınıza dağıtmak için, ayrı ve benzersiz bir Azure aboneliği oluşturun ve sağlayın. (Power BI test sürücüleri için gerekli değildir).

- **Azure ABONELIK kimliği** (Azure Resource Manager ve Logic Apps için gereklidir): Azure hesap hizmetlerinizi kaynak kullanımı raporlama ve faturalama için erişim izni vermek üzere abonelik kimliğini girin. Henüz bir tane yoksa, test sürücüleri için kullanmak üzere [ayrı bir Azure aboneliği oluşturmayı](https://docs.microsoft.com/azure/billing/billing-create-subscription) düşünmeniz önerilir. [Azure Portal](https://portal.azure.com/) oturum açarak ve sol taraftaki menüdeki **abonelikler** SEKMESINE giderek Azure abonelik kimliğinizi bulabilirsiniz. Sekmeyi seçtiğinizde, abonelik KIMLIĞINIZ görüntülenir (örneğin, "a83645ac-1234-5AB6-345-1h234g764ghty").

- **Azure AD KIRACı kimliği** (gerekli): Azure ACTIVE DIRECTORY (ad) [kiracı kimliğinizi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)girin. Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol taraftaki menüden Active Directory sekmesini seçin, * * Özellikler ' i seçin, sonra LISTELENEN **dizin kimliği** numarasını (örneğin, 50c464d3-4930-494c-963c-1e951d15360e) arayın. Kuruluşunuzun kiracı KIMLIĞINI Ayrıca, etki alanı adı URL 'nizi kullanarak da arayabilirsiniz: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure AD kiracı adı** (dinamik 365 için gereklidir): Azure ACTIVE DIRECTORY (ad) adınızı girin. Bu adı bulmak için sağ üst köşedeki [Azure Portal](https://portal.azure.com/)oturum açın, kiracı adınız hesap adınızın altında listelenecektir.

- **Azure AD uygulama kimliği** (gerekli): Azure ACTIVE DIRECTORY (ad) [uygulama kimliğinizi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)girin. Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol menüden Active Directory sekmesini seçin, **uygulama kayıtları**' yı seçin, ardından listelenen **uygulama kimliği** numarasını arayın (örneğin, 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD uygulama istemci parolası** (gerekli): Azure AD uygulama [Istemci gizli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)anahtarını girin. Bu değeri bulmak için [Azure Portal](https://portal.azure.com/)oturum açın. Sol menüdeki **Azure Active Directory** sekmesini seçin, **uygulama kayıtları**' i seçin ve ardından test sürücüsü uygulamanızı seçin. Sonra **Sertifikalar ve gizlilikler**' ı seçin, **yeni istemci parolası**' nı seçin, bir açıklama girin, **süresi dolmasın**' ı seçin ve ardından **Ekle**' yi Değeri kopyalamadığınızdan emin olun. (Bunu yapmadan önce sayfadan uzaklaşmayın, aksi takdirde değere erişemezsiniz.)

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

### <a name="test-drive-marketplace-listings"></a>Sınama sürücüsü Market listeleri

**Test sürücüsü** sekmesinde bulunan **Market listeleme** seçeneği, test sürücünüzün kullanılabildiği dilleri görüntüler. Şu anda **İngilizce (Birleşik Devletler)** kullanılabilir tek konumdur. Sınama sürücüsü deneyimini açıklayan bilgileri girmek için dil adını seçin.

- **Açıklama** (gerekli): test sürücünüzü, neyi gösterdiklerinizi, kullanıcının deneme amaçlarını, araştırılacak özellikleri ve kullanıcının teklifinizi elde edip etmeyeceğinizi belirlemesine yardımcı olmak için Ilgili bilgileri tanıtın. Bu alana en fazla 3.000 karakter metin girilebilir. 

- **Erişim bilgileri** (Azure Resource Manager ve mantıksal test sürücüleri için gereklidir): Bu test sürücüsüne erişmek ve kullanmak için müşterinin neleri bilmesi gerektiğini açıklayın. Teklifinizi kullanmaya yönelik bir senaryoya ve müşterinin test sürüşü genelinde özelliklere erişmek için bilmeleri gereken tam olarak ne olduğunu öğrenin. Bu alana en fazla 10.000 karakter metin girilebilir.

- **Kullanıcı el ile** (gerekli): test sürücünüzün deneyiminizin derinlemesine bir yolu. Kullanıcı el kitabı, müşterinin test sürücüsüyle karşılaşmasını istediğiniz şekilde tam olarak kapsamalıdır ve sahip olabileceği sorulara başvuru olarak görev yapar. Dosya, karşıya yüklendikten sonra PDF biçiminde ve adlandırılmalıdır (en fazla 255 karakter) olmalıdır.

- **Videolar: video ekleme** (isteğe bağlı): Videolar YouTube veya Vimeo 'a yüklenebilir ve burada, bir müşterinin test sürücüsünü daha iyi anlamasına yardımcı olmak için bir bağlantı ve küçük resim görüntüsü (533 x 324 piksel) ile birlikte başvuruda bulunabilir. Teklifinizin özelliklerini başarıyla kullanma ve avantajlarını vurgulayan senaryoları anlama.
  - **Ad** (gerekli)
  - **URL (yalnızca YouTube veya Vimeo)** (gerekli)
  - **Küçük resim (533 x 324 piksel)** : görüntü dosyası png biçiminde olmalıdır.

## <a name="supplemental-content"></a>Ek içerik

Bu sayfa teklifinizi doğrulamamıza yardımcı olmak için teklifiniz hakkında ek bilgiler sağlamanıza olanak tanır. Bu bilgiler müşterilere gösterilmez veya Market 'te yayımlanmamıştır.

### <a name="target-release"></a>Hedef yayın

Çözümünüzün hedeflediği Microsoft Dynamics Business 'ın hangi sürümü olduğunu belirtin: **geçerli**, **sonraki büyük**veya **sonraki küçük**. Bu bilgiler, çözümünüzü uygun şekilde test etmemize olanak sağlar.

### <a name="supported-editions"></a>Desteklenen sürümler

Teklifiniz Microsoft Dynamics 365 Business Central 'ın Premium sürümünü gerektiriyorsa yalnızca **Premium** ' u seçin. Aksi takdirde, hem **Essentials** hem de **Premium**' u seçin.

### <a name="key-usage-scenario"></a>Anahtar kullanımı senaryosu

Teklifinizin bir belge (. PDF biçiminde) içinde listelenen anahtar kullanımı senaryolarını listeleyen bir `.pdf` dosyası yüklemeniz gerekir. Burada listelenen tüm senaryolar, Market için teklifinizi onaylamadan önce doğrulama takımımız tarafından doğrulanabilir.

### <a name="app-tests-automation"></a>Uygulama testleri Otomasyonu

İsteğe bağlı olarak bir **uygulama testleri Otomasyon** dosyası yükleyebilirsiniz (. app).

### <a name="test-accounts"></a>Test hesapları

Sertifika ekibimizin teklifinizi doğru bir şekilde gözden geçirmesi için bir test hesabı gerekiyorsa, **test hesabı** bilgilerinizi içeren bir. PDF,. doc veya. docx dosyasını yükleyin.

## <a name="publish"></a>Yayımlama

### <a name="submit-offer-to-preview"></a>Önizlemeye teklif Gönder

Teklifin tüm gerekli bölümlerini tamamladıktan sonra portalın sağ üst köşesinde **Yayımla** ' yı seçin. **İnceleme ve yayımlama** sayfasına yönlendirilirsiniz. 

Bu teklifi ilk kez yayımladıysanız şunları yapabilirsiniz:

- Teklifin her bölümü için tamamlanma durumuna bakın.
    - *Başlatılmamış* -bölüm dokunulmamış ve tamamlanması gereken anlamına gelir.
    - *Tamamlanmamış* -bölümde düzeltilmesi gereken hatalar olduğu veya daha fazla bilgi sağlanması gerekir. Bölüm (ler) e geri dönün ve güncelleştirin.
    - *Tam* -bölümün tamamlandığı, tüm gerekli verilerin sağlandığı ve hata olmadığı anlamına gelir. Teklifi gönderebilmeniz için teklifin tüm bölümlerinin tamamen bir durumda olması gerekir.
- **Sertifika notları** bölümünde, uygulamanızı anlamak için yararlı olan tüm ek notlara ek olarak, uygulamanızın doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın.
- **Gönder**' i seçerek teklifi yayımlamaya gönderebilirsiniz. Teklifin önizleme sürümü gözden geçirmeniz ve onaylamanız için kullanılabilir olduğunda size bir e-posta göndereceğiz. Iş Ortağı Merkezi 'ne dönün ve teklifinizi herkese açık bir şekilde (veya özel bir teklif olarak özel hedef kitleye) yayımlama teklifi için **Go-Live** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te mevcut bir teklifi güncelleştirme](./update-existing-offer.md)
