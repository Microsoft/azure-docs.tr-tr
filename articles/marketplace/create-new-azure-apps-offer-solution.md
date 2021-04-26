---
title: Çözüm şablonu planı yapılandırma
description: Iş Ortağı Merkezi 'nde Azure Uygulama teklifiniz için bir çözüm şablonu planının nasıl yapılandırılacağını öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744627"
---
# <a name="configure-a-solution-template-plan"></a>Çözüm şablonu planı yapılandırma

Bu makale yalnızca bir Azure uygulaması teklifinin çözüm şablonu planları için geçerlidir. Yönetilen bir uygulama planı yapılandırıyorsanız, [yönetilen uygulama planını yapılandırma](create-new-azure-apps-offer-managed.md)' ya gidin.

## <a name="choose-who-can-see-your-plan"></a>Planınızı kimlerin görebileceğini seçin

Her planı herkese veya yalnızca belirli bir hedef kitleye görünür olacak şekilde yapılandırabilirsiniz. Atadığınız her abonelik KIMLIĞININ açıklamasını ekleme seçeneğiyle Azure abonelik kimliklerini kullanarak özel bir hedef kitleye erişim izni verirsiniz. Bir kullanarak en fazla 10 abonelik kimliği el ile veya 10.000 abonelik kimliği ekleyebilirsiniz. CSV dosyası. Azure abonelik kimlikleri, GUID 'Ler olarak temsil edilir ve harflerin küçük harf olması gerekir.

> [!NOTE]
> Özel bir plan yayımlarsanız, görünürlüğünü daha sonra herkese açık olarak değiştirebilirsiniz. Ancak, bir genel planı yayımladıktan sonra görünürlüğünü özel olarak değiştiremezsiniz.

**Kullanılabilirlik** sekmesinde, **plan görünürlüğü** altında aşağıdakilerden birini yapın:

- Planı genel hale getirmek için **ortak** seçenek düğmesini ( _radyo düğmesi_ olarak da bilinir) seçin.
- Planı özel hale getirmek için **özel** seçenek düğmesini seçin ve ardından Azure abonelik kimliklerini el ile veya bir CSV dosyası ile ekleyin.

    > [!NOTE]
    > Özel veya kısıtlanmış bir hedef kitle, **Önizleme** sekmesinde tanımladığınız önizleme izleyicilerinizden farklıdır. Bir önizleme hedef kitlesi, Market 'te canlı olarak yayımlanmadan önce teklifinizin erişimine açabilir. Özel hedef kitle seçimi yalnızca belirli bir plana uygulansa da, önizleme hedef kitlesi doğrulama amacıyla tüm planları (özel veya değil) görüntüleyebilir.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Özel bir plana yönelik olarak Azure abonelik kimliklerini el ile ekleme

1. **Plan görünürlüğü** altında **özel** seçenek düğmesini seçin.
1. Görüntülenen **Azure ABONELIK kimliği** kutusunda, bu özel plana erişim vermek istediğiniz hedef kitlesinin Azure abonelik kimliğini girin. En az bir abonelik KIMLIĞI gereklidir.
1. Seçim **Açıklama** kutusuna bu hedef kitle için bir açıklama girin.
1. Başka bir abonelik KIMLIĞI eklemek için, **Kimlik Ekle (en fazla 10)** bağlantısını seçin ve 2. ve 3. adımları yineleyin.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Bir kullanın. Özel bir plan için Azure abonelik kimlikleri eklemek üzere CSV dosyası

1. **Plan görünürlüğü** altında **özel** seçenek düğmesini seçin.
1. **Izleyiciyi dışarı aktar (CSV)** bağlantısını seçin.
1. Öğesini açın. CSV dosyası ve özel teklifin **kimlik** sütununa erişim izni vermek istediğiniz Azure abonelik kimliklerini ekleyin.
1. İsteğe bağlı olarak, **Açıklama** sütunundaki her bir hedef kitle için bir açıklama girin.
1. Abonelik KIMLIĞI olan her satır için **tür** sütununa "SubscriptionID" ekleyin.
1. Öğesini kaydedin. CSV dosyası.
1. **Kullanılabilirlik** sekmesinde, **plan görünürlüğü** altında, **izleyiciyi içeri aktar (CSV)** bağlantısını seçin.
1. Görüntülenen iletişim kutusunda **Evet**' i seçin.
1. Öğesini seçin. CSV dosyası ve sonra **Aç**' ı seçin. Olduğunu belirten bir ileti görüntülenir. CSV dosyası başarıyla içeri aktarıldı.

### <a name="hide-your-plan"></a>Planınızı gizleyin

Çözüm şablonunuz, başka bir çözüm şablonu veya yönetilen uygulama başvurulduğu sırada yalnızca dolaylı olarak dağıtılmak üzere tasarlanıyorsa, çözüm şablonunuzu yayımlamak için **planı Gizle** ' nin altındaki onay kutusunu seçin, ancak doğrudan arama yapan ve bunu açan müşterilerden gizleyin.

Sonraki bölüme geçmeden önce **Taslağı kaydet** ' i seçin: teknik yapılandırmayı tanımlayın.

## <a name="define-the-technical-configuration"></a>Teknik yapılandırmayı tanımlama

**Teknik yapılandırma** sekmesinde, müşterilerin planınızı dağıtmasına ve paket için bir sürüm numarası sağlamasına imkan tanıyan dağıtım paketini karşıya yüklersiniz.

> [!NOTE]
> Bu sekme, **plan kurulumu** sekmesinde başka bir plandan paketleri yeniden kullanmayı seçerseniz görünür olmayacaktır. Varsa, [planlarınızı görüntüleyin](#view-your-plans)bölümüne gidin.

### <a name="assign-a-version-number-for-the-package"></a>Paket için sürüm numarası atama

**Sürüm** kutusunda, teknik yapılandırmanın güncel sürümünü sağlayın. Bu sayfada her değişiklik yayımlaışınızda bu sürümü artırın. Sürüm numarası şu biçimde olmalıdır: Integer. Integer. Integer. Örneğin, `1.0.2`.

### <a name="upload-a-package-file"></a>Paket dosyasını karşıya yükle

**Paket dosyası (. zip)** altında, paket dosyanızı gri kutuya sürükleyin veya **Dosya bağlantısına gidin** bağlantısını seçin.

> [!NOTE]
> Dosya yükleme sorununuz varsa, yerel ağınızın `https://upload.xboxlive.com` Iş Ortağı Merkezi tarafından kullanılan hizmeti engellemediğinden emin olun.

### <a name="previously-published-packages"></a>Önceden yayımlanmış paketler

Teklifinizi canlı yayımladıktan sonra, **daha önce yayımlanmış paketler** alt sekmesi **Teknik yapılandırma** sayfasında görünür. Bu sekme, teknik yapılandırmanızın daha önce yayımlanmış tüm sürümlerini listeler.

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
