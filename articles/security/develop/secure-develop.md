---
title: Microsoft Azure güvenli uygulamalar geliştirin
description: Bu makalede, Web uygulaması projenizin uygulama ve doğrulama aşamaları sırasında göz önünde bulundurmanız gereken en iyi yöntemler açıklanmaktadır.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c1c7dd0bd017852144139a841ff609dabf0f1a27
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928064"
---
# <a name="develop-secure-applications-on-azure"></a>Azure 'da güvenli uygulamalar geliştirin
Bu makalede, bulut için uygulama geliştirirken göz önünde bulundurmanız gereken güvenlik etkinlikleri ve denetimler sunuyoruz. Microsoft [güvenlik geliştirme yaşam döngüsü 'nin (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) uygulama ve doğrulama aşamaları sırasında göz önünde bulundurmanız gereken güvenlik soruları ve kavramlar ele alınmıştır. Amaç, daha güvenli bir uygulama geliştirmek için kullanabileceğiniz etkinlikleri ve Azure hizmetlerini tanımlamanıza yardımcı olmaktır.

Aşağıdaki SDL aşamaları bu makalede ele alınmıştır:

- Uygulama
- Doğrulama

## <a name="implementation"></a>Uygulama
Uygulama aşamasının odağı, erken önleme için en iyi yöntemleri oluşturmak ve koddan güvenlik sorunlarını algılayıp kaldırmasıdır.
Uygulamanızın kullanılmasını amaçlamadığınız yollarla kullanılacağını varsayın. Bu, uygulamanızın yanlışlıkla veya kasıtlı olarak kötüye kullanılmasına karşı koruma sağlamanıza yardımcı olur.

### <a name="perform-code-reviews"></a>Kod İncelemeleri gerçekleştirme

Kodu iade etmeden önce, genel kod kalitesini artırmak ve hata oluşturma riskini azaltmak için [kod İncelemeleri](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) gerçekleştirin. Kod inceleme sürecini yönetmek için [Visual Studio 'yu](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) kullanabilirsiniz.

### <a name="perform-static-code-analysis"></a>Statik kod analizi gerçekleştir

[Statik kod analizi](https://www.owasp.org/index.php/Static_Code_Analysis) ( *kaynak kodu analizi*olarak da bilinir) genellikle kod incelemesinin bir parçası olarak gerçekleştirilir. Statik kod analizi, ortak olmayan kodda [taınt denetimi](https://en.wikipedia.org/wiki/Taint_checking) ve [veri akışı analizi](https://en.wikipedia.org/wiki/Data-flow_analysis)gibi teknikleri kullanarak olası güvenlik açıklarını bulmak için statik kod çözümleme araçları 'nı çalıştırmayı ifade eder.

Azure Marketi, statik kod analizi gerçekleştiren ve kod incelemelerine yardımcı olan [Geliştirici Araçları](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) sunar.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Uygulamanız için her girişi doğrulama ve Temizleme

Uygulamanızı en sık kullanılan Web uygulaması güvenlik açıklarına karşı korumak için tüm girişi güvenilmeyen olarak değerlendirin. Güvenilmeyen veriler, ekleme saldırılarına yönelik bir araç. Uygulamanızın girişinde URL 'deki parametreler, kullanıcıdan giriş, veritabanından veya bir API 'den gelen veriler ve bir kullanıcının potansiyel olarak işleyebildiği herhangi bir şey bulunur. Uygulama, uygulamanın verileri herhangi bir şekilde (kullanıcıya geri görüntüleme dahil) kullanmadan önce, verilerin sözdizimsel ve anlam açısından geçerli olduğunu [doğrulamalıdır](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) .

Yalnızca düzgün biçimlendirilmiş verilerin iş akışına girdiğinden emin olmak için girişi veri akışında erken doğrulayın. Hatalı biçimlendirilmiş verilerin veritabanınızda kalıcı olmasını veya bir aşağı akış bileşeninde bir arızası tetiklemesini istemezsiniz.

Kara liste ve beyaz listeleme, giriş sözdizimi doğrulaması gerçekleştirmeye yönelik iki genel yaklaşımlar sağlar:

  - Kara liste, belirli bir Kullanıcı girişinin "kötü amaçlı olarak bilinen" içerik içermediğini denetlemeye çalışır.

  - Beyaz listeleme, belirli bir Kullanıcı girişinin bir "bilinen iyi" girişler kümesiyle eşleşip eşleşmediğini denetlemeye çalışır. Karakter tabanlı beyaz liste, bir uygulamanın kullanıcı girişinin yalnızca "bilinen iyi" karakterler içerdiğini veya girişin bilinen bir biçimle eşleşip eşleşmediğini denetlediği bir beyaz listeleme biçimidir.
    Örneğin, bu, Kullanıcı adının yalnızca alfasayısal karakter içerdiğini veya tam olarak iki sayı içerdiğini denetlemeyi gerektirebilir.

Beyaz liste, güvenli yazılım oluşturmak için tercih edilen yaklaşımdır.
Büyük olasılıkla hatalı girişin tamamı listesini düşünmek imkansız olduğundan kara listeye hata açıktır.

Bu işi, istemci tarafında (veya sunucu ve istemci tarafında) değil, sunucuda yapın.

### <a name="verify-your-applications-outputs"></a>Uygulamanızın çıktılarını doğrulama

Görsel olarak veya bir belge içinde mevcut olan tüm çıktılar her zaman kodlanmış ve çıkış olmalıdır. *Çıkış kodlaması*olarak da bilinen [kaçış](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), güvenilmeyen verilerin ekleme saldırılarına karşı bir araç olmamasını sağlamaya yardımcı olmak için kullanılır. Kaçış, veri doğrulamayla birlikte, sistemin güvenliğini bir bütün olarak artırmak için katmanlı savunma sağlar.

Kaçış, her şeyin çıkış olarak görüntülendiğinden emin olur *.* Kaçış Ayrıca yorumlayıcı 'nın verilerin yürütülmesi amaçlandığını bilmesini sağlar ve bu da saldırıların çalışmasını önler. Bu, *siteler arası komut dosyası oluşturma* (XSS) adlı başka bir yaygın saldırı tekniğidir.

Üçüncü taraftan bir Web çerçevesi kullanıyorsanız, [OWASP XSS önleme sayfasını](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)kullanarak Web sitelerindeki çıkış kodlaması için seçeneklerinizi doğrulayabilirsiniz.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Veritabanıyla iletişim kurmak için parametreli sorguları kullanın

Kodunuzda "anında" bir satır içi veritabanı sorgusu oluşturun ve doğrudan veritabanına gönderin. Uygulamanıza yerleştirilen kötü amaçlı kod, veritabanınızın çalınmasına, silinmesine veya değiştirilmesine neden olabilir. Uygulamanız, veritabanınızı barındıran işletim sisteminde kötü amaçlı işletim sistemi komutlarını çalıştırmak için de kullanılabilir.

Bunun yerine, parametreli sorguları veya saklı yordamları kullanın. Parametreli sorgular kullandığınızda, kodunuzda yöntemi güvenli bir şekilde çağırabilir ve sorgu ifadesinin bir parçası olarak değerlendirilmek zorunda kalmadan bir dize geçirebilirsiniz.

### <a name="remove-standard-server-headers"></a>Standart sunucu üstbilgilerini kaldır

Sunucu, X ile güçlendirilmiştir ve X-AspNet-Version gibi üstbilgiler, sunucu ve temel teknolojiler hakkında bilgi açığa çıkar. Uygulamanın parmak izi baskıdan kaçınmak için bu üst bilgileri bastırmanızı öneririz.
Bkz. [Azure Web sitelerinde standart sunucu üstbilgilerini kaldırma](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Üretim verilerinizi ayırt edin

Üretim verileriniz veya "gerçek" verileriniz, geliştirme, test ya da işletmenin hedefinden farklı herhangi bir amaçla kullanılmamalıdır. Tüm geliştirme ve test için maskelenmiş ([anonimleştirilmiş](https://en.wikipedia.org/wiki/Data_anonymization)) veri kümesi kullanılmalıdır.

Bu, gerçek verilerinize daha az sayıda kişinin erişimi olduğu anlamına gelir ve bu da saldırı yüzeyini azaltır. Ayrıca, gizlilik açısından olası bir ihlali ortadan kaldıran daha az çalışanın kişisel verileri görür.

### <a name="implement-a-strong-password-policy"></a>Güçlü parola ilkesi uygulama

Deneme yanılma ve sözlük tabanlı tahminlere karşı savunmak için, kullanıcıların karmaşık bir parola oluşturmasını sağlamak için güçlü bir parola ilkesi uygulamanız gerekir (örneğin, 12 karakter minimum uzunluğu ve alfasayısal ve özel karakterler gerektirir).

Parola ilkeleri oluşturmak ve zorlamak için bir kimlik çerçevesi kullanabilirsiniz. Azure AD B2C, [Yerleşik ilkeler](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [self servis parola sıfırlama](../../active-directory-b2c/active-directory-b2c-reference-sspr.md)ve daha fazlasını sağlayarak parola yönetimine yardımcı olur.

Varsayılan hesapların saldırılarına karşı savunmak için tüm anahtarların ve parolaların değiştirilebilen ve kaynakları yükledikten sonra oluşturulduğunu veya değiştirildiğini doğrulayın.

Uygulamanın parolaları otomatik olarak oluşturması gerekiyorsa, oluşturulan parolaların rastgele olduğundan ve yüksek entrokdıklarından emin olun.

### <a name="validate-file-uploads"></a>Karşıya dosya yüklemelerini doğrula

Uygulamanız [dosya karşıya yüklemeye](https://www.owasp.org/index.php/Unrestricted_File_Upload)izin veriyorsa, bu riskli etkinlik için uygulayabileceğiniz önlemleri göz önünde bulundurun. Birçok saldırının ilk adımı, saldırı altında olan bir sisteme bazı kötü amaçlı kodlar almanızı sağlar. Bir dosya yükleme işleminin kullanılması, saldırganın bunu gerçekleştirmenize yardımcı olur. OWASP, karşıya yüklediğiniz dosyanın güvende olduğundan emin olmak için bir dosyayı doğrulamaya yönelik çözümler sunar.

Kötü amaçlı yazılımdan koruma, virüsler, casus yazılım ve diğer kötü amaçlı yazılımları belirleyip kaldırmanıza yardımcı olur. [Microsoft kötü amaçlı yazılımdan](../fundamentals/antimalware.md) koruma veya bir Microsoft iş ortağının Endpoint Protection çözümünü ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)ve [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)) yükleyebilirsiniz.

[Microsoft Antimalware](../fundamentals/antimalware.md) gerçek zamanlı koruma, zamanlanmış tarama, kötü amaçlı yazılım düzeltme, imza güncelleştirmeleri, altyapı güncelleştirmeleri, örnek raporlama ve dışlama olay toplama gibi özellikler içerir. Microsoft kötü amaçlı yazılımdan koruma ve iş ortağı çözümlerini, dağıtım kolaylığı ve yerleşik algılamalar (Uyarılar ve olaylar) için [Azure Güvenlik Merkezi](../../security-center/security-center-partner-integration.md) ile tümleştirebilirsiniz.

### <a name="dont-cache-sensitive-content"></a>Hassas içeriği önbelleğe alma

Gizli içeriği tarayıcıda önbelleğe alma. Tarayıcılar, önbelleğe alma ve geçmiş bilgilerini saklayabilir. Önbelleğe alınan dosyalar, Internet Explorer durumunda Temporary Internet Files klasörü gibi bir klasörde depolanır. Bu sayfalara yeniden başvurulur tarayıcı, sayfaları önbelleğinden görüntüler. Gizli bilgiler (adres, kredi kartı ayrıntıları, sosyal güvenlik numarası, Kullanıcı adı) kullanıcıya görüntüleniyorsa, bilgiler tarayıcının önbelleğinde depolanabilir ve tarayıcının önbelleğini inceleyerek ya da tarayıcının **önbelleğine basılarak alınabilir. Geri** düğmesi.

## <a name="verification"></a>Doğrulama
Doğrulama aşaması, kodun önceki aşamalarda oluşturulan güvenlik ve gizlilik tenetlerini karşıladığından emin olmak için kapsamlı bir çaba içerir.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Uygulamanızın bağımlılıklarındaki güvenlik açıklarını bulun ve onarın

Tüm bilinen bileşenleri belirlemek için uygulamanızı ve bağımlı kitaplıklarını taramanızı sağlar. Bu taramayı gerçekleştirmek için kullanılabilen ürünler [OWASP bağımlılık denetimi](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snrivk](https://snyk.io/)ve [Black Duck](https://www.blackducksoftware.com/)' i kapsar.

[Tinfoil Security](https://www.tinfoilsecurity.com/) tarafından desteklenen güvenlik açığı taraması, Azure App Service Web Apps için kullanılabilir. [App Service Ile tinfoil Security scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) , geliştiricilerin ve yöneticilerin kötü amaçlı bir aktör tarafından yararlanabilmeleri için hızlı, tümleşik ve ekonomik bir şekilde bulma ve adresleme olanakları sunmaktadır.

> [!NOTE]
> [Tinfoil Security 'Yi Azure AD ile de tümleştirebilirsiniz](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Tinfoil Security 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:
>  - Azure AD 'de, tinfoil Security 'ye kimlerin erişebileceğini kontrol edebilirsiniz.
>  - Kullanıcılarınız Azure AD hesapları kullanılarak tinfoil Security 'de (çoklu oturum açma) otomatik olarak oturum açabilir.
>  - Hesaplarınızı tek bir merkezi konumda, Azure portal yönetebilirsiniz.

### <a name="test-your-application-in-an-operating-state"></a>Uygulamanızı bir işletim durumunda test etme

Dinamik uygulama güvenliği testi (DAVST), güvenlik açıklarını bulmak için bir uygulamayı bir işletim durumunda test etme işlemidir. DADST araçları, bellek bozulması, güvenli olmayan sunucu yapılandırması, siteler arası betik oluşturma, Kullanıcı ayrıcalık sorunları, SQL ekleme ve diğer kritik güvenlik sorunları gibi güvenlik açıklarını bulmak için yürütürken programları analiz eder.

Bast, statik uygulama güvenliği testi 'nden (SAST) farklıdır. SAST araçları, kod yürütülemediği zaman güvenlik kusurunu bulmak için kaynak kodu veya derlenmiş kod sürümlerini analiz eder.

Bir güvenlik uzmanı (bir [sızma Sınayıcısı](../fundamentals/pen-testing.md) veya güvenlik açığı denetçisi) ile birlikte, tercihen Bir güvenlik uzmanı yoksa, bir Web proxy tarayıcısı ve bazı eğitimlerle kendiniz de gerçekleştirebilirsiniz. Kodunuzda açık güvenlik sorunları sunmatığınızdan emin olmak için bir VAST tarayıcıyı erken takın. Web uygulaması güvenlik açığı tarayıcıları listesi için [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) sitesine bakın.

### <a name="perform-fuzz-testing"></a>Belirsizlik testi gerçekleştir

[Belirsizlik testi](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)' nde, bir uygulamaya hatalı biçimlendirilmiş veya rastgele veri sunarak program hatası ile tanışın. Program hatası, uygulama oluşturulmadan önce olası güvenlik sorunlarının açığa çıkarılmaya yardımcı olur.

[Güvenlik riski algılama](https://docs.microsoft.com/security-risk-detection/) , yazılımdaki güvenlik açısından kritik hataları bulmak için Microsoft benzersiz belirsizlik test hizmetidir.

### <a name="conduct-attack-surface-review"></a>Saldırı yüzeyi incelemesi gerçekleştir

Kod tamamlama sonrasında saldırı yüzeyini gözden geçirmek, bir uygulama veya sistem üzerinde yapılan tüm tasarım veya uygulama değişikliklerinin dikkate alınmasının sağlanmasına yardımcı olur. Tehdit modelleri de dahil olmak üzere değişikliklerin sonucu olarak oluşturulan tüm yeni saldırı vektörlerini gözden geçirdiğinden ve azaltıldığından emin olmanıza yardımcı olur.

Uygulamayı tarayarak saldırı yüzeyinin bir resmini oluşturabilirsiniz. Microsoft, [saldırı yüzeyi Çözümleyicisi](https://www.microsoft.com/download/details.aspx?id=24487)adlı bir saldırı yüzeyi çözümleme aracı sunar. [OWASP tabanlı saldırı proxy projesi](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [arachnı](http://arachni-scanner.com/), [skipbalık](https://code.google.com/p/skipfish/)ve [w3af](http://w3af.sourceforge.net/)dahil birçok ticari dinamik test ve güvenlik açığı tarama araçları veya hizmetleri arasından seçim yapabilirsiniz. Bu tarama araçları, uygulamanızı gezin ve Web üzerinden erişilebilen uygulamanın bölümlerini eşler. Ayrıca, Azure Marketi 'Nde benzer [Geliştirici Araçları](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)için arama yapabilirsiniz.

### <a name="perform-security-penetration-testing"></a>Güvenlik, sızma testi gerçekleştirme

Uygulamanızın güvenli olmasını sağlamak, diğer tüm işlevleri test etmek kadar önemlidir. Derleme ve dağıtım sürecinin standart bir parçasını, [sızma testi](../fundamentals/pen-testing.md) yapın. Dağıtılan uygulamalarda düzenli güvenlik testlerini ve güvenlik açığı taramasını zamanlayın ve açık bağlantı noktaları, uç noktalar ve saldırıları izleyin.

### <a name="run-security-verification-tests"></a>Güvenlik doğrulama testlerini Çalıştır

[Azure Için güvenli DevOps seti](https://azsk.azurewebsites.net/index.html) (AzSK) Azure platformunun birden çok hizmeti için SVTs içerir. Azure aboneliğinizin ve uygulamanızı oluşturan farklı kaynakların güvenli bir durumda olduğundan emin olmak için bu SVTs 'yi düzenli olarak çalıştırırsınız. Bu testleri, AzSK 'nin sürekli tümleştirme/sürekli dağıtım (CI/CD) uzantıları özelliğini kullanarak otomatikleştirin ve bu da SVTs 'nin bir Visual Studio uzantısı olarak kullanılabilmesini sağlar.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, güvenli uygulamaları tasarlamanıza ve dağıtmanıza yardımcı olabilecek güvenlik denetimleri ve etkinlikleri önerilir.

- [Güvenli uygulamalar tasarlama](secure-design.md)
- [Güvenli uygulamalar dağıtma](secure-deploy.md)
