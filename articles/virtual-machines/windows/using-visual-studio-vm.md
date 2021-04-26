---
title: Azure sanal makinesinde Visual Studio 'Yu kullanma
description: Azure sanal makinesinde Visual Studio 'Yu kullanma.
author: andysterland
manager: andster
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: andster
keywords: visualstudio
ms.openlocfilehash: f26bec64012e0b0909b7df5422c57ff2cb1c347e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478565"
---
# <a name="visual-studio-images-on-azure"></a>Azure 'da Visual Studio görüntüleri
Visual Studio 'Yu önceden yapılandırılmış bir Azure sanal makinesinde (VM) kullanmak, herhangi bir şey için bir şeyi çalışır duruma getirmenin bir geliştirme ortamına hızlı ve kolay bir yoludur. Farklı Visual Studio yapılandırmalarına sahip sistem görüntüleri [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)'nde kullanılabilir.

Azure’da yeni misiniz? [Ücretsiz bir Azure hesabı oluşturun](https://azure.microsoft.com/free).

> [!NOTE]
> Tüm abonelikler Windows 10 görüntülerini dağıtmaya uygun değildir. Daha fazla bilgi için bkz. [geliştirme ve test senaryoları Için Azure 'Da Windows Istemcisini kullanma](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Hangi yapılandırma ve sürümler mevcuttur?
En son ana sürümlere ait görüntüler, Visual Studio 2019, Visual Studio 2017 ve Visual Studio 2015, Azure Marketi 'nde bulunabilir.  Yayınlanan her ana sürüm için, başlangıçta "Web 'e Yayınlandı" (RTW) sürümü ve en son güncelleştirilmiş sürümleri görürsünüz.  Bu sürümlerin her biri, Visual Studio Enterprise ve Visual Studio Community sürümlerini sunar.  En son Visual Studio ve Windows güncelleştirmelerini dahil etmek için bu görüntüler en az her ay güncellenir.  Görüntülerin adları aynı olmaya devam ederken, her bir görüntünün açıklaması yüklü ürün sürümünü ve görüntünün "itibariyle" tarihini içerir.

| Yayın sürümü                                                                                                                                                | Sürümler              | Ürün sürümü   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: en son (sürüm 16,8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Kuruluş, topluluk | Sürüm 16.8.0    |
| Visual Studio 2019: RTW                         | Kurumsal | Sürüm 16.0.20    |
| Visual Studio 2017: en son (sürüm 15,9)           | Kuruluş, topluluk | Sürüm 15.9.29   |
| Visual Studio 2017: RTW                             | Kuruluş, topluluk | Sürüm 15.0.28  |
| Visual Studio 2015: latest (güncelleştirme 3)               | Kuruluş, topluluk | Sürüm 14.0.25431.01 |

> [!NOTE]
> Microsoft hizmet ilkesine uygun olarak, Visual Studio 2015 ' nin ilk olarak yayınlanan (RTW) sürümü bakım için sona ermiştir. Visual Studio 2015 güncelleştirme 3, Visual Studio 2015 ürün satırı için sunulan tek sürümdür.

Daha fazla bilgi için bkz. [Visual Studio bakım ilkesi](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Hangi özellikler yüklendi?
Her görüntü, bu Visual Studio sürümü için önerilen özellik kümesini içerir. Genellikle, yükleme şunları içerir:

* Her iş yükünün önerilen isteğe bağlı bileşenleri dahil olmak üzere tüm kullanılabilir iş yükleri. Visual Studio 'da bulunan iş yükleri, bileşenler ve SDK 'lar hakkında daha fazla ayrıntı [Visual Studio belgelerinde](https://docs.microsoft.com/visualstudio/install/workload-and-component-ids) bulunabilir
* .NET 4.6.2 ve .NET 4,7 SDK 'Ları, hedeflenen paketleri ve Geliştirici Araçları
* Visual F#
* Visual Studio için GitHub Uzantısı
* LINQ to SQL araçları

Görüntüleri oluştururken Visual Studio 'Yu yüklemek için kullanılan komut satırı aşağıdaki gibidir:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Görüntüler için gereken bir Visual Studio özelliği içermiyorsa, sayfanın sağ üst köşesindeki geri bildirim aracı aracılığıyla geri bildirim sağlayın.

## <a name="what-size-vm-should-i-choose"></a>Hangi boyutta VM 'yi seçmem gerekir?
Azure, sanal makine boyutlarının tam bir aralığını sunar. Visual Studio güçlü, çok iş parçacıklı bir uygulama olduğundan, en az iki işlemci ve 7 GB bellek içeren bir VM boyutu istersiniz. Visual Studio görüntüleri için aşağıdaki VM boyutlarını öneririz:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
En son makine boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da Windows sanal makineleri Için boyutlar](../sizes.md).

Azure ile, sanal makineyi yeniden boyutlandırarak başlangıç seçiminizi yeniden dengelemeniz sağlayabilirsiniz. Daha uygun bir boyuta sahip yeni bir VM sağlayabilir veya mevcut sanal makineyi farklı temel donanımla yeniden boyutlandırabilirsiniz. Daha fazla bilgi için bkz. [Windows VM 'Yi yeniden boyutlandırma](./resize-vm.md).

## <a name="after-the-vm-is-running-whats-next"></a>VM çalışmaya başladıktan sonra ne var?
Visual Studio, "kendi lisansını getir" modelini Azure 'da izler. Özel donanım yüklemesinde olduğu gibi, ilk adımlardan biri Visual Studio yüklemenizi lisanslandır. Visual Studio 'Nun kilidini açmak için şunlardan birini yapın:
- Visual Studio aboneliğiyle ilişkili bir Microsoft hesabı oturum açın 
- Visual Studio 'Yu ilk satın alımınızla birlikte gelen ürün anahtarıyla birlikte açın

Daha fazla bilgi için bkz. [Visual Studio 'Da oturum açma](/visualstudio/ide/signing-in-to-visual-studio) ve [Visual Studio 'nun kilidini açma](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Geliştirme sanal makinesini gelecekteki veya ekip kullanımı için Nasıl yaparım? kaydetmek istiyor musunuz?

Geliştirme ortamlarının yelpazesi çok büyük ve daha karmaşık ortamları oluşturmaya ilişkin gerçek maliyet vardır. Ortamınızın yapılandırmasına bakılmaksızın, yapılandırılmış sanal makineyi gelecekte kullanılmak üzere veya takımınızın diğer üyeleri için "temel görüntü" olarak kaydedebilir veya yakalayabilirsiniz. Ardından, yeni bir VM 'yi önyüklerken Azure Market görüntüsü yerine temel görüntüden temin edersiniz.

Hızlı Özet: Sistem Hazırlama Aracı 'nı (Sysprep) kullanın ve çalışan VM 'yi kapatın ve ardından Azure portal VM 'yi bir görüntü olarak *(Şekil 1)* sanal makine aracılığıyla yakalayın. Azure `.vhd` , görüntüyü içeren dosyayı seçtiğiniz depolama hesabına kaydeder. Yeni görüntü daha sonra, aboneliğinizin kaynak listesinde bir görüntü kaynağı olarak gösterilir.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Şekil 1) Azure portal kullanıcı arabiriminden bir görüntü yakalayın.*</center>

Daha fazla bilgi için bkz. [Azure 'da Genelleştirilmiş BIR VM 'nin yönetilen görüntüsünü oluşturma](./capture-image-resource.md).

> [!IMPORTANT]
> VM 'yi hazırlamak için Sysprep 'ı kullanmayı unutmayın. Bu adımı kaçırırsanız Azure görüntüden bir VM sağlayamaz.

> [!NOTE]
> Görüntülerin depolanması için yine de bazı maliyetlerle karşılaşmanız gerekir, ancak bu artımlı maliyet, bir tane gerektiren her ekip üyesi için VM 'nin sıfırdan yeniden derlenmesi için ek gider maliyetlerine kıyasla çok önemli olabilir. Örneğin, tüm ekibiniz tarafından yeniden kullanılabilen bir ayda 127 GB görüntüsünü oluşturmak ve depolamak için birkaç dolar maliyeti vardır. Ancak, bu maliyetler her bir çalışanın, bireysel kullanımları için düzgün yapılandırılmış bir dev kutusunu oluşturmak ve doğrulamak üzere her çalışanın yatırım yaptıkları saatlere kıyasla önem altına alınır.

Ayrıca, geliştirme görevleriniz veya teknolojilerinizin, geliştirme yapılandırmalarının ve birden çok makine yapılandırmasının değişen özellikleri gibi daha fazla ölçeği olması gerekebilir. "Altın görüntü" oluşturmayı otomatikleştiren _Tarifler_ oluşturmak için Azure DevTest Labs kullanabilirsiniz. Takımınızın çalışan VM 'Lerinin ilkelerini yönetmek için DevTest Labs de kullanabilirsiniz. [Geliştiriciler için Azure DevTest Labs kullanmak](../../devtest-labs/devtest-lab-developer-lab.md) , DevTest Labs hakkında daha fazla bilgi için en iyi kaynaktır.

## <a name="next-steps"></a>Sonraki adımlar
Önceden yapılandırılmış Visual Studio görüntülerini öğrenmiş olduğunuza göre, sonraki adım yeni bir VM oluşturmaktır:

* [Azure portal aracılığıyla VM oluşturma](quick-create-portal.md)
* [Windows Sanal Makineleri genel bakış](overview.md)
