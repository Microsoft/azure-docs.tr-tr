---
title: Sınama sürücüsü teknik yapılandırması, Microsoft ticari marketi
description: Test sürücüleri hakkında bilgi edinin. Sınama sürücüleri, yeni müşterilerin satın alma işlemine geçmeden önce teklifinizi sürücüyü test edebilmesi için izin verir.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462901"
---
# <a name="test-drive-technical-configuration"></a>Test sürüşü teknik yapılandırması

Microsoft ticari Market 'teki test sürücüsü seçeneği, ürününüzün temel özelliklerine yönelik uygulamalı, kendinden kılavuzlu bir tur yapılandırmanıza olanak tanır. Bir sınama sürücüsüyle yeni müşteriler, satın alma işlemi gerçekleştirmeden önce teklifinizi deneyebilir. Daha fazla bilgi için bkz. [test sürücüsü nedir?](what-is-test-drive.md)

Teklifiniz için artık bir test sürücüsü sağlamak istemiyorsanız **teklif kurulumu** sayfasına dönün ve **Test sürücüsünü etkinleştir** onay kutusunu temizleyin. Tüm teklif türlerinde kullanılabilir bir sınama sürücüsü yok.

## <a name="azure-resource-manager-test-drive"></a>Test sürüşü Azure Resource Manager

