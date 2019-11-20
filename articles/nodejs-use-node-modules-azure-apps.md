---
title: Node. js modülleriyle çalışma
description: Azure App Service veya Cloud Services kullanırken Node. js modülleriyle çalışmayı öğrenin.
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
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309268"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Azure uygulamalarıyla Node.js Modüllerini kullanma
Bu belge, Azure 'da barındırılan uygulamalarla Node. js modüllerini kullanma hakkında rehberlik sağlar. Uygulamanızın belirli bir modülün belirli bir sürümünü kullanmasını ve Azure ile yerel modüller kullanmasını sağlamaya yönelik rehberlik sağlar.

Node. js modüllerini, **Package. JSON** ve **NPM-shrınkwrap. JSON** dosyalarını kullanmayı zaten biliyorsanız, aşağıdaki bilgiler bu makalede ele alındıklarla ilgili hızlı bir Özet sağlar:

* Azure App Service **Package. JSON** ve **NPM-shrınkwrap. JSON** dosyalarını anladığından, bu dosyalardaki girişlere göre modüller yükleyebilirsiniz.

* Azure Cloud Services, dağıtım paketinin bir parçası olarak dahil edilecek tüm modüllerin geliştirme ortamında ve **düğüm\_modülleri** dizininde yüklü olmasını bekler. Cloud Services; ' de **Package. JSON** veya **NPM-shrınkwrap. JSON** dosyalarını kullanarak modül yükleme desteğini etkinleştirmek mümkündür; Ancak, bu yapılandırma, bulut hizmeti projeleri tarafından kullanılan varsayılan betiklerin özelleştirilmesini gerektirir. Bu ortamın nasıl yapılandırılacağı hakkında bir örnek için, [düğüm modüllerini dağıtmaktan kaçınmak üzere NPM yüklemesini çalıştırmak Için Azure başlangıç görevi](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) ' ne bakın

> [!NOTE]
> Azure sanal makineleri bu makalede ele alınmamaktadır, çünkü bir VM 'deki dağıtım deneyimi, sanal makine tarafından barındırılan işletim sistemine bağımlıdır.
> 
> 

## <a name="nodejs-modules"></a>Node. js modülleri
Modüller, uygulamanız için belirli işlevler sağlayan, yüklenebilir JavaScript paketlerdir. Modüller genellikle **NPM** komut satırı aracı kullanılarak yüklenir, ancak bazı modüller (http modülü gibi) çekirdek Node. js paketinin bir parçası olarak sağlanır.

Modüller yüklendiğinde, uygulama dizin yapınızın kökündeki **düğüm\_modülleri** dizininde depolanır. **Düğüm\_modülleri** dizinindeki her modül, bağımlı olduğu tüm modülleri içeren kendi dizinini korur ve bu davranış her modül için bağımlılık zincirinin her bir şekilde yinelenir. Bu ortam, her modülün bağımlı olduğu modüller için kendi sürüm gereksinimlerine sahip olmasına olanak sağlar, ancak büyük bir dizin yapısına neden olabilir.

**Düğüm\_modülleri** dizinini uygulamanızın bir parçası olarak dağıtmak, bir **Package. JSON** veya **NPM-shrınkwrap. JSON** dosyası kullanmaya kıyasla dağıtımın boyutunu artırır; ancak, sürümlerinin Üretimde kullanılan modüller, geliştirmede kullanılan modüllerle aynıdır.

### <a name="native-modules"></a>Yerel modüller
Çoğu modül basit metin JavaScript dosyalarıdır ancak bazı modüller platforma özgü ikili görüntülerdir. Bu modüller, genellikle Python ve Node-cayp kullanılarak, saat olarak derlenir. Azure Cloud Services, uygulamanın bir parçası olarak dağıtılmakta olan **düğüm\_modülleri** klasörünü kullandığından, yüklü modüllerin bir parçası olarak dahil edilen tüm yerel modüllerin bir bulut hizmetinde yüklenmiş ve derlenmiş olduğu sürece bir Windows geliştirme sistemi.

Azure App Service tüm yerel modülleri desteklemez ve belirli önkoşullara sahip modüller derlenirken başarısız olabilir. MongoDB gibi bazı popüler modüller isteğe bağlı yerel bağımlılıklara sahip olur ve bunlar olmadan ince çalışır, günümüzde neredeyse tüm yerel modüllerle başarılı bir şekilde iki geçici çözüm sunulmaktadır:

