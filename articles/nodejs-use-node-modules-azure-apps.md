---
title: Node.js modülleriyle çalışma
description: Azure App Service veya Cloud Services kullanırken Node.js modüllerle nasıl çalışacağınızı öğrenin.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542227"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Azure uygulamalarıyla Node.js Modüllerini kullanma
Bu belge, Azure 'da barındırılan uygulamalarla Node.js modülleri kullanma hakkında rehberlik sağlar. Uygulamanızın belirli bir modülün belirli bir sürümünü kullanmasını ve Azure ile yerel modüller kullanmasını sağlamaya yönelik rehberlik sağlar.

Dosyalar üzerinde **package.js** ve **npm-shrinkwrap.js** Node.js modüller kullanmayı zaten biliyorsanız, aşağıdaki bilgiler bu makalede ele alındıklarla ilgili hızlı bir Özet sağlar:

* Azure App Service dosyalar **üzerindepackage.js** ve **npm-shrinkwrap.js** anlama ve bu dosyalardaki girişlere göre modüller yükleyebilir.

* Azure Cloud Services, dağıtım paketinin bir parçası olarak dahil edilecek tüm modüllerin geliştirme ortamında ve **düğüm \_ modülleri** dizininde yüklü olmasını bekler. Cloud Services dosyalar üzerinde **package.js** veya **npm-shrinkwrap.js** kullanarak modül yükleme desteğini etkinleştirmek mümkündür; Ancak, bu yapılandırma, bulut hizmeti projeleri tarafından kullanılan varsayılan betiklerin özelleştirilmesini gerektirir. Bu ortamın nasıl yapılandırılacağı hakkında bir örnek için, [düğüm modüllerini dağıtmaktan kaçınmak üzere NPM yüklemesini çalıştırmak Için Azure başlangıç görevi](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) ' ne bakın

> [!NOTE]
> Azure sanal makineleri bu makalede ele alınmamaktadır, çünkü bir VM 'deki dağıtım deneyimi, sanal makine tarafından barındırılan işletim sistemine bağımlıdır.
>
>

## <a name="nodejs-modules"></a>Node.js modüller
Modüller, uygulamanız için belirli işlevler sağlayan, yüklenebilir JavaScript paketlerdir. Modüller genellikle **NPM** komut satırı aracı kullanılarak yüklenir, ancak bazı modüller (örneğin http modülü) çekirdek Node.js paketinin bir parçası olarak sağlanır.

Modüller yüklendiğinde, uygulama dizin yapınızın kökündeki **düğüm \_ modülleri** dizininde depolanır. **Düğüm \_ modülleri** dizinindeki her modül, bağımlı olduğu tüm modülleri içeren kendi dizinini korur ve bu davranış her modül için bağımlılık zincirinin her bir şekilde yinelenir. Bu ortam, her modülün bağımlı olduğu modüller için kendi sürüm gereksinimlerine sahip olmasına olanak sağlar, ancak büyük bir dizin yapısına neden olabilir.

Uygulamanızın bir parçası olarak **düğüm \_ modülleri** dizininin dağıtılması, dosyanın üzerinde bir **package.js** kullanma veya **npm-shrinkwrap.js** ile karşılaştırıldığında dağıtım boyutunu artırır; ancak, üretimde kullanılan modüllerin sürümlerinin geliştirmede kullanılan modüllerle aynı olmasını güvence altına alır.

### <a name="native-modules"></a>Yerel modüller
Çoğu modül basit metin JavaScript dosyalarıdır ancak bazı modüller platforma özgü ikili görüntülerdir. Bu modüller, genellikle Python ve Node-cayp kullanılarak, saat olarak derlenir. Azure Cloud Services, uygulamanın bir parçası olarak dağıtılmakta olan **düğüm \_ modülleri** klasörüne güvendiğinden, yüklü modüllerin bir parçası olarak dahil edilen tüm yerel modüllerin bir Windows geliştirme sisteminde yüklü ve derlenmiş olduğu sürece bir bulut hizmetinde çalışması gerekir.

Azure App Service tüm yerel modülleri desteklemez ve belirli önkoşullara sahip modüller derlenirken başarısız olabilir. MongoDB gibi bazı popüler modüller isteğe bağlı yerel bağımlılıklara sahip olur ve bunlar olmadan ince çalışır, günümüzde neredeyse tüm yerel modüllerle başarılı bir şekilde iki geçici çözüm sunulmaktadır:

