---
title: Kaynak denetimi ve geliştirme dalları-LUSıS
description: Kaynak denetimi altında Language Understanding (LUSıS) uygulamanızı koruma. Bir geliştirme dalında çalışırken bir LUO uygulamasına güncelleştirmeleri uygulama.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932523"
---
# <a name="devops-practices-for-luis"></a>LUSıS için DevOps uygulamaları

Language Understanding (LUU) uygulaması geliştiren yazılım mühendisleri, bu yönergeleri izleyerek [kaynak denetimi](luis-concept-devops-sourcecontrol.md), [Otomatik derlemeler](luis-concept-devops-automation.md), [Test](luis-concept-devops-testing.md)ve [Sürüm Yönetimi](luis-concept-devops-automation.md#release-management) etrafında DevOps uygulamaları uygulayabilir.

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS için kaynak denetimi ve dal stratejileri

DevOps başarısının başarımının, [kaynak denetimi](/azure/devops/user-guide/source-control)olduğuna göre önemli faktörlerden biridir. Kaynak denetim sistemi, geliştiricilerin kod üzerinde işbirliği yapmasına ve değişiklikleri izlemesine olanak sağlar. Dalların kullanımı, geliştiricilerin kod tabanının farklı sürümleri arasında geçiş yapmasına ve takımın diğer üyelerinden bağımsız olarak çalışmasına izin verir. Geliştiriciler bir daldan diğerine güncelleştirme önermek üzere bir [çekme isteği](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) oluşturduğunda veya değişiklikler birleştirildiğinde, bu, [Otomatik derlemeler](luis-concept-devops-automation.md) için kod oluşturmak ve sürekli test etmek üzere tetikler olabilir.

Bu belgede açıklanan kavramları ve yönergeleri kullanarak, bir kaynak denetim sistemindeki değişiklikleri izlerken bir LUO uygulaması geliştirebilir ve bu yazılım mühendisliği en iyi yöntemlerini takip edebilirsiniz:

- **Kaynak Denetimi**
  - LUSıS uygulamanız için kaynak kodu, okunabilir bir biçimde.
  - Model, kaynaktan yinelenebilir bir biçimde oluşturulabilir.
  - Kaynak kodu bir kaynak kod deposu tarafından yönetilebilir.
  - Yazma ve abonelik anahtarları gibi kimlik bilgileri ve gizlilikler hiçbir şekilde kaynak kodunda depolanmaz.

- **Dallandırma ve birleştirme**
  - Geliştiriciler bağımsız dallardan çalışabilir.
  - Geliştiriciler birden çok dalda eşzamanlı olarak çalışabilir.
  - Yeniden temellendirme veya Merge aracılığıyla bir daldaki bir dalla yapılan değişiklikleri bir dalla bütünleştirmek mümkündür.
  - Geliştiriciler bir PR 'yi üst dala birleştirebilirler.

- **Sürüm Oluşturma**
  - Büyük bir uygulamadaki her bileşeni bağımsız olarak sürümlenmiş olmalıdır, böylece geliştiricilerin sürüm numarasına bakarak yalnızca önemli değişiklikleri veya güncelleştirmeleri algılamasına izin verir.

- **Kod Incelemeleri**
  - PR 'deki değişiklikler, çekme isteğini kabul etmeden önce incelenebilecek insan tarafından okunabilen kaynak kodu olarak sunulur.

## <a name="source-control"></a>Kaynak denetimi

Kaynak kod yönetimi sisteminde bir LUO uygulamasının [uygulama şeması tanımını](./app-schema-definition.md) korumak için, uygulamanın [LUI biçimi ( `.lu` )](/azure/bot-service/file-format/bot-builder-lu-file-format)  gösterimini kullanın. `.lu` biçimlendirme `.json` , okunabilir olduğundan, PR 'ler 'de değişiklikleri daha kolay hale getirmek ve gözden geçirmek daha kolay hale getiren biçim için tercih edilir.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Luo uygulamasını Lui biçimini kullanarak kaydetme

Bir LUO uygulamasını `.lu` biçimde kaydetmek ve kaynak denetimi altına yerleştirmek için:

- Şunlardan biri: [uygulama sürümünü](./luis-how-to-manage-versions.md#other-actions) `.lu` [Lusıs portalından](https://www.luis.ai/) dışarı aktarın ve kaynak denetimi deponuza ekleyin

- YA da: bir `.lu` lusıs uygulaması için dosya oluşturmak ve kaynak denetimi deponuza eklemek için bir metin düzenleyicisi kullanın

> [!TIP]
> Bir LUO uygulamasının JSON dışarı aktarılmasına çalışıyorsanız, [bunu Luaşağı dönüştürebilirsiniz](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert).  `--sort`Amaçları ve dıklarınızı alfabetik olarak sıralanmış olduğundan emin olmak için seçeneğini kullanın.  
> **.** Luu portalına YERLEŞTIRILMIŞ lu dışarı aktarma özelliği çıktıyı zaten sıralıyor.

### <a name="build-the-luis-app-from-source"></a>Kaynaktan LUSıS uygulamasını oluşturma

Bir LUSıS uygulaması için kaynaktan *derleme* yapmak, kaynağı içeri aktararak [sürümü eğitmek](./luis-how-to-train.md) ve [yayımlamak](./luis-how-to-publish-app.md)için [Yeni bir Luo uygulama `.lu` sürümü oluşturma](./luis-how-to-manage-versions.md#import-version) anlamına gelir. Bunu, LUO portalında veya komut satırında yapabilirsiniz:

- Kaynak denetiminden uygulamanın [ `.lu` sürümünü içeri aktarmak](./luis-how-to-manage-versions.md#import-version) ve uygulamayı [eğit](./luis-how-to-train.md) ve [yayımlamak](./luis-how-to-publish-app.md) için Luo portalını kullanın.

- Uygulamanın sürümünü kaynak denetiminden bir LUO uygulamasına [aktarmak](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) ve uygulamayı eğitme ve yayımlama için, komut satırında veya bir CI/CD Iş akışında [luya Için bot Framework komut satırı arabirimini](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) kullanın `.lu` . [](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) [](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)

### <a name="files-to-maintain-under-source-control"></a>Kaynak denetimi altında korunacak dosyalar

LUSıS uygulamanız için aşağıdaki dosya türleri kaynak denetimi altında tutulmalıdır:

- `.lu` LUSıS uygulaması dosyası

- [Birim testi tanım dosyaları](luis-concept-devops-testing.md#writing-tests) (utterslar ve beklenen sonuçlar)

- Performans testi için kullanılan [toplu işlem test dosyaları](./luis-how-to-batch-test.md#batch-test-file) (utterslar ve beklenen sonuçlar)

### <a name="credentials-and-keys-are-not-checked-in"></a>Kimlik bilgileri ve anahtarlar iade edilmedi

Abonelik anahtarlarını veya benzer gizli değerleri, deponuzda, yetkisiz personele görünür olabileceği durumlarda iade ettiğiniz dosyalara eklemeyin. İadeden engellemeniz gereken anahtarlar ve diğer değerler şunlardır:

- LUSıS yazma ve tahmin anahtarları
- LUSıS yazma ve tahmin uç noktaları
- Azure abonelik anahtarları
- Otomasyon kimlik doğrulaması için kullanılan bir Azure [hizmet sorumlusu](/cli/azure/ad/sp) belirteci gibi erişim belirteçleri

#### <a name="strategies-for-securely-managing-secrets"></a>Gizli dizileri güvenli bir şekilde yönetme stratejileri

Gizli dizileri güvenli bir şekilde yönetme stratejileri şunları içerir:

- Git sürüm denetimi kullanıyorsanız, çalışma zamanı gizli dizilerini yerel bir dosyada saklayabilir ve dosya adını bir [. gitignore](https://git-scm.com/docs/gitignore) dosyasına eşlemek için bir model ekleyerek dosyayı iade edebilirsiniz.
- Otomasyon iş akışında gizli dizileri, bu Otomasyon teknolojisi tarafından sunulan parametreler yapılandırmasında güvenli bir şekilde depolayabilirsiniz. Örneğin, [GitHub eylemleri](https://github.com/features/actions)kullanıyorsanız, gizli dizileri [GitHub gizli](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)dizileri ' nde güvenli bir şekilde depolayabilirsiniz.

## <a name="branching-and-merging"></a>Dallandırma ve birleştirme

Git gibi dağıtılmış sürüm denetim sistemleri, ekip üyelerinin başkalarıyla paylaşılan geliştirme dallarından kod değişikliklerini yayımlama, paylaşma, gözden geçirme ve yineleme konusunda esneklik sağlar. Takımınız için uygun olan bir [Git Dallanma stratejisi](/azure/devops/repos/git/git-branching-guidance) benimseyin.

Hangi dallanma stratejinizin olduğuna göre, bunların tümünün önemli bir prensibi, takım üyelerinin, bir *özellik dalında* çözüm üzerinde diğer dallarda yer alan iş 'ten bağımsız olarak çalışabilmesini sağlayabilir.

Bir LUO projesi ile bağımsız dallarda bağımsız çalışmayı desteklemek için:

- **Ana dalın kendi LUO uygulaması vardır.** Bu uygulama, projeniz için çözümünüzün geçerli durumunu temsil eder ve geçerli etkin sürümü her zaman `.lu` ana dalda olan kaynakla eşlenmelidir. Bu uygulamanın kaynağı için tüm güncelleştirmeler `.lu` incelenmeli ve test edilmelidir, böylece bu uygulama herhangi bir zamanda üretim gibi ortamlara dağıtılabilir. Üzerinde yapılan güncelleştirmeler `.lu` bir özellik dalından Main ile birleştirildiğinde, Luo uygulamasında yeni bir sürüm oluşturmanız ve [sürüm numarasını çarpmalısınız](#versioning).

- **Her özellik dalı, BIR lusıs uygulamasının kendi örneğini kullanmalıdır**. Geliştiriciler, diğer dallarda çalışan geliştiricilerin etkilenmelerine gerek kalmadan bu uygulamayla bir özellik dalında çalışır. Bu ' dev Branch ' uygulaması, özellik dalı silindiğinde silinmesi gereken bir çalışma kopyasıdır.

![Git özelliği dalı](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Geliştiriciler bağımsız dallardan çalışabilir

Geliştiriciler bir LUO uygulamasındaki güncelleştirmeler üzerinde diğer dallardan bağımsız olarak şu şekilde çalışabilir:

1. Ana daldan bir özellik dalı oluşturma (dal stratejinize, genellikle ana veya geliştirmeden) göre.

1. LUO portalında ("*dev Branch App*") yalnızca özellik dalındaki işi desteklemek için [Yeni bir Luo uygulaması oluşturun](./luis-how-to-start-new-app.md) .

   * `.lu`Çözümünüz için kaynak dalınızda zaten mevcutsa, bu, projede daha önce başka bir dalda yapıldıktan sonra kaydedildiğinden, dosyayı içeri aktararak dev Branch lusıs uygulamanızı oluşturun `.lu` .

   * Yeni bir proje üzerinde iş başlatıyorsanız, `.lu` depoya henüz ana lusıs uygulamanız için kaynağınız olmayacaktır. `.lu`Özellik dalı işinizi tamamladıktan sonra, bu dosyayı, geliştirme dalı uygulamanızı portaldan dışarı aktararak oluşturacaksınız ve bunu çekme isteğinin bir parçası olarak gönderebilirsiniz.

1. Gerekli değişiklikleri uygulamak için geliştirme dalı uygulamanızın etkin sürümünde çalışın. Tüm özellik dalı çalışması için yalnızca geliştirme dalı uygulamanızın tek bir sürümünde çalışmanız önerilir. Geliştirme Dalı uygulamanızda birden fazla sürüm oluşturursanız, çekme kodunuzu yükselttiğinizde iade etmek istediğiniz değişiklikleri içeren sürümü izlemeniz konusunda dikkatli olun.

1. Güncelleştirmeleri test et-geliştirme dalı uygulamanızı test etme hakkında daha fazla bilgi için bkz. [Lusıs DevOps Için test etme](luis-concept-devops-testing.md) .

1. Geliştirme Dalı uygulamanızın etkin sürümünü `.lu` [sürümler listesinden](./luis-how-to-manage-versions.md)dışa aktarın.

1. Güncelleştirmelerinizi iade edin ve güncelleştirmelerinizin eş incelemesini gözden geçirin. GitHub kullanıyorsanız, bir [çekme isteği](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)oluşturacaksınız.

1. Değişiklikler onaylandığında, güncelleştirmeleri ana dalda birleştirin. Bu noktada, *ana Luo* uygulamasının yeni bir [sürümünü](./luis-how-to-manage-versions.md) oluşturacaksınız `.lu` . Sürüm adını ayarlamayla ilgili konular için bkz. sürüm [oluşturma](#versioning) .

1. Özellik dalı silindiğinde, özellik dalı çalışması için oluşturduğunuz dev dalı LUO uygulamasını silmek iyi bir fikirdir.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Geliştiriciler eşzamanlı olarak birden çok dalda çalışabilir

[Geliştiriciler bağımsız dallardan çalışarak](#developers-can-work-from-independent-branches)yukarıda açıklanan kalıbı izlerseniz, her bir özellik dalında BENZERSIZ bir Luo uygulaması kullanırsınız. Tek bir geliştirici, üzerinde çalışmakta olduğu dala yönelik doğru dev dalı LUO uygulamasına geçiş yaptıkları sürece eşzamanlı olarak birden çok dalda çalışabilir.

Özellik dalı için aynı adı ve özellik dalı için oluşturduğunuz dev dalı LUSıS uygulamasını, yanlışlıkla yanlış uygulama üzerinde çalışmanıza daha az bir hale getirmek için kullanmanızı öneririz.

Yukarıda belirtildiği gibi, kolaylık sağlamak için her bir geliştirme dalı uygulamasındaki tek bir sürümde çalışmanız önerilir. Birden çok sürüm kullanıyorsanız, geliştirme dalı uygulamaları arasında geçiş yaparken doğru sürümü etkinleştirmeyi dikkate alın.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Birden çok geliştirici aynı dalda aynı anda çalışabilir

Aynı özellik dalında çalışan birden çok geliştiricisi aynı anda destekleyebilirsiniz:

- Geliştiriciler, iş yaptığı sırada normal olarak ilerledikçe aynı özellik dalını ve kendilerini ve diğer geliştiricilerden gönderilen itme ve çekme değişikliklerini gözden geçirir.

- [Geliştiriciler bağımsız dallardan çalışarak](#developers-can-work-from-independent-branches)yukarıda açıklanan kalıbı izlerseniz, bu dal geliştirmeyi desteklemek için BENZERSIZ bir Luo uygulaması kullanacaktır. Bu ' dev Branch ' LUO uygulaması, özellik dalında çalışmaya başlayan geliştirme ekibinin ilk üyesi tarafından oluşturulacaktır.

- [Takım üyelerini](./luis-how-to-collaborate.md) GELIŞTIRME dalı lusıs uygulamasına katkıda bulunan olarak ekleyin.

- Özellik dalı işi tamamlandığında, dev dalı LUO uygulamasının etkin sürümünü `.lu` [sürümler listesinden](./luis-how-to-manage-versions.md)dışarı aktarın, güncelleştirilmiş `.lu` dosyayı depoya kaydedin ve DEĞIŞIKLIKLERI iade edin ve PR 'yi gözden geçirin.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Yeniden temellendirme veya birleştirme ile bir daldaki değişiklikleri diğerine ekleme

Takımınızın başka bir dalda çalıştığı bazı diğer geliştiriciler, kaynak üzerinde güncelleştirme yapmış `.lu` ve bunları Özellik dalınızı oluşturduktan sonra Ana dala birleştirmiş olabilir. Özellik dalınızda kendi değişikliklerini yapmaya devam etmeden önce, değişikliklerini çalışma sürümünüzle birleştirmek isteyebilirsiniz. Bunu, diğer kod varlıklarınızla aynı şekilde yeniden [temellendirerek veya birleştirerek](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) yapabilirsiniz. Luu biçimindeki LUıN uygulaması insan tarafından okunabilir olduğundan, standart birleştirme araçları kullanılarak birleştirmeyi destekler.

LUSıS uygulamanızı bir özellik dalında yeniden dayandırdığınızda bu ipuçlarını izleyin:

- Yeniden temellendirmeden veya birleştirmeden önce, uygulamanızın kaynak kopyasının, `.lu` , uygulamanızı portaldan yeniden dışa AKTARARAK Luo portalını kullanarak uyguladığınız en son değişiklerinizi içerdiğinden emin olun. Bu şekilde, portalda yaptığınız ve henüz dışarıya aktarılmamış tüm değişikliklerin kaybolmamasını sağlayabilirsiniz.

- Birleştirme sırasında, tüm birleştirme çakışmalarını çözmek için standart araçları kullanın.

- Yeniden temellendirme veya birleştirme işlemi tamamlandıktan sonra, uygulamayı portala yeniden içeri aktarmak için, kendi değişikliklerinizi uygulamaya devam ederken güncelleştirilmiş uygulamayla çalışmaya başladıktan sonra unutmayın.

### <a name="merge-prs"></a>Birleştirme PR 'ler

Çekme isteği onaylandıktan sonra, yaptığınız değişiklikleri ana dalınızla birleştirebilirsiniz. Bir LUO uygulaması için Luaşağı kaynağı için özel bir önemli noktalar yoktur: insanlar okunabilir ve bu nedenle standart birleştirme araçlarını kullanarak birleştirmeyi destekler. Tüm birleştirme çakışmaları diğer kaynak dosyalarla aynı şekilde çözülebilir.

Çekme isteği birleştirildikten sonra temizlik yapmanız önerilir:

- Deponuzdaki dalı silme

- Özellik dalı çalışması için oluşturduğunuz ' dev Branch ' LUO uygulamasını silin.

Uygulama kodu varlıklarıyla aynı şekilde, LUSıS uygulama güncelleştirmeleriyle birlikte birim testlerini yazmanız gerekir. Test etmek için sürekli tümleştirme iş akışları kullanmalısınız:

- Çekme isteği birleştirilmeden önce bir PR 'deki güncelleştirmeler
- Ana dal, bir çekme isteği onaylandıktan ve değişiklikler Main ile birleştirildikten sonra bir uygulamadır.

LUO DevOps için test etme hakkında daha fazla bilgi için bkz. [Lufor DevOps Için test etme](luis-concept-devops-testing.md). İş akışlarını uygulama hakkında daha fazla bilgi için bkz. [Lusıs DevOps Için Otomasyon iş akışları](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Kod incelemeleri

Luu biçimindeki bir LUO uygulaması, gözden geçirime uygun bir PR 'deki değişikliklerin iletişimini destekleyen insandır. Birim test dosyaları da Luaşağı biçiminde yazılır ve ayrıca bir PR 'de kolayca yeniden görüntülenebilir.

## <a name="versioning"></a>Sürüm Oluşturma

Bir uygulama, [Azure bot hizmetinde](/azure/bot-service/bot-service-overview-introduction)çalışan bir bot, [soru-cevap oluşturma](https://www.qnamaker.ai/), [Azure konuşma hizmeti](../speech-service/overview.md)ve daha fazlası gibi işlemleri içerebilen birden çok bileşenden oluşur. Gevşek olarak bağlanmış uygulamaların amacını elde etmek için, bir uygulamanın her bileşeninin bağımsız olarak sürümü oluşturulmuş olması için [Sürüm denetimini](/azure/devops/learn/git/what-is-version-control) kullanın, böylece geliştiricilerin sürüm numarasına bakarak, önemli değişiklikleri veya güncelleştirmeleri algılamasına izin vermiş olur. LUSıS uygulamanızı kendi depolarından korumak için diğer bileşenlerden bağımsız olarak kolayca yükleyebilirsiniz.

Ana dala yönelik Lua uygulamasının bir sürüm oluşturma düzeni uygulanmış olması gerekir. `.lu`BIR Luo uygulaması için güncelleştirmelerini Main olarak birleştirdiğinizde, bu güncelleştirilmiş kaynağı, ana dal IÇIN Luo uygulamasındaki yeni bir sürüme içeri aktarırsınız.

Ana LUO uygulama sürümü için sayısal sürüm oluşturma düzeni kullanmanız önerilir, örneğin:

`major.minor[.build[.revision]]`

Sürüm numarası her güncelleştirme, son basamakla artırılır.

Birincil/ikincil sürüm, LUSıS uygulama işlevindeki değişikliklerin kapsamını göstermek için kullanılabilir:

* Ana sürüm: yeni bir [Amaç](./luis-concept-intent.md) veya [varlık](./luis-concept-entity-types.md) desteği gibi önemli bir değişiklik
* İkincil sürüm: önemli yeni eğitim sonrası gibi geriye dönük uyumlu küçük bir değişiklik
* Build: işlev değişikliği yok, yalnızca farklı bir derleme.

Ana LUSıS uygulamanızın en son düzeltmesi için sürüm numarasını belirledikten sonra, yeni uygulama sürümünü derleyip test etmeniz ve bu sürümü, kalite güvencesi veya üretim gibi farklı derleme ortamlarında kullanılabilecek bir uç noktada yayımlamanız gerekir. Bu adımların hepsini bir sürekli tümleştirme (CI) iş akışında otomatikleştirmeniz kesinlikle önerilir.

Bkz.
- Bir LUO uygulamasını test etmek ve serbest bırakmak için bir CI iş akışının nasıl uygulanacağı hakkındaki ayrıntılar için [Otomasyon iş akışları](luis-concept-devops-automation.md) .
- LUSıS uygulamanızı dağıtma hakkında daha fazla bilgi için [Release Management](luis-concept-devops-automation.md#release-management) .

### <a name="versioning-the-feature-branch-luis-app"></a>' Özellik Dalı ' LUSıS uygulamasının sürümü oluşturma

Bir özellik dalında çalışmayı desteklemek için oluşturduğunuz bir ' dev Branch ' LUO uygulaması ile çalışırken, çalışmanız tamamlandığında uygulamanızı dışarı aktaracaksınız ve bu uygulamayı çekme isteğinize dahil edersiniz `'lu` . Deponuzdaki dal ve ' dev Branch ' LUIN uygulaması, çekme isteği Main ile birleştirildikten sonra silinmelidir. Bu uygulama yalnızca özellik dalındaki işi desteklemek için mevcut olduğundan, bu uygulama içinde uygulamanız gereken belirli bir sürüm oluşturma düzeni yoktur.

Çekme ortamınızda yaptığınız değişiklikler Main ile birleştirildiğinde, bu, sürüm oluşturma uygulanması gerektiği için, ana için tüm güncelleştirmelerin bağımsız olarak sürümü oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

* [Luo DevOps için test etme](luis-concept-devops-testing.md) hakkında bilgi edinin
* [GitHub Ile lusıs Için DevOps 'u nasıl uygulayacağınızı](luis-how-to-devops-with-github.md) öğrenin
