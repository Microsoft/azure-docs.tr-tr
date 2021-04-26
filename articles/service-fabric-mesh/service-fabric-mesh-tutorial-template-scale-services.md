---
title: Öğretici-Azure Service Fabric ağı 'nda çalışan bir uygulamayı ölçeklendirme
description: Bu öğreticide, Service Fabric Mesh’te çalışan bir uygulamadaki hizmetleri ölçeklendirme işlemini öğreneceksiniz.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625391"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Öğretici: Service Fabric Mesh’te çalışan bir uygulamayı ölçeklendirme

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu öğretici, bir dizinin ikinci bölümüdür. [Önceden Service Fabric Mesh’e dağıtılmış olan](service-fabric-mesh-tutorial-template-deploy-app.md) bir uygulamanın hizmet örneği sayısını kendinizin nasıl ölçeklendireceğinizi öğrenin. İşlemi tamamladığınızda üç örnek çalıştıran bir ön uç hizmetine ve iki örnek çalıştıran bir veri hizmetine sahip olacaksınız.

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * İstenen hizmet örneği sayısını yapılandırma
> * Yükseltme gerçekleştirme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [Şablon kullanarak Service Fabric Mesh’e uygulama dağıtma](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Service Fabric Mesh’te çalışan bir uygulamayı ölçeklendirme
> * [Service Fabric Mesh’te çalışan bir uygulamayı yükseltme](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Uygulamayı kaldırma](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

* [Azure CLI ve Service Fabric Mesh CLI’sini yerel olarak yükleyin](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Hizmetlerinizin ölçeğini artırmanız veya azaltmanız

Service Fabric kafese uygulama dağıtmanın başlıca avantajlarından biri, hizmetlerinizi kolayca ölçeklendirebilme olanağı sağlar. Bu, hizmetinizdeki farklı yük miktarını işlemek veya kullanılabilirliği iyileştirmek için kullanılmalıdır.

Bu öğreticide, örnek olarak, [önceden dağıtılmış olan](service-fabric-mesh-tutorial-template-deploy-app.md) ve şimdi çalışıyor olması gereken Yapılacaklar Listesi örneği kullanılmaktadır. Uygulamada iki hizmet vardır: WebFrontEnd ve ToDoService. Her hizmet başlangıçta 1 çoğaltma sayısı ile dağıtılmıştır.  WebFrontEnd hizmeti için çalıştırılan çoğaltma sayısını görüntülemek için aşağıdakileri çalıştırın:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService hizmeti için çalıştırılan çoğaltma sayısını görüntülemek için aşağıdakileri çalıştırın:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

Uygulama kaynağı için dağıtım şablonunda her hizmet, hizmetin dağıtılmasını istediğiniz sayıyı ayarlamak için kullanılabilen bir *replicaCount* özelliğine sahiptir. Bir uygulama, birden çok hizmetten oluşabilir; her hizmet, birlikte dağıtılan ve yönetilen benzersiz bir *replicaCount* sayısına sahiptir. Hizmet çoğaltmaları sayısını ölçeklendirmek için, dağıtım şablonunda ölçeklendirmek istediğiniz her bir hizmet için *replicaCount* değerini veya parametreler dosyasını değiştirin.  Ardından uygulamayı yükseltin.

### <a name="modify-the-deployment-template-parameters"></a>Dağıtım şablonu parametrelerini değiştirme

Şablonunuzda, uygulama dağıtıldıktan sonra değişeceğini tahmin ettiğiniz değerler olduğunda veya dağıtım bazında değiştirme seçeneğine sahip olmak istediğinizde (diğer dağıtımlar için bu şablonu yeniden kullanmayı planlıyorsanız) en iyi uygulama, değerlerin parametrelerini oluşturmaktır.

Önceden uygulama, [mesh_rp.windows.json dağıtım şablonu](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) ve [mesh_rp.windows.parameter.json parametreleri](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) kullanılarak dağıtılıyordu.

[mesh_rp.windows.parameter.json parametreleri](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) dosyasını yerel olarak açın ve *frontEndReplicaCount* değerini 3 olarak, *serviceReplicaCount* değerini 2 olarak ayarlayın:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Parametreler dosyasına değişikliklerinizi kaydedin.  *frontEndReplicaCount* ve *serviceReplicaCount* parametreleri, [mesh_rp.windows.json dağıtım şablonunun](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)*parametreler* bölümünde bildirilir:

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

WebFrontEnd hizmetinin *replicaCount* özelliği, *frontEndReplicaCount* parametresine başvurur ve ToDoService hizmetinin *replicaCount* özelliği de *serviceReplicaCount* parametresine başvurur:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Şablonunuz değiştirildikten sonra uygulamanızı yükseltin.

### <a name="upgrade-your-application"></a>Uygulamanızı yükseltme

Uygulama çalışırken, şablonu ve güncelleştirilmiş parametreler dosyasını yeniden dağıtarak uygulamayı yükseltebilirsiniz:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Böylece uygulamanıza yönelik sıralı yükseltme başlatılır ve birkaç dakika içinde hizmet örneklerinin arttığını görmeniz gerekir.  WebFrontEnd hizmeti için çalıştırılan çoğaltma sayısını görüntülemek için aşağıdakileri çalıştırın:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService hizmeti için çalıştırılan çoğaltma sayısını görüntülemek için aşağıdakileri çalıştırın:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İstenen hizmet örneği sayısını yapılandırma
> * Yükseltme gerçekleştirme

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Service Fabric Mesh’te çalışan bir uygulamayı yükseltme](service-fabric-mesh-tutorial-template-upgrade-app.md)
