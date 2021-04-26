---
title: Microsoft Güvenlik kodu çözümleme belgelerine genel bakış
description: Microsoft Güvenlik kodu analiz uzantısı hakkında bilgi edinin. Bu uzantıyla, Azure DevOps CI/ID işlem hattına güvenlik kodu analizi ekleyebilirsiniz.
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
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801205"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft Güvenlik kodu analizi hakkında

> [!Note]
> 1 Mart 2022 ' den itibaren geçerli olmak üzere Microsoft Güvenlik kodu Analizi (MSCA) uzantısı kullanımdan kaldırılacak. Var olan MSCA müşterileri, 1 Mart 2022 ' e kadar MSCA erişimini koruacaktır. Azure DevOps 'daki alternatif seçenekler için lütfen [OWASP kaynak kodu çözümleme araçlarına](https://owasp.org/www-community/Source_Code_Analysis_Tools) bakın. GitHub 'a geçirmeyi planlayan müşteriler için [GitHub gelişmiş güvenlik](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)' i kullanıma alabilirsiniz.

Microsoft Güvenlik kodu analiz Uzantısı ile takımlar, Azure DevOps sürekli tümleştirme ve teslim (CI/CD) işlem hatlarına güvenlik kodu analizi ekleyebilir. Bu analiz, Microsoft 'ta [güvenli geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) uzmanları tarafından önerilir.

Tutarlı bir UX, çalışan araçların karmaşıklığını gizleyerek güvenliği basitleştirir. Araçların NuGet tabanlı teslimi sayesinde, takımların yüklemeyi veya güncelleştirme güncelleştirmesini yönetmesi gerekmez. Derleme görevleri için hem komut satırı hem de temel arabirimler sayesinde, tüm kullanıcılar istedikleri gibi araçlar üzerinde daha fazla denetime sahip olabilir.

Ekipler ayrıca şunları gibi güçlü bir gönderme işlemi özelliği de kullanabilir:

- Bekletme için Günlükler yayımlanıyor.
- Eylem yapılabilir, geliştiriciye odaklanmış raporlar oluşturma.
- Gerileme testlerinde yapı sonlarını yapılandırma.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Neden Microsoft Güvenlik kodu analizini kullanmalıyım?

### <a name="security-simplified"></a>Basitleştirilmiş güvenlik

Microsoft Güvenlik kodu çözümleme araçları 'nı Azure DevOps işlem hattınızla eklemek, yeni görevler eklemek kadar basittir. Görevleri özelleştirin veya varsayılan davranışlarını kullanın. Görevler, Azure DevOps işlem hattınızı bir parçası olarak çalışır ve çok sayıda sonuç ayrıntısı sağlayan Günlükler oluşturur.

### <a name="clean-builds"></a>Derlemeleri temizle

Araçların bildirdiği ilk sorunları çözdükten sonra, uzantıyı yeni sorunlarda derlemeleri bölmek için yapılandırabilirsiniz. Her çekme isteğinde sürekli tümleştirme yapıları ayarlamak kolaydır.

### <a name="set-it-and-forget-it"></a>Ayarlayın ve unutmayın

Varsayılan olarak, derleme görevleri ve araçları güncel kalır. Aracın güncelleştirilmiş bir sürümü varsa, bunu indirmeniz ve yüklemeniz gerekmez. Uzantı sizin için güncellemeyi üstlenir.

### <a name="under-the-hood"></a>Başlık altında

Uzantının derleme görevleri şu karmaşıklıkları gizler:
  - Güvenlik statik analiz araçları çalıştırılıyor.
  - Özet raporu oluşturmak veya derlemeyi bölmek için günlük dosyalarından sonuçları işleme.

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft Güvenlik kodu çözümleme aracı kümesi

Microsoft Güvenlik kodu çözümleme uzantısı, önemli çözümleme araçlarının en son sürümlerini size hazır hale getirir. Uzantı hem Microsoft tarafından yönetilen araçları hem de açık kaynaklı araçları içerir.

Bu araçlar, işlem hattını yapılandırmak ve çalıştırmak için ilgili derleme görevini kullandıktan sonra otomatik olarak bulutta barındırılan aracıya indirilir.

Bu bölüm, uzantısında Şu anda kullanılabilir olan araçların kümesini listeler. Daha fazla araç ekleme hakkında bilgi için izleyin. Ayrıca, eklememizi istediğiniz araçlarla ilgili önerilerinizi bize gönderin.

### <a name="anti-malware-scanner"></a>Kötü amaçlı yazılımdan koruma tarayıcısı

