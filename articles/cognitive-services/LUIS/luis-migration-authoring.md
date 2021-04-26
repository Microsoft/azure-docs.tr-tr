---
title: Azure Kaynak yazma anahtarına geçiş
titleSuffix: Azure Cognitive Services
description: Bu makalede, Language Understanding (LUSıS) yazma kimlik doğrulamasının bir e-posta hesabından Azure kaynağına geçirilmesi açıklanmaktadır.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787646"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure Kaynak yazma anahtarına geçiş

> [!IMPORTANT]
>  3 Aralık 'tan itibaren, var olan LUYA kullanıcıları, LUSıS uygulamalarına yazmaya devam etmek için geçiş işlemini tamamlamalıdır.

Language Understanding (LUSıS) yazma kimlik doğrulaması, bir e-posta hesabından Azure kaynağına değiştirilmiştir. Henüz geçirmemişse, hesabınızı nasıl geçirebileceğinizi öğrenmek için bu makaleyi kullanın.  


## <a name="what-is-migration"></a>Geçiş nedir?

Geçiş, bir e-posta hesabından Azure kaynağına yazma kimlik doğrulamasını değiştirme işlemidir. Hesabınız, geçiş işleminden sonra bir Azure aboneliğine ve bir Azure yazma kaynağına bağlanır.

