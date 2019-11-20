---
title: Uygulama yükseltme öğreticisini Service Fabric | Microsoft Docs
description: Bu makalede bir Service Fabric uygulaması dağıtma, kodu değiştirme ve Visual Studio kullanarak bir yükseltmeyi kullanıma alma deneyimi gösterilmektedir.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 5e693a219c4a430f742ebd27878518ebb99ce5da
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167365"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Visual Studio kullanarak uygulama yükseltme öğreticisini Service Fabric
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric, yalnızca değiştirilen hizmetlerin yükseltildiğini ve yükseltme işlemi boyunca uygulama durumunun izlendiğini sağlayarak bulut uygulamalarını yükseltme sürecini basitleştirir. Ayrıca, sorunlarla karşılaşıldığında otomatik olarak uygulamayı önceki sürüme geri alır. Uygulamanın kapalı kalma süresi olmadan yükseltilemediğinden, uygulama yükseltmelerinin Service Fabric *sıfır kapalı kalma süresi*vardır. Bu öğreticide, Visual Studio 'dan bir sıralı yükseltmenin nasıl tamamlanacağı ele alınmaktadır.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1\. Adım: görsel nesneler örneğini derleme ve yayımlama
İlk olarak, GitHub 'dan [görsel nesneler](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) uygulamasını indirin. Ardından uygulama projesine sağ tıklayıp, **Visualobjects**' i sağ tıklayıp Service Fabric menü öğesinde **Yayımla** komutunu seçerek uygulamayı derleyin ve yayımlayın.

![Service Fabric uygulaması için bağlam menüsü][image1]

**Yayımla** ' yı seçtiğinizde bir açılan pencere açılır ve **hedef profili** **publishprofiles\local.xml**olarak ayarlayabilirsiniz. **Yayımla**' ya tıklamadan önce pencere aşağıdaki gibi görünmelidir.

![Service Fabric uygulaması yayımlama][image2]

Artık iletişim kutusunda **Yayımla** ' ya tıklayabilirsiniz. [Kümeyi ve uygulamayı görüntülemek için Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)kullanabilirsiniz. Görsel nesneler uygulamasının, tarayıcınızın adres çubuğuna [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) yazarak gidebilmeniz için bir Web hizmeti vardır.  Ekranda hareket eden 10 kayan görsel nesne görmeniz gerekir.

**Note:** @No__t-1 profiline (Azure Service Fabric) dağıtım yapıyorsanız, uygulamanın **http://{ServiceFabricName} konumunda kullanılabilir olması gerekir. { Region}. cloudapp. Azure. com: 8081/visualobjects/** . Load Balancer yapılandırılmış `8081/TCP` olduğundan emin olun (Service Fabric örneğiyle aynı kaynak grubunda Load Balancer bulun).

## <a name="step-2-update-the-visual-objects-sample"></a>2\. Adım: görsel nesneler örneğini güncelleştirme
Adım 1 ' de dağıtılan sürümde, görsel nesnelerin döndürüleceğini fark edebilirsiniz. Bu uygulamayı görsel nesnelerin de her yerde de döndürelim.

VisualObjects çözüm içindeki VisualObjects. ActorService projesini seçin ve **VisualObjectActor.cs** dosyasını açın. Bu dosya içinde `MoveObject` yöntemine gidin, açıklama `visualObject.Move(false)` ' i ve `visualObject.Move(true)` ' yi kaldırın. Bu kod değişikliği, hizmet yükseltildikten sonra nesneleri döndürür.  Artık değiştirilen projeleri oluşturan **çözümü oluşturabilir (yeniden derlenemez)** . *Tümünü yeniden derle*' yi seçerseniz, tüm projelerin sürümlerini güncelleştirmeniz gerekir.

Uygulamamız da bizim için de gereklidir. **Visualobjects** projesine sağ tıkladıktan sonra sürüm değişikliği yapmak Için, Visual Studio **bildirim sürümlerini Düzenle** seçeneğini kullanabilirsiniz. Bu seçeneğin belirlenmesi, sürüm sürümleri için iletişim kutusunu aşağıdaki şekilde getirir:

![Sürüm oluşturma iletişim kutusu][image3]

Değiştirilen projeler ve bunların kod paketleri için sürümleri, 2.0.0 sürümüne uygulama ile birlikte güncelleştirin. Değişiklikler yapıldıktan sonra, bildirim aşağıdaki gibi görünmelidir (kalın bölümler değişiklikleri gösterir):

![Sürümler güncelleştiriliyor][image4]

Visual Studio Araçları, **uygulama ve hizmet sürümlerini otomatik olarak güncelleştir '** i seçtikten sonra otomatik sürüm paketleri gerçekleştirebilir. [Semver](http://www.semver.org)kullanıyorsanız, bu seçenek belirlenmişse kodu ve/veya yapılandırma paketi sürümünü tek başına güncelleştirmeniz gerekir.

Değişiklikleri kaydedin ve şimdi **uygulama kutusunu Yükselt** ' i kontrol edin.

## <a name="step-3--upgrade-your-application"></a>3\. Adım: uygulamanızı yükseltme
Uygulanabilecek çeşitli yükseltme parametrelerini, zaman aşımlarını ve durum ölçütlerini iyi anlamak için [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md) ve [yükseltme işlemi](service-fabric-application-upgrade.md) hakkında bilgi edinin. Bu izlenecek yol için hizmet durumu değerlendirmesi ölçütü varsayılan (izlenmeyen mod) olarak ayarlanır. Bu ayarları, **yükseltme ayarlarını yapılandır** ' ı seçerek ve parametreleri istediğiniz şekilde değiştirerek yapılandırabilirsiniz.

Şimdi **Yayımla**' yı seçerek uygulama yükseltmesini başlatacak şekilde ayarlanıyoruz. Bu seçenek, uygulamanızı nesnenin döndürüleceği 2.0.0 sürümüne yükseltir. Service Fabric, tek seferde bir güncelleştirme etki alanını yükseltir (bazı nesneler önce, sonra diğerleri tarafından güncelleştirilir) ve yükseltme sırasında hizmet erişilebilir kalır. Hizmete erişim, istemciniz (tarayıcı) aracılığıyla denetlenebilir.  

Uygulama yükseltmesi devam ettikçe, uygulamalar altında **yükseltmeler devam eden** sekmesini kullanarak Service Fabric Explorer ile izleyebilirsiniz.

Birkaç dakika içinde tüm güncelleştirme etki alanları yükseltilmelidir (tamamlandı) ve Visual Studio çıktı penceresi de yükseltmenin tamamlandığını de bilmelidir. Ve tarayıcı pencerinizdeki *Tüm* görsel nesnelerin şimdi döndürüleceğini de fark etmelisiniz!

Sürümleri değiştirmeyi ve sürüm 2.0.0 ' den sürüm 3.0.0 ' den sürüm 1.0.0 'ye geçmeyi denemek isteyebilirsiniz. Zaman aşımları ve sistem durumu ilkeleriyle oynayın ve bunlarla ilgili bilgi sahibi olun. Yerel bir kümenin aksine bir Azure kümesine dağıtım yaparken, kullanılan parametrelerin farklı olması gerekebilir. Zaman aşımlarını tasarrufunu ayarlamanız önerilir.

## <a name="next-steps"></a>Sonraki adımlar
[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltildiğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

[Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
