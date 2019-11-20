---
title: Inceleme Aracı ayarlarını yapılandırma-Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator için ekibiniz, Etiketler, bağlayıcılar, iş akışları ve kimlik bilgilerinizi yapılandırmak ya da almak için Inceleme aracını kullanın.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2ba314c814bdc92f62a607e28aefa30372bf297f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757427"
---
# <a name="configure-the-review-tool"></a>Gözden Geçirme aracını yapılandırma

[İnceleme aracında](https://contentmoderator.cognitive.microsoft.com) , panodaki **Ayarlar** menüsünde erişebileceğiniz bazı önemli özellikler vardır.

![Content Moderator gözden geçirme ayarları menüsü](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Ekip ve alt takımları yönetme

**Takım** sekmesi, takımınızın ve alt takımlarınızın &mdash;groups, bazı [insan İncelemeleri](../review-api.md#reviews) başlatıldığında bilgilendirilecek kullanıcıların yönetmenizi sağlar. Yalnızca bir ekibiniz olabilir (gözden geçirme aracıyla kaydolduğunuzda oluşturursunuz), ancak birden çok alt takım oluşturabilirsiniz. Ekip Yöneticisi üyeleri davet edebilir, izinlerini ayarlayabilir ve bunları farklı alt takımlara atayabilir.

![Araç takımı ayarlarını gözden geçirme](images/settings-2-team.png)

Alt takımlar, belirli içerik kategorilerini gözden geçirmek için adanmış, yükseltme ekipleri veya takımlar oluşturmak için faydalıdır. Örneğin, daha fazla gözden geçirmek için yetişkinlere yönelik içeriği ayrı bir ekibe gönderebilirsiniz.

Bu bölümde, subekipler oluşturma ve anında İncelemeleri hızlı bir şekilde atama işlemleri açıklanmaktadır. Ancak, belirli ölçütlere göre incelemeler atamak için [Iş akışları](workflows.md) kullanabilirsiniz.

### <a name="create-a-subteam"></a>Alt takım oluşturma

**Alt takımlar** bölümüne gidin ve **alt takım Ekle**' ye tıklayın. İletişim kutusuna alt takımınızın adını girin ve **Kaydet**' e tıklayın.

![Alt takım adı](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Ekip mates 'i davet etme

Zaten varsayılan ekibin üyesi olmayan bir alt ekibe bir Kullanıcı atayamazsınız, bu nedenle önce varsayılan ekibe gözden geçirenler eklemeniz gerekir. **Takım** sekmesinde **davet et** ' e tıklayın.

![Kullanıcıları davet etme](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Ekip mates 'yi alt ekibe atama

Varsayılan takımınızdan bir veya daha fazla alt takıma üye atamak için **üye Ekle** düğmesine tıklayın. Yalnızca bir alt takıma mevcut kullanıcıları ekleyebilirsiniz. İnceleme aracında olmayan yeni kullanıcılar eklemek için, takım ayarları sayfasındaki "davet" düğmesini kullanarak bunları davet edin.

![Alt takım üyeleri atama](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Alt takımlara incelemeler atama

Alt ekiplerinizi oluşturduktan ve üyeleri atadıktan sonra, bu alt takımlara içerik [İncelemeleri](../review-api.md#reviews) atamaya başlayabilirsiniz. Bu, sitenin **Gözden geçirme** sekmesinden yapılır.
Bir alt takıma içerik atamak için sağ üst köşedeki üç noktaya tıklayın, **Git**' i seçin ve bir alt takım seçin.

![Alt ekibe görüntü incelemesi atama](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alt takımlar arasında geçiş yap

Birden fazla alt ekibin üyesiyseniz, sizin için görüntülenen içerik incelemelerini değiştirmek üzere bu alt takımlar arasında geçiş yapabilirsiniz. **Gözden geçir** sekmesinde **varsayılan** etiketli açılan menüyü seçin ve **alt takım seç**' i seçin. Farklı alt takımlar için içerik incelemelerini, ancak yalnızca sizin üye olduğunu görüntüleyebilirsiniz.

![Alt takımlar arasında geçiş yap](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Etiketler

**Etiketler** sekmesi,**ıyetişkin** (**a**) ve **isracy** (**r**) &mdash; iki varsayılan denetleme etiketine ek olarak özel denetleme etiketleri tanımlamanıza olanak sağlar. Özel bir etiket oluşturduğunuzda, bu, varsayılan etiketlerin yanı sıra incelemelerde kullanılabilir hale gelir. Görünürlük ayarlarını değiştirerek, gözden geçirmeler halinde hangi etiketlerin gösterileceğini değiştirebilirsiniz.

!["Görünür" onay kutuları dahil olmak üzere etiket görünümü](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Özel Etiketler oluşturma

Yeni bir etiket oluşturmak için ilgili alanlara kısa bir kod, ad ve açıklama girmeniz gerekir.

- **Kısa kod**: etiketinize yönelik iki harfli bir kod girin. Örnek: **CB**
- **Ad**: boşluk olmadan küçük harfli bir kısa ve açıklayıcı etiket adı girin. Örnek: **ıbulber**.
- **Açıklama**: (isteğe bağlı) etiketlerinizin hedeflediği içerik türünün bir açıklamasını girin. Örnek: **tasber bulun tasviri veya örnekleri**.

Etiket eklemek için **Ekle** ' ye tıklayın ve etiket oluşturmayı bitirdiğinizde **Kaydet** ' e tıklayın.

![İnceleme aracı yeni etiket oluştur iletişim kutusu](images/settings-3-tags.png)

### <a name="delete-tags"></a>Etiketleri Sil

Etiketler listesindeki girişlerinin yanındaki çöp kutusu simgesini seçerek özel etiketleri silebilirsiniz, ancak varsayılan etiketleri silemezsiniz.

## <a name="connectors"></a>Bağlayıcılar

**Bağlayıcılar** sekmesi, içerik [iş akışlarının](../review-api.md#workflows)bir parçası olarak farklı yollarla içeriği işleyebilen, hizmete özgü eklentiler olan bağlayıcılarınızı yönetmenizi sağlar.

Bir iş akışı oluşturduğunuzda varsayılan bağlayıcı Content Moderator bağlayıcıdır. Bu, içeriği **yetişkin** veya **korklık**olarak işaretleyebilir, küfür bulun vb. Bununla birlikte, burada listelenen diğer bağlayıcıları, ilgili hizmetlerinin kimlik bilgileriniz olduğu sürece (örneğin, Yüz Tanıma API'si bağlayıcısını kullanmak için) kullanabilirsiniz. bu şekilde, [Yüz Tanıma API'si](https://docs.microsoft.com/azure/cognitive-services/face/overview) bir abonelik anahtarı almanız gerekir.

[İnceleme aracında](./human-in-the-loop.md) aşağıdaki bağlayıcılar bulunur:

- Duygu Tanıma API'si
- Yüz API'si
- PhotoDNA bulut hizmeti
- Metin Analizi API’si

### <a name="add-a-connector"></a>Bağlayıcı ekleme

Bir bağlayıcı eklemek için (ve içerik [iş akışlarında](../review-api.md#workflows)kullanılabilir hale getirmek için), uygun **Bağlan** düğmesini seçin. Sonraki iletişim kutusunda, bu hizmet için abonelik anahtarınızı girin. İşiniz bittiğinde yeni bağlayıcınızın sayfanın en üstünde görünmesi gerekir.

![Content Moderator bağlayıcı ayarları](images/settings-4-connectors.png)

## <a name="workflows"></a>İş akışları

**İş** akışları sekmesi, [iş](../review-api.md#workflows)akışlarınızı yönetmenizi sağlar. İş akışları, içerik için bulut tabanlı filtrelerdir ve içeriği farklı yollarla sıralamak ve uygun işlemleri yapmak için bağlayıcılarla birlikte çalışırlar. Burada, iş akışlarınızı tanımlayabilir, düzenleyebilir ve test edebilirsiniz. Bunun nasıl yapılacağını gösteren yönergeler için bkz. [iş akışlarını tanımlama ve kullanma](Workflows.md) .

![Content Moderator Iş akışı ayarları](images/settings-5-workflows.png)

## <a name="credentials"></a>Kimlik bilgileri

**Kimlik bilgileri** sekmesi, bir rest çağrısından veya istemci SDK 'sına ait denetleme hizmetlerinden birine erişmeniz gereken Content moderator abonelik anahtarınız için hızlı erişim sağlar.

![Content Moderator kimlik bilgileri](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>İş akışları için dış kimlik bilgilerini kullan

[İnceleme aracı](https://contentmoderator.cognitive.microsoft.com) , kaydolduğunuzda Azure Content moderator Hizmetleri için ücretsiz bir deneme anahtarı oluşturur, ancak Azure hesabınızdan mevcut bir anahtarı kullanacak şekilde de yapılandırabilirsiniz. Bu, büyük ölçekli senaryolar için önerilir, çünkü ücretsiz deneme anahtarları katı kullanım sınırlarına sahiptir ([fiyatlandırma ve sınırlar](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Azure 'da bir [Content moderator kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) oluşturduysanız, bu sayfaya Azure Portal ve **anahtarlar** dikey penceresini seçin. Anahtarlarınızın birini kopyalayın.

![Azure portal anahtarları Content Moderator](images/credentials-azure-portal-keys.PNG)

[İnceleme aracının](https://contentmoderator.cognitive.microsoft.com) **kimlik bilgileri** sekmesinde, **iş akışı ayarları** bölmesine gidin, **Düzenle**' yi seçin ve anahtarınızı **OCP-apim-Subscription-Key** alanına yapıştırın. Artık, denetleme API 'Lerini çağıran iş akışları Azure kimlik bilgilerinizi kullanacaktır.

> [!NOTE]
> **Iş akışı ayarları** bölmesindeki diğer iki alan özel terim ve görüntü listeleri içindir. Bunlar hakkında bilgi edinmek için [özel terimleri](../try-terms-list-api.md) veya [özel görüntü](../try-image-list-api.md) kılavuzlarını inceleyin.

### <a name="use-your-azure-account-with-the-review-apis"></a>Azure hesabınızı İnceleme API 'Leriyle kullanma

Azure anahtarınızı gözden geçirme API 'Leriyle birlikte kullanmak için, kaynak KIMLIĞINIZI almanız gerekir. Azure portal Content Moderator kaynağına gidin ve **Özellikler** dikey penceresini seçin. Kaynak KIMLIĞI değerini kopyalayın ve gözden geçirme aracının **kimlik bilgileri** sekmesinin **beyaz listelenmiş kaynak kimliği** alanına yapıştırın.

![Azure portal Content Moderator kaynak KIMLIĞI](images/credentials-azure-portal-resourceid.PNG)

Abonelik anahtarınızı her iki yere de girdiyseniz, gözden geçirme aracı hesabınızla birlikte gelen deneme anahtarı kullanılmaz, ancak kullanılabilir olmaya devam edecektir.

## <a name="next-steps"></a>Sonraki adımlar

İçerik denetleme senaryolarında Inceleme aracını kullanmaya başlamak için [İnceleme aracının hızlı](../quick-start.md) başlangıcını izleyin.