---
title: Microsoft Güvenlik kodu çözümleme belgelerine genel bakış
description: Bu makale, Microsoft Güvenlik kodu analiz uzantısı 'na genel bakış
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
ms.openlocfilehash: c3e22b34dbb770af2b4a719f8b4eaf040b724c47
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695424"
---
# <a name="about-microsoft-security-code-analysis"></a>Microsoft Güvenlik kodu analizi hakkında

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

Araçların bildirdiği ilk sorunları çözdükten sonra, uzantıyı yeni sorunlarda derlemeleri bölmek için yapılandırabilirsiniz. Her çekme isteğinde sürekli tümleştirme yapıları ayarlamak kolaydır.

### <a name="set-it-and-forget-it"></a>Ayarlayın ve unutmayın

Varsayılan olarak, derleme görevleri ve araçları güncel kalır. Aracın güncelleştirilmiş bir sürümü varsa, bunu indirmeniz ve yüklemeniz gerekmez. Uzantı sizin için güncellemeyi üstlenir.

### <a name="under-the-hood"></a>Üzerinde

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

Binskım, derleyici ayarlarını, bağlayıcı ayarlarını ve ikili dosyaların güvenlikle ilgili diğer özelliklerini doğrulayan bir taşınabilir yürütülebilir (PE) hafif tarayıcıdır. Bu derleme görevi, binskim. exe konsol uygulaması etrafında bir komut satırı sarmalayıcı sağlar. BinSkim, açık kaynaklı bir araçtır. Daha fazla bilgi için bkz. [GitHub 'Da Binskım](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Kimlik bilgisi tarayıcısı

Kaynak kodda depolanan parolalar ve diğer gizli dizileri önemli bir sorundur. Kimlik bilgisi tarayıcısı, bu sorunu çözmeye yardımcı olan özel bir statik analiz aracıdır. Araç, kaynak kodunuzda ve yapı çıkışındaki kimlik bilgilerini, sırları, sertifikaları ve diğer hassas içeriği algılar.

### <a name="microsoft-security-risk-detection"></a>Microsoft güvenlik riski algılama

Microsoft güvenlik riski algılama (MSRD), belirsizlik testi için bulut tabanlı bir hizmettir. Yazılımda açıktan yararlanma güvenlik hatalarını tanımlar. Bu hizmet ayrı bir abonelik ve etkinleştirme gerektirir. Daha fazla bilgi için bkz. [Msrd Geliştirici Merkezi](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Roslyn Çözümleyicileri

Roslyn Çözümleyicileri, Microsoft 'un yönetilen C# ve Visual Basic kodu statik olarak analiz etmek için derleyici ile tümleşik bir araçtır. Daha fazla bilgi için bkz. [Roslyn tabanlı çözümleyiciler](https://docs.microsoft.com/dotnet/standard/analyzers/).

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

Yapı görevlerini yapılandırma hakkında daha fazla bilgi için [yapılandırma kılavuzumuza](security-code-analysis-customize.md)bakın.

Uzantı ve sunulan araçlar hakkında başka sorularınız varsa, [SSS sayfamıza](security-code-analysis-faq.md)göz atın.
