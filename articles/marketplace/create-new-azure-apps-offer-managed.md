---
title: Yönetilen uygulama planını yapılandırma
description: Iş Ortağı Merkezi 'nde Azure Uygulama teklifiniz için yönetilen bir uygulama planı yapılandırmayı öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744644"
---
# <a name="configure-a-managed-application-plan"></a>Yönetilen uygulama planını yapılandırma

Bu makale yalnızca bir Azure Uygulama teklifi için yönetilen uygulama planları için geçerlidir. Bir çözüm şablonu planı yapılandırıyorsanız, [çözüm şablonu planı yapılandırma](create-new-azure-apps-offer-solution.md)' ya gidin.

## <a name="define-markets-pricing-and-availability"></a>Pazarlar, fiyatlandırma ve kullanılabilirliği tanımlama

Her plan en az bir pazarda kullanılabilir olmalıdır. **Fiyatlandırma ve kullanılabilirlik** sekmesinde, bu planın kullanılabileceği, Fiyat ve planı herkese açık ya da yalnızca belirli müşterilere (özel bir plan olarak da anılır) görünür hale getirme gibi bir şekilde yapılandırabilirsiniz.

1. **Pazarlar**' ın altında, **pazarlar Düzenle** bağlantısını seçin.
1. Görüntülenen iletişim kutusunda, planınızı kullanılabilir hale getirmek istediğiniz Pazar konumlarını seçin. En az bir adet 141 pazarı seçmeniz gerekir.

    > [!NOTE]
    > Bu iletişim kutusu, Microsoft remits Sales ve sizin adınıza vergi üzerinden yalnızca "vergi havalesi" ülkelerine filtre uygulamak için bir arama kutusu ve bir seçenek içerir.

1. İletişim kutusunu kapatmak için **Kaydet**' i seçin.

## <a name="define-pricing"></a>Fiyatlandırma tanımla

**Fiyat** kutusunda, bu plan için aylık fiyat fiyatını belirtin. Bu fiyat, bu çözüm tarafından dağıtılan kaynaklar tarafından tahakkuk eden tüm Azure altyapısına veya kullanım tabanlı maliyetlere ek olarak yapılır.

Aylık fiyata ek olarak, [ölçülen faturalandırmayı](partner-center-portal/azure-app-metered-billing.md)kullanarak standart olmayan birimlerin tüketimine yönelik fiyatlar da ayarlayabilirsiniz. Aylık fiyat fiyatını sıfır olarak ayarlayabilir ve isterseniz tarifeli faturalandırma kullanarak özel olarak ücretlendirme yapabilirsiniz.

Fiyatlar ABD Doları cinsinden ayarlanır (USD = Birleşik Devletler dolar), kaydedildiğinde geçerli döviz kurları kullanılarak tüm seçili piyasaların yerel para birimine dönüştürülür. Fiyatlandırma elektronik tablosunu dışarı aktarıp her bir pazardaki fiyatı inceleyerek yayımlamadan önce bu fiyatları doğrulayın. Tek bir pazarda özel fiyatlar ayarlamak isterseniz, fiyatlandırma elektronik tablosunu değiştirin ve içeri aktarın.

### <a name="add-a-custom-meter-dimension-optional"></a>Özel ölçüm boyutu Ekle (isteğe bağlı)

1. **Market ölçüm hizmeti boyutları** altında **özel ölçüm boyutu Ekle (en fazla 18)** bağlantısını seçin.
1. **Kimlik** kutusuna, kullanım olaylarını yayırken sabit tanımlayıcı başvurusunu girin.
1. **Görünen ad** kutusuna boyutla ilişkili görünen adı girin. Örneğin, "gönderilen metin iletileri".
1. **Ölçü birimi** kutusuna faturalandırma biriminin açıklamasını girin. Örneğin, "metin başına ileti" veya "100 başına e-posta".
1. **USD cinsinden birim başına fiyat** kutusunda, boyutun bir biriminin fiyatını girin.
1. Temel kutuya **dahil edilen aylık miktar** ' da, yinelenen aylık ücreti ödeyen müşteriler için her ay dahil edilen boyutun miktarını (tamsayı olarak) girin. Sınırsız bir miktar ayarlamak için bunun yerine onay kutusunu işaretleyin.
1. Başka bir özel ölçüm boyutu eklemek için 1 ile 7 arasındaki adımları yineleyin.

