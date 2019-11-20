---
title: Azure Service Fabric Docker Compose dağıtım önizlemesi
description: Azure Service Fabric, Service Fabric kullanarak var olan kapsayıcıları düzenlemenizi kolaylaştırmak için Docker Compose biçimini kabul eder. Bu destek şu anda önizlemededir.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: d9d135136efea72017399b5888bc6591582ffe67
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553563"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Azure Service Fabric Docker Compose dağıtım desteği (Önizleme)

Docker, çok Kapsayıcılı uygulamalar tanımlamak için [Docker-Compose. yıml](https://docs.docker.com/compose) dosyasını kullanır. Docker 'ın sunduğumuz müşterilerin Azure Service Fabric 'deki mevcut kapsayıcı uygulamalarını düzenleme konusunda daha kolay hale getirmek için, platformda yerel olarak Docker Compose dağıtımı için Önizleme desteği eklenmiştir. Service Fabric, `docker-compose.yml` dosyalarının sürüm 3 ' ü ve üstünü kabul edebilir. 

Bu destek önizlemede olduğundan, oluşturma yönergelerinin yalnızca bir alt kümesi desteklenir.

Bu önizlemeyi kullanmak için, Service Fabric çalışma Azure portal zamanının sürüm 5,7 veya daha büyük bir sürümüyle birlikte ilgili SDK ile birlikte kümenizi oluşturun. 

> [!NOTE]
> Bu özellik önizleme aşamasındadır ve üretimde desteklenmez.
> Aşağıdaki örnekler çalışma zamanı sürüm 6,0 ve SDK sürüm 2,8 ' i temel alır.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Service Fabric üzerinde Docker Compose dosyası dağıtma

Aşağıdaki komutlar, diğer Service Fabric uygulamaları gibi izleyebilmeniz ve yönetebileceğiniz bir Service Fabric uygulaması (`fabric:/TestContainerApp`) oluşturur. Sistem durumu sorguları için belirtilen uygulama adını kullanabilirsiniz.
Service Fabric, oluşturma dağıtımının tanımlayıcısı olarak "DeploymentName" i tanır.

### <a name="use-powershell"></a>PowerShell'i kullanma

PowerShell 'de aşağıdaki komutu çalıştırarak bir Docker-Compose. yml dosyasından Service Fabric oluşturma dağıtımı oluşturun:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` ve `RegistryPassword` kapsayıcı kayıt defteri Kullanıcı adı ve parolasına başvurur. Dağıtımı tamamladıktan sonra, aşağıdaki komutu kullanarak durumunu kontrol edebilirsiniz:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

PowerShell aracılığıyla oluşturma dağıtımını silmek için aşağıdaki komutu kullanın:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

PowerShell aracılığıyla bir dağıtım yükseltmesi oluşturmaya başlamak için aşağıdaki komutu kullanın:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

PowerShell aracılığıyla dağıtım yükseltmesini oluşturma işlemini geri almak için aşağıdaki komutu kullanın:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Yükseltme işlemi kabul edildikten sonra, aşağıdaki komutu kullanarak yükseltme ilerlemesi izlenebilir:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLı (sfctl) kullanma

Alternatif olarak, aşağıdaki Service Fabric CLı komutunu kullanabilirsiniz:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Dağıtımı oluşturduktan sonra, aşağıdaki komutu kullanarak durumunu kontrol edebilirsiniz:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Oluşturma dağıtımını silmek için aşağıdaki komutu kullanın:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Bir dağıtım yükseltmesi oluşturma başlatmak için aşağıdaki komutu kullanın:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Dağıtımı oluşturma yükseltmesini geri almak için aşağıdaki komutu kullanın:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Yükseltme işlemi kabul edildikten sonra, aşağıdaki komutu kullanarak yükseltme ilerlemesi izlenebilir:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Desteklenen oluşturma yönergeleri

Bu önizleme, aşağıdaki temel elemanlar dahil olmak üzere, oluşturma sürümü 3 biçimindeki yapılandırma seçeneklerinin bir alt kümesini destekler:

* > Çoğaltmaları dağıtmak > Hizmetler
* Hizmetler > Dağıtım > yerleştirme > kısıtlamaları
* Hizmetler > > kaynaklarını dağıtma > limitleri
    * -CPU-paylaşımlar
    * -bellek
    * -bellek-takas
* Hizmetler > komutları
* Hizmetler > ortamı
* Hizmetler > bağlantı noktaları
* Hizmetler > görüntü
* Hizmet > yalıtımı (yalnızca Windows için)
* Hizmet > günlük > sürücüsü
* Hizmetler > günlük > sürücü > seçenekleri
* Volume & > birimi dağıt

Kaynak sınırlarını zorlayıp [Service Fabric kaynak](service-fabric-resource-governance.md)Yönetimi bölümünde açıklandığı gibi kümeyi ayarlayın. Diğer tüm Docker Compose yönergeleri bu önizleme için desteklenmez.

### <a name="ports-section"></a>Bağlantı noktaları bölümü

Service Fabric hizmeti dinleyicisi tarafından kullanılacak bağlantı noktaları bölümünde http veya https protokolünü belirtin. Bu, ters proxy 'nin istekleri iletmesini sağlamak için, uç nokta protokolünün adlandırma hizmeti ile doğru şekilde yayımlanmasını sağlar:
* Güvenli olmayan Service Fabric oluşturma hizmetleri 'ne yönlendirmek için **/http**belirtin. Örneğin,- **"80:80/http"** .
* Güvenli Service Fabric oluşturma hizmetleri 'ne yönlendirmek için **/https**değerini belirtin. Örneğin,- **"443:443/HTTPS"** .

> [!NOTE]
> /Http ve/HTTPS bağlantı noktaları bölümü sözdizimi, doğru Service Fabric dinleyici URL 'sini kaydetmek için Service Fabric özgüdür.  Docker Compose dosya sözdizimi program aracılığıyla doğrulanırsa, doğrulama hatasına neden olabilir.

## <a name="servicednsname-computation"></a>ServiceDnsName hesaplama

Bir oluşturma dosyasında belirttiğiniz hizmet adı tam etki alanı adıdır (yani, bir nokta [.] içeriyorsa) Service Fabric tarafından kaydedilen DNS adı `<ServiceName>` (nokta dahil). Aksi takdirde, uygulama adındaki her yol segmenti, en üst düzey etki alanı etiketine sahip olan ilk yol segmenti olan hizmet DNS adında bir etki alanı etiketi haline gelir.

Örneğin, belirtilen uygulama adı `fabric:/SampleApp/MyComposeApp` ise, `<ServiceName>.MyComposeApp.SampleApp` kayıtlı DNS adı olacaktır.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Dağıtım (örnek tanımı) ve Service Fabric uygulama modeli (tür tanımı) karşılaştırması

Docker-Compose. yıml dosyası, özellikleri ve yapılandırmalarına dahil olmak üzere dağıtılabilir bir kapsayıcı kümesi tanımlar.
Örneğin, dosya ortam değişkenleri ve bağlantı noktaları içerebilir. Ayrıca, Docker-Compose. yıml dosyasındaki yerleştirme kısıtlamaları, kaynak sınırları ve DNS adları gibi dağıtım parametrelerini de belirtebilirsiniz.

[Service Fabric uygulama modeli](service-fabric-application-model.md) , hizmet türlerini ve uygulama türlerini kullanır, burada aynı türden birçok uygulama örneğine sahip olabilirsiniz. Örneğin, müşteri başına bir uygulama örneğiniz olabilir. Bu tür tabanlı model, çalışma zamanına kayıtlı aynı uygulama türünün birden çok sürümünü destekler.

Örneğin, A müşterisi, AppTypeA türü 1,0 ile örneklenen bir uygulamaya sahip olabilir ve B müşterisi aynı tür ve sürümle oluşturulmuş başka bir uygulamaya sahip olabilir. Uygulama türleri uygulama bildirimlerini tanımlar ve uygulamayı oluştururken uygulama adı ve dağıtım parametrelerini belirtirsiniz.

Bu modelde esneklik sunulmasına karşın, türlerin bildirim dosyasından örtük olduğu daha basit, örnek tabanlı bir dağıtım modelini desteklemeyi planlıyoruz. Bu modelde, her uygulama kendi bağımsız bildirimini alır. Örnek tabanlı bir dağıtım biçimi olan Docker-Compose. yıml için destek ekleyerek bu çabayı önizliyoruz.

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric uygulama modelinde](service-fabric-application-model.md) oku
* [Service Fabric CLI kullanmaya başlama](service-fabric-cli.md)
