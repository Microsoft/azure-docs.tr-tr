---
title: Microsoft Güvenlik kodu analiz görevlerini özelleştirme
titleSuffix: Azure
description: Bu makalede, Microsoft Güvenlik kodu analiz uzantısı 'ndaki görevlerin özelleştirilmesi açıklanmaktadır
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ad395e1b782edb28845bb7db0607d2bab5b5697c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802021"
---
# <a name="configure-and-customize-the-build-tasks"></a>Derleme görevlerini yapılandırma ve özelleştirme

> [!Note]
> 1 Mart 2022 ' den itibaren geçerli olmak üzere Microsoft Güvenlik kodu Analizi (MSCA) uzantısı kullanımdan kaldırılacak. Var olan MSCA müşterileri, 1 Mart 2022 ' e kadar MSCA erişimini koruacaktır. Azure DevOps 'daki alternatif seçenekler için lütfen [OWASP kaynak kodu çözümleme araçlarına](https://owasp.org/www-community/Source_Code_Analysis_Tools) bakın. GitHub 'a geçirmeyi planlayan müşteriler için [GitHub gelişmiş güvenlik](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)' i kullanıma alabilirsiniz.

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

Windows Update hatalar ve bunların hafifletme hakkında daha fazla bilgi için bkz. [bileşene göre Windows Update hata kodları](/windows/deployment/update/windows-update-error-reference) ve [Windows Update aracı hata kodları](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)hakkında TechNet makalesi.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için, [kötü amaçlı yazılımdan koruma YAML seçeneklerimizi](yaml-configuration.md#anti-malware-scanner-task) inceleyin

## <a name="binskim-task"></a>Binskım görevi

> [!NOTE]
> BinSkim görevini çalıştırmadan önce, derlemeniz şu koşullardan birini karşılamalıdır:
>  - Derlemeniz yönetilen koddan ikili yapıtlar üretir.
>  - Binskım ile analiz etmek istediğiniz ikili yapıtlar vardır.

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![BinSkim derleme görevini yapılandırma](./media/security-tools/7-binskim-task-details.png)

- Derleme yapılandırmasını hata ayıklama olarak ayarlayın. pdb hata ayıklama dosyaları oluşturulur. Binskım, çıkış ikilileriyle ilgili sorunları kaynak koduna geri eşlemek için bu dosyaları kullanır.
- Araştırmamak ve kendi Komut satırlarınızı oluşturmak için:
     - **Tür** listesinde **temel**' yı seçin.
     - **İşlev** listesinde, **Çözümle**' yi seçin.
- **Hedef** bölümünde, bir dosya, dizin veya filtre deseninin bir veya daha fazla belirticilerini girin. Bu tanımlayıcılar çözümlenecek bir veya daha fazla ikiliye çözümlenmelidir:
    - Birden çok belirtilen hedefin noktalı virgülle ayrılması gerekir (;).
    - Belirleyici tek bir dosya olabilir veya joker karakterler içerebilir.
    - Dizin belirtimlerinin her zaman * ile bitmesi gerekir \\ .
    - Örnekler:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- **Tür** listesinde **komut satırı** ' nı seçerseniz binskim.exe çalıştırmanız gerekir:
     - binskim.exe ilk bağımsız değişkenlerin, ardından bir veya daha fazla yol belirtimlerinden sonra **analiz** olduğundan emin olun. Her yol, kaynak dizine göre tam yol veya yol olabilir.
     - Birden çok hedef yol bir boşluk ile ayrılmalıdır.
     - **/O** veya **/output** seçeneğini atlayabilirsiniz. Çıkış değeri sizin için eklenir veya değiştirilmez.
     - Standart komut satırı yapılandırması aşağıdaki gibi gösterilmiştir.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> \\Hedef için dizinler belirtirseniz sonda * önemlidir.

BinSkim komut satırı bağımsız değişkenleri, KIMLIĞE göre kurallar veya çıkış kodları hakkında daha fazla bilgi için bkz. [Binskim Kullanıcı Kılavuzu](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için, [Binskım YAML seçeneklerimizi](yaml-configuration.md#binskim-task) inceleyin

## <a name="credential-scanner-task"></a>Kimlik bilgisi tarayıcı görevi

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Kimlik bilgisi tarayıcısı derleme görevini yapılandırma](./media/security-tools/3-taskdetails.png)

Mevcut seçenekler şunlardır:
  - **Görünen ad**: Azure DevOps görevinin adı. Varsayılan değer, Çalıştır kimlik bilgisi tarayıcısı
  - **Araç ana sürümü**: kullanılabilir değerler **credscan v2**, **credscan v1** içerir. Müşterilerin **Credscan v2** sürümünü kullanmasını öneririz.
  - **Çıktı biçimi**: mevcut değerler **TSV**, **CSV**, **Sarif** ve **PREfast**' i içerir.
  - **Araç sürümü**: **en son**' u seçmenizi öneririz.
  - **Tarama klasörü**: taranacak depo klasörü.
  - **Aramacılar dosya türü**: taramak için kullanılan Searchers dosyasını bulmaya yönelik seçenekler.
  - **Suppressions dosyası**: bir [JSON](https://json.org/) dosyası çıkış günlüğündeki sorunları engelleyebilir. Gizleme senaryoları hakkında daha fazla bilgi için bu makalenin SSS bölümüne bakın.
  - **Ayrıntılı çıkış**: kendi kendine açıklama.
  - **Toplu Iş boyutu**: kimlik bilgisi tarayıcısını çalıştırmak için kullanılan eşzamanlı iş parçacıklarının sayısı. Varsayılan değer 20 ' dir. Olası değerler 1 ila 2.147.483.647 arasındadır.
  - **Eşleşme zaman aşımı**: denetimi bırakmadan önce bir arama eşleşmesi gerçekleştirmeye harcanan saniye cinsinden süre.
  - **Dosya taraması okuma arabelleği boyutu**: içerik okunurken kullanılan arabelleğin bayt cinsinden boyutu. Varsayılan değer 524.288 ' dir.  
  - **Dosya taraması okuma bayt üst sınırı**: içerik analizi sırasında bir dosyadan okunan en fazla bayt sayısı. Varsayılan değer 104.857.600 ' dir.
  - **Denetim seçenekleri**  >  **Bu görevi çalıştır**: görevin ne zaman çalışacağını belirtir. Daha karmaşık koşullar belirtmek için **özel koşullar** ' ı seçin.
  - **Sürüm**: Azure DevOps içindeki derleme görevi sürümü. Bu seçenek sıklıkla kullanılmaz.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için [kimlik bilgisi tarayıcımız YAML seçenekleri](yaml-configuration.md#credential-scanner-task) ' ne bakın

## <a name="roslyn-analyzers-task"></a>Roslyn Çözümleyicileri görevi

> [!NOTE]
> Roslyn Çözümleyicileri görevini çalıştırmadan önce, derlemeniz bu koşullara uymalıdır:
>
> - Derleme tanımınızda C# veya Visual Basic kodu derlemek için yerleşik MSBuild veya VSBuild derleme görevi bulunur. Çözümleyiciler görevi, Roslyn Çözümleyicileri etkin olan MSBuild derlemesini çalıştırmak için yerleşik görevin giriş ve çıktısına bağımlıdır.
> - Bu derleme görevinin çalıştırıldığı derleme aracısı, Visual Studio 2017 sürüm 15,5 veya üzeri bir sürüme sahiptir, bu nedenle derleyici sürümü 2,6 veya üzerini kullanır.

Görev yapılandırmasının ayrıntıları aşağıdaki listede ve notta gösterilmiştir.

Mevcut seçenekler şunlardır:

- **RuleSet**: değerler **SDL gerekir**, **SDL önerilir** veya kendi özel kural kümesidir.
- **Çözümleyiciler sürümü**: **en son**' u seçmenizi öneririz.
- **Derleyici uyarıları gizlemeleri dosyası**: gizlenen bir uyarı kimlikleri listesi içeren bir metin dosyası.
- **Denetim seçenekleri**  >  **Bu görevi çalıştır**: görevin ne zaman çalışacağını belirtir. Daha karmaşık koşullar belirtmek için **özel koşullar** ' ı seçin.

> [!NOTE]
>
> - Roslyn Çözümleyicileri derleyici ile tümleşiktir ve yalnızca csc.exe derlemesinin bir parçası olarak çalıştırılabilir. Bu nedenle, bu görev, daha önce derleme içinde çalışan derleyici komutunun yeniden çalınmasını veya yeniden çalıştırılmasını gerektirir. Bu yeniden yürütme veya çalıştırma, MSBuild derleme görev günlükleri için Azure DevOps (eski adıyla Visual Studio Team Services) sorgulanarak yapılır.
>
>   Görevin, derleme tanımından MSBuild derleme komut satırını güvenilir bir şekilde alması için başka bir yol yoktur. Kullanıcıların komut satırlarını girmesini sağlamak için bir serbest biçim metin kutusu ekleme kabul ettik. Ancak, bu komut satırlarını güncel ve ana yapıyla eşitlenmiş halde tutmak zor olabilir.
>
>   Özel derlemeler yalnızca derleyici komutlarının değil, tüm komut kümesini yeniden gerektirir. Bu durumlarda, Roslyn çözümleyicilerinin etkinleştirilmesi önemsiz veya güvenilir değildir.
>
> - Roslyn Çözümleyicileri derleyici ile tümleşiktir. Çağrılması için Roslyn Çözümleyicileri derleme gerektirir.
>
>   Bu yeni derleme görevi önceden oluşturulmuş C# projelerini yeniden derleyerek uygulanır. Yeni görev, özgün görevle aynı derleme veya derleme tanımındaki yalnızca MSBuild ve VSBuild derleme görevlerini kullanır. Bununla birlikte, bu durumda yeni görev bunları Roslyn Çözümleyicileri etkin olarak kullanır.
>
>   Yeni görev, özgün görevle aynı aracıda çalışıyorsa, yeni görevin çıktısı, *s* kaynakları klasöründeki özgün görevin çıktısının üzerine yazar. Yapı çıkışı aynı olsa da, MSBuild 'i çalıştırmanızı, çıkışları yapıt hazırlama dizinine kopyalamanızı ve sonra da Roslyn çözümleyicilerinin çalıştırılmasını tavsiye ederiz.

Roslyn Çözümleyicileri görevi için ek kaynaklar için Microsoft Docs bulunan [Roslyn tabanlı Çözümleyicileri](/dotnet/standard/analyzers/api-analyzer) inceleyin.

Bu derleme görevi tarafından yüklenen ve kullanılan çözümleyici paketini [Microsoft. CodeAnalysis. Fxcopçözümleyiciler](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)NuGet sayfasında bulabilirsiniz.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için [Roslyn çözümleyiciler YAML seçeneklerimizi](yaml-configuration.md#roslyn-analyzers-task) inceleyin

## <a name="tslint-task"></a>Tslınt görevi

TSLint hakkında daha fazla bilgi için [Tslint GitHub](https://github.com/palantir/tslint)deposuna gidin.

>[!NOTE] 
>Farkında olabileceğiniz gibi [Tslint GitHub deposu](https://github.com/palantir/tslint) giriş sayfası, 2019 ' de tslint 'in kullanım dışı olacağını söyler. Microsoft, [Eslint](https://github.com/eslint/eslint) 'i alternatif bir görev olarak araştırmakta.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için [Tslınt YAML seçeneklerimizi](yaml-configuration.md#tslint-task) inceleyin

## <a name="publish-security-analysis-logs-task"></a>Güvenlik analizi günlüklerini Yayımla görevi

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Güvenlik analizi günlüklerini Yayımla derleme görevini yapılandırma](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Yapıt adı**: herhangi bir dize tanımlayıcısı.
- **Yapıt türü**: seçiminize bağlı olarak, Azure DevOps Server veya yapı aracısının erişebileceği paylaşılan bir dosya için günlükleri yayımlayabilirsiniz.
- **Araçlar**: belirli araçların günlüklerini korumayı seçebilirsiniz veya tüm günlükleri korumak Için **tüm araçları** seçebilirsiniz.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için, [Yayımlama güvenlik günlüklerimize BAKıN YAML seçenekleri](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Güvenlik raporu görevi

Güvenlik raporu yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Güvenlik raporu derleme görevini yapılandırma](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Raporlar**: Işlem **hattı konsolu**, **tsv dosyası** ve **HTML dosya** biçimlerinden birini seçin. Seçili her biçim için bir rapor dosyası oluşturulur.
- **Araçlar**: Derleme tanımınızda algılanan sorunların özetini istediğiniz araçları seçin. Her bir araç seçili olduğunda, yalnızca hataları görüp görmeyeceğinizi veya Özet raporda hem hataları hem de uyarıları görme seçeneğini belirleyebilirsiniz.
- **Gelişmiş Seçenekler**: seçili araçlardan biri için günlük yoksa, bir uyarı veya hata günlüğü seçebilirsiniz. Bir hata günlüğünde, görev başarısız olur.
- **Temel Günlükler klasörü**: günlüklerin bulunduğu temel Günlükler klasörünü özelleştirebilirsiniz. Ancak bu seçenek genellikle kullanılmaz.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için [güvenlik raporumuzu ve YAML seçeneklerinizi](yaml-configuration.md#security-report-task) denetleyin

## <a name="post-analysis-task"></a>Analiz sonrası görev

Görev yapılandırmasının ayrıntıları aşağıdaki ekran görüntüsünde ve listesinde gösterilir.

![Analiz sonrası derleme görevini yapılandırma](./media/security-tools/a-post-analysis600.png)

- **Araçlar**: oluşturma tanımınızda, derleme kesmeyi koşullu olarak eklemek istediğiniz araçları seçin. Seçilen her bir araç için, yalnızca hatalara veya hata ve uyarılarla ilgili hataları kesmek isteyip istemediğinizi belirlemek için bir seçenek olabilir.
- **Rapor**: isteğe bağlı olarak, derleme kesmesine neden olan sonuçları yazabilirsiniz. Sonuçlar, Azure DevOps konsol penceresine ve günlük dosyasına yazılır.
- **Gelişmiş Seçenekler**: seçili araçlardan biri için günlük yoksa, bir uyarı veya hata günlüğü seçebilirsiniz. Bir hata günlüğünde, görev başarısız olur.

Bu görev için YAML yapılandırması hakkında daha fazla bilgi için, [gönderdiğimiz Analize BAKıN YAML seçenekleri](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Sonraki adımlar

YAML tabanlı yapılandırma hakkında daha fazla bilgi için [YAML yapılandırma kılavuzumuza](yaml-configuration.md)bakın.

Güvenlik kodu analiz uzantısı ve sunulan araçlar hakkında başka sorularınız varsa, [SSS sayfamıza](security-code-analysis-faq.md)göz atın.