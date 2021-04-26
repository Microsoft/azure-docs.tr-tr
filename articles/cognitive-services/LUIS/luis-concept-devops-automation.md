---
title: LUSıS uygulamaları için sürekli iş akışları
description: Language Understanding (LUSıS) için DevOps için CI/CD iş akışlarını uygulama.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932531"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUSıS DevOps için sürekli tümleştirme ve sürekli teslim iş akışları

Language Understanding (LUU) uygulaması geliştiren yazılım mühendisleri, [kaynak denetimi](luis-concept-devops-sourcecontrol.md), [Otomatik derlemeler](luis-concept-devops-automation.md), [Test](luis-concept-devops-testing.md)ve [Release Management](luis-concept-devops-automation.md#release-management)uygulamalarında DevOps uygulamaları uygulayabilir. Bu makalede, LUSıS için otomatikleştirilmiş derlemeler uygulamaya yönelik kavramlar açıklanmaktadır.

## <a name="build-automation-workflows-for-luis"></a>LUSıS için derleme Otomasyonu iş akışları

![CI iş akışları](./media/luis-concept-devops-automation/luis-automation.png)

Kaynak kod Yönetimi (SCM) sisteminizde, otomatik derleme işlem hatlarını aşağıdaki olaylarda çalışacak şekilde yapılandırın:

1. [Çekme isteği](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) harekete geçirildiğinde tetiklenen **PR iş akışı** . Bu iş akışı, güncelleştirmeler Ana dala *birleştirilmeden önce* PR içeriğini doğrular.
1. Bir PR 'den değişiklikler birleştirilirken, örneğin, güncelleştirmeler Ana dala gönderildiğinde tetiklenen **CI/CD iş akışı** . Bu iş akışı, Ana daldaki tüm güncelleştirmelerin kalitesini sağlar.

**CI/CD iş akışı** , iki tamamlayıcı geliştirme işlemini birleştirir:

* [Sürekli tümleştirme](/azure/devops/learn/what-is-continuous-integration) (CI), paylaşılan bir depodaki kodu sık sık yürüten ve üzerinde otomatik bir yapı gerçekleştiren mühendislik uygulamasıdır. Otomatikleştirilmiş bir [Test](luis-concept-devops-testing.md) yaklaşımıyla eşleştirilmiş, sürekli tümleştirme her güncelleştirme Için, luaşağı kaynağın hala geçerli olduğunu ve bir Luo uygulamasına içeri aktarılabildiğini ve ayrıca eğitilen uygulamanın çözümünüz için gereken amaçları ve varlıkları tanıyabileceğini doğrulayan bir test grubu geçirmemizi sağlar.

* [Sürekli teslim](/azure/devops/learn/what-is-continuous-delivery) (CD), sürekli tümleştirme kavramını, uygulamayı daha ayrıntılı test yapabileceğiniz bir ortama otomatik olarak dağıtmak için daha fazla kullanır. CD, değişikliklerden en kısa sürede ortaya çıkan ve ayrıca test kapsamımızda boşluklar hakkında bilgi edinmek için sık görülen tüm sorunlar hakkında daha önce öğrenmemizi sağlar.

Sürekli tümleştirme ve sürekli teslimin amacı, "Main 'in her zaman shiprilebilir" olmasını sağlamaktır. Bir LUSıS uygulaması için bu, gerekirse, ana dal LUO uygulamasındaki herhangi bir sürümü alıp üretime teslim ettiğimiz anlamına gelir.

### <a name="tools-for-building-automation-workflows-for-luis"></a>LUSıS için Otomasyon iş akışları oluşturmaya yönelik araçlar

Derleme Otomasyonu iş akışları oluşturmak için kullanılabilecek farklı derleme Otomasyonu teknolojileri vardır. Tüm bunlar, bir yapı sunucusunda yürütebilmeleri için bir komut satırı arabirimi (CLı) veya REST çağrıları kullanarak adımlara komut dosyası oluşturmanızı gerektirir.

LUSıS için Otomasyon iş akışları oluşturmak üzere aşağıdaki araçları kullanın:

* [Bot Framework Tools lusıs](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) , lusıs uygulamaları ve sürümleri ile çalışmak, eğitme, test etme ve onları lusıs hizmeti içinde yayımlamaktır.

* [Azure 'da](/cli/azure/) Azure aboneliklerini sorgulama, lusıs yazma ve tahmin anahtarları getirme ve otomasyon kimlik doğrulaması için kullanılan bir Azure [hizmet sorumlusu](/cli/azure/ad/sp) oluşturma.

* [NLU. ](https://github.com/microsoft/NLU.DevOps) [Bir lusıs uygulamasını test](luis-concept-devops-testing.md) etmek ve test sonuçlarını çözümlemek için DevOps aracı.

### <a name="the-pr-workflow"></a>PR iş akışı

Belirtildiği gibi, bir geliştirici bir özellik dalından Ana dala birleştirilecek değişiklikler önermek üzere bir PR başlatıldığında bu iş akışını çalışacak şekilde yapılandırırsınız. Amaç, ana Dalla birleştirilmeden önce, çekme isteği içindeki değişikliklerin kalitesini doğrulamadır.

Bu iş akışı:

* Kaynağı PR 'ye aktararak geçici bir LUO uygulaması oluşturun `.lu` .
* LUSıS uygulama sürümünü eğitme ve yayımlama.
* Tüm [birim testlerini](luis-concept-devops-testing.md) buna karşı çalıştırın.
* Tüm testler başarılı olursa iş akışını geçirin, aksi takdirde başarısız olur.
* Geçici uygulamayı temizleyin ve silin.

SCM 'niz tarafından destekleniyorsa, çekme isteği tamamlanmadan önce bu iş akışının başarıyla tamamlanması için dal koruma kurallarını yapılandırın.

### <a name="the-main-branch-cicd-workflow"></a>Ana dal CI/CD iş akışı

PR 'deki güncelleştirmeler Ana dala birleştirildikten sonra bu iş akışını çalışacak şekilde yapılandırın. Bunun amacı, güncelleştirmeleri test ederek ana dalınızın kalite çubuğunu en yüksek düzeyde tutmamadır. Güncelleştirmeler kalite çubuğunu karşılıyorsa, bu iş akışı yeni LUO uygulama sürümünü, daha ayrıntılı test yapabileceğiniz bir ortama dağıtır.

Bu iş akışı:

* Güncelleştirilmiş kaynak kodu kullanarak birincil LUSıS uygulamanızda (ana dal için tuttuğunuz uygulama) yeni bir sürüm oluşturun.

* LUSıS uygulama sürümünü eğitme ve yayımlama.

  > [!NOTE]
  > [Otomatikleştirilmiş derleme iş akışında testleri çalıştırma](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) bölümünde açıklandığı gıbı, NLU gibi araçların test edilen lusıs uygulama sürümünü yayımlamanız gerekir. DevOps buna erişebilir. Lua, bir LUO uygulaması için yalnızca iki adet adlandırılmış yayın yuvası, *hazırlama* ve *üretimi* destekler, ancak aynı zamanda [bir sürümü doğrudan ve bir](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) sürüme [göre sorgulama](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name)de yapabilirsiniz. Adlandırılmış yayımlama yuvalarını kullanmayla sınırlı olmamak için Otomasyon iş akışlarınızda doğrudan sürüm yayımlamayı kullanın.

* Tüm [birim testlerini](luis-concept-devops-testing.md)çalıştırın.

* İsteğe bağlı olarak, LUSıS uygulamasının sürümünün kalitesini ve doğruluğunu ölçmek için [toplu testleri](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) çalıştırın ve onu bir taban çizgisiyle karşılaştırın.

* Testler başarıyla tamamlandıysanız:
  * Kaynağı depoda etiketleyin.
  * Daha fazla test için LUSıS uygulama sürümünü ortamlara dağıtmak üzere sürekli teslim (CD) işini çalıştırın.

### <a name="continuous-delivery-cd"></a>Sürekli teslim (CD)

Bir CI/CD iş akışındaki CD işi, derleme ve otomatik birim testlerinin başarısı üzerinde koşullu olarak çalışır. İşi, LUO uygulamasını daha fazla test yapabileceğiniz bir ortama otomatik olarak dağıtmaktır.

LUSıS uygulamanızın ne kadar iyi dağıtılacağı konusunda önerilen bir çözüm yoktur ve projeniz için uygun olan işlemi uygulamanız gerekir. [Luın DevOps şablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) deposu, [Yeni Luo uygulama sürümünü](./luis-how-to-publish-app.md) *Üretim* yayımlama yuvasına yayımlamak için kullanabileceğiniz basit bir çözüm uygular. Bu, basit bir kurulum için uygundur. Ancak, *geliştirme*, *hazırlama* ve *UAT* gibi farklı üretim ortamlarının sayısını aynı anda desteklemeniz gerekiyorsa, uygulama başına iki adet adlandırılmış yayımlama yuvası sınırı yetersizdir.

Uygulama sürümünü dağıtmaya yönelik diğer seçenekler şunlardır:

* Uygulama sürümünü doğrudan sürüm uç noktasında yayımlanmış bırakın ve doğrudan sürüm uç noktasıyla gerektiği şekilde aşağı akış üretim ortamlarını yapılandırmak için bir işlem uygulayın.
* Her üretim ortamları için farklı LUSıS uygulamalarını koruyun ve `.lu` uygulamayı hedef üretim ortamına YÖNELIK Luo uygulamasında yeni bir sürüme aktarmak, eğitme ve yayımlamak için Otomasyon adımlarını yazın.
* Sınanan LUSıS uygulama sürümünü bir [lusıs Docker kapsayıcısına](./luis-container-howto.md?tabs=v3) AKTARıN ve Lusıs kapsayıcısını Azure [Container Instances](../../container-instances/index.yml)'a dağıtın.

## <a name="release-management"></a>Yayın yönetimi

Genellikle, geliştirme ve hazırlama gibi üretim dışı ortamlarınız için sürekli teslim yapmanızı öneririz. Çoğu takım, bir üretim ortamına dağıtım için el ile gözden geçirme ve onay süreci gerektirir. Bir üretim dağıtımı için, geliştirme ekibinizdeki önemli kişilerin destek için kullanılabilir olduğu veya düşük trafikli dönemler sırasında olduğundan emin olmak isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub Ile lusıs Için DevOps 'u nasıl uygulayacağınızı](luis-how-to-devops-with-github.md) öğrenin
* [NLU Ile GitHub eylemleri iş akışını yazmayı öğrenin. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