Kötü amaçlı yazılımdan koruma tarayıcı oluşturma görevi, Microsoft Güvenlik kodu çözümleme uzantısına eklenmiştir. Bu görevin, Windows Defender zaten yüklü olan bir derleme aracısında çalıştırılması gerekir. Daha fazla bilgi için bkz. [Windows Defender web sitesi](https://aka.ms/defender).

### <a name="binskim"></a>Binskım

Binskım, derleyici ayarlarını, bağlayıcı ayarlarını ve ikili dosyaların güvenlikle ilgili diğer özelliklerini doğrulayan bir taşınabilir yürütülebilir (PE) hafif tarayıcıdır. Bu derleme görevi binskim.exe konsol uygulamasının etrafında bir komut satırı sarmalayıcısı sağlar. BinSkim, açık kaynaklı bir araçtır. Daha fazla bilgi için bkz. [GitHub 'Da Binskım](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Kimlik bilgisi tarayıcısı

Kaynak kodda depolanan parolalar ve diğer gizli dizileri önemli bir sorundur. Kimlik bilgisi tarayıcısı, bu sorunu çözmeye yardımcı olan özel bir statik analiz aracıdır. Araç, kaynak kodunuzda ve yapı çıkışındaki kimlik bilgilerini, sırları, sertifikaları ve diğer hassas içeriği algılar.

### <a name="roslyn-analyzers"></a>Roslyn Çözümleyicileri

Roslyn Çözümleyicileri, Microsoft 'un yönetilen C# ve Visual Basic kodu statik olarak analiz etmek için derleyici ile tümleşik bir araçtır. Daha fazla bilgi için bkz. [Roslyn tabanlı çözümleyiciler](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

TSLint, okunabilir, bakım ve işlevlerde hatalara yönelik TypeScript kodunu denetleyen genişletilebilir bir statik analiz aracıdır. Modern düzenleyiciler ve derleme sistemleri tarafından yaygın olarak desteklenir. Kendi LINT kurallarınızı, yapılandırmalarında ve formatlamalarınızla özelleştirebilirsiniz. TSLint, açık kaynaklı bir araçtır. Daha fazla bilgi için bkz. [GitHub 'Da Tslint](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Sonuçları analiz ve sonrası işleme

Microsoft Güvenlik kodu analizi uzantısında Ayrıca üç gönderme işlemi görevi vardır. Bu görevler, güvenlik aracı görevlerinde bulunan sonuçları çözümlemenize yardımcı olur. Bir işlem hattına eklendiğinde, bu görevler genellikle diğer tüm araç görevlerini izler.

### <a name="publish-security-analysis-logs"></a>Güvenlik analizi günlüklerini yayımlama

Güvenlik analizi günlük oluşturmayı Yayımla görevi, derleme sırasında çalıştırılan güvenlik araçlarının günlük dosyalarını korur. Araştırma ve izleme için bu günlükleri okuyabilirsiniz.

Günlük dosyalarını bir. zip dosyası olarak Azure Artifacts yayımlayabilirsiniz. Ayrıca, bunları özel yapı aracıınızdan erişilebilir bir dosya paylaşımında kopyalayabilirsiniz.

### <a name="security-report"></a>Güvenlik raporu

Güvenlik raporu derleme görevi günlük dosyalarını ayrıştırır. Bu dosyalar, derleme sırasında çalışan güvenlik araçları tarafından oluşturulur. Build görevi sonra tek bir Özet rapor dosyası oluşturur. Bu dosya, çözümleme araçları tarafından bulunan tüm sorunları gösterir.

Bu görevi, belirli araçlara veya tüm araçlara yönelik sonuçları bildirmek üzere yapılandırabilirsiniz. Raporlanacak sorun düzeyini, yalnızca hatalar veya hata ve uyarılarla aynı şekilde seçebilirsiniz.

### <a name="post-analysis-build-break"></a>Analiz sonrası (derleme kesmesi)

Analiz sonrası derleme görevi sayesinde, bir yapı kesmeyi, özellikle de bir yapılandırmanın başarısız olmasına neden olabilir. Bir veya daha fazla analiz aracı kodda sorun bildir, bir derleme kesmesi eklersiniz.

Bu görevi, belirli araçların veya tüm araçların bulduğu sorunlar için derlemeyi bölmek üzere yapılandırabilirsiniz. Ayrıca, hatalar veya uyarılar gibi bulunan sorunların önem derecesine göre de yapılandırabilirsiniz.

>[!NOTE]
>Tasarım yaparak, görev başarıyla tamamlandığında her derleme görevi başarılı olur. Bu, bir aracın sorunları bulup bulmayacağı ve bu sayede, tüm araçların çalışmasına izin vererek oluşturma işleminin tamamlanmasını sağlamak için geçerlidir.

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Güvenlik kodu analizini ekleme ve yükleme yönergeleri için [ekleme ve yükleme kılavuzumuza](security-code-analysis-onboard.md)bakın.

Yapı görevlerini yapılandırma hakkında daha fazla bilgi için bkz. [yapılandırma kılavuzumuzu](security-code-analysis-customize.md) veya [YAML yapılandırma kılavuzu](yaml-configuration.md).

Uzantı ve sunulan araçlar hakkında başka sorularınız varsa, [SSS sayfamıza](security-code-analysis-faq.md)göz atın.