Geçişin, [Luın portalından](https://www.luis.ai)gerçekleştirilmesi gerekiyor. Örneğin, LUSıS CLı 'yı kullanarak yazma anahtarlarını oluşturursanız, HALSıS portalındaki geçiş işlemini gerçekleştirmeniz gerekir. Geçiş sonrasında uygulamalarınızda hala ortak yazarlara sahip olabilirsiniz, ancak bunlar uygulama düzeyi yerine Azure Kaynak düzeyine eklenecektir. Hesabınızı geçirme geri alınamaz.

> [!Note]
> * Bir tahmin çalışma zamanı kaynağı oluşturmanız gerekiyorsa, bunu oluşturmak için [ayrı bir işlem](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vardır.
> * Uygulamalarınızın ve katkıda bulunanların nasıl etkileneceği hakkında bilgi için aşağıdaki [geçiş notları](#migration-notes) bölümüne bakın. 
> * F0 katmanında gösterildiği gibi, LUSıS uygulamanızı yazma işlemi ücretsizdir. [Fiyatlandırma katmanları hakkında daha fazla](luis-limits.md#key-limits)bilgi edinin.

## <a name="migration-prerequisites"></a>Geçiş önkoşulları

* Geçerli bir Azure aboneliği. Kiracı yöneticinizden aboneliğinizi size eklemesini isteyin veya [ücretsiz bir abonelik için kaydolun](https://azure.microsoft.com/free/cognitive-services).
* Bir LUSıS, LUO portalından veya [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)Azure yazma kaynağıdır. 
    * LUO portalından bir yazma kaynağı oluşturma, sonraki bölümde açıklanan geçiş sürecinin bir parçasıdır.
* Uygulamalar üzerinde ortak çalışan değilseniz, uygulamalar otomatik olarak geçirilmez. Geçiş akışından geçiş yaparken bu uygulamaları dışarı aktarmanız istenir. [Dışarı aktarma API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)'sini de kullanabilirsiniz. Geçişten sonra uygulamayı LUO 'ya geri aktarabilirsiniz. İçeri aktarma işlemi, sahip olduğunuz yeni bir uygulama KIMLIĞIYLE yeni bir uygulama oluşturur.        
* Uygulamanın sahibiyseniz, otomatik olarak geçiş yapacağından uygulamalarınızı dışarı aktarmanız gerekmez. Her uygulama için tüm ortak çalışanların listesini içeren bir e-posta şablonu sağlanır, bu nedenle geçiş süreci hakkında bildirim alabilir.

## <a name="migration-steps"></a>Geçiş adımları

1. [Luu portalında](https://www.luis.ai)oturum açtığınızda, geçiş adımlarını Içeren bir Azure geçiş penceresi açılır. Bunu kapatırsanız, LUSıS uygulamalarınızı yazmaya devam edemezsiniz ve görüntülenecek tek eylem, geçişe devam etmek için olacaktır.

    > [!div class="mx-imgBorder"]
    > ![Geçiş penceresi girişi](./media/migrate-authoring-key/notify-azure-migration.png)

2. Uygulamalarınızdan herhangi birinde ortak çalışanlar varsa, size ait olan uygulama adlarının bir listesini, her bir uygulamadaki yazma bölgesi ve ortak e-postalarıyla birlikte görürsünüz. Uygulama adının sol tarafındaki simge **Gönder** düğmesine tıklayarak, ortak çalışanlarla ilgili bilgileri bildiren bir e-posta gönderilmesini öneririz.
Ortak çalışan, `*` uygulamanıza atanan bir tahmin kaynağına sahipse, uygulama adının yanında bir simge görünür. Geçişten sonra bu uygulamalara bu tahmin kaynakları atanmış olur, çünkü bu uygulamalar, ortak çalışanların uygulamalarınızı yazmak için erişimi olmaz. Ancak, tahmin kaynağının sahibi anahtarları Azure portal yeniden [üretildiğinde](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) Bu atama bozulur.  

   > [!div class="mx-imgBorder"]
   > ![Ortak çalışanları bilgilendir](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Her ortak çalışan ve uygulama için varsayılan e-posta uygulaması, hafif biçimli bir e-posta ile açılır. Göndermeden önce e-postayı düzenleyebilirsiniz. E-posta şablonu, tam uygulama KIMLIĞI ve uygulama adını içerir.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Hesabınızı Azure 'a geçirdikten sonra uygulamalarınız artık ortak olmayan uygulamalar tarafından kullanılamaz.

3. Herhangi bir uygulama üzerinde ortak çalışan değilseniz, sizinle paylaşılan uygulama adlarının bir listesi, her uygulamadaki yazma bölgesi ve sahip e-postalarıyla birlikte gösterilir. Uygulama adının solundaki dışarı aktar düğmesine tıklayarak uygulamaların bir kopyasını dışarı aktarmanız önerilir. Bu uygulamaları geçirdikten sonra geri aktarabilirsiniz, çünkü bu uygulamalar sizinle otomatik olarak geçirilmeyecektir.
Bir `*` uygulamaya atanan bir tahmin kaynağınız varsa, uygulama adının yanında bir simge görünür. Geçişten sonra, bu uygulamaları yazmak için artık erişiminizin olmadığı halde, tahmin kaynağınız bu uygulamalara atanır. Tahmin kaynağınız ile uygulama arasındaki atamayı bölmek istiyorsanız, Azure portal gitmeniz ve [anahtarları yeniden oluşturmanız](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)gerekir.

   > [!div class="mx-imgBorder"]
   > ![Uygulamalarınızı dışarı aktarın.](./media/migrate-authoring-key/migration-export-apps.png)


4. Bölgeleri geçirmeye yönelik pencerede, uygulamalarınızın yazıldığı bölgedeki bir Azure kaynağına geçirilmesi istenir... Lua 'nın üç yazma bölgesi [ve portalı](./luis-reference-regions.md#luis-authoring-regions)vardır. Pencere, sahip olduğu uygulamalarınızın yazıldığı bölgeleri gösterir. Görüntülenmekte olan geçiş bölgeleri, kullandığınız bölgesel portala ve yazdığınız uygulamalara bağlı olarak farklı olabilir. 

   > [!div class="mx-imgBorder"]
   > ![Çok bölgeli geçiş.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Her bölge için, yeni bir LUSıS yazma kaynağı oluşturmayı veya düğmeleri kullanarak var olan bir nesneye geçirmeyi seçin.

   > [!div class="mx-imgBorder"]
   > ![Mevcut yazma kaynağı oluşturmayı veya varolanı seçin](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Şu bilgileri belirtin:

   * **Kiracı adı**: Azure aboneliğinizin ilişkilendirildiği kiracı. Bu, varsayılan olarak, kullanmakta olduğunuz kiracıya ayarlanır. Bu pencereyi kapatarak ve ekran harflerinizi içeren ekranın sağ üst kısmında avatarını seçerek kiracılar arasında geçiş yapabilirsiniz. Pencereyi yeniden açmak için **Azure 'A geçiş** ' e tıklayın.
   * **Azure abonelik adı**: kaynakla ilişkilendirilecek abonelik. Kiracınıza ait birden fazla aboneliğiniz varsa, açılan listeden istediğiniz birini seçin.
   * **Kaynak adı yazma**: seçtiğiniz özel bir ad. Yazma ve tahmin uç noktası sorgularınız için URL 'nin bir parçası olarak kullanılır. Yeni bir yazma kaynağı oluşturuyorsanız, kaynak adı yalnızca alfasayısal karakterler içerebilir `-` ve başlayamaz veya bitemez `-` . Adda başka semboller varsa, kaynak oluşturma ve geçiş başarısız olur.
   * **Azure Kaynak grubu adı**: açılan listeden seçtiğiniz özel bir kaynak grubu adı. Kaynak grupları, erişim ve yönetim için Azure kaynaklarını gruplandıreklemenize olanak tanır. Şu anda aboneliğinizde bir kaynak grubunuz yoksa, LUı portalında bir tane oluşturmanıza izin verilmez. Bir tane oluşturmak için [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) gidin ve oturum açma işlemine devam etmek için lusıs 'e gidin.

6. Tüm bölgelerde başarılı bir şekilde geçiş yaptıktan sonra son ' a tıklayın. Artık uygulamalarınıza erişim sahibi olursunuz. Portalın içindeki tüm bölgelerde tüm uygulamalarınızı yazmaya ve korumaya devam edebilirsiniz.

## <a name="migration-notes"></a>Geçiş notları

* Geçişten önce, coyazarlar, LUSıS uygulama düzeyinde _ortak çalışanlar_ olarak bilinir. Geçişten sonra, Azure Kaynak düzeyinde aynı işlevsellik için _katılımcı_ Azure rolü kullanılır.
* Birden fazla [Luo bölgesel portala](./luis-reference-regions.md#luis-authoring-regions)oturum açtıysanız, aynı anda birden çok bölgeye geçiş yapmanız istenir.
* Uygulama sahibiyseniz uygulamalar sizinle otomatik olarak geçiş yapılır. Uygulama üzerinde ortak çalışan bir çalışmasanız, uygulamalar sizinle geçiş uygulamacaktır. Ancak, ortak çalışanların ihtiyaç duydukları uygulamaları dışarı aktarmaları istenir.
* Uygulama sahipleri geçirilecek uygulamaların bir alt kümesini seçemez ve bir sahibin, ortak çalışanların geçirilip geçirilmediğini bilmesi için bir yol yoktur.
* Geçiş, Azure yazma kaynağına otomatik olarak hareket etmez veya eklemez. Uygulama sahibi, geçişten sonra bu adımı tamamlaması gereken bir uygulamadır. Bu adım [, Azure yazma kaynağı için izinler](./luis-how-to-collaborate.md)gerektirir.
* Katkıda bulunanlar Azure kaynağına atandıktan sonra, uygulamalara erişebilmek için önce geçiş yapması gerekecektir. Aksi takdirde, uygulamaları yazmak için erişimleri olmaz.


## <a name="using-apps-after-migration"></a>Geçişten sonra uygulamaları kullanma

Geçiş işleminden sonra, sahibi olduğunuz tüm LUO uygulamalarınız artık tek bir LUıN yazma kaynağına atanır.
**Uygulamalarım** listesinde, yeni yazma kaynağına geçirilen uygulamalar gösterilir. Uygulamalarınıza erişmeden önce, yazılabilir uygulamaları görüntülemek için abonelik ve yazma kaynağını seçmek üzere **farklı bir yazma kaynağı** seçin öğesini seçin.

> [!div class="mx-imgBorder"]
> ![abonelik ve yazma kaynağı seçin](./media/migrate-authoring-key/select-sub-and-resource.png)


Uygulamalarınızı programlama yoluyla düzenlemeyi planlıyorsanız, yazma anahtarı değerleri gerekir. Bu değerler, LUO portalındaki ekranın üst kısmında bulunan **Yönet** ' e tıklayarak ve ardından **Azure kaynakları**' nı seçerek görüntülenir. Bunlar ayrıca kaynağın **anahtar ve uç noktalar** sayfasında Azure Portal de mevcuttur. Ayrıca, daha fazla yazma kaynağı oluşturabilir ve bunları aynı sayfadan atayabilirsiniz.

## <a name="adding-contributors-to-authoring-resources"></a>Yazma kaynaklarına katkıda bulunanlar ekleme

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Yazma kaynağınız üzerinde [katkıda bulunanlar eklemeyi](luis-how-to-collaborate.md) öğrenin. Katkıda bulunanlar, bu kaynaktaki tüm uygulamalara erişebilir.

Kaynak için **Access Control (IAM)** sayfasında Azure Portal yazma kaynağına katkıda bulunanlar ekleyebilirsiniz. Daha fazla bilgi için bkz. [uygulamanıza katkıda bulunanlar ekleme](luis-how-to-collaborate.md).

> [!Note]
> LUSıS uygulamasının sahibi geçirilmiş ve Azure kaynağına katkıda bulunan çalışanı olarak eklediyseniz, ortak çalışan, aynı zamanda geçirilmediği sürece uygulamaya erişemez.

## <a name="troubleshooting-the-migration-process"></a>Geçiş işlemi sorunlarını giderme

Azure aboneliğinizi, açılan listede bulamazsanız:
* Bilişsel hizmetler kaynakları oluşturma yetkisine sahip geçerli bir Azure aboneliğiniz olduğundan emin olun. [Azure Portal](https://ms.portal.azure.com) gidin ve aboneliğin durumunu denetleyin. Bir tane yoksa, [ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free/cognitive-services/).
* Geçerli aboneliğinizle ilişkili doğru kiracıda olduğunuzdan emin olun. Baş harflerinizi içeren, ekranın sağ üst kısmında bulunan avatarcıyı seçerek kiracılar arasında geçiş yapabilirsiniz.

  > [!div class="mx-imgBorder"]
  > ![Dizin değiştirme sayfası](./media/migrate-authoring-key/switch-directories.png)

Var olan bir yazma kaynağınız varsa ancak **mevcut yazma kaynağını kullan** seçeneğini belirlediğinizde bu kaynağı bulamıyorsanız:
* Kaynağınız, geçiş yapmaya çalıştığınız olandan farklı bir bölgede oluşturulmuş olabilir.
* Bunun yerine LUO portalından yeni bir kaynak oluşturun.

**Yeni yazma kaynağı oluştur** seçeneğini belirlerseniz geçiş işlemi "kullanıcının Azure bilgileri alınamadı, daha sonra yeniden deneyin" hata iletisiyle başarısız olur.
* Aboneliğiniz, her abonelik için bölge başına 10 veya daha fazla yazma kaynağına sahip olabilir. Bu durumda, yeni bir yazma kaynağı oluşturamayacak.
* **Mevcut yazma kaynağını kullan** seçeneğini belirleyerek ve aboneliğinizdeki mevcut kaynaklardan birini seçerek geçiş yapın.

## <a name="create-new-support-request"></a>Yeni destek isteği oluştur

Geçişle ilgili sorun giderme bölümünde açıklanmayan bir sorun yaşıyorsanız, lütfen [bir destek konusu oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ve aşağıdaki alanlarla aşağıdaki bilgileri sağlayın:

   * **Sorun türü**: Teknik
   * **Abonelik**: açılan listeden bir abonelik seçin
   * **Hizmet**: "bilişsel hizmetler" i arayın ve seçin
   * **Kaynak**: var olan bir Luo kaynağı seçin. Aksi takdirde, genel soru ' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Yazma ve çalışma zamanı anahtarları hakkındaki kavramları](luis-how-to-azure-subscription.md) gözden geçirin
* [Anahtar atamayı](luis-how-to-azure-subscription.md) ve [katkıda bulunanlar eklemeyi](luis-how-to-collaborate.md) inceleyin