### <a name="set-custom-prices-optional"></a>Özel fiyatlar ayarlama (isteğe bağlı)

ABD Doları cinsinden ayarlanan fiyatlar (USD = Birleşik Devletler dolar), kaydedildiği sırada geçerli döviz kurları kullanılarak tüm seçili piyasaların yerel para birimine dönüştürülür. Fiyatlandırma elektronik tablosunu dışarı aktarıp her bir pazardaki fiyatı inceleyerek yayımlamadan önce bu fiyatları doğrulayın. Tek bir pazarda özel fiyatlar ayarlamak isterseniz, fiyatlandırma elektronik tablosunu değiştirin ve içeri aktarın.

Bir plan yayımlandıktan sonra nelerin değiştirebilecekleri hakkında bazı kısıtlamalar olduğundan, yayımlamadan önce fiyatlarınızı dikkatlice gözden geçirin.

> [!NOTE]
> Planınızdaki bir pazar bedeli yayımlandıktan sonra, daha sonra değiştirilemez.

Tek bir pazarda özel fiyatlar ayarlamak için, fiyatlandırma elektronik tablosunu dışarı aktarın, değiştirin ve içeri aktarın. Bu fiyatlandırmayı doğrulamadan ve bu ayarların sahibi olduğunuzdan siz sorumlusunuz. Ayrıntılı bilgi için bkz. [özel fiyatlar](plans-pricing.md#custom-prices).

1. Fiyatlandırma verilerinin dışarı aktarılmasını sağlamak için öncelikle fiyatlandırma değişikliklerinizi kaydetmeniz gerekir. **Fiyatlandırma ve kullanılabilirlik** sekmesinin altındaki **Taslağı kaydet**' i seçin.
1. **Fiyatlandırma** bölümünde **fiyatlandırma verilerini dışarı aktar** bağlantısını seçin.
1. exportedPrice.xlsx dosyasını Microsoft Excel 'de açın.
1. Elektronik tabloda, istediğiniz güncelleştirmeleri Fiyatlandırma bilgileriniz yapın ve ardından dosyayı kaydedin.

   Dosyayı güncelleştirebilmeniz için önce Excel 'de düzenlemesini etkinleştirmeniz gerekebilir.

1. Fiyatlandırma **ve kullanılabilirlik** sekmesindeki **fiyatlandırma** bölümünde **fiyatlandırma verilerini içeri aktar** bağlantısını seçin.
1. Görüntülenen iletişim kutusunda **Evet**' e tıklayın.
1. Güncelleştirdiğiniz exportedPrice.xlsx dosyasını seçin ve ardından **Aç**' a tıklayın.

## <a name="choose-who-can-see-your-plan"></a>Planınızı kimlerin görebileceğini seçin

Her planı herkese veya yalnızca belirli bir hedef kitleye görünür olacak şekilde yapılandırabilirsiniz. Atadığınız her abonelik KIMLIĞININ açıklamasını ekleme seçeneğiyle Azure abonelik kimliklerini kullanarak özel bir hedef kitleye erişim izni verirsiniz. Bir kullanarak en fazla 10 abonelik kimliği el ile veya 10.000 abonelik kimliği ekleyebilirsiniz. CSV dosyası. Azure abonelik kimlikleri, GUID 'Ler olarak temsil edilir ve harflerin küçük harf olması gerekir.

> [!NOTE]
> Özel bir plan yayımlarsanız, görünürlüğünü daha sonra herkese açık olarak değiştirebilirsiniz. Ancak, bir genel planı yayımladıktan sonra görünürlüğünü özel olarak değiştiremezsiniz.

**Plan görünürlüğü** altında aşağıdakilerden birini yapın:

- Planı genel hale getirmek için **ortak** seçenek düğmesini ( _radyo düğmesi_ olarak da bilinir) seçin.
- Planı özel hale getirmek için **özel** seçenek düğmesini seçin ve ardından Azure abonelik kimliklerini el ile veya bir CSV dosyası ile ekleyin.

> [!NOTE]
> Özel veya kısıtlanmış bir hedef kitle, **Önizleme** sekmesinde tanımladığınız önizleme izleyicilerinizden farklıdır. Bir önizleme hedef kitlesi, Market 'te canlı olarak yayımlanmadan önce teklifinizin erişimine açabilir. Özel hedef kitle seçimi yalnızca belirli bir plana uygulansa da, önizleme hedef kitlesi doğrulama amacıyla tüm planları (özel veya değil) görüntüleyebilir.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Özel bir plana yönelik olarak Azure abonelik kimliklerini el ile ekleme

1. **Plan görünürlüğü** altında **özel** seçenek düğmesini seçin.
1. Görüntülenen **Azure ABONELIK kimliği** kutusunda, bu özel plana erişim vermek istediğiniz hedef kitlesinin Azure abonelik kimliğini girin. En az bir abonelik KIMLIĞI gereklidir.
1. Seçim **Açıklama** kutusuna bu hedef kitle için bir açıklama girin.
1. Başka bir abonelik KIMLIĞI eklemek için, **Kimlik Ekle (en fazla 10)** bağlantısını seçin ve 2. ve 3. adımları yineleyin.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Bir kullanın. Özel bir plan için Azure abonelik kimlikleri eklemek üzere CSV dosyası

1. **Plan görünürlüğü** altında **özel** seçenek düğmesini seçin.
1. **Izleyiciyi dışarı aktar (CSV)** bağlantısını seçin.
1. Öğesini açın. CSV dosyası ve özel teklifin **kimlik** sütununa erişim izni vermek istediğiniz Azure abonelik kimliklerini ekleyin.
1. İsteğe bağlı olarak, **Açıklama** sütunundaki her bir hedef kitle için bir açıklama girin.
1. Abonelik KIMLIĞI olan her satır için **tür** sütununa "SubscriptionID" ekleyin.
1. Öğesini kaydedin. CSV dosyası.
1. **Kullanılabilirlik** sekmesinde, **plan görünürlüğü** altında, **izleyiciyi içeri aktar (CSV)** bağlantısını seçin.
1. Görüntülenen iletişim kutusunda **Evet**' i seçin.
1. Öğesini seçin. CSV dosyası ve sonra **Aç**' ı seçin. Olduğunu belirten bir ileti görüntülenir. CSV dosyası başarıyla içeri aktarıldı.

## <a name="define-the-technical-configuration"></a>Teknik yapılandırmayı tanımlama

**Teknik yapılandırma** sekmesinde, müşterilerin planınızı dağıtmasına ve paket için bir sürüm numarası sağlamasına imkan tanıyan dağıtım paketini karşıya yüklersiniz. Diğer teknik bilgileri de sağlarsınız.

> [!NOTE]
> Bu sekme, **plan kurulumu** sekmesinde başka bir plandan paketleri yeniden kullanmayı seçerseniz görünür olmayacaktır. Varsa, [planlarınızı görüntüleyin](#view-your-plans)bölümüne gidin.

### <a name="assign-a-version-number-for-the-package"></a>Paket için sürüm numarası atama

**Sürüm** kutusunda, teknik yapılandırmanın güncel sürümünü sağlayın. Bu sayfada her değişiklik yayımlaışınızda bu sürümü artırın. Sürüm numarası şu biçimde olmalıdır: Integer. Integer. Integer. Örneğin, `1.0.2`.

### <a name="upload-a-package-file"></a>Paket dosyasını karşıya yükle

**Paket dosyası (. zip)** altında, paket dosyanızı gri kutuya sürükleyin veya **Dosya bağlantısına gidin** bağlantısını seçin.

> [!NOTE]
> Dosya yükleme sorununuz varsa, yerel ağınızın `https://upload.xboxlive.com` Iş Ortağı Merkezi tarafından kullanılan hizmeti engellemediğinden emin olun.

#### <a name="previously-published-packages"></a>Önceden yayımlanmış paketler

**Daha önce yayınlanan paketler** alt sekmesi, teknik yapılandırmanızın tüm yayınlanan sürümlerini görüntülemenize olanak sağlar.

### <a name="enable-just-in-time-jit-access-optional"></a>Tam zamanında (JıT) erişimi etkinleştir (isteğe bağlı)

Bu plan için JıT erişimini etkinleştirmek üzere **tam zamanında (JIT) erişimi etkinleştir** onay kutusunu seçin. Yönetilen uygulamanızın tüketicilerinin hesabınıza kalıcı erişim vermesini gerektirmek için, bu seçeneği işaretlenmemiş olarak bırakın. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [tam zamanında (JIT) erişim](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Dağıtım modu seçin

**Tamamlanmış** veya **artımlı** Dağıtım modunu seçin.

- **Tüm** modda, kaynak [üzerindemainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)tanımlanmamışsa, uygulamanın müşteri tarafından yeniden dağıtımı, yönetilen kaynak grubundaki kaynakların kaldırılmasına neden olur.
- **Artımlı** modda, uygulamanın yeniden dağıtımı mevcut kaynakları değişmeden bırakır.

Dağıtım modları hakkında daha fazla bilgi için bkz. [Azure Resource Manager Dağıtım modları](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Bildirim uç noktası URL 'SI sağlayın

**Bildirim uç noktası URL 'si** kutusunda, bu plan sürümünün yönetilen uygulama örneklerinde bulunan tüm CRUD işlemleri hakkında bildirim almak IÇIN bir HTTPS Web kancası uç noktası belirtin.

### <a name="customize-allowed-customer-actions-optional"></a>İzin verilen müşteri eylemlerini özelleştirme (isteğe bağlı)

1. Müşterilerin varsayılan olarak kullanılabilir olan "" eylemlerine ek olarak yönetilen kaynaklarda gerçekleştirebileceği eylemleri belirtmek için `*/read` **izin verilen müşteri eylemlerini Özelleştir** kutusunu seçin.
1. Görüntülenen kutulara, müşterinizin gerçekleştirmesini sağlamak istediğiniz ek denetim eylemlerini ve izin verilen veri eylemlerini, noktalı virgülle ayırarak girin. Örneğin, tüketicilerin sanal makineleri yeniden başlatmasına izin vermek için `Microsoft.Compute/virtualMachines/restart/action` **izin verilen denetim eylemleri** kutusuna ekleyin.

### <a name="choose-who-can-manage-the-application"></a>Uygulamayı kimin yönetebileceğini seçin

Seçili her Azure bölgesinde bu yönetilen uygulamaya yönetim erişiminin olması gereken kişileri belirtin: _küresel Azure_ ve _Azure Kamu Bulutu_. Yönetilen kaynak grubuna izin vermek istediğiniz kullanıcıları, grupları veya uygulamaları belirlemek için Azure AD kimliklerini kullanacaksınız. Daha fazla bilgi için bkz. Azure [Uygulama teklifi Için Azure yönetilen uygulaması planlayın](plan-azure-application-offer.md).

Genel Azure ve Azure Kamu Bulutu için geçerli olan aşağıdaki adımları izleyin.

1. **Azure Active Directory KIRACı kimliği** kutusuna, izin vermek istediğiniz kullanıcıların, grupların veya uygulamaların kimliklerini IÇEREN Azure AD Kiracı kimliğini (dizin kimliği olarak da bilinir) girin.
1. **Asıl kimlik** kutusunda, yönetilen kaynak grubu için izin verilmesini istediğiniz kullanıcı, Grup veya UYGULAMANıN Azure AD nesne kimliğini belirtin. Kullanıcıyı, Azure portal [Azure Active Directory Kullanıcıları dikey penceresinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) bulunan birincil kimliğine göre belirler.
1. **Rol tanımı** listesinden BIR Azure AD yerleşik rolü seçin. Seçtiğiniz rol, sorumlunun müşteri aboneliğindeki kaynaklarda sahip olacağı izinleri açıklar.
1. Başka bir yetkilendirme eklemek için, **Yetkilendirme Ekle (en fazla 100)** bağlantısını seçin ve 1 ile 3 arasındaki adımları yineleyin.

### <a name="policy-settings-optional"></a>İlke ayarları (isteğe bağlı)

En fazla beş ilke ve her Ilke seçeneğinin yalnızca bir örneğini yapılandırabilirsiniz. Bazı ilkeler ek parametreler gerektirir.

1. **İlke ayarları** altında **+ ilke Ekle (en fazla 5)** bağlantısını seçin.
1. **Ad** kutusuna ilke atama adını (50 karakterle sınırlı) girin.
1. **İlkeler** liste kutusundan, müşteri aboneliğinde yönetilen uygulama tarafından oluşturulan kaynaklara uygulanacak Azure ilkesini seçin.
1. **İlke parametreleri** kutusunda, denetim ve Tanılama ayarları ilkelerinin uygulanacağı parametreyi belirtin.
1. **Ilke SKU** 'su liste kutusundan ilke SKU türünü seçin.

    > [!NOTE]
    > Denetim ilkeleri için _Standart ilke_ SKU 'su gereklidir.

## <a name="view-your-plans"></a>Planlarınızı görüntüleyin

- **Taslağı kaydet**' i seçin ve sayfanın sol üst **kısmında plana genel** bakış ' ı seçerek **plana genel bakış** sayfasına dönün.

Bir veya daha fazla plan oluşturduktan sonra plan adı, plan KIMLIĞI, plan türü, kullanılabilirliği (genel veya özel), geçerli yayımlama durumu ve **plana genel bakış** sekmesinde tüm kullanılabilir eylemler görüntülenir.

**Plana genel bakış** sekmesinin **eylem** sütununda bulunan Eylemler planınızın durumuna göre farklılık gösterir ve şunları içerebilir:

- Plan durumu **taslak** Ise, **eylem** sütunundaki bağlantı **taslak Sil**' i söylecektir.
- Plan durumu **canlı** Ise, **eylem** sütunundaki bağlantı, **planı satmaya** veya **özel izleyiciyi eşitlemeye** yönelik olur. **Eşitleme özel hedef kitle** bağlantısı, yalnızca özel izleyicilerinizdeki değişiklikleri, teklifte yapmış olabileceğiniz herhangi bir güncelleştirmeyi yayımlamadan yayımlar.
- Bu teklif için başka bir plan oluşturmak üzere **plana genel bakış** sekmesinin en üstünde **+ Yeni plan oluştur**' u seçin. Ardından [Azure Uygulama teklifiniz için plan oluşturma](create-new-azure-apps-offer-plans.md)bölümündeki adımları yineleyin. Aksi takdirde, planlar oluşturmayı bitirdiğinizde sonraki bölüme gidin: sonraki adımlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure uygulama teklifinizi test etme ve yayımlama](create-new-azure-apps-offer-test-publish.md).
- [Azure uygulama teklifinizi](create-new-azure-apps-offer-marketing.md) Microsoft ile ortak satış aracılığıyla satma ve CSP programlarını kullanarak yeniden satma hakkında bilgi edinin.