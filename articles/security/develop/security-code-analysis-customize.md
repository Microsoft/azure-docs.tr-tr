---
title: Microsoft Azure Güvenlik kodu analiz görevi Özelleştirme Kılavuzu
description: Bu makalede, Microsoft Güvenlik kodu analiz uzantısı 'ndaki görevlerin özelleştirilmesi açıklanmaktadır
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241808"
---
# <a name="configure-and-customize-the-build-tasks"></a>Derleme görevlerini yapılandırma ve özelleştirme

Bu makalede, her derleme görevinin her birinde kullanılabilen yapılandırma seçeneklerini ayrıntılı olarak açıklanmaktadır. Makale, güvenlik kodu analiz araçları görevleriyle başlar. İşlem sonrası görevlerle biter.

## <a name="anti-malware-scanner-task"></a>Kötü amaçlı yazılımdan koruma tarayıcı görevi

>[!NOTE]
> Kötü amaçlı yazılımdan koruma tarayıcı oluşturma görevi, Windows Defender 'ın etkinleştirildiği bir derleme Aracısı gerektirir. Barındırılan Visual Studio 2017 ve üzeri, böyle bir aracı sağlar. Derleme görevi, Visual Studio 2015 barındırılan aracısında çalışmaz.
>
> İmzalar bu aracılarda güncelleştirilemeyebilir, ancak imzaların her zaman üç saat öncesine eşit olması gerekir.

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve metinde gösterilmiştir.

![Kötü amaçlı yazılımdan koruma tarayıcı oluşturma görevini yapılandırma](./media/security-tools/5-add-anti-malware-task600.png)

Ekran görüntüsünün **tür** listesi kutusunda **temel** seçilidir. Taramayı özelleştiren komut satırı bağımsız değişkenlerini sağlamak için **özel** ' i seçin.

Windows Defender, imzaları indirmek ve yüklemek için Windows Update istemcisini kullanır. Derleme aracısında imza güncellemesi başarısız olursa, **HRESULT** hata kodu muhtemelen Windows Update geliyor.

Windows Update hatalar ve bunların hafifletme hakkında daha fazla bilgi için bkz. [bileşene göre Windows Update hata kodları](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) ve [Windows Update aracı hata kodları](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)hakkında TechNet makalesi.

## <a name="binskim-task"></a>Binskım görevi

> [!NOTE]
> BinSkim görevini çalıştırmadan önce, derlemeniz şu koşullardan birini karşılamalıdır:
>    - Derlemeniz yönetilen koddan ikili yapıtlar üretir.
>    - Binskım ile analiz etmek istediğiniz ikili yapıtlar vardır.

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![BinSkim derleme görevini yapılandırma](./media/security-tools/7-binskim-task-details.png)

- Derleme yapılandırmasını hata ayıklama olarak ayarlayın. pdb hata ayıklama dosyaları oluşturulur. Binskım, çıkış ikilileriyle ilgili sorunları kaynak koduna geri eşlemek için bu dosyaları kullanır.
- Araştırmamak ve kendi Komut satırlarınızı oluşturmak için:
     - **Tür** listesinde **temel**' yı seçin.
     - **İşlev** listesinde, **Çözümle**' yi seçin.
- **Hedef**bölümünde, bir dosya, dizin veya filtre deseninin bir veya daha fazla belirticilerini girin. Bu tanımlayıcılar çözümlenecek bir veya daha fazla ikiliye çözümlenmelidir:
    - Birden çok belirtilen hedefin noktalı virgülle ayrılması gerekir (;).
    - Belirleyici tek bir dosya olabilir veya joker karakterler içerebilir.
    - Dizin belirtimlerinin her zaman * \\ile bitmesi gerekir.
    - Örnekler:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- **Tür** listesinde **komut satırı** ' nı seçerseniz, binskim. exe ' yi çalıştırmanız gerekir:
     - Binskım. exe ' ye yönelik ilk bağımsız değişkenlerin **, izlenen bir** veya daha fazla yol belirtimlerinden sonra olduğundan emin olun. Her yol, kaynak dizine göre tam yol veya yol olabilir.
     - Birden çok hedef yol bir boşluk ile ayrılmalıdır.
     - **/O** veya **/output** seçeneğini atlayabilirsiniz. Çıkış değeri sizin için eklenir veya değiştirilmez.
     - Standart komut satırı yapılandırması aşağıdaki gibi gösterilmiştir.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Hedef için \\dizinler belirtirseniz sonda * önemlidir.

