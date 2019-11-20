---
title: Azure Service Fabric uygulama kaynak modeli | Microsoft Docs
description: Bu makalede, Azure Resource Manager ile Azure Service Fabric uygulamasının yönetilmesine ilişkin bir genel bakış sunulmaktadır
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: atsenthi
ms.openlocfilehash: b9a3534c24649e71385cd8fdc8b4981ac471cf90
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752313"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Service Fabric uygulama kaynak modeli nedir?
Service Fabric uygulamaların Azure Resource Manager aracılığıyla Service Fabric kümenize dağıtılması önerilir. Bu yöntem, JSON 'daki uygulama ve Hizmetleri tanımlamaya ve bunları kümeniz ile aynı Kaynak Yöneticisi şablonunda dağıtmanıza olanak tanır. Uygulamaları PowerShell veya Azure CLı aracılığıyla dağıtmanın ve yönetmenin aksine, kümenin kullanıma başlamasını beklemeniz gerekmez. Uygulama kaydetme, hazırlama ve dağıtma işlemlerinin tümü tek bir adımda gerçekleşebilir. Bu, kümenizdeki uygulama yaşam döngüsünü yönetmek için kullanılabilecek en iyi yöntemdir. Daha fazla bilgi için [en iyi uygulamalara](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)bakın.

Uygun olduğunda, uygulamalarınızı geliştirmek için Kaynak Yöneticisi kaynak olarak yönetin:
* Denetim izi: Kaynak Yöneticisi her işlemi denetler ve bu uygulamalarda ve kümeniz üzerinde yapılan değişiklikleri izlemenize yardımcı olabilecek ayrıntılı bir *etkinlik günlüğü* tutar.
* Rol tabanlı erişim denetimi: kümelere erişimin yanı sıra küme üzerinde dağıtılan uygulamaların de aynı Kaynak Yöneticisi şablonu aracılığıyla yapılması yapılabilir.
* Azure Resource Manager (Azure portal aracılığıyla), kümenizin ve kritik uygulama dağıtımlarınızın yönetilmesi için tek bir durdurulmalı bir mağaza haline gelir.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Azure Resource Manager ile uygulama yaşam döngüsünü Service Fabric 
Bu belgede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Azure Resource Manager kullanarak uygulama kaynaklarını dağıtma 
> * Azure Resource Manager kullanarak uygulama kaynaklarını yükseltme
> * Uygulama kaynaklarını Sil

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Azure Resource Manager kullanarak uygulama kaynaklarını dağıtma  
Azure Resource Manager uygulama kaynak modelini kullanarak bir uygulamayı ve hizmetlerini dağıtmak için, uygulama kodu paketleyip paketi yüklemeniz ve sonra bir Azure Resource Manager şablonundaki paketin konumuna uygulama olarak başvurması gerekir Kaynak. Daha fazla bilgi için, [paketi bir uygulamayı](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)görüntüleyin.
          
Ardından, bir Azure Resource Manager şablonu oluşturun, Parameters dosyasını uygulama ayrıntıları ile güncelleştirin ve Service Fabric kümesine dağıtın. [Buradaki](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)örneklere bakın.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma 
Kaynak Yöneticisi şablondan uygulama dağıtmak, uygulama görüntüsünü hazırlamak için bir depolama hesabı gerektirir. Uygulamalarınızı hazırlamak için, mevcut bir depolama hesabını yeniden kullanabilir veya yeni bir depolama hesabı oluşturabilirsiniz. Mevcut bir depolama hesabını kullanmak istiyorsanız, bu adımı atlayabilirsiniz. 

![Depolama hesabı oluşturma][CreateStorageAccount]

### <a name="configure-storage-account"></a>Depolama hesabını yapılandırma 
Depolama hesabı oluşturulduktan sonra, uygulamaların hazırlanbildiği bir blob kapsayıcısı oluşturmanız gerekir. Azure portal, uygulamalarınızı depolamak istediğiniz depolama hesabına gidin. **Bloblar** dikey penceresini seçin ve **kapsayıcı Ekle** düğmesine tıklayın. Kümenizin içindeki kaynaklara genel erişim düzeyi özel olarak ayarlanarak güvenli hale getirebilirsiniz. Erişim, birkaç yolla verilebilir:
* [Azure Active Directory ile bloblara ve kuyruklara erişim yetkisi verme](../storage/common/storage-auth-aad-app.md)
* [Azure portalında RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](../storage/common/storage-auth-aad-rbac-portal.md)
* [Paylaşılan erişim imzasıyla (SAS) erişim yetkisi verme](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 Bu örnekte, Bloblar için anonim okuma erişimi kullanmaya devam edeceğiz.

![Blob oluştur][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Uygulamayı bir depolama hesabında aşamalandırın
Uygulama dağıtılmadan önce, blob depolamada hazırlanmalıdır. Bu öğreticide uygulama paketini el ile oluşturacağız, ancak bu adım otomatik hale getirilebilir.  Daha fazla bilgi için, [paketi bir uygulamayı](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)görüntüleyin. Aşağıdaki adımlarda, [Oylama örnek uygulaması](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) kullanılacaktır.

1. Visual Studio 'da oylama projesine sağ tıklayın ve paket ' i seçin.   
![Paket uygulaması][PackageApplication]  
2. Yeni oluşturduğunuz **.\Service-Fabric-DotNet-quickstart\Voting\pkg\Debug** dizinini açın ve içeriği,, ApplicationManifest. xml dosyası ZIP dosyasının kökünde olacak şekilde **Oylama. zip** adlı bir dosyaya ekleyin.  
![ZIP uygulaması][ZipApplication]  
3. Dosyanın uzantısını. zip konumundan **. sfpkg**olarak yeniden adlandırın.
4. Azure portal, depolama hesabınızın **uygulamalar** kapsayıcısında, **karşıya yükle** ' ye tıklayın ve **Oylama. sfpkg**' ı karşıya yükleyin.  
![uygulama paketini karşıya yükle][UploadAppPkg]

Uygulama artık hazırlandı. Şimdi uygulamayı dağıtmak için Azure Resource Manager şablonu oluşturmaya hazırsınız.      
   
### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager şablonu oluşturma
Örnek uygulama, uygulamayı dağıtmak için kullanılabilecek [Azure Resource Manager şablonlar](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) içerir. Şablon dosyaları **userapp. JSON** ve **Userapp. Parameters. JSON**olarak adlandırılır. 

> [!NOTE] 
> **Userapp. Parameters. JSON** dosyası kümenizin adıyla birlikte güncelleştirilmeleri gerekir.
>
>

| Parametre              | Açıklama                                 | Örnek                                                      | Yorumlar                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Dağıtım yaptığınız kümenin adı | SF-cluster123                                                |                                                              |
| Uygulamanızı            | Uygulamanın adı                 | Lerinizi                                                       |
| applicationTypeName    | Uygulamanın tür adı           | VotingType                                                   | ApplicationManifest. xml ' deki yeniliklere uymalıdır                 |
| ApplicationTypeVersion | Uygulama türü sürümü         | 1.0.0                                                        | ApplicationManifest. xml ' deki yeniliklere uymalıdır                 |
| HizmetAdı            | Hizmetin hizmetin adı         | Oylama ~ VotingWeb                                             | ApplicationName ~ ServiceType biçiminde olmalıdır            |
| serviceTypeName        | Hizmetin tür adı                | VotingWeb                                                    | ServiceManifest. xml dosyasındaki yeniliklere uymalıdır                 |
| appPackageUrl 'Si          | Uygulamanın BLOB depolama URL 'SI     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob depolamada uygulama paketinin URL 'SI (Bu ayarı ayarlama yordamı aşağıda açıklanmıştır) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Uygulamayı dağıtma 
Uygulamayı dağıtmak için, kümenizi içeren kaynak grubuna dağıtmak üzere New-AzResourceGroupDeployment komutunu çalıştırın.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Azure Resource Manager kullanarak Service Fabric uygulamayı yükseltme
Bir Service Fabric kümesine zaten dağıtılan uygulamalar aşağıdaki nedenlerden dolayı yükseltilecektir:

1. Uygulamaya yeni bir hizmet eklenir. Service-manifest. xml ve Application-manifest. xml dosyasına bir hizmet tanımı eklenmelidir. Daha sonra uygulamanın yeni sürümünü yansıtmak için, uygulama türü sürümünü 1.0.0 ' dan 1.0.1 [Userapp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)olarak güncelleştirmeniz gerekir.

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. Uygulamaya mevcut bir hizmetin yeni bir sürümü eklenir. Bu, uygulama kodu değişikliklerini ve uygulama türü sürümüne ve adına yapılan güncelleştirmeleri içerir.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Uygulama kaynaklarını Sil
Azure Resource Manager içinde uygulama kaynak modeli kullanılarak dağıtılan uygulamalar, aşağıdaki adımlar kullanılarak kümeden silinebilir

1) [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) komutunu kullanarak uygulama için kaynak kimliği al  

    #### <a name="get-resource-id-for-application"></a>Uygulama için kaynak KIMLIĞI al
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) kullanarak uygulama kaynaklarını silme  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Kaynak KIMLIĞINI kullanarak uygulama kaynağını Sil
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Sonraki adımlar
Uygulama kaynak modeli hakkında bilgi alın:

* [Service Fabric bir uygulama modelleme](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Uygulama ve hizmet bildirimlerini Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Ayrıca Bkz.
* [En iyi uygulamalar](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Uygulamaları ve Hizmetleri Azure kaynakları olarak yönetme](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
