---
title: PowerShell aracılığıyla Service Fabric yönetilen bir kümeye uygulama dağıtma (Önizleme)
description: Bu öğreticide, Service Fabric yönetilen bir kümeye bağlanacak ve PowerShell aracılığıyla bir uygulama dağıtacaksınız.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410474"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Öğretici: Service Fabric yönetilen bir kümeye uygulama dağıtma (Önizleme)

Bu öğretici serisinde şunları tartışacağız:

> [!div class="checklist"]
> * [Service Fabric yönetilen bir küme dağıtma](tutorial-managed-cluster-deploy.md)
> * [Service Fabric yönetilen bir kümeyi daraltma](tutorial-managed-cluster-scale.md)
> * [Service Fabric yönetilen bir kümede düğüm ekleme ve kaldırma](tutorial-managed-cluster-add-remove-node-type.md)
> * Service Fabric yönetilen bir kümeye uygulama dağıtma

Serinin bu bölümü aşağıdaki konuları içerir:

> [!div class="checklist"]
> * Service Fabric yönetilen kümenize bağlanma
> * Bir uygulamayı kümeye yükleme
> * Kümede bir uygulama örneği oluşturma
> * Kümeden bir uygulamayı kaldırma

## <a name="prerequisites"></a>Önkoşullar

* Service Fabric yönetilen bir küme (bkz. [*yönetilen küme dağıtma*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Kümenize bağlanma

Kümenize bağlanmak için küme sertifikası parmak izinin olması gerekir. Bu değeri, kaynak dağıtımınızın küme özellikleri çıktısında veya mevcut bir kaynaktaki küme özelliklerini sorgulayarak bulabilirsiniz.

Küme sertifikası parmak izi için küme kaynağınızı sorgulamak üzere aşağıdaki komut kullanılabilir.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Küme sertifikası parmak izine sahip olan kümenize bağlanmaya hazırsınız demektir.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Uygulama paketini karşıya yükle

Bu öğreticide, [Service Fabric oylama uygulaması](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) örneğini kullanacağız. PowerShell aracılığıyla uygulama dağıtımı Service Fabric hakkında daha fazla bilgi için bkz. [Service Fabric uygulamaları dağıtma ve kaldırma](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> Service Fabric yönetilen küme önizlemesinde, uygulamaları doğrudan Visual Studio 'dan yayımlayamayacak.

Öncelikle [Uygulamayı dağıtım için paketlemenize](service-fabric-package-apps.md)gerek duyarsınız. Bu öğreticide, lütfen Visual Studio içinden uygulama paketleme adımlarını izleyin. Aşağıdaki yolda kullanılacak şekilde, uygulamanın paketlendiği yolu göz önünde bulundurulmalıdır.

Uygulama paketi oluşturulduktan sonra, uygulama paketini kümenize yükleyebilirsiniz. Değeri, `$path` uygulama paketinizin bulunduğu yolu gösterecek şekilde güncelleştirin ve aşağıdakileri çalıştırın:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Uygulama oluşturma

New-ServiceFabricApplication cmdlet 'ini kullanarak başarıyla kaydedilmiş herhangi bir uygulama türü sürümünden bir uygulama örneği oluşturabilirsiniz. Her uygulamanın adı "Fabric:" şeması ile başlamalı ve her uygulama örneği için benzersiz olmalıdır. Hedef uygulama türünün uygulama bildiriminde tanımlanan varsayılan hizmetler de oluşturulur.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Bu işlem tamamlandıktan sonra, Service Fabric Explorer çalışan uygulama örneklerinizi görmeniz gerekir.

### <a name="remove-an-application"></a>Uygulamayı kaldırma

Bir uygulama örneğine artık ihtiyaç duyulmadığında, cmdlet 'ini kullanarak adı ile kalıcı olarak kaldırabilirsiniz, bu `Remove-ServiceFabricApplication` da uygulamanın ait olduğu tüm hizmetleri otomatik olarak kaldırır ve tüm hizmet durumlarını kalıcı olarak kaldırır.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Sonraki adımlar

Bu adımda, Service Fabric yönetilen bir kümeye uygulama dağıdık. Service Fabric yönetilen kümeler hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Service Fabric yönetilen kümeler hakkında sık sorulan sorular](faq-managed-cluster.md)