BinSkim komut satırı bağımsız değişkenleri, KIMLIĞE göre kurallar veya çıkış kodları hakkında daha fazla bilgi için bkz. [Binskim Kullanıcı Kılavuzu](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

## <a name="credential-scanner-task"></a>Kimlik bilgisi tarayıcı görevi

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Kimlik bilgisi tarayıcısı derleme görevini yapılandırma](./media/security-tools/3-taskdetails.png)

Kullanılabilir seçenekler şunlardır:

  - **Çıkış biçimi**: Kullanılabilir değerler **TSV**, **CSV**, **Sarif**ve **PREfast**' i içerir.
  - **Araç sürümü**: **En son**' u seçmenizi öneririz.
  - **Tarama klasörü**: Taranacak depo klasörü.
  - **Aramacılar dosya türü**: Tarama için kullanılan Searchers dosyasını bulma seçenekleri.
  - **Suppressions dosyası**: [JSON](https://json.org/) dosyası çıkış günlüğündeki sorunları engelleyebilir. Gizleme senaryoları hakkında daha fazla bilgi için bu makalenin SSS bölümüne bakın.
  - **Ayrıntılı çıkış**: Kendi kendine açıklama.
  - **Toplu Iş boyutu**: Kimlik bilgisi tarayıcısını çalıştırmak için kullanılan eşzamanlı iş parçacıklarının sayısı. Varsayılan değer 20'dir. Olası değerler 1 ila 2.147.483.647 arasındadır.
  - **Eşleşme zaman aşımı**: Denetimi bırakmadan önce bir arama eşleşmesi gerçekleştirmeye harcanan saniye cinsinden süre.
  - **Dosya taraması okuma arabelleği boyutu**: İçerik okunurken kullanılan arabelleğin bayt cinsinden boyutu. Varsayılan değer 524.288 ' dir.  
  - **En fazla dosya taraması okunan bayt**: İçerik analizi sırasında bir dosyadan okunacak en fazla bayt sayısı. Varsayılan değer 104.857.600 ' dir.
  - **Denetim seçenekleri** > **Bu görevi çalıştır**: Görevin ne zaman çalışacağını belirtir. Daha karmaşık koşullar belirtmek için **özel koşullar** ' ı seçin.
  - **Sürüm**: Azure DevOps içindeki derleme görevi sürümü. Bu seçenek sıklıkla kullanılmaz.

## <a name="microsoft-security-risk-detection-task"></a>Microsoft güvenlik riski algılama görevi

> [!NOTE]
> MSRD görevini kullanmadan önce Microsoft güvenlik riski algılama (MSRD) hizmeti ile bir hesap oluşturmanız ve yapılandırmanız gerekir. Bu hizmet ayrı bir ekleme işlemi gerektirir. Bu uzantıdaki birçok görevin aksine, bu görev MSRD ile ayrı bir abonelik gerektirir.
>
> Lütfen [Microsoft güvenlik riski algılama](https://aka.ms/msrddocs) ve Microsoft Güvenlik [riski algılama ' ye başvurun: ](https://docs.microsoft.com/security-risk-detection/how-to/) Yönergeler için.

Bu görevi yapılandırma ayrıntıları aşağıdaki listede gösterilmektedir. Herhangi bir kullanıcı arabirimi öğesi için, yardım almak için bu öğenin üzerine gelin.

   - **MSRD Için Azure DevOps hizmeti uç noktası adı**: Azure DevOps hizmet uç noktası 'nın genel bir türü, eklendi MSRD örnek URL 'nizi ve REST API erişim belirtecinizi depolar. Böyle bir uç nokta oluşturduysanız, burada belirtebilirsiniz. Aksi takdirde, bu MSRD görevi için yeni bir hizmet uç noktası oluşturmak ve yapılandırmak üzere **Yönet** bağlantısını seçin.
   - **Hesap kimliği**: MSRD hesap URL 'sinden alınabilecek bir GUID.
   - **Ikililerin URL 'leri**: Genel olarak kullanılabilir URL 'lerin noktalı virgülle ayrılmış listesi. Bu URL, ikili dosyaları indirmek için bu URL 'Leri kullanır.
   - **Çekirdek dosyalarının URL 'leri**: Genel olarak kullanılabilir URL 'lerin noktalı virgülle ayrılmış listesi. Belirsizlik makinesi, bu URL 'Leri kullanarak Seeds 'yi indirir. Çekirdek dosyalar ikili dosyalarla birlikte indirildiyse bu değerin belirtilmesi isteğe bağlıdır.
   - **Işletim sistemi platformu türü**: Belirsizlik işini çalıştıran makinelerin işletim sistemi (OS) platformu. Kullanılabilir değerler **Windows** ve **Linux**' dir.
   - **Windows sürümü/Linux sürümü**: Belirsizlik işini çalıştıran makinelerin işletim sistemi sürümü. Makinelerinizde farklı bir işletim sistemi sürümü varsa varsayılan değerin üzerine yazabilirsiniz.
   - **Paket yükleme betiği**: Bir test makinesinde çalıştırılacak komut dosyası. Bu betik, belirsizlik işi gönderilmeden önce test hedef programını ve bağımlılıklarını yüklenir.
   - **Iş gönderme parametreleri**:
       - **Çekirdek Dizin**: Seeds 'yi içeren bir makinedeki dizin yolu.
       - **Çekirdek uzantısı**: Seeds 'nin dosya adı uzantısı.
       - **Test sürücüsü yürütülebilir dosyası**: Hedeflenen makinedeki hedef yürütülebilir dosyanın yolu.
       - **Test sürücüsü yürütülebilir mimarisi**: Hedef yürütülebilir dosyanın mimarisi. Kullanılabilir değerler **x86** ve **AMD64**' dir.
       - **Test sürücüsü bağımsız değişkenleri**: Test yürütülebilir dosyasına geçirilen komut satırı bağımsız değişkenleri. Tırnak işaretleri de dahil olmak üzere "% TestFile%" bağımsız değişkeni otomatik olarak hedef dosyanın tam yolu ile değiştiriliyor. Bu dosya, test sürücüsü tarafından ayrıştırılır ve gereklidir.
       - Test **sürücüsü Işlemi test tamamlandıktan sonra çıkıyor**: Sınama sürücüsünün tamamlandığında sonlandırılacağı bu onay kutusunu seçin. Sınama sürücüsünün zorla kapatılması gerekiyorsa, temizleyin.
       - **En uzun süre (saniye cinsinden)** : Hedef programın bir giriş dosyasını ayrıştırmak için gereken en uzun tahmini beklenen zaman. Tahmine göre daha doğru, uygulamanın düzgün çalıştığı daha verimli bir şekilde çalışır.
       - **Test sürücüsü sürekli çalıştırılabilir**: Bu onay kutusunu, test sürücüsü kalıcı veya paylaşılan bir genel duruma bağlı olarak tekrar tekrar çalıştırılabilmesi durumunda seçin.
       - **Test sürücüsü yeniden adlandırılabilir**: Test sürücüsü yürütülebilir dosyası yeniden adlandırılabilir ve yine de düzgün şekilde çalışılabilmezseniz bu onay kutusunu işaretleyin.
       - **Uygulama, tek bir işletim sistemi işlemi olarak çalışır**: Test sürücüsü tek bir işletim sistemi işlemi altında çalışıyorsa bu onay kutusunu işaretleyin. Test sürücüsü ek işlemler işliyorsa bunu temizleyin.

## <a name="roslyn-analyzers-task"></a>Roslyn Çözümleyicileri görevi

> [!NOTE]
> Roslyn Çözümleyicileri görevini çalıştırmadan önce, derlemeniz bu koşullara uymalıdır:
> - Derleme tanımınızda kod derlemek C# veya Visual Basic Için yerleşik MSBuild veya vsbuild derleme görevi bulunur. Çözümleyiciler görevi, Roslyn Çözümleyicileri etkin olan MSBuild derlemesini çalıştırmak için yerleşik görevin giriş ve çıktısına bağımlıdır.
> - Bu derleme görevinin çalıştırıldığı derleme aracısı, Visual Studio 2017 sürüm 15,5 veya üzeri bir sürüme sahiptir, bu nedenle derleyici sürümü 2,6 veya üzerini kullanır.

Görev yapılandırmasının ayrıntıları aşağıdaki listede ve notta gösterilmiştir.

Kullanılabilir seçenekler şunlardır:

- **Kural kümesi**: Değerler **SDL gerekir**, **SDL önerilir**veya kendi özel kural kümesidir.
- **Çözümleyiciler sürümü**: **En son**' u seçmenizi öneririz.
- **Derleyici uyarıları gizlemeleri dosyası**: Gizlenen bir uyarı kimlikleri listesi içeren bir metin dosyası.
- **Denetim seçenekleri** > **Bu görevi çalıştır**: Görevin ne zaman çalışacağını belirtir. Daha karmaşık koşullar belirtmek için **özel koşullar** ' ı seçin.

> [!NOTE]
> - Roslyn Çözümleyicileri derleyici ile tümleşiktir ve yalnızca CSC. exe derlemesinin bir parçası olarak çalıştırılabilir. Bu nedenle, bu görev, daha önce derleme içinde çalışan derleyici komutunun yeniden çalınmasını veya yeniden çalıştırılmasını gerektirir. Bu yeniden yürütme veya çalıştırma, MSBuild derleme görev günlükleri için Visual Studio Team Services (VSTS) sorgulanarak yapılır.
>
>   Görevin, derleme tanımından MSBuild derleme komut satırını güvenilir bir şekilde alması için başka bir yol yoktur. Kullanıcıların komut satırlarını girmesini sağlamak için bir serbest biçim metin kutusu ekleme kabul ettik. Ancak, bu komut satırlarını güncel ve ana yapıyla eşitlenmiş halde tutmak zor olabilir.
>
>   Özel derlemeler yalnızca derleyici komutlarının değil, tüm komut kümesini yeniden gerektirir. Bu durumlarda, Roslyn çözümleyicilerinin etkinleştirilmesi önemsiz veya güvenilir değildir.
>
> - Roslyn Çözümleyicileri derleyici ile tümleşiktir. Çağrılması için Roslyn Çözümleyicileri derleme gerektirir.
>
>   Bu yeni derleme görevi önceden oluşturulmuş C# projeleri yeniden derleyerek uygulanır. Yeni görev, özgün görevle aynı derleme veya derleme tanımındaki yalnızca MSBuild ve VSBuild derleme görevlerini kullanır. Bununla birlikte, bu durumda yeni görev bunları Roslyn Çözümleyicileri etkin olarak kullanır.
>
>   Yeni görev, özgün görevle aynı aracıda çalışıyorsa, yeni görevin çıktısı, *s* kaynakları klasöründeki özgün görevin çıktısının üzerine yazar. Yapı çıkışı aynı olsa da, MSBuild 'i çalıştırmanızı, çıkışları yapıt hazırlama dizinine kopyalamanızı ve sonra da Roslyn çözümleyicilerinin çalıştırılmasını tavsiye ederiz.

Roslyn Çözümleyicileri görevi için ek kaynaklar için Microsoft Docs bulunan [Roslyn tabanlı Çözümleyicileri](https://docs.microsoft.com/dotnet/standard/analyzers/) inceleyin.

Bu derleme görevi tarafından yüklenen ve kullanılan çözümleyici paketini [Microsoft. CodeAnalysis. Fxcopçözümleyiciler](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)NuGet sayfasında bulabilirsiniz.

## <a name="tslint-task"></a>Tslınt görevi

TSLint hakkında daha fazla bilgi için [Tslint GitHub](https://github.com/palantir/tslint)deposuna gidin.

>[!NOTE] 
>Farkında olabileceğiniz gibi [Tslint GitHub deposu](https://github.com/palantir/tslint) giriş sayfası, 2019 ' de tslint 'in kullanım dışı olacağını söyler. Microsoft, [Eslint](https://github.com/eslint/eslint) 'i alternatif bir görev olarak araştırmakta.

## <a name="publish-security-analysis-logs-task"></a>Güvenlik analizi günlüklerini Yayımla görevi

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Güvenlik analizi günlüklerini Yayımla derleme görevini yapılandırma](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Yapıt adı**: Herhangi bir dize tanımlayıcısı.
- **Yapıt türü**: Seçiminize bağlı olarak, günlükleri Azure DevOps sunucunuzda veya yapı aracısının erişebileceği paylaşılan bir dosyaya yayımlayabilirsiniz.
- **Araçlar**: Belirli araçların günlüklerini korumayı seçebilirsiniz veya tüm günlükleri korumak için **tüm araçları** seçebilirsiniz.

## <a name="security-report-task"></a>Güvenlik raporu görevi

Güvenlik raporu yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Güvenlik raporu derleme görevini yapılandırma](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Raporlar**: İşlem **hattı konsolu**, **tsv dosyası**ve **HTML dosya** biçimlerinden birini seçin. Seçili her biçim için bir rapor dosyası oluşturulur.
- **Araçlar**: Derleme tanımınızda algılanan sorunların özetini istediğiniz araçları seçin. Her bir araç seçili olduğunda, yalnızca hataları görüp görmeyeceğinizi veya Özet raporda hem hataları hem de uyarıları görme seçeneğini belirleyebilirsiniz.
- **Gelişmiş Seçenekler**: Seçili araçlardan biri için günlük yoksa, bir uyarı veya hata günlüğü seçebilirsiniz. Bir hata günlüğünde, görev başarısız olur.
- **Temel Günlükler klasörü**: Günlüklerin bulunduğu temel Günlükler klasörünü özelleştirebilirsiniz. Ancak bu seçenek genellikle kullanılmaz.

## <a name="post-analysis-task"></a>Analiz sonrası görev

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Analiz sonrası derleme görevini yapılandırma](./media/security-tools/a-post-analysis600.png)

- **Araçlar**: Derleme tanımınızda, derleme kesmeyi koşullu olarak eklemek istediğiniz araçları seçin. Seçilen her bir araç için, yalnızca hatalara veya hata ve uyarılarla ilgili hataları kesmek isteyip istemediğinizi belirlemek için bir seçenek olabilir.
- **Rapor**: İsteğe bağlı olarak, derleme kesmesine neden olan sonuçları yazabilirsiniz. Sonuçlar, Azure DevOps konsol penceresine ve günlük dosyasına yazılır.
- **Gelişmiş Seçenekler**: Seçili araçlardan biri için günlük yoksa, bir uyarı veya hata günlüğü seçebilirsiniz. Bir hata günlüğünde, görev başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik kodu analiz uzantısı ve sunulan araçlar hakkında başka sorularınız varsa, [SSS sayfamıza](security-code-analysis-faq.md)göz atın.
