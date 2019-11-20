---
title: Microsoft Güvenlik kodu çözümleme belgeleri SSS
description: Bu makale, Microsoft Güvenlik kodu analiz uzantısı hakkında bir SSS içerir
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
ms.openlocfilehash: b28d02dd0ca375451f6ff75b1253ae8874bf2ab4
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828241"
---
# <a name="frequently-asked-questions"></a>Sık sorulan sorular
Sorularınız mı var? Daha fazla bilgi için aşağıdaki SSS bölümüne bakın.

## <a name="general-faq"></a>Genel SSS

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Uzantıyı bir Azure DevOps örneği yerine Visual Studio Team Foundation Server örneğine yükleyebilir miyim?

Hayır. Uzantı Visual Studio Team Foundation Server İndiriyor ve yüklenmeye hazır değil.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Microsoft Güvenlik kodu çözümlemesini benim yapımda çalıştırdım mı? 

Belki. Analiz aracının türüne bağlıdır. Kaynak kodu, gereken tek şey olabilir veya derleme çıktısı gerekli olabilir.

Örneğin, kimlik bilgisi tarayıcısı (CredScan), dosyaları kod deposunun klasör yapısı içinde analiz eder. Bu analiz nedeniyle, sonuçları almak için bir tek başına derlemede CredScan ve güvenlik Analizi günlüklerini Yayımla oluşturma görevlerini çalıştırabilirsiniz.

Derleme sonrası yapıtları çözümleyen BinSkim gibi diğer araçlar için öncelikle derleme gereklidir.

### <a name="can-i-break-my-build-when-results-are-found"></a>Sonuçlar bulunduğunda derlemeden bölebilir miyim?

Evet. Bir araç, günlük dosyasında bir sorun veya sorun bildirdiğinde, derleme kesmesi oluşturabilirsiniz. Analiz sonrası derleme görevini eklemeniz yeterlidir ve derlemeyi bölmek istediğiniz herhangi bir araçla ilgili onay kutusunu seçin.

Analiz sonrası görevinin Kullanıcı arabiriminde, herhangi bir araç yalnızca hata veya hata ve uyarı bildirdiğinde derlemeyi kesmeyi seçebilirsiniz.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 'daki komut satırı bağımsız değişkenleri, tek başına masaüstü araçlarında bu bağımsız değişkenlerden farklı midir? 

Çoğu bölümde, Azure DevOps derleme görevleri, güvenlik araçlarının komut satırı bağımsız değişkenleri etrafında doğrudan sarmalayıcılardır. Bir yapı görevine, normalde bir komut satırı aracına geçirdiğiniz her şeyi bağımsız değişken olarak geçirebilirsiniz.

Dikkat çekici farklılıklar:

- Araçlar, aracının $ (Build. SourcesDirectory) kaynak klasöründen veya% BUILD_SOURCESDIRECTORY% öğesinden çalıştırılır. Örnek: C:\agent @ no__t-0work\1\s.
- Bağımsız değişkenlerdeki yollar, daha önce listelenen kaynak dizinin köküne göre olabilir. Yollar da mutlak olabilir. Azure DevOps derleme değişkenlerini kullanarak ya da yerel kaynakların bilinen dağıtım konumlarına sahip şirket içi bir aracı çalıştırarak mutlak yollar alırsınız.
- Araçlar otomatik olarak bir çıkış dosyası yolu veya klasörü sağlar. Derleme görevi için bir çıkış konumu sağlarsanız, bu konum derleme aracısındaki iyi bilinen günlüklerin konumuyla birlikte değişir
- Bazı araçlar için bazı ek komut satırı bağımsız değişkenleri değiştirilmiştir. Bir örnek, GUI 'nin başlatılmasından emin olan seçeneklerin eklenmesi veya kaldırılması.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps derlemesinde birden fazla depoda kimlik bilgisi tarayıcısı gibi bir yapı görevini çalıştırabilir miyim?

Hayır. Güvenli geliştirme araçlarının tek bir işlem hattında birden çok depoda çalıştırılması desteklenmez.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Belirtdiğim çıkış dosyası oluşturulmıyor veya belirtilen çıkış dosyasını bulamıyorum

Derleme görevleri bazı kullanıcı girişlerini filtreleyebilir. Özellikle bu soru için, oluşturulan çıkış dosyasının konumunu yapı aracısında ortak bir konum olacak şekilde güncelleştirirler. Bu konum hakkında daha fazla bilgi için aşağıdaki sorulara bakın.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Araçlar tarafından oluşturulan çıkış dosyaları nerede kaydedilir? 