* Tüm yerel modülün ön koşullarını yüklemiş olan bir Windows makinesinde **NPM yükleme** çalıştırın. Ardından, oluşturulan **düğüm \_ modülleri** klasörünü Azure App Service için uygulamanın bir parçası olarak dağıtın.

  * Derlenmeden önce, yerel Node.js kurulumunuzun aynı mimariye sahip olduğunu ve sürümün Azure 'da kullanılan bir sürüme (geçerli değerler, çalışma zamanında, Özellikler **işlemi. Arch** ve **Process. Version**) denetlenebilmesini kontrol edin.

* Azure App Service dağıtım sırasında özel bash veya Shell betikleri yürütmek üzere yapılandırılabilir, böylece özel komutları yürütme ve **NPM yüklemesinin** çalıştırılma biçimini tam olarak yapılandırma fırsatına sahip olursunuz. Bu ortamın nasıl yapılandırılacağını gösteren bir video için bkz. [kudu Ile özel Web sitesi dağıtım betikleri](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Dosya üzerinde package.jskullanma

**package.js** dosyadaki en üst düzey bağımlılıklarını belirtmenin bir yolu, barındırma platformunun, uygulamanın bir parçası olarak **düğüm \_ modülleri** klasörünü içermesini gerektirmek yerine, bağımlılıkları yükleyebilmeleri için bir yoldur. Uygulama dağıtıldıktan sonra, dosyadaki **package.js** ayrıştırmak ve listelenen tüm bağımlılıkları yüklemek için **NPM install** komutu kullanılır.

Geliştirme sırasında, modül yüklerken **--** Kaydet, **--Save-dev** veya **--Save-isteğe bağlı** parametrelerini, dosya **package.js** otomatik olarak bir modüle eklemek için kullanabilirsiniz. Daha fazla bilgi için bkz. [NPM-Install](https://docs.npmjs.com/cli/install).

Dosyadaki **package.js** olası bir sorun yalnızca en üst düzey bağımlılıkların sürümünü belirttiğinden emin değildir. Yüklü her modül, bağımlı olduğu modüllerin sürümünü gösterebilir veya belirtemez ve bu nedenle, geliştirmede kullanılandan farklı bir bağımlılık zinciriyle bitebilmeniz mümkündür.

> [!NOTE]
> Azure App Service ' ye dağıtım yaparken, dosya <b>package.js</b> yerel bir modüle başvuruyorsa, git kullanarak uygulamayı yayımlarken aşağıdaki örneğe benzer bir hata görebilirsiniz:
>
> NPM hatası! module-name@0.6.0 Install: ' node-cayp configure Build '
>
> NPM hatası! ' cmd "/c" "node-cayp configure derlemesi" ', 1 ile başarısız oldu
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Dosya üzerinde npm-shrinkwrap.jskullanma
Dosyadaki **npm-shrinkwrap.js** , dosyadaki **package.js** Modül sürümü oluşturma sınırlamalarını ele almak için girişimde bulunur. Dosya **package.js** yalnızca üst düzey modüller için sürümler içerdiğinden, **npm-shrinkwrap.json** dosyası tam modül bağımlılığı zincirinin sürüm gereksinimlerini içerir.

Uygulamanız üretime hazırsanız, sürüm gereksinimlerini kilitleyebilir ve **NPM shrınkwrap** komutunu kullanarak dosya üzerinde bir **npm-shrinkwrap.js** oluşturabilirsiniz. Bu komut, **düğüm \_ modülleri** klasöründe yüklü olan sürümleri kullanır ve bu sürümleri dosyadaki **npm-shrinkwrap.js** kaydeder. Uygulama barındırma ortamına dağıtıldıktan sonra, dosyadaki **npm-shrinkwrap.js** ayrıştırmak ve listelenen tüm bağımlılıkları yüklemek için **NPM install** komutu kullanılır. Daha fazla bilgi için bkz. [NPM-shrınkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Azure App Service ' ye dağıtım yaparken, dosya <b>npm-shrinkwrap.js</b> yerel bir modüle başvuruyorsa, git kullanarak uygulamayı yayımlarken aşağıdaki örneğe benzer bir hata görebilirsiniz:
>
> NPM hatası! module-name@0.6.0 Install: ' node-cayp configure Build '
>
> NPM hatası! ' cmd "/c" "node-cayp configure derlemesi" ', 1 ile başarısız oldu
>
>

## <a name="next-steps"></a>Sonraki adımlar
Azure ile Node.js modüllerinin nasıl kullanılacağını anladığınıza göre, [Node.js sürümünü](https://github.com/squillace/nodejs-microservice)nasıl kullanacağınızı, [Node.js web uygulaması derleyip Dağıteceğinizi](app-service/quickstart-nodejs.md)ve [Mac ve Linux için Azure Command-Line arabirimini nasıl kullanacağınızı](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)öğrenin.

Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi](/azure/developer/javascript/).

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo