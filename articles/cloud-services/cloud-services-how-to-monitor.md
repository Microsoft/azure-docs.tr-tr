---
title: Azure bulut hizmetini izleme (klasik) | Microsoft Docs
description: Bir Azure bulut hizmeti izlemenin ne olduğunu ve bazı seçeneklerinizin ne olduğunu açıklar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743381"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Bulut hizmeti (klasik) Izlemeye giriş

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Herhangi bir bulut hizmeti için önemli performans ölçümlerini izleyebilirsiniz. Her bulut hizmeti rolü en az veri toplar: CPU kullanımı, ağ kullanımı ve disk kullanımı. Bulut hizmetinde `Microsoft.Azure.Diagnostics` uzantı bir role uygulanmışsa, bu rol ek veri noktaları toplayabilir. Bu makalede Cloud Services için Azure Tanılama bir giriş sunulmaktadır.

Temel izleme ile, rol örneklerinden alınan performans sayacı verileri, 3 dakikalık aralıklarla örneklenir ve toplanır. Bu temel izleme verileri depolama hesabınızda depolanmaz ve bununla ilişkili ek bir ücret yoktur.

Gelişmiş izleme sayesinde ek ölçümler, 5 dakika, 1 saat ve 12 saat aralıklarıyla örneklenir ve toplanır. Toplanan veriler bir depolama hesabında, tablolarda depolanır ve 10 gün sonra temizlenir. Kullanılan depolama hesabı role göre yapılandırılır; farklı roller için farklı depolama hesapları kullanabilirsiniz. Bu, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ve [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) dosyalarında bir bağlantı dizesiyle yapılandırılır.


## <a name="basic-monitoring"></a>Temel izleme

Giriş bölümünde belirtildiği gibi, bir bulut hizmeti, ana bilgisayar sanal makinesinden temel izleme verilerini otomatik olarak toplar. Bu veriler CPU yüzdesi, ağ/çıkış ve disk okuma/yazma bilgilerini içerir. Toplanan izleme verileri, Azure portal bulut hizmetinin genel bakış ve ölçümler sayfalarında otomatik olarak görüntülenir. 

Temel izleme için bir depolama hesabı gerekmez. 

![temel bulut hizmeti izleme kutucukları](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Gelişmiş izleme

Gelişmiş izleme, izlemek istediğiniz rol üzerinde **Azure tanılama** uzantısının (ve isteğe bağlı olarak Application Insights SDK) kullanılmasını içerir. Tanılama uzantısı, izlenen tanılama ölçümlerini yapılandırmak için **Diagnostics. wadcfgx** adlı bir yapılandırma dosyası (rol başına) kullanır. Azure tanılama uzantısı, verileri bir Azure depolama hesabında toplar ve depolar. Bu ayarlar **. wadcfgx**, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)ve [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) dosyalarında yapılandırılır. Bu, Gelişmiş izleme ile ilişkili ek bir maliyet olduğu anlamına gelir.

Her bir rol oluşturulduğunda, Visual Studio buna Azure Tanılama uzantısını ekler. Bu tanılama uzantısı aşağıdaki bilgi türlerini toplayabilir:

* Özel performans sayaçları
* Uygulama günlükleri
* Windows olay günlükleri
* .NET olay kaynağı
* IIS günlükleri
* Bildirim tabanlı ETW
* Kilitlenme bilgi dökümleri
* Müşteri hata günlükleri

> [!IMPORTANT]
> Bu veriler depolama hesabında toplanırken, Portal verileri grafiğe yönelik yerel bir yol **sağlamaz.** Application Insights gibi başka bir hizmeti uygulamanıza tümleştirmeniz kesinlikle önerilir.

## <a name="setup-diagnostics-extension"></a>Tanılama uzantısını ayarla

İlk olarak, **Klasik** bir depolama hesabınız yoksa [bir tane oluşturun](../storage/common/storage-account-create.md). Depolama hesabının belirtilen **klasik dağıtım modeliyle** oluşturulduğundan emin olun.

Ardından, **depolama hesabı (klasik)** kaynağına gidin. **Ayarlar**  >  **erişim anahtarları** ' nı seçin ve **birincil bağlantı dizesi** değerini kopyalayın. Bulut hizmeti için bu değere ihtiyacınız vardır. 

Gelişmiş tanılamaları, **ServiceDefinition. csdef** ve **ServiceConfiguration. cscfg**' i etkinleştirmek için değiştirmeniz gereken iki yapılandırma dosyası vardır.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef

**ServiceDefinition. csdef** dosyasında, `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` Gelişmiş tanılama kullanan her bir rol için adlı yeni bir ayar ekleyin. Yeni bir proje oluşturduğunuzda Visual Studio bu değeri dosyaya ekler. Eksik olması durumunda şimdi ekleyebilirsiniz. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Bu, her **ServiceConfiguration. cscfg** dosyasına eklenmesi gereken yeni bir ayar tanımlar. 

Büyük olasılıkla Azure 'a dağıtmak için **ServiceConfiguration. Cloud. cscfg** adlı iki **. cscfg** dosyasına ve öykünülmüş ortamda yerel dağıtımlar için kullanılan **ServiceConfiguration. Local. cscfg** adlı bir ada sahip olabilirsiniz. Her **. cscfg** dosyasını açın ve değiştirin. Adlı bir ayar ekleyin `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Değeri, klasik depolama hesabının **birincil bağlantı dizesine** ayarlayın. Geliştirme makinenizde yerel depolamayı kullanmak istiyorsanız kullanın `UseDevelopmentStorage=true` .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Application Insights kullan

Bulut hizmetini Visual Studio 'dan yayımladığınızda, Application Insights tanılama verilerini gönderme seçeneği sunulur. Bu anda Application Insights Azure kaynağını oluşturabilir veya verileri mevcut bir Azure kaynağına gönderebilirsiniz. Bulut hizmetiniz, kullanılabilirlik, performans, başarısızlık ve kullanım için Application Insights izlenebilir. En önemli verileri görebilmeniz için Application Insights özel grafikler eklenebilir. Rol örneği verileri, bulut hizmeti projenizdeki Application Insights SDK kullanılarak toplanabilir. Application Insights tümleştirme hakkında daha fazla bilgi için [Cloud Services ile Application Insights](../azure-monitor/app/cloudservices.md)bakın.

Windows Azure Tanılama uzantısı aracılığıyla belirttiğiniz performans sayaçlarını (ve diğer ayarları) göstermek için Application Insights kullanabileceğinizi unutmayın; Application Insights SDK 'sını çalışan ve Web rolleriniz ile tümleştirerek daha zengin bir deneyim edinirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

- [Cloud Services Application Insights hakkında bilgi edinin](../azure-monitor/app/cloudservices.md)
- [Performans sayaçlarını ayarlama](diagnostics-performance-counters.md)




