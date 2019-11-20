---
title: include dosyası
description: include dosyası
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 831e3330126d82795cece128c28cb96b7199d69a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825577"
---
**Test sürücüsü** sekmesi, müşterilerin satın alma işlemine geçmeden önce teklifinizi denemesini sağlayacak bir tanıtım (veya "test sürücüsü") ayarlamanıza olanak sağlar. [Test sürücüsü nedir?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)makalesinde daha fazla bilgi edinin. Teklifiniz için artık bir test sürücüsü sağlamak istemiyorsanız, **teklif kurulumu** sayfasına dönün ve **Test sürücüsünü etkinleştir**' i işaretleyin.

### <a name="technical-configuration"></a>Teknik yapılandırma
Aşağıdaki test sürücüsü türleri, her biri kendi teknik yapılandırma gereksinimlerine sahip kullanılabilir.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Mantıksal uygulama](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Teknik yapılandırma gerekli değil)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager test sürücüsü için teknik yapılandırma

Çözümünüzü oluşturan tüm Azure kaynaklarını içeren bir dağıtım şablonu. Bu senaryoya uyan ürünler yalnızca Azure kaynaklarını kullanır. [Azure Resource Manager test sürücüsü](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)ayarlama hakkında daha fazla bilgi edinin.

- **Bölgeler** (gerekli): Şu anda, test sürücünüzün kullanılabilir hale getirilbileceği 26 Azure tarafından desteklenen bölge vardır. Genellikle, en iyi performans için en yakın bölgeyi seçebilmeniz için, en fazla sayıda müşteriyi tahmin ettiğiniz bölgelerde test sürücünüzü kullanılabilir hale getirmek isteyeceksiniz. Aboneliğinizin seçtiğiniz her bölgede gereken tüm kaynakları dağıtmasına izin verildiğinden emin olmanız gerekir.

- **Örnekler**: bir türü (sıcak veya soğuk) ve kullanılabilir örneklerin sayısını seçin; Bu değer teklifinizin kullanılabildiği bölge sayısıyla çarpılacak.

**Sık**erişimli: Bu örnek türü dağıtılır ve seçilen bölgeye göre erişim bekleniyor. Müşteriler, bir dağıtımı beklemek yerine bir test sürücüsünün *sık* erişimli örneklerine anında erişebilir. Zorunluluğunu getirir, bu örneklerin her zaman Azure aboneliğinizde çalışıyor olması, bu nedenle daha büyük bir çalışma süresi maliyeti doğuracaktır. En az bir *sıcak* örnek olması önemle önerilir, çünkü çoğu müşteri tam dağıtımları beklemek Istemelidir ve *etkin* örnek yoksa, müşteri kullanımındaki bir bırakmaya neden olur.

**Soğuk**: Bu örnek türü, bölge başına büyük olasılıkla dağıtılabilecek örneklerin toplam sayısını temsil eder. Soğuk örnekler, bir müşteri test sürücüsünü istediğinde tüm test sürücüsü Kaynak Yöneticisi şablonun dağıtılmasını gerektirir, böylece *soğuk* örnekler, *etkin* örneklere göre yüklenmeye çok daha yavaştır. Zorunluluğunu getirir, yalnızca test sürücüsünün süresi için ödeme yapmanız gereken *bir deyişle, Azure aboneliğinizde her zaman* bir *sıcak* örnekle birlikte çalışmıyor.

- **Test sürücüsü Azure Resource Manager şablonu**: Azure Resource Manager şablonunuzu içeren. zip ' i yükleyin.  Hızlı başlangıç makalesinde Azure Resource Manager şablonu oluşturma hakkında daha fazla bilgi edinin [Azure Portal kullanarak Azure Resource Manager şablonları oluşturun ve dağıtın](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Sınama sürücüsü süresi** (zorunlu): test sürücüsünün etkin kalacağı sürenin uzunluğunu saat cinsinden belirtin. Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer. Bu süre yalnızca birkaç saat (örn. "2" saat, "1,5" geçerli değildir) ile ayarlanabilir.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 test sürücüsü için teknik yapılandırma

Microsoft, bu test sürücüsü türünü kullanarak hizmet sağlamayı ve dağıtımını barındırarak ve tutarak bir test sürücüsü ayarlamanın karmaşıklığını ortadan kaldırabilir. Bu barındırılan test sürücüsü türünün yapılandırması, test sürücüsünün bir Iş Merkezi, müşteri katılımı veya Işlem kitlesini hedeflediğinden bağımsız olarak aynıdır.

- **En fazla eşzamanlı test sürücüsü** (gerekli): test sürücünüzü tek seferde kullanabilen en fazla müşteri sayısını ayarlayın. Her eşzamanlı kullanıcı, test sürücüsü etkinken bir Dynamics 365 lisansını kullanır, bu nedenle maksimum sınır kümesini destekleyecek yeterli lisansa sahip olduğunuzdan emin olmanız gerekir. Önerilen 3-5 değeri.