Derleme görevleri, derleme aracısındaki bu iyi bilinen konuma çıkış yollarını otomatik olarak ekler: $ (Agent. BuildDirectory) \_sdt \ Günlükler. Bu konumda standartlaştırdığımız için, kod analizi günlüklerini üreten veya kullanan tüm ekipler çıkışa erişebilir.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Bu görevleri barındırılan bir yapı aracısında çalıştırmak için bir derlemeyi sıraya alabilir miyim? 

Evet. Uzantıdaki tüm görevler ve araçlar barındırılan bir yapı aracısında yürütülebilir.

>[!NOTE]
> Kötü amaçlı yazılımdan koruma tarayıcı oluşturma görevi, Windows Defender 'ın etkinleştirildiği bir derleme Aracısı gerektirir. Barındırılan Visual Studio 2017 ve üzeri, böyle bir aracı sağlar. Derleme görevi, Visual Studio 2015 barındırılan aracısında çalışmaz.
>
> İmzalar bu aracılarda güncelleştirilemeyebilir, ancak imzaların her zaman üç saat öncesine eşit olması gerekir.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Derleme işlem hattının aksine, bu derleme görevlerini bir yayın işlem hattının parçası olarak çalıştırabilir miyim?

Çoğu durumda, evet.

Ancak, Azure DevOps, bu görevler yapıtları yayımladığınızda, yayın işlem hatları içinde görevleri çalıştırmayı desteklemez. Bu destek eksikliği, bir yayın ardışık düzeninde güvenlik Analizi günlüklerini Yayımla görevinin başarıyla çalışmasını önler. Bunun yerine görev, açıklayıcı bir hata iletisiyle başarısız olur.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Derleme görevleri nereden araçlar indirir?

Yapı görevleri, araçların NuGet paketlerini [Azure DevOps paket yönetimi akışından](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)indirebilir. Yapı görevleri, derleme aracısında önceden yüklenmiş olması gereken düğüm paket yöneticisini da kullanabilir. Bu tür bir yüklemeye örnek **NPM Install tslint**komutu vardır.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Uzantıyı yükleyen efekt, Azure DevOps kuruluşuma sahip mi? 

Yükleme sonrasında, uzantı tarafından sağlanan güvenlik derleme görevleri kuruluşunuzdaki tüm kullanıcılar için kullanılabilir hale gelir. Bir Azure işlem hattı oluşturduğunuzda veya düzenlediğinizde, bu görevler yapı-görev koleksiyonu listesinden kullanılabilir. Aksi takdirde, uzantıyı Azure DevOps kuruluşunuzda yüklemek hiçbir etkiye sahip değildir. Yükleme, hesap ayarlarını, proje ayarlarını veya işlem hatlarını değiştirmez.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Uzantı yükleniyor var olan Azure işlem hatlarımı değiştirir mi? 

Hayır. Uzantının yüklenmesi, işlem hatlarınızın yanı sıra güvenlik yapı görevlerinin kullanılabilmesini sağlar. Araçların yapı süreciyle çalışabilmesi için derleme tanımlarını eklemeniz veya güncelleştirmeniz gerekir.

## <a name="task-specific-faq"></a>Göreve özgü SSS

Derleme görevlerine özgü sorular bu bölümde listelenmiştir.

### <a name="credential-scanner"></a>Kimlik bilgisi tarayıcısı

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Yaygın gizleme senaryoları ve örnekleri nelerdir?

En yaygın gizleme senaryolarından ikisi için Ayrıntılar aşağıda verilmiştir.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Belirli bir gizli dizi içindeki tüm oluşumları belirtilen yolda gizlemek için

Aşağıdaki örnekte gösterildiği gibi, CredScan çıkış dosyasındaki gizli dizinin karma anahtarı gereklidir.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> Karma anahtar, eşleşen değerin veya dosya içeriğinin bir kısmı tarafından oluşturulur. Herhangi bir kaynak kodu düzeltmesi, karma anahtarı değiştirebilir ve gizleme kuralını devre dışı bırakabilir.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Belirli bir dosyadaki tüm gizli dizileri bastırmak veya gizli dizileri dosyanın kendisini bastırmak için

Dosya ifadesi bir dosya adı olabilir. Ayrıca, tam dosya yolunun veya dosya adının baseName bir bölümü de olabilir. Joker karakterler desteklenmez.

