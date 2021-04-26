---
title: Azure Service Fabric ağı CLı 'sını ayarlama
description: Service Fabric ağ komut satırı arabirimi (CLı), kaynakları yerel olarak ve Azure Service Fabric ağı 'nda dağıtmak ve yönetmek için gereklidir. Nasıl ayarlanacağı aşağıda verilmiştir.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613350"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI’yi ayarlama

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Service Fabric ağ komut satırı arabirimi (CLı), kaynakları yerel olarak ve Azure Service Fabric ağı 'nda dağıtmak ve yönetmek için gereklidir. Nasıl ayarlanacağı aşağıda verilmiştir.

Kullanılabilecek üç tür CLı bulunur ve bunlar aşağıdaki tabloda özetlenmiştir.

| CLı modülü | Hedef Ortam |  Description | 
|---|---|---|
| az ağ | Azure Service Fabric ağı | Azure Service Fabric kafes ortamında uygulamalarınızı dağıtmanıza ve kaynakları yönetmenize olanak tanıyan birincil CLı. 
| sfctl | Yerel kümeler | Yerel kümelerdeki Service Fabric kaynaklarının dağıtımına ve test edilmesine izin veren Service Fabric CLı.  
| Maven CLı | Azure Service Fabric ağ & yerel kümeler | Ve çevresindeki bir sarmalayıcı `az mesh` , `sfctl` Java geliştiricilerinin yerel ve Azure geliştirme deneyimi için tanıdık bir komut satırı deneyimi kullanmasına olanak sağlar.  

Önizleme için, Azure Fabric Mesh CLI Azure CLI’nin uzantısı olarak yazılır. Azure Cloud Shell’de veya Azure CLI’nin yerel kurulumunda bunu yükleyebilirsiniz. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.67 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Azure Service Fabric Kafesclı 'yi yükler

Daha önce yapmadıysanız, aşağıdaki komutu kullanarak Azure Service Fabric ağı CLı uzantısı modülünü yükleyebilirsiniz: 
 
```azurecli-interactive
az extension add --name mesh
```

Zaten yüklüyse, mevcut Azure Service Fabric kafesi CLı modülünüzü aşağıdaki komutu kullanarak güncelleştirin:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLı 'yi (sfctl) yükler 

[SERVICE fabrıc CLI 'Yi ayarlama](../service-fabric/service-fabric-cli.md)yönergelerini izleyin. **Sfctl** modülü, kaynak modeline bağlı olarak, yerel makinenizde Service Fabric kümelerine göre uygulama dağıtımı için kullanılabilir. 

## <a name="install-the-maven-cli"></a>Maven CLı 'yı yükler 

Maven CLı 'yı kullanabilmeniz için, makinenizde aşağıdakiler yüklü olmalıdır: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure ağ CLı (az ağ)-Azure Service Fabric ağı hedeflemek Için 
* SFCTL (sfctl)-yerel kümeleri hedeflemek Için 

Service Fabric için Maven CLı hala önizlemededir. 

Maven Java uygulamanızda Maven eklentisini kullanmak için, pom.xml dosyanıza aşağıdaki kod parçacığını ekleyin:

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
