---
title: Dataköpek oluşturma-Azure iş ortağı çözümleri
description: Bu makalede, bir veri Köbir örnek oluşturmak için Azure portal nasıl kullanılacağı açıklanır.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748892"
---
# <a name="quickstart-get-started-with-datadog"></a>Hızlı başlangıç: veri köpek ile çalışmaya başlama

Bu hızlı başlangıçta bir veri Köpeyi örneği oluşturacaksınız. Yeni bir Dataköpek organizasyonu oluşturabilir veya var olan bir veri köpek kuruluşuna bağlantı oluşturabilirsiniz.

## <a name="pre-requisites"></a>Ön koşullar

Azure veri köpek tümleştirmesini ayarlamak için Azure aboneliğinde **sahip** erişiminizin olması gerekir. Kurulum 'u başlatmadan önce uygun erişime sahip olduğunuzdan emin olun.

Dataköpek kaynağı içinde Security Assertion Markup Language (SAML) tek Sign-On (SSO) özelliğini kullanmak için bir kurumsal uygulama ayarlamanız gerekir. Bir kurumsal uygulama eklemek için şu rollerden birine ihtiyacınız vardır: genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi.

Kurumsal uygulamayı ayarlamak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com)gidin. **Azure Active Directory** seçin.
1. Sol bölmede **Kurumsal uygulamalar**’ı seçin.
1. **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle**' de *veri köpek*' ı arayın. Arama sonucunu seçin ve ardından **Ekle**' yi seçin.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="AAD kurumsal galerisinde dataköpek uygulaması." border="true":::

1. Uygulama oluşturulduktan sonra yan panelden Özellikler ' e gidin. **Kullanıcı atamasını gerekli mi** belirleyin, **Hayır** olarak ayarlayın ve **Kaydet**' i seçin.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Dataköpek uygulamasının özelliklerini ayarlama" border="true":::

1. Yan panelden **Çoklu oturum açma** seçeneğine gidin. Ardından **SAML**' yi seçin.

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML kimlik doğrulaması." border="true":::

1. **Çoklu oturum açma ayarlarını kaydetmek** isteyip Istemediğiniz sorulduğunda **Evet** ' i seçin.

   :::image type="content" source="media/create/save-sso.png" alt-text="Dataköpek uygulaması için çoklu oturum açma Kaydet" border="true":::

1. Çoklu oturum açma kurulumu artık tamamlanmıştır.

## <a name="find-offer"></a>Teklif bul

Veri köpek bulmak için Azure portal kullanın.

1. [Azure portalına](https://portal.azure.com/) gidin ve oturum açın.

1. **Market** 'i son zamanlarda ziyaret ediyorsanız, kullanılabilir seçeneklerden simgesini seçin. Aksi takdirde _Market_' i arayın.

    :::image type="content" source="media/create/marketplace.png" alt-text="Market simgesi.":::

1. Market 'te, **veri köpek** için arama yapın.

1. Plana genel bakış ekranında, **Ayarla + abone ol**' u seçin.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Azure Marketi 'nde dataköpek uygulaması.":::

## <a name="create-a-datadog-resource-in-azure"></a>Azure 'da bir Dataköpek kaynağı oluşturma

Portal, Dataköpek kaynağı oluşturmak için bir form görüntüler.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Dataköpek kaynağı oluştur" border="true":::

Aşağıdaki değerleri sağlayın.

|Özellik | Açıklama
|:-----------|:-------- |
| Abonelik | Dataköpek kaynağını oluşturmak için kullanmak istediğiniz Azure aboneliğini seçin. Sahip erişiminizin olması gerekir. |
| Kaynak grubu | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. [Kaynak grubu](../../azure-resource-manager/management/overview.md#resource-groups), bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. |
| Kaynak adı | Dataköpek kaynağı için bir ad belirtin. Bu ad, yeni bir veri köpek kuruluşu oluştururken yeni Dataköpek kuruluşunun adı olacaktır. |
| Konum | Batı ABD 2'yi seçin. Şu anda desteklenen tek bölge Batı ABD 2. |
| Dataköpek organizasyonu | Yeni bir veri köpek organizasyonu oluşturmak için **Yeni**' yi seçin. Mevcut bir veri köpek kuruluşuna bağlamak için **mevcut**' ı seçin. |
| Fiyatlandırma planı | Yeni bir kuruluş oluştururken, kullanılabilir veri köpek planları listesinden öğesini seçin. |
| Fatura dönemi | Aylık. |

Var olan bir veri köpek kuruluşuna bağlanıyorsanız, sonraki bölüme bakın. Aksi takdirde, **İleri: ölçümler ve Günlükler** ' i seçin ve sonraki bölümü atlayın.

## <a name="link-to-existing-datadog-organization"></a>Mevcut Datadog kuruluşuna bağlanma

Azure 'daki yeni Dataköpek kaynağınızı, mevcut bir veri köpek kuruluşuna bağlayabilirsiniz.

Veri organizasyonu için **mevcut** ' ı seçin ve ardından **Dataköpek kuruluşu bağlantısı**' nı seçin.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Mevcut Dataköpek kuruluşunun bağlantısı." border="true":::

Bağlantı, bir Dataköpek kimlik doğrulama penceresi açar. Veri Köpede oturum açın.

Azure, varsayılan olarak geçerli veri köpek kuruluşunuzu veri köpek kaynağına bağlar. Farklı bir kuruluşa bağlamak isterseniz, kimlik doğrulama penceresinde aşağıda gösterildiği gibi uygun kuruluşu seçin.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Bağlanacak uygun Dataköpek organizasyonunu seçin" border="true":::

## <a name="configure-metrics-and-logs"></a>Ölçümleri ve günlükleri yapılandırma

Hangi ölçüm ve günlüklerin veri köpeye gönderileceğini yapılandırmak için Azure Kaynak etiketlerini kullanın. Belirli kaynaklar için ölçümleri ve günlükleri dahil edebilir veya dışlayabilirsiniz.

**Ölçüm** göndermek için etiket kuralları şunlardır:

- Varsayılan olarak, ölçümler sanal makineler, sanal makine ölçek kümeleri ve App Service planları dışında tüm kaynaklar için toplanır.
- *Include* etiketleri içeren sanal makineler, sanal makine ölçek kümeleri ve App Service planları, ölçümleri veri köbir şekilde gönderir.
- *Dışlama* etiketleri olan sanal makineler, sanal makine ölçek kümeleri ve App Service planları, veri köklerini ölçüm göndermez.
- Dahil etme ve dışlama kuralları arasında bir çakışma varsa, dışlama önceliklidir

**Günlükleri** göndermek için etiket kuralları şunlardır:

- Varsayılan olarak, Günlükler tüm kaynaklar için toplanır.
- *Include* etiketli Azure kaynakları, günlükleri veri köbir kişiye gönderir.
- *Hariç tutulacak* Etiketler içeren Azure kaynakları veri köbir şekilde Günlükler göndermez.
- Dahil etme ve dışlama kuralları arasında bir çakışma varsa, dışlama önceliklidir.

Örneğin, aşağıdaki ekran görüntüsünde yalnızca sanal makinelerin, sanal makine ölçek kümelerinin ve *dataköpek = true* olarak etiketlenen App Service planlarının, verileri veri köbir yere gönderebilmesi için bir etiket kuralı gösterilmektedir.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Günlükleri ve ölçümleri yapılandırın." border="true":::

Azure 'dan Dataköpek 'e yayılan iki tür günlük vardır.

1. **Abonelik düzeyi günlükleri** - [Denetim düzleminde](../../azure-resource-manager/management/control-plane-and-data-plane.md)kaynaklarınızda işlemler hakkında öngörüler sağlar. Hizmet durumu olaylarında güncelleştirmeler de dahildir. Herhangi bir yazma işlemi için ne, kim ve ne zaman yapılacağını belirlemek için etkinlik günlüğünü kullanın (PUT, POST, DELETE). Her bir Azure aboneliği için tek bir etkinlik günlüğü vardır.

1. **Azure Kaynak günlükleri** - [veri düzleminde](../../azure-resource-manager/management/control-plane-and-data-plane.md)bir Azure kaynağında gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Örneğin, bir Key Vault gizli anahtar almak bir veri düzlemi işlemidir. Veya, bir veritabanına yönelik bir istek yapıldığında de veri düzlemi işlemi de vardır. Kaynak günlüklerinin içeriği, Azure hizmeti ve kaynak türüne göre farklılık gösterir.

Abonelik düzeyi günlüklerini veri Köbir kişiye göndermek için **abonelik Gönder etkinlik günlükleri**' ni seçin. Bu seçenek işaretsiz bırakılırsa, abonelik düzeyi günlüklerinin hiçbiri veri köpeye gönderilmez.

Azure Kaynak günlüklerini veri Köbir kişiye göndermek için **Tüm tanımlı kaynaklar Için Azure Kaynak günlüklerini Gönder**' i seçin. Azure Kaynak günlüklerinin türleri [Azure Izleyici kaynak günlüğü kategorilerinde](../../azure-monitor/essentials/resource-logs-categories.md)listelenmiştir.  Günlükleri veri köpeye gönderen Azure kaynakları kümesini filtrelemek için Azure Kaynak etiketlerini kullanın.  

Veri köpeye gönderilen Günlükler Azure tarafından ücretlendirilecektir. Daha fazla bilgi için bkz. Azure Market iş ortaklarına gönderilen [Platform günlüklerinin fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/) .

Ölçümleri ve günlükleri yapılandırmayı tamamladıktan sonra, Ileri ' yi seçin **: çoklu oturum açma**.

## <a name="configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma

Kuruluşunuz kimlik sağlayıcısı olarak Azure Active Directory kullanıyorsa, Azure portal veri Köbir yerden çoklu oturum açma oluşturabilirsiniz. Kuruluşunuz farklı bir kimlik sağlayıcısı kullanıyorsa veya şu anda çoklu oturum açma kurmak istemiyorsanız, bu bölümü atlayabilirsiniz.

Dataköpek kaynağını mevcut bir veri köpek kuruluşuna bağlıyorsanız, bu adımda çoklu oturum açmayı ayarlayamazsınız. Bunun yerine, Dataköpek kaynağını oluşturduktan sonra çoklu oturum açma 'yı ayarlarsınız. Daha fazla bilgi için bkz. [Çoklu oturum açmayı yeniden yapılandırma](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Mevcut Dataköpek kuruluşuna bağlamak için çoklu oturum açma." border="true":::

Azure Active Directory aracılığıyla çoklu oturum açmayı **etkinleştirmek için Azure Active Directory aracılığıyla çoklu oturum açmayı etkinleştir** onay kutusunu işaretleyin.

Azure portal uygun Dataköpek uygulamasını Azure Active Directory alır. Uygulama, daha önceki bir adımda verdiğiniz kurumsal uygulamayla eşleşiyor.

Dataköpek uygulama adını seçin.

:::image type="content" source="media/create/sso.png" alt-text="Veri Köpede çoklu oturum açmayı etkinleştirin." border="true":::

**Sonraki: Etiketler**' i seçin.

## <a name="add-custom-tags"></a>Özel etiket ekle

Yeni Dataköpek kaynağı için özel Etiketler belirtebilirsiniz. Dataköpek kaynağına uygulanacak etiketlerin ad ve değer çiftlerini belirtin.

:::image type="content" source="media/create/tags.png" alt-text="Dataköpek kaynağı için özel etiketler ekleyin." border="true":::

Etiket eklemeyi bitirdiğinizde, Ileri ' yi seçin **: gözden geçir + oluştur**.

## <a name="review--create-datadog-resource"></a>Gözden geçir + Dataköpek kaynağı oluştur

Seçimlerinizi ve kullanım koşullarını gözden geçirin. Doğrulama tamamlandıktan sonra **Oluştur**' u seçin.

:::image type="content" source="media/create/review-create.png" alt-text="Dataköpek kaynağını inceleyin ve oluşturun." border="true":::

Azure, Dataköpek kaynağını dağıtır.

İşlem tamamlandığında, Dataköpek kaynağını görmek için **Kaynağa Git** ' i seçin.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Dataköpek kaynak dağıtımı." border="true":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dataköpek kaynağını yönetme](manage.md)
