---
title: Azure Service Fabric Mesh CLI’yi ayarlama | Microsoft Docs
description: Service Fabric ağ komut satırı arabirimi (CLı), kaynakları yerel olarak ve Azure Service Fabric ağı 'nda dağıtmak ve yönetmek için gereklidir. Nasıl ayarlanacağı aşağıda verilmiştir.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5c9733a5fc5aec337a326fe39e78c3e5c0b8ca4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686294"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI’yi ayarlama
Service Fabric ağ komut satırı arabirimi (CLı), kaynakları yerel olarak ve Azure Service Fabric ağı 'nda dağıtmak ve yönetmek için gereklidir. Nasıl ayarlanacağı aşağıda verilmiştir.

Kullanılabilecek üç tür CLı bulunur ve bunlar aşağıdaki tabloda özetlenmiştir.

| CLı modülü | Hedef ortam |  Açıklama | 
|---|---|---|
| az ağ | Azure Service Fabric ağı | Azure Service Fabric kafes ortamında uygulamalarınızı dağıtmanıza ve kaynakları yönetmenize olanak tanıyan birincil CLı. 
| sfctl | Yerel kümeler | Yerel kümelerdeki Service Fabric kaynaklarının dağıtımına ve test edilmesine izin veren Service Fabric CLı.  
| Maven CLı | Azure Service Fabric ağ & yerel kümeler | `az mesh` ve `sfctl` çevresindeki bir sarmalayıcı, Java geliştiricilerinin yerel ve Azure geliştirme deneyimi için tanıdık bir komut satırı deneyimi kullanmasına izin verir.  

Önizleme için, Azure Fabric Mesh CLI Azure CLI’nin uzantısı olarak yazılır. Azure Cloud Shell’de veya Azure CLI’nin yerel kurulumunda bunu yükleyebilirsiniz. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Azure Service Fabric Kafesclı 'yi yükler
1. Azure CLı sürüm 2.0.67 veya üstünü yüklemelisiniz. Sürümü bulmak için `az --version` komutunu çalıştırın. CLı 'nın en son sürümünü yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek][azure-cli-install].

2. Aşağıdaki komutu kullanarak Azure Service Fabric kafes CLı uzantısı modülünü yükler. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Aşağıdaki komutu kullanarak mevcut bir Azure Service Fabric kafesi CLı modülünü güncelleştirin.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLı 'yi (sfctl) yükler 

[SERVICE fabrıc CLI 'Yi ayarlama](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)yönergelerini izleyin. **Sfctl** modülü, kaynak modeline bağlı olarak, yerel makinenizde Service Fabric kümelerine göre uygulama dağıtımı için kullanılabilir. 

## <a name="install-the-maven-cli"></a>Maven CLı 'yı yükler 

Maven CLı 'yı kullanabilmeniz için, makinenizde aşağıdakiler yüklü olmalıdır: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure ağ CLı (az ağ)-Azure Service Fabric ağı hedeflemek Için 
* SFCTL (sfctl)-yerel kümeleri hedeflemek Için 

Service Fabric için Maven CLı hala önizlemededir. 

Maven Java uygulamanızda Maven eklentisini kullanmak için, potm. xml dosyanıza aşağıdaki kod parçacığını ekleyin:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Ayrıntılı kullanım hakkında bilgi edinmek için [Maven CLI başvurusu](service-fabric-mesh-reference-maven.md) bölümünü okuyun.

## <a name="next-steps"></a>Sonraki adımlar

[Windows dağıtım ortamınızı](service-fabric-mesh-howto-setup-developer-environment-sdk.md) da ayarlayabilirsiniz.

[Sık sorulan soruların ve sorunların](service-fabric-mesh-faq.md) yanıtlarını bulun.

[azure-cli-install]: /cli/azure/install-azure-cli
