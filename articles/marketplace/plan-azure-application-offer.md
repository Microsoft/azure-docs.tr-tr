---
title: Ticari Market için bir Azure uygulaması teklifi planlayın
description: Azure Marketi 'nde listeleme veya satma için yeni bir Azure uygulaması teklifinin nasıl planlanacağını veya Microsoft Iş Ortağı Merkezi 'nde ticari Market portalını kullanan bulut çözümü sağlayıcısı (CSP) programını kullanmayı öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: f98f128e56810e3dd710ce454c8dc34b333690d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879301"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Ticari Market için bir Azure uygulaması teklifi planlayın

Bu makalede, Microsoft ticari Market 'e bir Azure uygulaması teklifi yayımlamaya yönelik farklı seçenekler ve gereksinimler açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Azure Uygulama tekliflerini tasarlama, derleme ve test etme, hem Azure platformu hem de teklifi oluşturmak için kullanılan teknolojiler hakkında teknik bilgi gerektirir. Mühendislik ekibiniz aşağıdaki Microsoft teknolojileri hakkında bilgi sahibi olmalıdır:

- [Azure hizmetlerinin](https://azure.microsoft.com/services/)temel olarak anlaşılmasıdır.
- [Azure uygulamaları tasarlama ve mimari](https://azure.microsoft.com/solutions/architecture/)oluşturma.
- [Azure sanal makineleri](https://azure.microsoft.com/services/virtual-machines/), [Azure depolama](https://azure.microsoft.com/services/?filter=storage#storage)ve [Azure ağ](https://azure.microsoft.com/services/?filter=networking#networking)ile çalışma hakkında bilgi.
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)çalışma bilgileri.
- [JSON](https://www.json.org/)hakkında çalışma bilgisi.

### <a name="technical-documentation-and-resources"></a>Teknik belgeler ve kaynaklar

Ticari Market için Azure uygulama teklifinizi planlarken aşağıdaki kaynakları gözden geçirin.

- [Azure Resource Manager şablonlarını anlama](../azure-resource-manager/templates/template-syntax.md)
- Hızlı Başlangıçlar:
    - [Azure Hızlı Başlangıç şablonları](https://azure.microsoft.com/documentation/templates/)
    - [Azure şablonları en iyi yöntemler Kılavuzu](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Uygulama tanımını yayımlama](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Hizmet kataloğu uygulaması dağıtma](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Öğreticiler:
    - [Tanım dosyaları oluşturma](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Lerinizi
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Yönetilen uygulama çözümleri](../azure-resource-manager/managed-applications/sample-projects.md)

[Azure Market 'e yönelik çözüm şablonları ve yönetilen uygulamalar için](https://channel9.msdn.com/Events/Build/2018/BRK3603) sunulan video, Azure Uygulama teklifi türüne kapsamlı bir giriş sağlar:

- Kullanılabilir teklif türleri
- Hangi teknik varlıkların gerekli olduğu
- Azure Resource Manager şablonu yazma
- Uygulama kullanıcı arabirimini geliştirme ve test etme
- Uygulama teklifini yayımlama
- Uygulama İnceleme işlemi

### <a name="suggested-tools"></a>Önerilen araçlar

Azure uygulamanızın yönetilmesine yardımcı olmak için aşağıdaki komut dosyası ortamlarının birini veya her ikisini birden seçin:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

Geliştirme ortamınıza aşağıdaki araçları eklemeniz önerilir:

- [Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- Aşağıdaki uzantılara sahip [Visual Studio Code](https://code.visualstudio.com/) :
    - Uzantı: [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Uzantı: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Uzantı: [prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Mevcut araçları [Azure Geliştirici Araçları](https://azure.microsoft.com/tools/) sayfasında inceleyebilirsiniz. Visual Studio kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/)bakın.

## <a name="listing-options"></a>Listeleme seçenekleri

Teklifiniz yayımlandıktan sonra teklifiniz için liste seçenekleri, teklifinizin liste sayfasının sol üst köşesinde bir düğme olarak görünür. Örneğin, aşağıdaki ekran görüntüsünde, _Şimdi al_ düğmesine sahip Azure Marketi 'nde bir teklif listesi sayfası gösterilmektedir. Bir sınama sürücüsü sunmayı seçtiyseniz, _test sürücüsü_ düğmesi de gösterilir.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Azure Marketi 'ndeki bir liste sayfasını gösterir.":::

## <a name="test-drive"></a>Sınama sürücüsü

Azure Uygulama teklifiniz için, müşterilerin satın almadan önce teklifinizi denemesini sağlayan bir test sürücüsünü etkinleştirmeyi seçebilirsiniz. Sınama sürücüleri hakkında daha fazla bilgi için bkz. [test sürücüsü nedir?](what-is-test-drive.md). Farklı türlerde test sürücüleri yapılandırma hakkında daha fazla bilgi için bkz. [Test Drive Technical Configuration](test-drive-technical-configuration.md).

Ayrıca, [test sürücüsü en iyi uygulamaları](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) hakkında bilgi alabilir ve [Test sürücülerine genel bakış](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF 'sini indirebilirsiniz (açılır pencere engelinizin kapalı olduğundan emin olun).

> [!NOTE]
> Kullanıcılar, Skımingolsa bile, tüm Azure uygulamaları Azure Resource Manager şablonu kullanılarak uygulandığından, bir Azure uygulaması için kullanılabilen tek bir test sürücüsü türü [Azure Resource Manager tabanlı bir test sürücüsü](azure-resource-manager-test-drive.md)olduğundan, Kullanıcı tarafından bildirilmeli.

## <a name="customer-leads"></a>Müşteri liderleri

Müşteri bilgilerini toplamak için teklifinizi müşteri ilişkileri yönetimi (CRM) sisteminize bağlamanız gerekir. Müşterinin, bilgilerini paylaşması için izin istenir. Teklif adı, KIMLIĞI ve çevrimiçi mağazalarla birlikte bu müşteri ayrıntıları, yapılandırdığınız CRM sistemine gönderilir. Ticari Market, Azure tablosu kullanma veya Power otomatikleştirmeyi kullanarak bir HTTPS uç noktası yapılandırma seçeneğiyle birlikte çeşitli CRM sistemlerini destekler.

Teklif oluşturma sırasında veya sonrasında bir CRM bağlantısını dilediğiniz zaman ekleyebilir veya değiştirebilirsiniz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategoriler ve alt kategoriler

Teklifinizi uygun ticari Market arama alanlarında gruplandırmak için en az bir ve en fazla iki kategori seçebilirsiniz. Her birincil ve ikincil kategori için en fazla iki alt kategori seçebilirsiniz. Kategorilerin ve alt kategorilerin tam listesi için bkz. [teklif listesi En Iyi uygulamaları](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Yasal sözleşmeler

Müşteriler için satın alma sürecini basitleştirmek ve yazılım satıcılarının yasal karmaşıklığını azaltmak için, Microsoft, ticari Market 'teki tekliflerinizi kullanabileceğiniz standart bir sözleşme sunar. Yazılımınızı standart sözleşme kapsamında sunışınızda, müşterilerin yalnızca bir kez okuyup kabul etmesi ve özel hüküm ve koşullar oluşturmanız gerekmez.

Standart sözleşmeyi kullanmayı seçerseniz, standart sözleşmeye genel değişiklik koşullarını ve en fazla 10 özel değişikliği ekleme seçeneğiniz vardır. Standart sözleşme yerine kendi hüküm ve koşullarınızı da kullanabilirsiniz. Bu ayrıntıları **Özellikler** sayfasında yöneteceksiniz. Ayrıntılı bilgi için bkz. [Microsoft Commercial Market Için standart sözleşme](standard-contract.md).

> [!NOTE]
> Ticari Market için standart sözleşmeyi kullanarak bir teklifi yayımladıktan sonra, kendi özel hüküm ve koşullarınızı kullanamazsınız. Bu bir "veya" senaryosudur. Çözümünüzü standart sözleşme kapsamında ya da kendi hüküm ve koşullarınız için sunun. Standart sözleşmenin şartlarını değiştirmek istiyorsanız, standart sözleşme değişiklikleri aracılığıyla bunu yapabilirsiniz.

## <a name="offer-listing-details"></a>Teklif listeleme ayrıntıları

Iş Ortağı Merkezi 'nde yeni bir Azure uygulaması teklifi oluşturduğunuzda, teklif listesi sayfasına metin, görüntü, isteğe bağlı videolar ve diğer ayrıntıları girersiniz. Bu, aşağıdaki örnekte gösterildiği gibi, müşterilerin Azure Marketi 'nde teklif listelerinizi bulduklarında göreceği bilgiler olacaktır.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Bu teklifin Azure Marketi 'nde nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Logo
2. Kategoriler
3. Destek adresi (bağlantı)
4. Kullanım koşulları
5. Gizlilik ilkesi adresi (bağlantı)
6. Teklif adı
7. Özet
8. Description
9. Ekran görüntüleri/videolar

Aşağıdaki ekran görüntüsünde, teklif bilgilerinin Azure portal nasıl göründüğü gösterilmektedir:

[![Bu teklifin Azure portal nasıl göründüğünü gösterir.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Başlık
2. Açıklama
3. Yararlı bağlantılar
4. Ekran görüntüleri

> [!NOTE]
> Teklif açıklamasının "Bu uygulama yalnızca [Ingilizce olmayan dil]" ifadesi ile başlıyorsa, teklif listesi içeriğinin Ingilizce olması gerekmez.

Teklifinizi daha kolay bir şekilde oluşturmaya yardımcı olmak için bu öğelerin bazılarını zaman önce hazırlayın. Aksi belirtilmediği takdirde, aşağıdaki öğeler gereklidir.

- **Ad**: Bu ad, ticari Market 'teki teklif listelerinizin başlığı olarak görünür. Ad trademarked olabilir. Bu, emojıs (ticari marka ve telif hakkı sembolleri olmadıkları müddetçe) içeremez ve 50 karakterle sınırlı olmalıdır.
- **Arama sonuçları Özeti**: teklifinizin amacını veya işlevini tek bir cümle olarak, satır sonu olmayan düz metin olarak, 100 karakter veya daha kısa bir sürede açıklama. Bu Özet, ticari Market listeleme arama sonuçlarında kullanılır.
- **Kısa açıklama**: en fazla 256 karakter uzunluğunda düz metin sağlar. Bu Özet, teklifinizin ayrıntılar sayfasında görünecektir.
- **Açıklama**: Bu açıklama Azure Marketi dökümüne genel bakış bölümünde görüntülenecektir. Bir değer teklifi, önemli avantajlar, hedeflenen kullanıcı tabanı, herhangi bir kategori veya sektör ilişkilendirmesi, uygulama içi satın alma fırsatları, müşteri ihtiyacı veya bir sorun, teklif adreslerinin, gerekli kaldırların ve daha fazla bilgi edinmek için bir bağlantı dahil etmeyi göz önünde bulundurun.

    Bu metin kutusunda, açıklamanızı daha ilgi çekici hale getirmek için kullanabileceğiniz zengin metin düzenleyici denetimleri vardır. Ayrıca, açıklamanızı biçimlendirmek için HTML etiketlerini de kullanabilirsiniz. Bu kutuya, HTML biçimlendirme ve boşluklar içeren en fazla 3.000 karakter yazabilirsiniz. Ek ipuçları için bkz. [ticari Market teklif açıklamalarında](supported-html-tags.md) [harika bir uygulama açıklaması](/windows/uwp/publish/write-a-great-app-description) ve HTML etiketi yazma.

- **Anahtar sözcükleri ara** (isteğe bağlı): müşterilerin teklifinizi çevrimiçi mağazada bulmak için kullanabileceği en fazla üç arama anahtar sözcüğü sağlayın. En iyi sonuçlar için, tanımlarınızda bu anahtar sözcükleri de kullanın. Teklif **adı** ve **açıklamasını** eklemeniz gerekmez. Bu metin, aramaya otomatik olarak eklenir.
- **Gizlilik ilkesi bağlantısı**: şirketinizin gizlilik ilkesinin URL 'si. Geçerli bir gizlilik ilkesi sağlamanız gerekir ve uygulamanızın gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan sorumludur.
- **Faydalı bağlantılar** (isteğe bağlı): teklifinizin kullanıcıları için çeşitli kaynaklara bağlantılar sağlayabilirsiniz. Örneğin, forumlar, SSS ve sürüm notları.
- **İletişim bilgileri**: aşağıdaki kişileri kuruluşunuzdan atamanız gerekir:
  - **Destek kişisi**: müşterileriniz sorguları açtıklarında kullanılacak Microsoft iş ortakları için ad, telefon ve e-posta adresi sağlayın. Destek Web sitenizin URL 'sini de eklemeniz gerekir.
  - **Mühendislik ilgili kişisi**: Teklifinizle ilgili sorunlar olduğunda, Microsoft 'un doğrudan kullanacağı adı, telefonu ve e-postayı belirtin. Bu iletişim bilgileri ticari Market 'te listelenmez.
  - **CSP program kişisi** (isteğe bağlı): bulut çözümü sağlayıcısı (CSP) programını kabul ediyorsanız, bu iş ortaklarının sizinle iletişim kurabilmesi için ad, telefon ve e-posta sağlayın. Pazarlama malzemelerinize da bir URL ekleyebilirsiniz.
- **Medya – logolar**: **büyük** boyutlu amblem için bir PNG dosyası sağlayın. İş Ortağı Merkezi, **küçük** ve **Orta ölçekli** bir amblem oluşturmak için bunu kullanacaktır. İsterseniz bunları daha sonra farklı görüntülerle değiştirebilirsiniz.
  - Büyük (216 x 216-350 x 350 px, gereklidir)
  - Orta (90 x 90 piksel, isteğe bağlı)
  - Küçük (48 x 48 piksel, isteğe bağlı)

  Bu amblemler çevrimiçi mağazalarda farklı yerlerde kullanılır:
  - Azure Marketi arama sonuçlarında küçük logo görüntülenir.
  - Orta logo, Microsoft Azure yeni bir kaynak oluşturduğunuzda görüntülenir.
  - Büyük logo, Azure Marketi 'ndeki teklif listesi sayfanızda görünür.

  Logolarınız için şu yönergeleri izleyin:

  - Azure tasarımının basit bir renk paleti vardır. Logonuzdaki birincil ve ikincil renklerinin sayısını sınırlandırın.
  - Portalın tema renkleri siyah ve beyazdır. Bu renkleri logonuzun arka plan rengi olarak kullanmayın. Logonuzun portalda öne çıkmasını sağlayan bir renk kullanın. Basit birincil renkleri öneririz.
  - Saydam bir arka plan kullanıyorsanız logo ve metnin beyaz, siyah ve mavi olmadığından emin olun.
  - Logonuzun görünmesi ve kullanılması, amblem veya arka plandaki degradelerin düz olması gerekir. Şirket veya marka adınız dahil olmak üzere logoya metin yerleştirmeyin. Bulanık görüntüler gönderiminizin reddedilmesine neden olur.
  - Logonuzun esnetilmediğinden emin olun.

- **Medya-ekran görüntüleri** (isteğe bağlı): teklifinizin nasıl çalıştığını gösteren ekran görüntüleri eklemenizi öneririz. Teklifinizin nasıl çalıştığını gösteren aşağıdaki gereksinimlere sahip en fazla beş ekran görüntüsü ekleyebilirsiniz:
  - 1280 x 720 piksel
  - . png dosyası
  - Bir resim yazısı içermelidir
- **Medya – videolar** (isteğe bağlı): aşağıdaki gereksinimlere sahip en fazla beş video ekleyebilirsiniz, bu da teklifinizi gösterir:
  - Name
  - URL: yalnızca YouTube veya Vimeo 'da barındırılmalıdır.
  - Küçük resim: 1280 x 720. png dosyası

> [!NOTE]
> Teklifinizin ticari Market 'te yayımlanacak genel [ticari Market sertifika ilkelerini](/legal/marketplace/certification-policies#100-general.md) karşılaması gerekir.

## <a name="preview-audience"></a>İzleyiciyi Önizle

Önizleme hedef kitlesi, canlı yayımlamadan önce uçtan uca işlevselliği test etmek için çevrimiçi mağazalarda canlı olarak yayınlanmadan önce teklifinizin erişimine açabilir.

> [!NOTE]
> Önizleme hedef kitlesi özel bir plandan farklıdır. Özel bir plan yalnızca seçtiğiniz belirli bir hedef kitle için kullanılabilir hale getirir. Bu, belirli müşterilerle özel bir plana anlaşma yapmanızı sağlar.

Azure abonelik kimliklerini kullanarak önizleme kitleyi, her biri için isteğe bağlı bir açıklama ile tanımlarsınız. Bu alanlardan hiçbiri müşteriler tarafından görülenebilir.

## <a name="technical-configuration"></a>Teknik yapılandırma

[Market ölçüm hizmeti API 'lerini](partner-center-portal/marketplace-metering-service-apis.md)kullanarak ölçüm olaylarını yayan yönetilen uygulamalar için, hizmetinizin ölçüm olaylarını yayırken kullanacağı kimliği sağlamanız gerekir.

[Toplu kullanım olayını](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)kullanmak istiyorsanız bu yapılandırma gereklidir. [Kullanım olayı](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)göndermek Isterseniz, [JSON Web belirteci (JWT) taşıyıcı belirtecini](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)almak için [örnek meta verileri hizmetini](../active-directory/managed-identities-azure-resources/overview.md) de kullanabilirsiniz.

- **Azure Active Directory KIRACı kimliği** (gerekli): Azure Portal içinde, iki hizmetimiz arasındaki bağlantının kimliği doğrulanmış bir iletişimin arkasında olduğunu doğrulayabilmemiz için [BIR Azure Active Directory (ad) uygulaması oluşturmanız](../active-directory/develop/howto-create-service-principal-portal.md) gerekir. Azure Active Directory (Azure AD) uygulamanızın [KIRACı kimliğini](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Azure Active Directory [uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dikey penceresine bulmak için. **Görünen ad** sütununda, uygulamayı seçin. Ardından **Özellikler**' e ve ardından **Dizin (kiracı) kimliği** için (örneğin `50c464d3-4930-494c-963c-1e951d15360e` ) bakın.
- **Azure Active Directory Uygulama kimliği** (gerekli): Ayrıca, [Uygulama Kimliğiniz](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) ve bir kimlik doğrulama anahtarınız olması gerekir. Uygulama KIMLIĞINIZI bulmak için Azure Active Directory olduğunuz [uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dikey penceresine gidin. **Görünen ad** sütununda, uygulamayı seçin ve ardından **uygulama (istemci) kimliğini** (örneğin `50c464d3-4930-494c-963c-1e951d15360e` ) arayın. Kimlik doğrulama anahtarını bulmak için **Ayarlar** ' a gidin ve **anahtarlar**' ı seçin. Bir açıklama ve süre sağlamanız gerekir ve ardından bir sayı değeri sağlanacaktır.

> [!NOTE]
> Azure uygulama KIMLIĞI, yayımcı KIMLIĞINIZLE ilişkilendirilecektir ve bu yayımcı hesabında yalnızca yeniden kullanılabilir.

## <a name="additional-sales-opportunities"></a>Ek satış fırsatları

Microsoft tarafından desteklenen pazarlama ve satış kanallarını kabul edebilirsiniz. Iş Ortağı Merkezi 'nde teklifinizi oluştururken işlemin sonuna doğru iki sekme görürsünüz:

- **CSP 'ler aracılığıyla yeniden Satım**: Microsoft bulut çözüm SAĞLAYıCıLARıNıN (CSP) iş ortaklarının, çözümünüzü bir paketlenmiş teklifin parçası olarak yeniden satmasına izin vermek için bu seçeneği kullanın. Daha fazla bilgi için bkz. [bulut çözümü sağlayıcısı programı](./cloud-solution-providers.md) .
- **Microsoft Ile ortak satış**: Bu seçenek, Microsoft satış ekiplerinin, müşterilerinin IHTIYAÇLARıNı değerlendirirken IP ortak satış için uygun çözümünüzü kabul etmenize olanak tanır. Teklifinizi değerlendirme için hazırlama hakkında ayrıntılı bilgi için, bkz. [ticari Market 'Teki ortak satış seçeneği](commercial-marketplace-co-sell.md). IP ortak satışı gereksinimleriyle ilgili ayrıntılar için bkz. [ortak satış durumu gereksinimleri](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için bkz. [Cloud Solution Providers](cloud-solution-providers.md).

Daha fazla bilgi edinmek için bkz. [Azure Marketi ile bulut Işletmenizi büyütme](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Planlar

Azure Uygulama teklifleri için en az bir plan gereklidir. Bir plan, çözüm kapsamını ve sınırlarını ve varsa ilişkili fiyatlandırmayı tanımlar. Müşterilerinize farklı teknik ve fiyatlandırma seçenekleri sunmak için teklifiniz için birden çok plan oluşturabilirsiniz.

Fiyatlandırma modelleri ve özel planlar dahil olmak üzere planlar hakkında genel rehberlik için bkz. [ticari Market teklifleri Için planlar ve fiyatlandırma](plans-pricing.md). Aşağıdaki bölümlerde, Azure Uygulama planlarına özgü ek bilgiler ele alınmaktadır.

### <a name="types-of-plans"></a>Plan türleri

İki tür Azure uygulama planı vardır: _çözüm şablonu_ ve _yönetilen uygulama_. Her iki plan türü, tek bir sanal makinenin (VM) ötesinde bir çözümün dağıtımını ve yapılandırılmasını otomatik hale getirmeye yönelik destek. IaaS çözümleri gibi karmaşık çözümler sağlamak üzere VM 'Ler, ağ oluşturma ve depolama kaynakları dahil olmak üzere birden çok kaynak sağlama sürecini otomatikleştirebiliriz. Her iki plan türü de dahil olmak üzere, VM 'Lerle sınırlı olmamak üzere birçok farklı türde Azure kaynağı kullanabilir.

- **Çözüm şablonu** planları, ticari Market 'te çözüm yayımlamanın ana yollarından biridir. Çözüm şablonu planları ticari Market 'te transactable değildir, ancak ticari Market aracılığıyla faturalandırılan ücretli VM tekliflerini dağıtmak için kullanılabilirler. Müşteri çözümü yöneteceği ve işlemler başka bir plan üzerinden faturalandırılırken çözüm şablonu plan türünü kullanın. Çözüm şablonları oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager nedir?](../azure-resource-manager/management/overview.md)
- **Yönetilen uygulama** planları, müşterileriniz için tam olarak yönetilen, anahtar uygulamaları kolayca oluşturup sunmanıza olanak tanır. Çözüm şablonu planlarıyla aynı yeteneklere sahiptir ve bazı önemli farklılıklar vardır:
    - Kaynaklar bir kaynak grubuna dağıtılır ve uygulamanın yayımcısı tarafından yönetilir. Kaynak grubu, tüketicinin aboneliğinde mevcuttur ancak yayımcının kiracısındaki bir kimlik, kaynak grubuna erişime sahiptir. 
    - Yayımcı olarak, çözümün sürekli desteğinin maliyetini belirtirsiniz ve işlemler ticari Market aracılığıyla desteklenir.
 
    Siz veya müşteriniz çözümün bir iş ortağı tarafından yönetilmesini gerektirdiğinde veya abonelik tabanlı bir çözüm dağıtacaksanız, yönetilen uygulama planı türünü kullanın. Yönetilen uygulamaların avantajları ve türleri hakkında daha fazla bilgi için bkz. [Azure yönetilen uygulamalara genel bakış](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- Bir çözüm şablonu planlamak için bkz. [Azure Uygulama teklifi için çözüm şablonu planlayın](plan-azure-app-solution-template.md).
- Azure yönetilen uygulamasını planlamak için bkz. Azure [Uygulama teklifi Için Azure yönetilen uygulaması planlayın](plan-azure-app-managed-app.md).