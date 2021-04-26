---
title: Kubernetes Köprüsü’ne geçiş
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Azure Dev Spaces, Kubernetes 'e köprülemek için geçiş işlemini açıklar
keywords: Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Kubernetes ile Köprü oluşturma
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499300"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Kubernetes Köprüsü’ne geçiş

> [!IMPORTANT]
> Azure Dev Spaces 31 Ekim 2023 ' de kullanımdan kaldırılacaktır. Müşteriler, bir istemci Geliştirici Aracı olan Kubernetes için köprü kullanarak geçiş yapılmalıdır.
>
> Azure Dev Spaces amacı, kullanıcıların Kubernetes üzerinde geliştirmeye yönelik kolaylaştırıcı kullanım ile ilgilidir. Azure Dev Spaces yaklaşımında önemli bir zorunluluğunu getirir, Docker ve Kubernetes yapılandırmalarının yanı sıra Kubernetes dağıtım kavramlarını anlamak için kullanıcılara ek yük koymamıştı. Zaman içinde, Azure Dev Spaces yaklaşımın Kubernetes üzerindeki iç döngü geliştirmenin hızını etkili bir şekilde düşürmediği de net hale geldi. Kubernetes Köprüsü, iç döngü geliştirmenin hızını etkili bir şekilde düşürür ve kullanıcıların gereksiz yükünü önler.
>
> Temel görev değişmeden kalır: mikro hizmet kodunu daha büyük uygulama bağlamında geliştirmek, test etmek ve hata ayıklamak için en iyi deneyimleri oluşturun.

Kubernetes Köprüsü, Azure Dev Spaces ile çalışan çoğu geliştirme senaryosunda daha hafif bir alternatif sağlar. Kubernetes Köprüsü, [Visual Studio][vs]   ve [Visual Studio Code][vsc]uzantıları kullanılarak yalnızca istemci tarafı bir deneyimdir.  

Kubernetes Köprüsü, kurulu bir Kubernetes uygulamasının yerel geliştirme iş istasyonunuzda çalışan bir hizmeti içermesini sağlayarak geliştirme deneyiminize yardımcı olur. Geliştirme alanlarının aksine, Kubernetes Köprüsü, Docker ve Kubernetes yapılandırmalarının oluşturulması gereksinimini ortadan kaldırarak iç döngü karmaşıklıklarını azaltır. böylece geliştiricilerin, mikro hizmet kodunun iş mantığına odaklanmasını sağlar.

Kubernetes Köprüsü, Kubernetes ortamınızdan bağımlılıklar ve mevcut yapılandırma kullanılırken geliştirme bilgisayarınızda çalışan kodda yineleme yapmanıza yardımcı olur. Buna karşılık, Azure Dev Spaces hizmetinize uzaktan hata ayıklamanıza ve kodunuzda tekrarlanmadan önce mikro hizmetinizi Kubernetes ortamına dağıtır.

Bu makalede, Kubernetes 'e Azure Dev Spaces ile Köprü oluşturma ile Kubernetes arasında Azure Dev Spaces geçiş yönergeleri arasında bir karşılaştırma sağlanır.

## <a name="development-approaches"></a>Geliştirme yaklaşımları

![Geliştirme yaklaşımları](media/migrate-to-btk/development-approaches.svg)

Kubernetes geliştiricileri, doğrudan kendi AKS kümesinde çalışan kodla çalışarak, dağıtılan ortama benzemeyen bir yerel ortam gereksinimini ortadan kaldırma. Azure Dev Spaces Bu yaklaşım, geliştirmenin belirli yönlerini geliştirmektedir, ancak Azure Dev Spaces kullanmaya başlayabilmeniz için Docker, Kubernetes ve Held gibi ek kavramların öğrenmesinin ve sürdürülmesi için de bir önkoşul getirmiştir.

Kubernetes Köprüsü, geliştiricilerin, kümesinin geri kalanı ile etkileşim kurarken doğrudan geliştirme bilgisayarlarında çalışmasına izin verir. Bu yaklaşım, kümesi tarafından sunulan bağımlılıkları ve ortamı paylaşırken doğrudan geliştirme bilgisayarlarında kod çalıştırmanın öğrenilmesi ve hızlarından yararlanır. Bu yaklaşım ayrıca, Kubernetes 'de çalışmaya gelen Aslına göre ve ölçeklendirmeden faydalanır.

## <a name="feature-comparison"></a>Özellik Karşılaştırması

Kubernetes 'in Azure Dev Spaces ve Köprüsü benzer özelliklere sahiptir ve ayrıca çeşitli alanlarda farklılık gösterir:

| Gereksinim  | Azure Dev Spaces  | Kubernetes Köprüsü  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 15 Azure bölgesinde | Herhangi bir AKS hizmet bölgesi    |
| **Güvenlik** |
| Kümenizde güvenlik erişimi gerekiyor  | AKS kümesi Katılımcısı  | Kubernetes RBAC-dağıtım güncelleştirmesi   |
| Geliştirme bilgisayarınızda güvenlik erişimi gerekli  | Yok  | Yerel yönetici/sudo   |
| **Stillerin** |
| Kubernetes ve Docker yapılarından bağımsız  | Hayır  | Yes   |
| Değişiklikleri otomatik geri alma, hata ayıklama sonrası  | Hayır  | Yes   |
| **Desteklenen Istemci araçları** |
| Visual Studio 2019 ile birlikte çalışma  | Yes  | Yes   |
| Visual Studio Code ile birlikte çalışma  | Yes  | Yes   |
| CLı ile birlikte çalışma  | Yes  | Hayır   |
| **İşletim sistemi uyumluluğu** |
| Windows 10 ' da geçerlidir  | Yes  | Yes  |
| Linux üzerinde çalışma  | Yes  | Yes  |
| MacOS 'ta çalışma  | Yes  | Yes  |
| **Özellikler** |
| Geliştirici yalıtımı veya takım geliştirme  | Yes  | Yes  |
| Ortam değişkenlerinin seçmeli olarak üzerine yaz  | Hayır  | Yes  |
| Dockerfile ve Held grafiğinin oluşturulması  | Yes  | Hayır  |
| Kubernetes 'e kalıcı kod dağıtımı  | Yes  | Hayır  |
| Bir Kubernetes Pod 'da uzaktan hata ayıklama  | Yes  | Hayır  |
| Yerel hata ayıklama, Kubernetes 'e bağlanıldı  | Hayır  | Yes  |
| Aynı iş istasyonunda aynı anda birden fazla hizmetin hatalarını ayıklama  | Yes  | Yes  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes iç döngüsü geliştirme

Kubernetes 'e Azure Dev Spaces ve köprü arasındaki en büyük fark kodunuzun çalıştığı yerdir. Azure Dev Spaces, mikro hizmet kodunuzda geliştirme ve hata ayıklamanıza yardımcı olur, ancak bu kodu kümenizde çalıştırmanızı gerektirir. Kubernetes Köprüsü, Kubernetes 'te çalışan büyük uygulamanın bağlamında, mikro hizmet kodunuzu doğrudan geliştirme bilgisayarınızda geliştirmenize ve hata ayıklamanıza olanak tanır. Kubernetes Köprüsü, Kubernetes kümesinin çevre düzeyini genişletir ve yerel işlemlerin Kubernetes yapılandırmalarını devralmasını sağlar.

![İç döngü geliştirme](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Kubernetes için köprü kullanırken, geliştirme bilgisayarınız ile kümeniz arasında bir ağ bağlantısı oluşturulur.Bu bağlantının kullanım ömrü boyunca, istekleri hizmete geliştirme bilgisayarınıza yönlendiren Kubernetes dağıtımınızın yerine kümenize bir ara sunucu eklenir. Bağlantıyı kestiğinizde uygulama dağıtımı, küme üzerinde çalışan dağıtımın orijinal sürümünü kullanmaya döndürülür. Bu yaklaşım, Azure Dev Spaces kod ile hangi kodun eşitlendiğinde çalışır, oluşturulur ve çalıştırılır. Azure Dev Spaces Ayrıca kodunuzu geri almaz.

Kubernetes Köprüsü, dağıtım yöntemlerinden bağımsız olarak Kubernetes 'te çalışan uygulamalarla çalışma esnekliği sunar. Uygulamanızı derlemek ve çalıştırmak için CI/CD kullanıyorsanız, sağlanan araçları veya özel betikleri kullanıp kullanmayacağınızı bağımsız olarak, kodunuzu geliştirmek ve hatalarını ayıklamak için Kubernetes ile Köprü kullanmaya devam edebilirsiniz.

> [!TIP]
>  [Microsoft Kubernetes uzantısı][kubernetes-extension] , IntelliSense Ile Kubernetes bildirimlerini hızlı bir şekilde geliştirmenize olanak tanır.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Azure Dev Spaces 'ten Kubernetes 'e geçiş geçişi

1. Visual Studio kullanıyorsanız, Visual Studio IDE 'nizi 16,7 veya üzeri bir sürüme güncelleştirin ve köprüyü [Visual Studio Market][vs-marketplace]Kubernetes uzantısına yükleyebilirsiniz. Visual Studio Code kullanıyorsanız, [Kubernetes uzantısına köprü][vsc-marketplace]yükleyebilirsiniz.
1. Azure portal veya [Azure Dev Spaces CLı] [AZD-Delete] kullanarak Azure Dev Spaces denetleyiciyi devre dışı bırakın.
1. [Azure Cloud Shell](https://shell.azure.com)kullanın. Ya da Bash yüklü olan Mac, Linux veya Windows üzerinde, bir bash kabuğu istemi açın. Aşağıdaki araçların komut satırı ortamınızda kullanılabildiğinden emin olun: Azure CLı, Docker, kubectl, kıvrık, tar ve gunzip.
1. Bir kapsayıcı kayıt defteri oluşturun veya var olan bir kayıt defteri kullanın. Azure 'da [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) kullanarak veya [Docker Hub](https://hub.docker.com/)kullanarak bir kapsayıcı kayıt defteri oluşturabilirsiniz. Azure Cloud Shell kullanırken Docker görüntülerini barındırmak için yalnızca Azure Container Registry kullanılabilir.
1. Azure Dev Spaces varlıkları Kubernetes varlıklarına köprü haline dönüştürmek için geçiş betiğini çalıştırın. Betik, Kubernetes ile Köprüle uyumlu yeni bir görüntü oluşturur, onu belirlenen kayıt defterine yükler ve ardından kümeyi görüntüyle güncelleştirmek için [Held](https://helm.sh) kullanır. Kaynak grubu, AKS kümesinin adı ve bir kapsayıcı kayıt defteri sağlamanız gerekir. Burada gösterildiği gibi diğer komut satırı seçenekleri vardır:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Betik aşağıdaki bayrakları destekler:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. *AZD. YAML* içindeki ortam değişkeni ayarları gibi özelleştirmeleri, projenizin *values. yıml* dosyasına el ile geçirin.
1. seçim `azds.yaml` Dosyayı projenizden kaldırın.
1. Dağıtılan uygulamanızda Kubernetes için köprü yapılandırın. Visual Studio 'da Kubernetes ile Köprü kullanma hakkında daha fazla bilgi için bkz. [Visual Studio 'Da Kubernetes Için köprü kullanma][use-btk-vs]. VS Code için bkz. [vs Code Kubernetes Için köprü kullanma][use-btk-vsc].
1. Yeni oluşturulan köprü ile Kubernetes hata ayıklama/başlatma profilini kullanarak hata ayıklamayı başlatın.
1. Kümenizi yeniden dağıtmak için gerektiğinde betiği tekrar çalıştırabilirsiniz.

## <a name="team-development-in-a-shared-cluster"></a>Paylaşılan kümede takım geliştirme

Ayrıca, Kubernetes ile bir Köprüle, geliştiriciye özgü yönlendirmeyi de kullanabilirsiniz. Azure Dev Spaces takım geliştirme senaryosu, üst ve alt ad alanları kavramını kullanarak bir hizmeti uygulamanın geri kalanından yalıtmak için birden çok Kubernetes ad alanı kullanır. Kubernetes Köprüsü, gelişmiş performans özellikleriyle ve aynı uygulama ad alanı içinde aynı özelliği sunar.

Hem Kubernetes hem de Azure Dev Spaces Köprüsü, HTTP üstbilgilerinin mevcut olmasını ve uygulamanın tamamında yayılmesini gerektirir. Uygulamanızı Azure Dev Spaces için üst bilgi yaymayı işleyecek şekilde yapılandırdıysanız üstbilginin güncelleştirilmesi gerekir. Azure Dev Spaces 'den Kubernetes 'e geçiş yapmak için, yapılandırılmış üstbilgiyi *azds* 'den, *Kubernetes*'e (as) güncelleştirin.

## <a name="evaluate-bridge-to-kubernetes"></a>Kubernetes için köprüyü değerlendir

Kümenizde Azure Dev Spaces devre dışı bırakmadan önce Kubernetes Köprüsü ile denemeler yapmak isterseniz, en kolay yöntem yeni bir küme kullanmaktır. Aynı kümedeki aynı anda Kubernetes 'i Azure Dev Spaces ve Köprüsü kullanmaya çalışırsanız, her ikisinde de yönlendirme özelliklerini kullanırken sorunlarla karşılaşacaktır.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Kubernetes köprüsünü değerlendirmek için Visual Studio 'Yu kullanma

1. Visual Studio IDE 'nizi 16,7 veya üzeri bir sürüme güncelleştirin ve köprüyü [Visual Studio Market][vs-marketplace]Kubernetes uzantısına yüklersiniz.
1. Yeni bir AKS kümesi oluşturun ve uygulamanızı dağıtın. [Örnek bir uygulama][btk-sample-app]da kullanabilirsiniz.
1. Dağıtılan uygulamanızda Kubernetes için köprü yapılandırın. Visual Studio 'da Kubernetes ile Köprü kullanma hakkında daha fazla bilgi için bkz. [Kubernetes Için köprü kullanma][use-btk-vs].
1. Yeni oluşturulan köprü ile Kubernetes hata ayıklama profilini kullanarak Visual Studio 'da hata ayıklamayı başlatın.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Kubernetes köprüsünü değerlendirmek için Visual Studio Code kullanın

1. [Kubernetes uzantısına köprü][vsc-marketplace]yükler.
1. Yeni bir AKS kümesi oluşturun ve uygulamanızı dağıtın. [Örnek bir uygulama][btk-sample-app]da kullanabilirsiniz.
1. Dağıtılan uygulamanızda Kubernetes için köprü yapılandırın. Visual Studio Code 'de Kubernetes ile Köprü kullanma hakkında daha fazla bilgi için bkz. [Kubernetes Için köprü kullanma][use-btk-vsc].
1. Yeni oluşturulan köprüyü Kubernetes başlatma profiline kullanarak Visual Studio 'da hata ayıklamayı başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes köprüsünün nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Bridge to Kubernetes’in işleyiş biçimi][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
