---
title: Azure Dev Spaces ile CI/CD kullanma
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Azure’da kapsayıcılar ve mikro hizmetlerle hızlı Kubernetes geliştirme
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kapsayıcılar
ms.openlocfilehash: 7058806e58dbc2d9a196062c129688e6a96c5f31
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264449"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Azure Dev Spaces ile CI/CD kullanma

Bu makalede, geliştirme alanları etkinken Azure Kubernetes Service (AKS) ile sürekli tümleştirme/sürekli dağıtım (CI/CD) ayarlama sırasında size kılavuzluk eder. AKS 'e CI/CD, her kaydedilmiş kod kaynak deponuza gönderildiğinde uygulama güncelleştirmelerinin otomatik olarak dağıtılmasını sağlar. CI/CD 'nin bir geliştirme alanları etkinleştirilmiş kümeyle birlikte kullanılması faydalı olur çünkü ekibin bir taban çizgisini takımın birlikte çalışması için güncel tutar.

![Örnek CI/CD diyagramı](../media/common/ci-cd-simple.png)

Bu makalede Azure DevOps ile ilgili yol, ancak Jenkins, TeamCity vb. gibi CI/CD sistemleri için de aynı kavramlar geçerlidir.

## <a name="prerequisites"></a>Önkoşullar
* [Azure Dev Spaces etkin Azure Kubernetes hizmeti (AKS) kümesi](../get-started-netcore.md)
* [Azure Dev Spaces CLı yüklendi](upgrade-tools.md)
* [Bir proje ile Azure DevOps organizasyonu](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [yönetici hesabı](../../container-registry/container-registry-authentication.md#admin-account) ayrıntıları kullanılabilir
* [AKS kümenizi Azure Container Registry çekmek üzere yetkilendirin](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Örnek kodu indir
Bu süre boyunca örnek kod GitHub deponuzun bir çatalını oluşturalım. @No__t-0 ' a gidin ve **çatal**' ı seçin. Çatallı işlem tamamlandıktan sonra, deponun kendi çatallanmış sürümünü yerel olarak **kopyalayın** . _Ana_ dal varsayılan olarak kullanıma sunulacaktır, ancak çatalınızda de aktarılmak üzere _azds_updates_ dalında bazı zaman kazandıran değişiklikler ekledik. _Azds_updates_ dalı, dev Spaces öğretici bölümlerinde el ile yapmanız GEREKTIĞINI ve CI/CD sisteminin dağıtımını kolaylaştırmak için önceden oluşturulmuş bazı YAML ve JSON dosyalarını içeren güncelleştirmeleri içerir. Yerel deponuzdaki _azds_updates_ dalını kullanıma almak için `git checkout -b azds_updates origin/azds_updates` gibi bir komut kullanabilirsiniz.

## <a name="dev-spaces-setup"></a>Geliştirme alanları kurulumu
@No__t-1 komutunu kullanarak _dev_ adlı yeni bir alan oluşturun. _Geliştirme_ alanı, CI/CD işlem hattınızda kod değişiklerinizi göndermek için kullanılacaktır. Bu, _dev_temelinde _alt alanlar_ oluşturmak için de kullanılacaktır.

```cmd
azds space select -n dev
```

Bir üst dev alanı seçmeniz istendiğinde _\<none @ no__t-2_' yi seçin.

Geliştirme alanınız oluşturulduktan sonra konak sonekini belirlemeniz gerekir. Azure Dev Spaces giriş denetleyicisinin ana bilgisayar sonekini göstermek için `azds show-context` komutunu kullanın.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

Yukarıdaki örnekte, ana bilgisayar soneki _fedcba098.EUS.azds.io_. Bu değer daha sonra yayın tanımınızın oluşturulması sırasında kullanılır.

Geliştirici _alanı her_ zaman en son bir ana hat durumuna sahip olur, böylece geliştiricilerin _geliştirme_ verilerinden daha büyük bir uygulama bağlamında yalıtılmış değişiklikleri test edebilmesi için _alt_ sınır oluşturabilir. Bu kavram, dev Spaces öğreticilerinde daha ayrıntılı bir şekilde ele alınmıştır.

## <a name="creating-the-build-definition"></a>Derleme tanımı oluşturma
Azure DevOps takım projenizi bir Web tarayıcısında açın ve işlem _hatları_ bölümüne gidin. İlk olarak, Azure DevOps sitesinin sağ üst köşesindeki profil fotoğrafınızı tıklatın, Önizleme özellikleri bölmesini açın ve _yenı YAML ardışık düzen oluşturma deneyimini_devre dışı bırakın:

![Önizleme özellikleri bölmesi açılıyor](../media/common/preview-feature-open.png)

Devre dışı bırakma seçeneği:

![Yeni YAML ardışık düzen oluşturma deneyimi seçeneği](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _New YAML işlem hattı oluşturma deneyimi_ önizleme özelliği şu anda önceden tanımlanmış derleme işlem hatları oluşturma ile çakışıyor. Önceden tanımlanmış derleme işlem hattımızı dağıtmak için bunu şimdilik devre dışı bırakmanız gerekir.

_Azds_updates_ dalında, *mywebapi* ve *webön uç*için gereken derleme adımlarını tanımlayan basit bir [Azure işlem hattı YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) sunuyoruz.

Seçtiğiniz dile bağlı olarak, YAML ardışık düzeni şuna benzer bir yolda iade edildi: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Bu dosyadan bir işlem hattı oluşturmak için:
1. DevOps proje ana sayfanızda Yapılar > işlem hatları ' na gidin.
1. **Yeni** bir derleme işlem hattı oluşturma seçeneğini belirleyin.
1. Kaynak olarak **GitHub** ' ı seçin, gerekirse GitHub hesabınızla yetkilendirme yapın ve _dev-Spaces_ örnek uygulama deposunun dallanmış sürümünden _azds_updates_ dalını seçin.
1. Şablon olarak **yapılandırmayı kod olarak**veya **YAML**'yi seçin.
1. Artık derleme işlem hattınız için bir yapılandırma sayfasıyla karşılaşırsınız. Yukarıda belirtildiği gibi, **...** düğmesini kullanarak **YAML dosya yolu** için dile özgü yola gidin. Örneğin, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. **Değişkenler** sekmesine gidin.
1. _Dockerıd_ 'yi, [Azure Container Registry Yönetici hesabınızın](../../container-registry/container-registry-authentication.md#admin-account)Kullanıcı adı olan bir değişken olarak el ile ekleyin. (Makale ön koşulları 'nda bahsedildi)
1. _Dockerpassword_ öğesini, [Azure Container Registry Yönetici hesabınızın](../../container-registry/container-registry-authentication.md#admin-account)parolası olan bir değişken olarak el ile ekleyin. Güvenlik amacıyla _Dockerpassword_ ' i gizli (kilit simgesini seçerek) olarak belirttiğinizden emin olun.
1. **& Kuyruğu kaydet**' i seçin.

Artık GitHub çatalınızın _azds_updates_ dalına gönderilen herhangi bir güncelleştirme için *mywebapi* ve *webön uç* 'yi otomatik olarak oluşturacak bir CI çözümüdür. Azure portal giderek, Azure Container Registry seçerek ve **depolar** sekmesine göz atarak Docker görüntülerinin itildiğini doğrulayabilirsiniz. Resimlerin, kapsayıcı kayıt defterinizde oluşturulması ve gösterilmesi birkaç dakika sürebilir.

![Azure Container Registry depoları](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Yayın tanımı oluşturuluyor

1. DevOps proje ana sayfanızda, işlem hatları > sürümler ' e gidin
1. Henüz bir yayın tanımı içermeyen yepyeni bir DevOps projesinde çalışıyorsanız, önce devam etmeden önce boş bir yayın tanımı oluşturmanız gerekir. Içeri aktarma seçeneği, var olan bir yayın tanımına sahip olana kadar Kullanıcı arabiriminde görüntülenmez.
1. Sol tarafta **+ Yeni** düğmesine tıklayın ve ardından Işlem hattını **İçeri Aktar ' a**tıklayın.
1. **Görüntüle** ' ye tıklayın ve projenizden `samples/release.json` ' i seçin.
1. **Tamam**’a tıklayın. İşlem hattı bölmesinin yayın tanımı düzenleme sayfasıyla yüklendiğini unutmayın. Ayrıca, hala yapılandırılması gereken kümeye özgü ayrıntıları belirten bazı kırmızı uyarı simgeleri olduğunu unutmayın.
1. Ardışık düzen bölmesinin sol tarafında, **yapıt balonu Ekle** ' ye tıklayın.
1. **Kaynak** açılan listesinde, daha önce oluşturduğunuz derleme işlem hattını seçin.
1. **Varsayılan sürüm**Için, **en son yapı ardışık düzen varsayılan dalından etiketlerle**' ı seçin.
1. **Etiketleri** boş bırakın.
1. **Kaynak diğer adını** `drop` olarak ayarlayın. **Kaynak diğer adı** değeri, önceden tanımlanmış yayın görevleri tarafından kullanılır, bu nedenle ayarlanması gerekir.
1. **Ekle**'ye tıklayın.
1. Şimdi, aşağıda gösterildiği gibi yeni oluşturulan `drop` yapıt kaynağı üzerinde şimşek sürgüsü simgesine tıklayın:

    ![Yayın yapıtı sürekli dağıtım kurulumu](../media/common/release-artifact-cd-setup.png)
1. **Sürekli dağıtım tetikleyicisini**etkinleştirin.
1. İşlem **hattı** ' nın yanındaki **Görevler** sekmesinin üzerine gelin ve _dev aşama görevlerini_ düzenlemek için _geliştirme_ ' ye tıklayın.
1. **Azure Resource Manager** **bağlantı türü** altında seçildiğini doğrulayın. kırmızı renkle vurgulanmış üç açılan menü denetimini görürsünüz: ![Yayın tanımı kurulumu @ no__t-1
1. Azure Dev Spaces ile kullanmakta olduğunuz Azure aboneliğini seçin. Ayrıca **Yetkilendir**' e tıklamanız gerekebilir.
1. Azure Dev Spaces ile kullandığınız kaynak grubunu ve kümeyi seçin.
1. **Aracı işi**' ne tıklayın.
1. **Aracı havuzu**altında **barındırılan ubuntu 1604** ' ı seçin.
1. En üstteki **Görevler** seçicisinin üzerine _gelin, üretim aşaması görevlerini_ düzenlemek için _Üretim_ öğesine tıklayın.
1. **Azure Resource Manager** **bağlantı türü** altında seçildiğini doğrulayın. ve Azure Dev Spaces ile kullandığınız Azure aboneliğini, kaynak grubunu ve kümeyi seçin.
1. **Aracı işi**' ne tıklayın.
1. **Aracı havuzu**altında **barındırılan ubuntu 1604** ' ı seçin.
1. Yayınlarınızın değişkenlerini güncelleştirmek için **değişkenler** sekmesine tıklayın.
1. **Devspaceshostsuffix** değerini **UPDATE_ME** öğesinden konak sonekine güncelleştirin. Ana bilgisayar soneki, daha önce `azds show-context` komutunu çalıştırdığınızda görüntülenir.
1. Sağ üst köşedeki **Kaydet** ' e tıklayıp **Tamam**' a tıklayın.
1. **+ Release** (Kaydet düğmesinin yanında) seçeneğine tıklayın ve **bir yayın oluşturun**.
1. **Yapıtlar**bölümünde, derleme işlem hattınızdan en son derlemeyi doğrulayın.
1. **Oluştur**’a tıklayın.

Otomatik bir yayın işlemi başlar, şimdi _geliştirme_ üst düzey alanında *mywebapi* ve *Webön uç* grafiklerini Kubernetes kümenize dağıtacaksınız. Azure DevOps web portalında, yayınınızın ilerlemesini izleyebilirsiniz:

1. İşlem **hatları**altında **yayınlar** bölümüne gidin.
1. Örnek uygulama için yayın ardışık düzenine tıklayın.
1. En son sürümün adına tıklayın.
1. **Aşamalar** bölümündeki **geliştirme** kutusunun üzerine gelin ve **Günlükler**' e tıklayın.

Tüm görevler tamamlandığında yayın yapılır.

> [!TIP]
> Sürüm yükseltme başarısız olduğu bir hata iletisiyle başarısız olursa *: durum beklerken zaman aşımına uğradı*, [Kubernetes panosunu kullanarak](../../aks/kubernetes-dashboard.md)kümenizdeki kümelerinizi inceleyerek deneyin. Pod 'nin "azdsexample.azurecr.io/mywebapi:122" görüntüsünü çekmek için başarısız olan hata iletileriyle başlayabilmesinin başarısız olduğunu görürseniz *: RPC hatası: Code = Unknown DESC = arka plan programından hata yanıtı: Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: yetkisiz: kimlik doğrulaması gerekli*, bu olabilir kümenizin Azure Container Registry alma yetkisi olmadığı için. [AKS kümenizi Azure Container Registry önkoşulinizden çekmek üzere yetkilendirmeyi](../../aks/cluster-container-registry-integration.md) tamamladığınızdan emin olun.

Artık dev Spaces örnek uygulamalarının GitHub çatalınız için tam otomatik bir CI/CD işlem hattına sahipsiniz. Kodu her işlediğinizde ve gönderişinizde, derleme işlem hattı *mywebapi* ve *webön uç* görüntülerini özel ACR Örneğinizde derleyip göndermeyecektir. Ardından, yayın işlem hattı her bir uygulama için hele grafiğini, dev Spaces özellikli kümenizdeki _dev_ alanına dağıtır.

## <a name="accessing-your-_dev_-services"></a>_Geliştirme_ hizmetlerinize erişme
Dağıtımdan sonra *webön uç* 'nin _dev_ sürümüne: `http://dev.webfrontend.fedcba098.eus.azds.io` gibi genel bir URL ile erişilebilir. @No__t-0 komutunu çalıştırarak bu URL 'YI bulabilirsiniz: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Üretime dağıtma

Bu öğreticide oluşturulan CI/CD sistemini kullanarak belirli bir yayını _Üretim_ için el ile yükseltmek için:
1. İşlem **hatları**altında **yayınlar** bölümüne gidin.
1. Örnek uygulama için yayın ardışık düzenine tıklayın.
1. En son sürümün adına tıklayın.
1. **Aşamalar** altında **Üretim** kutusunun üzerine gelin ve **Dağıt**' a tıklayın.
    @no__t-üretime Yükselt @ no__t-1
1. **Aşamalar** altında **Üretim** kutusunun üzerine gelin ve **Günlükler**' e tıklayın.

Tüm görevler tamamlandığında yayın yapılır.

CI/CD işlem hattının _Üretim_ aşaması, _Üretim_ Hizmetleri 'Ne erişim sağlamak üzere dev Spaces giriş denetleyicisi yerine yük dengeleyici kullanır. _Üretim_ aşamasında DAĞıTıLAN hizmetlere DNS adları yerine IP adresleri olarak erişilebilir. Bir üretim ortamında, hizmetlerinizi kendi DNS yapılandırmanıza göre barındırmak için kendi giriş denetleyicinizi oluşturmayı seçebilirsiniz.

Web ön uç hizmetinin IP 'sini öğrenmek için, günlük çıktısını genişletmek üzere **Web ön uç genel IP 'Sini Yazdır** adımına tıklayın. **Web ön uç** uygulamasına erişmek için günlük ÇıKTıSıNDA görüntülenecek IP 'yi kullanın.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Üretimde dev Spaces izleme
Geliştirme alanları izleme, uygulamanızın normal çalışma yöntemiyle _değil_ tasarlanmasa da, üretim iş yüklerinizi dev alanları ile etkinleştirilmemiş bir Kubernetes ad alanında çalıştırmayı öneririz. Bu tür Kubernetes ad alanını kullanmak, `kubectl` CLı kullanarak üretim ad alanınızı oluşturmanız ya da CI/CD sisteminizin ilk helk dağıtımı sırasında bunu oluşturmasına izin vermeniz gerekir. Geliştirme alanları araçlarını kullanarak bir alan _seçme_ veya başka bir şekilde oluşturma, bu ad alanına dev Spaces araçları ekler.

İşte, tek bir Kubernetes kümesinde Özellik geliştirmeyi, ' dev ' ortamını _ve_ üretimi destekleyen örnek bir ad alanı yapısı:

![Örnek ad alanı yapısı](../media/common/cicd-namespaces.png)

> [!Tip]
> Zaten bir `prod` alanı oluşturduysanız ve bunu dev Spaces araçları 'ndan dışlamak istiyorsanız (It!), bunu aşağıdaki dev Spaces CLı komutuyla yapabilirsiniz:
>
> `azds space remove -n prod --no-delete`
>
> @No__t-0 ad alanındaki tüm Pod 'leri, geliştirme alanları izleme olmadan yeniden oluşturulabilen şekilde silmeniz gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Dev Spaces kullanarak takım geliştirme hakkında bilgi edinin](../team-development-netcore.md)