* Tüm yerel modülün ön koşullarını yüklemiş olan bir Windows makinesinde **NPM yükleme** çalıştırın. Ardından, oluşturulan **düğüm\_modülleri** klasörünü Azure App Service için uygulamanın bir parçası olarak dağıtın.

  * Derlenmeden önce, yerel Node. js yüklemenizin eşleşen mimariye sahip olduğunu ve sürümün Azure 'da kullanılan bir şekilde (geçerli değerler, çalışma zamanında çalışma zamanında denetlenebilir **. mimari** ve **Process. Version) olup olmadığını denetleyin.** ).

* Azure App Service dağıtım sırasında özel bash veya Shell betikleri yürütmek üzere yapılandırılabilir, böylece özel komutları yürütme ve **NPM yüklemesinin** çalıştırılma biçimini tam olarak yapılandırma fırsatına sahip olursunuz. Bu ortamın nasıl yapılandırılacağını gösteren bir video için bkz. [kudu Ile özel Web sitesi dağıtım betikleri](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Bir Package. JSON dosyası kullanma

**Package. JSON** dosyası, uygulamanızın gerektirdiği en üst düzey bağımlılıkları belirtmenin bir yoludur. böylece, barındırma platformunun, **\_düğüm modülleri** klasörünü bir parçası olarak dahil etmeniz gerekmez. dağıtmak. Uygulama dağıtıldıktan sonra, **Package. JSON** dosyasını ayrıştırmak ve listelenen tüm bağımlılıkları yüklemek için **NPM install** komutu kullanılır.

Geliştirme sırasında, modüle **Package. JSON** dosyanıza otomatik olarak bir giriş eklemek üzere modüller yüklerken **--** Save, **--Save-dev**veya--Save- **isteğe bağlı** parametrelerini kullanabilirsiniz. Daha fazla bilgi için bkz. [NPM-Install](https://docs.npmjs.com/cli/install).

**Package. JSON** dosyası ile ilgili olası bir sorun yalnızca en üst düzey bağımlılıklara yönelik sürümü belirtir. Yüklü her modül, bağımlı olduğu modüllerin sürümünü gösterebilir veya belirtemez ve bu nedenle, geliştirmede kullanılandan farklı bir bağımlılık zinciriyle bitebilmeniz mümkündür.

> [!NOTE]
> Azure App Service ' ye dağıtım yaparken, <b>Package. JSON</b> dosyanız yerel bir modüle başvuruyorsa, git kullanarak uygulamayı yayımlarken aşağıdaki örneğe benzer bir hata görebilirsiniz:
> 
> NPM hatası! module-name@0.6.0Install: ' node-cayp configure Build '
> 
> NPM hatası! ' cmd "/c" "node-cayp configure derlemesi" ', 1 ile başarısız oldu
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>NPM-shrınkwrap. JSON dosyası kullanma
**NPM-shrınkwrap. JSON** dosyası, **Package. JSON** dosyasının Modül sürümü oluşturma sınırlamalarını ele almak için bir girişimdir. **Package. JSON** dosyası yalnızca üst düzey modüllerin sürümlerini içerdiğinden, **NPM-shrınkwrap. JSON** dosyası tam modül bağımlılığı zincirinin sürüm gereksinimlerini içerir.

Uygulamanız üretime hazırsanız, sürüm gereksinimlerini kilitleyebilir ve **NPM shrınkwrap** komutunu kullanarak **NPM-shrınkwrap. JSON** dosyası oluşturabilirsiniz. Bu komut, **düğüm\_modülleri** klasöründe yüklü olan sürümleri kullanır ve bu sürümleri **NPM-shrınkwrap. JSON** dosyasına kaydeder. Uygulama barındırma ortamına dağıtıldıktan sonra **NPM-shrınkwrap. JSON** dosyasını ayrıştırmak ve listelenen tüm bağımlılıkları yüklemek için **NPM install** komutu kullanılır. Daha fazla bilgi için bkz. [NPM-shrınkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Azure App Service ' ye dağıtım yaparken, <b>NPM-shrınkwrap. JSON</b> dosyanız yerel bir modüle başvuruyorsa, git kullanarak uygulamayı yayımlarken aşağıdaki örneğe benzer bir hata görebilirsiniz:
> 
> NPM hatası! module-name@0.6.0Install: ' node-cayp configure Build '
> 
> NPM hatası! ' cmd "/c" "node-cayp configure derlemesi" ', 1 ile başarısız oldu
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Azure ile Node. js modüllerini nasıl kullanacağınızı anladığınıza göre, Node. [js sürümünü belirtme](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [Node. js web uygulaması oluşturma ve dağıtma](app-service/app-service-web-get-started-nodejs.md)ve [Mac ve Linux için Azure komut satırı arabirimini kullanma](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)hakkında bilgi edinin.

Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