Bu, sanal makine veya Azure Uygulama teklifleri için tek sınama sürücüsü seçeneğidir ve ayrıntılı kurulum gerektirir. [Dağıtım aboneliği ayrıntıları](#deployment-subscription-details) ve [test sürücüsü listelerinin](#test-drive-listings)aşağıdaki bölümlerini okuyun ve [Azure Resource Manager test sürücüsü yapılandırması](azure-resource-manager-test-drive.md)için ayrı konuyla devam edin.

## <a name="hosted-test-drive"></a>Barındırılan test sürücüsü

Microsoft, hizmet sağlama ve sağlamayı kaldırma işlemlerini barındırarak ve tutarak bir test sürücüsü ayarlamanın karmaşıklığını ortadan kaldırabilir. Bu tür test sürücüsü için yapılandırma, test sürücüsünün bir Dynamics 365 müşteri katılımı veya Dynamics 365 Işlemler kitlesini hedeflediğinden bağımsız olarak aynıdır.

- **Maksimum eşzamanlı test sürücüleri** (gerekli) – test sürücünüzü tek seferde kullanılabilecek maksimum müşteri sayısını ayarlayın. Her eşzamanlı kullanıcı, test sürücüsü etkinken bir Dynamics 365 lisansını kullanır, bu nedenle maksimum sınır kümesini destekleyecek yeterli lisansa sahip olduğunuzdan emin olun. Önerilen değer 3-5 ' dir.

- **Sınama sürücüsü süresi** (zorunlu) – test sürücüsünün her müşteri için etkin kalacağı saat sayısını girin. Bu dönemden sonra, oturum sona erdirmek ve artık lisanslarınızdan birini tüketmeyecektir. Teklifinizin karmaşıklığına bağlı olarak 2-24 saat arasında bir değer öneririz. Bu süre yalnızca saat olarak ayarlanabilir (örneğin, "2 saat" geçerlidir; "1,5 saat" değil). Kullanıcı zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde yeni bir oturum isteyebilir.

- **Örnek URL** (gerekli) – müşterinin test sürücüsüne başlayacağı URL. Genellikle uygulamanızı örnek verilerle çalıştıran Dynamics 365 örneğinizin URL 'SI (örneğin, `https://testdrive.crm.dynamics.com` ).

- **Örnek Web API URL 'si** (gerekli) – Microsoft 365 hesabınızda oturum açarak ve **Ayarlar**  >  **Özelleştirme**  >  **Geliştirici kaynakları**  >  **örneği Web API 'si (hizmet kök URL 'si)**' ne giderek Dynamics 365 örneğiniz için Web API URL 'sini alın, burada bulunan URL 'yi kopyalayın (örneğin, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rol adı** (gerekli) – özel Dynamics 365 test sürücünüzde tanımladığınız güvenlik rolü adını belirtin, bu, test sürücüleri sırasında kullanıcıya atanır (örneğin, test-sürücü-rolü).

Test sürüşü için Dynamics 365 ortamınızı ayarlama ve kiracınızdaki test sürücüsü kullanıcılarını sağlama ve sağlama konusunda AppSource iznini verme hakkında yardım için [Bu yönergeleri](./test-drive-azure-subscription-setup.md)izleyin.

Barındırılan test sürücünüzü listeleme ve yapılandırmaya yönelik adım adım yönergeler için lütfen [barındırılan test sürücüsü Için ayrıntılı yapılandırma](./test-drive-hosted-detailed-config.md) sayfasını ziyaret edin.

## <a name="logic-app-test-drive"></a>Mantıksal uygulama sınama sürücüsü

Bu tür bir test sürücüsü Microsoft tarafından barındırılmamaktadır. Çeşitli karmaşık çözüm mimarilerini kapsayan bir Dynamics 365 teklifiyle veya diğer özel kaynakla bağlantı kurmak için bunu kullanın. Mantıksal uygulama test sürücüleri ayarlama hakkında daha fazla bilgi için bkz. GitHub üzerinde [işlemler](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) ve [müşteri katılımı](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Bölge** (gerekli, tek seçim açılan listesi) – Şu anda, test sürücünüzün kullanılabilir hale getirilbileceği 26 Azure tarafından desteklenen bölge vardır. Mantıksal uygulamanızın kaynakları seçtiğiniz bölgede dağıtılır. Mantıksal uygulamanızda belirli bir bölgede depolanan özel kaynaklar varsa bölgenin burada seçildiğinden emin olun. En iyi yöntem, mantıksal uygulamanızı portalda Azure aboneliğinizde yerel olarak dağıtmaktır ve bu seçimi yapmadan önce doğru çalıştığını doğrular.

- **Maksimum eşzamanlı test sürücüleri** (gerekli) – test sürücünüzü tek seferde kullanılabilecek maksimum müşteri sayısını ayarlayın. Bu test sürücüleri zaten dağıtılır ve müşterilerin bir dağıtımı beklemeden bunlara anında erişmelerini sağlar.

- **Sınama sürücüsü süresi** (zorunlu) – test sürücüsünün, saat sayısı içinde etkin kalacağı sürenin uzunluğunu girin. Bu süre dolduktan sonra test sürücüsü otomatik olarak sona erer.

- **Azure Kaynak grubu adı** (gerekli) – mantıksal uygulama test sürücünüzün kaydedildiği [Azure Kaynak grubu](../azure-resource-manager/management/overview.md#resource-groups)adını girin.

- **Azure mantıksal uygulama adı** (gerekli) – test sürücüsünü kullanıcıya atayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

- **Mantıksal uygulama adı sağlamasını** Kaldır (gerekli) – müşteri bittikten sonra test sürücüsünü sağlayan mantıksal uygulamanın adını girin. Bu mantıksal uygulama, yukarıdaki Azure kaynakları grubuna kaydedilmelidir.

## <a name="power-bi-test-drive"></a>Test sürüşü Power BI

Etkileşimli bir Power BI görseli göstermek isteyen ürünler, özel olarak oluşturulmuş bir panoyu test sürücüleri olarak paylaşmak için, daha fazla teknik yapılandırma gerekmeden gömülü bir bağlantı kullanabilir. Burada yapmanız gereken, gömülü Power BI URL 'nizi karşıya yükleyin.

Power BI uygulamaları ayarlama hakkında daha fazla bilgi için bkz. [Power BI uygulamalar nelerdir?](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Dağıtım aboneliği ayrıntıları

Microsoft 'un test sürücüsünü sizin adınıza dağıtmasına izin vermek için, ayrı ve benzersiz bir Azure aboneliği oluşturun ve sağlayın (Power BI test sürücüleri için gerekli değildir).

- **Azure ABONELIK kimliği** (Azure Resource Manager ve Logic Apps için gereklidir) – Azure hesap hizmetlerinizi kaynak kullanımı raporlama ve faturalama için erişim izni vermek üzere abonelik kimliğini girin. Henüz bir tane yoksa, test sürücüleri için kullanmak üzere [ayrı bir Azure aboneliği oluşturmayı](../cost-management-billing/manage/create-subscription.md) düşünmeniz önerilir. [Azure Portal](https://portal.azure.com/) oturum açarak ve sol taraftaki menüdeki **abonelikler** SEKMESINE giderek Azure abonelik kimliğinizi bulabilirsiniz. Sekmeyi seçtiğinizde, abonelik KIMLIĞINIZ görüntülenir (örneğin, "a83645ac-1234-5AB6-345-1h234g764ghty").

- **Azure AD KIRACı kimliği** (gerekli) – Azure ACTIVE DIRECTORY (ad) [kiracı kimliğinizi](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)girin). Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol taraftaki menüden Active Directory sekmesini seçin, **Özellikler**' i seçin, sonra listelenen **dizin kimliği** numarasını (örneğin, 50c464d3-4930-494c-963c-1e951d15360e) arayın. Ayrıca, etki alanı adı adresinizi kullanarak kuruluşunuzun kiracı KIMLIĞINI de arayabilirsiniz [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Azure AD kiracı adı** (dinamik 365 için gereklidir) – Azure ACTIVE DIRECTORY (ad) adınızı girin. Bu adı bulmak için sağ üst köşedeki [Azure Portal](https://portal.azure.com/)oturum açın, kiracı adınız hesap adınızın altında listelenecektir.

- **Azure AD uygulama kimliği** (gerekli) – Azure ACTIVE DIRECTORY (ad) [uygulama kimliğinizi](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)girin). Bu KIMLIĞI bulmak için [Azure Portal](https://portal.azure.com/)oturum açın, sol taraftaki menüden Active Directory sekmesini seçin, **uygulama kayıtları**' yı SEÇIN ve listelenen **uygulama kimliği** numarasını (örneğin, `50c464d3-4930-494c-963c-1e951d15360e` ) arayın.

- **Azure AD uygulama istemci parolası** (gerekli) – Azure AD uygulamanızın [Istemci gizli](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)anahtarını girin. Bu değeri bulmak için [Azure Portal](https://portal.azure.com/)oturum açın. Sol menüdeki **Azure Active Directory** sekmesini seçin, **uygulama kayıtları**' i seçin ve test sürücüsü uygulamanızı seçin. Ardından, **Sertifikalar ve gizlilikler**' ı seçin **, yeni istemci parolası**' nı seçin, bir açıklama girin, **süresi dolmasın**' ı seçin ve **Ekle**' yi  Değeri kopyalamadığınızdan emin olun. Değeri kopyalamamadan önce sayfadan ayrılmayın.

## <a name="test-drive-listings"></a>Test sürücü listeleri

Iş Ortağı Merkezi 'nde **Test** sürücüsü sekmesinde bulunan **Test sürücü listeleri** seçeneği, test sürücünüzün kullanılabildiği dilleri (ve pazarları) görüntüler; şu anda İngilizce (Birleşik Devletler) kullanılabilir tek konumdur. Ayrıca, Bu sayfa dile özgü listenin durumunu ve eklendiği tarih/saati görüntüler. Her dil/Pazar için test sürücüsü ayrıntılarını (Açıklama, Kullanıcı el ile, videolar vb.) tanımlamanız gerekir.

- **Açıklama** (gerekli): test sürücünüzü, neyi gösterdiklerinizi, kullanıcının deneme amaçlarını, araştırılacak özellikleri ve kullanıcının teklifinizi elde edip etmeyeceğinizi belirlemesine yardımcı olmak için Ilgili bilgileri tanıtın. Bu alana en fazla 3.000 karakter metin girilebilir.

- **Erişim bilgileri** (Azure Resource Manager ve mantıksal test sürücüleri için gereklidir): Bu test sürücüsüne erişmek ve kullanmak için müşterinin neleri bilmesi gerektiğini açıklayın. Teklifinizi kullanmaya yönelik bir senaryoya ve müşterinin test sürüşü genelinde özelliklere erişmek için bilmeleri gereken tam olarak ne olduğunu öğrenin. Bu alana en fazla 10.000 karakter metin girilebilir.

- **Kullanıcı el ile** (gerekli): test sürücünüzün deneyiminizdeki derinlemesine bir adım adım. Kullanıcı el kitabı, müşterinin test sürücüsüyle karşılaşmasını istediğiniz şekilde tam olarak kapsamalıdır ve sahip olabileceği sorulara başvuru olarak görev yapar. Dosya, karşıya yüklendikten sonra PDF biçiminde ve adlandırılmalıdır (en fazla 255 karakter) olmalıdır.

- **Videolar: video ekleme** (isteğe bağlı): bir müşterinin bir bağlantı ve küçük resim görüntüsü (533 x 324 piksel) ile başka bir yerde barındırılan videolar, bu sayede teklifinizin özelliklerinin başarıyla nasıl kullanılacağı ve avantajlarının vurgulanmasını sağlayan senaryoların nasıl kullanılacağı de dahil olmak üzere, test sürücüsünü daha iyi anlamasına yardımcı olmak için daha fazla bilgi almak üzere
  - **Ad** (gerekli)
  - **URL** (yalnızca YouTube veya Vimeo) gereklidir)
  - **Küçük resim** (533 x 324 piksel) – görüntü, PNG biçiminde olmalıdır.

Şu anda Iş Ortağı Merkezi 'nde test sürücünüzü oluşturuyorsanız, devam etmeden önce **Taslağı kaydet** ' i seçin.

Barındırılan test sürücünüzü listeleme ve yapılandırmaya yönelik adım adım yönergeler için lütfen [barındırılan test sürücüsü Için ayrıntılı yapılandırma](./test-drive-hosted-detailed-config.md) sayfasını ziyaret edin.

## <a name="additional-resources"></a>Ek Kaynaklar

## <a name="next-steps"></a>Sonraki adımlar

- [Sınama sürücüsü En Iyi uygulamaları](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Genel bakış](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; açılır pencere engelleyicinizin kapalı olduğundan emin olun)
- [Ticari Market 'te mevcut bir teklifi güncelleştirme](partner-center-portal/update-existing-offer.md)