- **Sınama sürücüsü süresi** (zorunlu): saat sayısını tanımlayarak test sürücüsünün etkin kalacağı sürenin uzunluğunu girin. Bu süre dolduktan sonra, oturum sona erdir ve artık lisanslarınızdan birini tüketmez. Teklifinizin karmaşıklığına bağlı olarak 2-24 saat arasında bir değer öneririz. Bu süre yalnızca birkaç saat (örn. "2" saat, "1,5" geçerli değildir) ile ayarlanabilir.  Kullanıcı zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde yeni bir oturum isteyebilir.

- **Örnek URL** (gerekli): müşterinin test sürücüsüne başlayacağı URL. Genellikle uygulamanızı örnek verilerle çalıştıran Dynamics 365 örneğinizin URL 'SI (ör. https://testdrive.crm.dynamics.com).

- **Örnek Web API URL 'si** (gerekli): Microsoft 365 hesabınızda oturum açarak ve **Ayarlar** ' a giderek, Dynamics 365 ÖRNEĞINIZ IÇIN Web apı URL 'sini alın \&gt; **Özelleştirme** \&gt; **Geliştirici kaynakları** \&gt; **Örnek Web API 'si (hizmet kök URL 'si)** , burada bulunan URL 'yi kopyalayın (ör. https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Rol adı** (gerekli): özel Dynamics 365 test sürücünüzde tanımladığınız güvenlik rolü adını sağlayın. Bu, kullanıcıya test sürücüleri sırasında atanır (örn. Test-Drive-Role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Mantıksal uygulama sınama sürücüsü için teknik yapılandırma

Herhangi bir özel ürün, çeşitli karmaşık çözüm mimarilerini kapsayan bu tür test sürücüsü Dağıtım şablonunu kullanmalıdır. Mantıksal uygulama test sürücüleri ayarlama hakkında daha fazla bilgi için bkz. GitHub üzerinde [işlemler](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) ve [müşteri katılımı](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Bölge** (gerekli, tek seçimli açılan liste): Şu anda, test sürücünüzün kullanılabilir hale getirilbileceği 26 Azure tarafından desteklenen bölge vardır. Mantıksal uygulamanızın kaynakları seçtiğiniz bölgede dağıtılır. Mantıksal uygulamanızda belirli bir bölgede depolanan özel kaynaklar varsa bölgenin burada seçildiğinden emin olun. Bunu yapmanın en iyi yolu, mantıksal uygulamanızı portalda Azure aboneliğinizde yerel olarak dağıtmaktır ve bu seçimi yapmadan önce doğru çalıştığını doğrular.

- **En fazla eşzamanlı test sürücüsü** (gerekli): test sürücünüzü tek seferde kullanabilen en fazla müşteri sayısını ayarlayın. Bu test sürücüleri zaten dağıtılır ve müşterilerin bir dağıtımı beklemeden bunlara anında erişmelerini sağlar.

- **Sınama sürücüsü süresi** (zorunlu): test sürücüsünün etkin kalacağı sürenin uzunluğunu saat cinsinden belirtin. Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer.

- **Azure Kaynak grubu adı** (gerekli): mantıksal uygulama test sürücünüzün kaydedildiği [Azure Kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) adını girin.

- **Azure mantıksal uygulama adı** (gerekli): kullanıcıya test sürücüsünü atayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

- **Mantıksal uygulama adı** (gerekli) sağlamasını kaldırma: müşteri bittikten sonra test sürücüsünü sağlayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI test sürücüleri için teknik yapılandırma gerekli değildir

Etkileşimli bir Power BI görseli göstermek isteyen ürünler, özel olarak oluşturulmuş bir panoyu test sürücüleri olarak paylaşmak için, daha fazla teknik yapılandırma gerekmeden gömülü bir bağlantı kullanabilir. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) şablonu uygulamalarını ayarlama hakkında daha fazla bilgi edinin.

### <a name="deployment-subscription-details"></a>Dağıtım aboneliği ayrıntıları

Test sürücüsünü sizin adınıza dağıtmak için lütfen benzersiz ve benzersiz bir Azure aboneliği oluşturun ve sağlayın. (Power BI test sürücüleri için gerekli değildir).

- **Azure ABONELIK kimliği** (Azure Resource Manager ve Logic Apps için gereklidir): Azure hesap hizmetlerinizi kaynak kullanımı raporlama ve faturalama için erişim izni vermek üzere abonelik kimliğini girin. Henüz bir tane yoksa, test sürücüleri için kullanmak üzere [ayrı bir Azure aboneliği oluşturmayı](https://docs.microsoft.com/azure/billing/billing-create-subscription) düşünmeniz önerilir. [Azure Portal](https://portal.azure.com/) oturum açarak ve sol taraftaki menüdeki **abonelikler** SEKMESINE giderek Azure abonelik kimliğinizi bulabilirsiniz. Sekmeyi seçtiğinizde, abonelik KIMLIĞINIZ görüntülenir (örneğin, "a83645ac-1234-5AB6-345-1h234g764ghty").

- **Azure AD KIRACı kimliği** (gerekli): Azure ACTIVE DIRECTORY (ad) [kiracı kimliğinizi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)girin. Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol taraftaki menüden Active Directory sekmesini seçin, **Özellikler** ' i seçin, sonra listelenen **dizin kimliği** numarasını (ör. 50c464d3-4930-494c-963c-1e951d15360e) arayın. Kuruluşunuzun kiracı KIMLIĞINI Ayrıca, etki alanı adı URL 'nizi kullanarak da arayabilirsiniz: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure AD kiracı adı** (dinamik 365 için gereklidir): Azure ACTIVE DIRECTORY (ad) adınızı girin. Bu adı bulmak için sağ üst köşedeki [Azure Portal](https://portal.azure.com/)oturum açın, kiracı adınız hesap adınızın altında listelenecektir.

- **Azure AD uygulama kimliği** (gerekli): Azure ACTIVE DIRECTORY (ad) [uygulama kimliğinizi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)girin. Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol menüden Active Directory sekmesini seçin, **uygulama kayıtları**' yı seçin, ardından listelenen **uygulama kimliği** numarasını (ör. 50c464d3-4930-494c-963c-1e951d15360e) arayın.

- **Azure AD uygulama istemci parolası** (gerekli): Azure AD uygulama [Istemci gizli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)anahtarını girin. Bu değeri bulmak için [Azure Portal](https://portal.azure.com/)oturum açın. Sol menüdeki **Azure Active Directory** sekmesini seçin, **uygulama kayıtları**' i seçin ve ardından test sürücüsü uygulamanızı seçin. Sonra **Sertifikalar ve gizlilikler**' ı seçin, **yeni istemci parolası**' nı seçin, bir açıklama girin, **süresi dolmasın**' ı seçin ve ardından **Ekle**' yi Değeri kopyalamadığınızdan emin olun. (Bunu yapmadan önce sayfadan uzaklaşmayın, aksi takdirde değere erişemezsiniz.)

Sonraki bölüme geçmeden önce **kaydetmeyi** unutmayın!

### <a name="test-drive-listings-optional"></a>Sürücü listelerini test etme (isteğe bağlı)

**Test sürücüsü sekmesinde bulunan** **Test sürücü listeleri** seçeneği, test sürücünüzün kullanılabildiği dilleri (ve pazarlar) görüntüler; şu anda İngilizce (Birleşik Devletler) kullanılabilir tek konumdur. Ayrıca, Bu sayfa dile özgü listenin durumunu ve eklendiği tarih/saati görüntüler. Her dil/Pazar için test sürücüsü ayrıntılarını (Açıklama, Kullanıcı el ile, videolar vb.) tanımlamanız gerekir.

- **Açıklama** (gerekli): test sürücünüzü, neyi gösterdiklerinizi, kullanıcının deneme amaçlarını, araştırılacak özellikleri ve kullanıcının teklifinizi elde edip etmeyeceğinizi belirlemesine yardımcı olmak için Ilgili bilgileri tanıtın. Bu alana en fazla 3.000 karakter metin girilebilir. 

- **Erişim bilgileri** (Azure Resource Manager ve mantıksal test sürücüleri için gereklidir): Bu test sürücüsüne erişmek ve kullanmak için müşterinin neleri bilmesi gerektiğini açıklayın. Teklifinizi kullanmaya yönelik bir senaryoya ve müşterinin test sürüşü genelinde özelliklere erişmek için bilmeleri gereken tam olarak ne olduğunu öğrenin. Bu alana en fazla 10.000 karakter metin girilebilir.

- **Kullanıcı el ile** (gerekli): test sürücünüzün deneyiminizin derinlemesine bir yolu. Kullanıcı el kitabı, müşterinin test sürücüsüyle karşılaşmasını istediğiniz şekilde tam olarak kapsamalıdır ve sahip olabileceği sorulara başvuru olarak görev yapar. Dosya, karşıya yüklendikten sonra PDF biçiminde ve adlandırılmalıdır (en fazla 255 karakter) olmalıdır.

- **Videolar: video ekleme** (isteğe bağlı): Videolar YouTube veya Vimeo 'a yüklenebilir ve burada bir bağlantı ve küçük resim görüntüsü (533 x 324 piksel) ile birlikte başvuruluyorsa, müşterinin test sürücüsünü daha iyi anlamasına yardımcı olmak için bilgi Teklifinizin özelliklerini başarıyla kullanma ve avantajlarını vurgulayan senaryoları anlama.
  - **Ad** (gerekli)
  - **URL (yalnızca YouTube veya Vimeo)** (gerekli)
  - **Küçük resim (533 x 324px)** : görüntü dosyası png biçiminde olmalıdır.

Bu alanları tamamladıktan sonra **Kaydet** ' i seçin.