Aşağıdaki örneklerde \<ınputpath > dosyasının nasıl bastıralınacağını gösterilmektedir \src\JS\lib\angular.js

Geçerli gizleme kuralları örnekleri:

- \<ınputpath > \src\JS\lib\angular.js-belirtilen yoldaki dosyayı bastırır
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- Angular. js-aynı ada sahip herhangi bir dosyayı bastırır

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Dosyaya eklenen tüm sonraki gizlilikler da otomatik olarak bastırılır.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Gizli dizileri yönetmeye yönelik tavsiye edilen yönergeler nelerdir?

Aşağıdaki kaynaklar, gizli dizileri güvenli bir şekilde yönetmenize ve uygulamalarınızın içinden hassas bilgilere erişmenize yardımcı olur:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD Yönetilen Hizmet Kimliği (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure App Service ve Azure Işlevlerinde Yönetilen kimlikler](../../app-service/overview-managed-identity.md)
 - [AppAuthentication kitaplığı](../../key-vault/service-to-service-authentication.md)


Daha fazla bilgi için bkz. Web günlüğü gönderisi, [bulutta gizli dizileri güvenli bir şekilde yönetme](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Kendi özel aramalarımı yazabilir miyim?

Kimlik bilgisi tarayıcısı, buildsearchers. xml dosyasında yaygın olarak tanımlanan bir dizi içerik arayanları kullanır. Dosya, bir **Contentsearcher** nesnesini temsil eden bir XML serileştirilmiş nesneler dizisi içerir. Program, iyi test edilmiş bir arayanlar kümesiyle dağıtılır. Ancak kendi özel aramalarınızı da uygulayabilirsiniz.

Bir içerik arayici aşağıdaki gibi tanımlanır:

- **Ad**: kimlik bilgisi tarayıcısı çıktı dosyalarında kullanılacak açıklayıcı Arayıcının adı. Arayıcının adları için Camel-Case adlandırma kuralını kullanmanızı öneririz.
- **RuleId**: Searcher 'ın KARARLı donuk kimliği:
    - Kimlik bilgisi tarayıcısı varsayılan Arayıcının, CSCAN0010, CSCAN0020 veya CSCAN0030 gibi bir **RuleId** değeri atanır. Son basamak, normal ifadeler (Regex) yoluyla arama gruplarını birleştirmek veya bölmek için ayrılmıştır.
    - Özelleştirilmiş bir Arayıcının için **RuleId** değeri kendi ad alanına sahip olmalıdır. Örnek olarak CSCAN-\<Namespace @ no__t-10010, CSCAN-\<Namespace @ no__t-30020 ve CSCAN-\<Namespace @ no__t-50030 verilebilir.
    - Tam bir Arayıcının adı **RuleId** değerinin ve bir Arayıcının adının birleşimidir. Örnekler şunlardır CSCAN0010. KeyStoreFiles ve CSCAN0020. Base64EncodedCertificate.
- **Resourcematchmodel**: araya karşı Denetlenecek dosya uzantılarının Regex.
- **Contentsearchpatterns**: eşleştirilecek Regex deyimlerini içeren bir dizeler dizisi. Arama desenleri tanımlanmamışsa, **Resourcematchmodel** değeriyle eşleşen tüm dosyalar döndürülür.
- **Contentsearchfilters**: Searcher 'a özgü hatalı pozitifleri filtrelemek için Regex deyimlerini içeren bir dize dizisi.
- **Matchdetails**: her bir araya eşleşmesi için eklenen açıklayıcı bir ileti, azaltma yönergeleri veya her ikisi.
- **Öneri**: PREfast rapor biçimini kullanarak bir eşleşme için öneriler-alan içeriği.
- **Önem derecesi**: bir sorunun önem derecesini yansıtan bir tamsayı. En yüksek önem derecesi 1 ' dir.

  ![Kimlik bilgisi tarayıcı kurulumunu gösteren XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn Çözümleyicileri

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn çözümleyiciler görevi kullanılırken sık karşılaşılan hatalar nelerdir?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Proje yanlış bir Microsoft. NETCore. app sürümü kullanılarak geri yüklendi

Tam hata iletisi:

"Hata: proje Microsoft. NETCore. app Version *x. x*. x kullanılarak geri yüklendi, ancak geçerli ayarlarla birlikte sürüm *y. y. y* kullanılacak. Bu sorunu çözmek için, geri yükleme ve derleme ya da yayımlama gibi sonraki işlemler için aynı ayarların kullanıldığından emin olun. Genellikle bu sorun, Runtimeıdentifier özelliği Build veya Publish sırasında ayarlandıysa, geri yükleme sırasında olmasa da oluşabilir. "

Roslyn, görevleri derlemenin bir parçası olarak çalıştırır, derleme makinesindeki kaynak ağacının oluşturulabilir bir durumda olması gerekir.

Ana derleme ve Roslyn Çözümleyicileri adımları arasındaki bir adım, kaynak ağacını derlemeyi engelleyen bir duruma koymalarından kaynaklanabilir. Bu ek adım büyük olasılıkla **DotNet. exe ' nin yayınlaması**. Yalnızca Roslyn Çözümleyicileri adımından önce NuGet geri yüklemesi yapan adımı çoğaltma işlemini deneyin. Bu yinelenen adım, kaynak ağacını oluşturulabilir bir duruma geri yerleştirebilir.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc. exe, bir çözümleyici örneği oluşturamaz

Tam hata iletisi:

"' CSC. exe", 1 hata koduyla çıkıldı--C: \\*bbbb*. dll ' den bir Analyzer *aaaa* örneği oluşturulamıyor: ' Microsoft. CodeAnalysis, Version =*X. x. x. x*, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' veya bağımlılıklarından biri. Sistem belirtilen dosyayı bulamıyor. "

Derleyicisinin Roslyn çözümleyicilerinin desteklediğinden emin olun. **CSC. exe/Version** komutunu çalıştırmak, 2,6 veya üzeri bir sürüm değeri bildirmelidir.

Bazen bir. csproj dosyası, Microsoft.Net. derleyicileri bir pakete başvurarak derleme makinesinin Visual Studio yüklemesini geçersiz kılabilir. Derleyicinin belirli bir sürümünü kullanmayı düşünmüyorsanız, Microsoft.Net. derleyicilere başvuruları kaldırın. Aksi takdirde, başvurulan paketin sürümünün de 2,6 veya üzeri olduğundan emin olun.

**CSC. exe/Errorlog** seçeneğinde belirtilen hata günlüğü yolunu almayı deneyin. Seçenek ve yol, Roslyn Çözümleyicileri derleme görevinin günlüğünde görüntülenir. **/Errorlog: F:\ts-Services-123 @ no__t-1work\456\s\Some\Project\Code\Code.csproj.Sarif** gibi bir şey görünebilir

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# Derleyici sürümü yeterince yeni değil

C# Derleyicinin en son sürümlerini almak için [Microsoft.net. derleyiciler](https://www.nuget.org/packages/Microsoft.Net.Compilers)sayfasına gidin. Yüklü sürümünüzü almak için, bir komut isteminde **CSC. exe/Version** komutunu çalıştırın. Sürüm 2,6 veya üzeri bir Microsoft.Net. derleyiciler NuGet paketine başvurtığınızdan emin olun.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild ve VSBuild günlükleri bulunamadı

Roslyn çözümleyiciler derleme görevinin MSBuild Build görevinin MSBuild günlüğü için Azure DevOps 'u sorgulaması gerekir. Çözümleyici görevi MSBuild görevinden hemen sonra çalışırsa, günlük henüz kullanılamaz. MSBuild görevi ve Roslyn Çözümleyicileri görevi arasına diğer görevleri yerleştirin. Diğer görevlere örnek olarak Binskım ve kötü amaçlı yazılımdan koruma tarayıcısı verilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Ek yardıma ihtiyacınız varsa, Microsoft Güvenlik kodu analizi desteği Pazartesi-saat Pasifik Standart Saati 9:00 ile 5:00 00 arasında olmalıdır.

  - Ekleme: başlamak için Teknik Hesap yöneticileriniz ile Iletişim kurun.
  
  - Destek: [Microsoft Güvenlik kodu analizi desteğiyle](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)ekibimize e-posta gönderin.

  >[!NOTE] 
  >Microsoft ile ücretli destek ilişkiniz olmayabilir. Ya da Phoenix kataloğundan hizmetleri satın almayı önleyen bir destek teklifi de olabilir. Bu koşullardan biri doğru ise, daha fazla bilgi için lütfen [Destek Hizmetleri giriş sayfamızı](https://www.microsoft.com/enterprise/services/support) ziyaret edin.
