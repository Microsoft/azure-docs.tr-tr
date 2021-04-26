---
title: Genel Bakış
description: Azure App Service’in web uygulamaları geliştirmenize ve barındırmanıza nasıl yardımcı olabileceğini öğrenin.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767237"
---
# <a name="app-service-overview"></a>App Service’e genel bakış

*Azure App Service* Web UYGULAMALARıNı, REST API 'leri ve mobil arka uçları BARıNDıRMAK için HTTP tabanlı bir hizmettir. .NET, .NET Core, Java, Ruby, Node.js, PHP veya Python dahil en sevdiğiniz dilde geliştirebilirsiniz. Uygulamalar hem Windows hem de [Linux](#app-service-on-linux)tabanlı ortamlarda kolayca çalışır ve ölçeklenebilir.

App Service, uygulamanıza güvenlik, Yük Dengeleme, otomatik ölçeklendirme ve otomatik yönetim gibi Microsoft Azure gücünü eklemektedir. Ayrıca, Azure DevOps, GitHub, Docker Hub 'dan sürekli dağıtım ve diğer kaynaklardan, paket yönetimi, hazırlık ortamları, özel etki alanı ve TLS/SSL sertifikaları gibi DevOps özelliklerinden de yararlanabilirsiniz. 

App Service ile kullandığınız Azure işlem kaynakları için ödeme yaparsınız. Kullandığınız işlem kaynakları, uygulamalarınızı çalıştırdığınız _App Service planına_ göre belirlenir. Daha fazla bilgi için bkz. [Azure App Service planlarına genel bakış](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>App Service nedir?

App Service temel özelliklerinden bazıları aşağıda verilmiştir:

* **Birden çok dil ve çerçeve** -App Service ASP.NET, ASP.NET Core, Java, Ruby, Node.js, php veya Python için birinci sınıf desteğe sahiptir. Ayrıca, [PowerShell’i ve diğer betikleri ya da yürütülebilir hizmetleri](webjobs-create.md) arka plan hizmetleri olarak çalıştırabilirsiniz.
* **Yönetilen üretim ortamı** -otomatik [düzeltme eklerini App Service ve işletim sistemi ve dil](overview-patch-os-runtime.md) çerçevelerini sizin için korur. Harika uygulamalar yazmaya ve Azure 'un platformu hakkında endişelenmenize zaman harcaın.
* **Kapsayıcılama ve Docker** -uygulamanızı yeniden renklendirme ve App Service özel bir Windows veya Linux kapsayıcısı barındırın. Docker Compose ile çok Kapsayıcılı uygulamalar çalıştırın. Docker becerilerinizi doğrudan App Service geçirin.
* **DevOps iyileştirmesi** - Azure DevOps, GitHub, BitBucket, Docker Hub veya Azure Container Registry ile [sürekli tümleştirme ve dağıtım](deploy-continuous-deployment.md) ayarlayın. [Test ve hazırlık ortamları](deploy-staging-slots.md) aracılığıyla güncelleştirmeleri yükseltin. [Azure PowerShell](/powershell/azure/) veya [platformlar arası komut satırı arabirimi (CLI)](/cli/azure/install-azure-cli) kullanarak uygulamalarınızı App Service’de yönetin.
* **Yüksek kullanılabilirlik ile küresel ölçeklendirme** - El ile veya otomatik olarak ölçek [artırabilir](manage-scale-up.md) veya [genişletebilirsiniz](../azure-monitor/autoscale/autoscale-get-started.md). Uygulamalarınızı Microsoft'un küresel veri merkezi altyapılarının herhangi bir yerinde barındırın. App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/)’sı yüksek kullanılabilirlik taahhüt eder.
* **SaaS platformları ve şirket için veri bağlantıları** - Kurumsal sistemler (SAP gibi), SaaS hizmetleri (Salesforce gibi) ve İnternet hizmetleri (Facebook gibi) için 50’den fazla [bağlayıcı](../connectors/apis-list.md) arasından seçim yapın. [Karma Bağlantılar](app-service-hybrid-connections.md)’ı ve [Azure Sanal Ağlar](web-sites-integrate-with-vnet.md)’ı kullanarak şirket içi verilere erişin.
* **Güvenlik ve uyumluluk** - App Service [ISO, SOC ve PCI uyumludur](https://www.microsoft.com/en-us/trustcenter). [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md)veya [Microsoft hesabı](configure-authentication-provider-microsoft.md)kullanıcıların kimliğini doğrulayın. [IP adresi kısıtlamaları](app-service-ip-restrictions.md) oluşturun ve [hizmet kimliklerini yönetin](overview-managed-identity.md).
* **Uygulama şablonları** - [Azure Market](https://azure.microsoft.com/marketplace/)’teki WordPress, Joomla ve Drupal’i de içeren kapsamlı uygulama şablonu listesinden seçiminizi yapın.
* Visual **Studio ve Visual Studio Code tümleştirme** temelli Araçlar Visual Studio ve oluşturma, dağıtma ve hata ayıklama işini kolaylaştırır Visual Studio Code.
* **API ve mobil Özellikler** -App Service, daha fazla API senaryosu Için, YENIDEN anahtar cors desteği sağlar ve kimlik doğrulama, çevrimdışı veri eşitleme, anında iletme bildirimleri ve daha fazlasını etkinleştirerek mobil uygulama senaryolarını basitleştirir.
* **Sunucusuz kod** - Açıkça altyapı sağlamanıza veya yönetmenize gerek kalmadan isteğe bağlı olarak bir kod parçacığı veya betik çalıştırın ve yalnızca kodunuzun gerçekte kullandığı işlem süresi (bkz. [Azure İşlevleri](../azure-functions/index.yml)) için ücret ödeyin.

App Service yanı sıra Azure, Web sitelerini ve Web uygulamalarını barındırmak için kullanılabilecek başka hizmetler de sunar. Çoğu senaryo için en iyi seçenektir App Service.  Mikro hizmet mimarisi için [Azure Spring-Cloud hizmetini](../spring-cloud/index.yml) veya [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)kullanmayı deneyin.  Kodunuzun çalıştırıldığı VM 'Ler üzerinde daha fazla denetime ihtiyacınız varsa, [Azure sanal makinelerini](https://azure.microsoft.com/documentation/services/virtual-machines/)göz önünde bulundurun. Bu Azure hizmetleri arasında seçim yapma hakkında daha fazla bilgi için bkz. [Azure App Service, Virtual Machines, Service Fabric ve Cloud Services karşılaştırması](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>Linux’ta App Service

App Service, Web uygulamalarını desteklenen uygulama yığınları için yerel olarak Linux üzerinde de barındırabilir. Ayrıca, özel Linux kapsayıcıları (Kapsayıcılar için Web App olarak da bilinir) çalıştırabilir.

### <a name="built-in-languages-and-frameworks"></a>Yerleşik diller ve çerçeveler

Linux üzerinde App Service, bir dizi dile özgü yerleşik görüntüyü destekler. Kodunuzu dağıtmanız yeterlidir. Desteklenen diller şunlardır: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core ve Ruby. [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes)En son dilleri ve desteklenen sürümleri görüntülemek için ' i çalıştırın. Uygulamanızın gerektirdiği çalışma zamanı yerleşik resimlerde desteklenmiyorsa, özel bir kapsayıcı ile dağıtabilirsiniz.

Süresi geçmiş çalışma zamanları, portalda Web Apps oluşturma ve yapılandırma dikey penceresinden düzenli aralıklarla kaldırılır. Bu çalışma zamanları, bakım organizasyonu tarafından kullanım dışı olduklarında veya önemli güvenlik açıklarına sahip olduğu durumlarda portaldan gizlenir. Bu seçenekler, müşterileri en başarılı olacağı en son çalışma zamanları ile rehberlik etmek için gizlidir. 

Süresi geçmiş bir çalışma zamanı portaldan gizliyse, bu sürümü kullanan mevcut siteleriniz çalışmaya devam edecektir. Bir çalışma zamanı App Service platformundan tamamen kaldırılırsa, Azure abonelik sahipleriniz kaldırılmadan önce bir e-posta bildirimi alır.

Artık portalda gösterilmemiş eski bir çalışma zamanı sürümü ile başka bir Web uygulaması oluşturmanız gerekiyorsa, sitenizin çalışma zamanı sürümünü alma yönergeleri için dil yapılandırma kılavuzlarını inceleyin. Aynı çalışma zamanına sahip başka bir site oluşturmak için Azure CLı kullanabilirsiniz. Alternatif olarak, sitenin ARM şablonunu dışarı aktarmak için portalda Web uygulaması dikey penceresinde **şablonu dışarı aktar** düğmesini de kullanabilirsiniz. Aynı çalışma zamanına ve yapılandırmaya sahip yeni bir site dağıtmak için bu şablonu yeniden kullanabilirsiniz.

### <a name="limitations"></a>Sınırlamalar

- Linux üzerinde App Service, [paylaşılan](https://azure.microsoft.com/pricing/details/app-service/plans/) fiyatlandırma katmanında desteklenmez. 
- Aynı App Service planında Windows ve Linux uygulamalarını karıştıramazsınız.  
- Tarihsel olarak, aynı kaynak grubunda Windows ve Linux uygulamalarını karıştıramazsınız. Ancak, 21 Ocak 2021 tarihinde veya bu tarihten sonra oluşturulan tüm kaynak grupları bu senaryoyu destekler. 21 Ocak 2021 tarihinden önce oluşturulan kaynak grupları için, karma platform dağıtımları ekleme özelliği yakında Azure bölgeleri (Ulusal bulut bölgeleri dahil) arasında kullanıma sunulacaktır.
- Azure portal, yalnızca şu anda Linux uygulamaları için çalışan özellikleri gösterir. Özellikler etkinken, portalda etkinleştirilir.
- Yerleşik görüntülere dağıtıldığında, kodunuz ve içeriğiniz, Azure depolama tarafından desteklenen Web içeriği için bir depolama birimi olarak ayrılır. Bu birimin disk gecikmesi, kapsayıcı FileSystem 'ın gecikmesinden daha yüksek ve daha fazla değişken. İçerik dosyalarına ağır salt okuma erişimi gerektiren uygulamalar, dosyaları içerik birimi yerine kapsayıcı dosya sistemine yerleştiren özel kapsayıcı seçeneğinden faydalanabilir.

## <a name="next-steps"></a>Sonraki adımlar

İlk web uygulamanızı oluşturun.

> [!div class="nextstepaction"]
> [ASP.NET Core (Windows veya Linux 'ta)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (Windows üzerinde)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (Windows veya Linux 'ta)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linux üzerinde)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (Windows veya Linux 'ta)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (Windows veya Linux 'ta)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (Linux üzerinde)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (Windows veya Linux 'ta)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Özel kapsayıcı (Windows veya Linux)](tutorial-custom-container.md)
