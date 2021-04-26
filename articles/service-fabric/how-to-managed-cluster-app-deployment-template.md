---
title: ARM şablonunu kullanarak Service Fabric yönetilen küme (Önizleme) uygulaması dağıtma
description: Bir uygulamayı Azure Service Fabric yönetilen kümesine (Önizleme) Azure Resource Manager şablonu kullanarak dağıtın.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: e860c77d77e3aabb70f70defdaa25de14e77e0e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728020"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>ARM şablonunu kullanarak Service Fabric yönetilen küme (Önizleme) uygulaması dağıtma

Azure Service Fabric uygulamalarını Service Fabric yönetilen kümenize dağıtmaya yönelik birden çok seçeneğiniz vardır. Azure Resource Manager kullanmanızı öneririz. Kaynak Yöneticisi kullanıyorsanız, JSON 'da uygulamaları ve Hizmetleri tanımlayabilir ve ardından bunları kümeniz ile aynı Kaynak Yöneticisi şablonunda dağıtabilirsiniz. Uygulamaları dağıtmak ve yönetmek için PowerShell veya Azure CLı kullanmaktan farklı olarak, Kaynak Yöneticisi kullanıyorsanız, kümenin hazırlanmaya beklemeniz gerekmez; uygulama kaydı, sağlama ve dağıtımın hepsi tek bir adımda gerçekleşebilir. Kaynak Yöneticisi kullanmak, kümenizdeki uygulama yaşam döngüsünü yönetmenin en iyi yoludur. Daha fazla bilgi için bkz. [en iyi uygulamalar: kod olarak altyapı](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Uygulamalarınızı Kaynak Yöneticisi kaynak olarak yönetmek, bu alanlarda geliştirmeler elde etmenize yardımcı olabilir:

* Denetim izi: Kaynak Yöneticisi her işlemi denetler ve ayrıntılı bir etkinlik günlüğü tutar. Etkinlik günlüğü, uygulamalarda ve kümeniz üzerinde yapılan değişiklikleri izlemenize yardımcı olabilir.
* Rol tabanlı erişim denetimi: aynı Kaynak Yöneticisi şablonunu kullanarak kümelere ve kümeye dağıtılan uygulamalara erişimi yönetebilirsiniz.
* Yönetim verimliliği: Kaynak Yöneticisi kullanımı, kümenizin ve kritik uygulama dağıtımlarınızın yönetilmesi için tek bir konum (Azure portal) sağlar.

Bu belgede aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
>
> * Kaynak Yöneticisi kullanarak uygulama kaynaklarını dağıtın.
> * Kaynak Yöneticisi kullanarak uygulama kaynaklarını yükseltin.
> * Uygulama kaynaklarını silin.

## <a name="deploy-application-resources"></a>Uygulama kaynaklarını dağıtma

Kaynak Yöneticisi uygulama kaynak modeli kullanarak bir uygulamayı ve hizmetlerini dağıtmak için gerçekleştirmeniz gereken üst düzey adımlar şunlardır:
1. Uygulama kodunu paketleyin.
1. Paketi karşıya yükleyin.
1. Bir Kaynak Yöneticisi şablonundaki paketin konumunu uygulama kaynağı olarak başvuru. 

Daha fazla bilgi için, [paketi bir uygulamayı](service-fabric-package-apps.md#create-an-sfpkg)görüntüleyin.

Ardından, bir Kaynak Yöneticisi şablonu oluşturur, Parameters dosyasını uygulama ayrıntıları ile güncelleştirir ve şablonu Service Fabric kümesine dağıtabilirsiniz. [Örnekleri araştırma](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Kaynak Yöneticisi şablondan bir uygulama dağıtmak için bir depolama hesabınız olması gerekir. Depolama hesabı, uygulama görüntüsünü hazırlamak için kullanılır. 

Mevcut bir depolama hesabını yeniden kullanabilir veya uygulamalarınızı hazırlamak için yeni bir depolama hesabı oluşturabilirsiniz. Mevcut bir depolama hesabı kullanıyorsanız, bu adımı atlayabilirsiniz. 

![Depolama hesabı oluşturma][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Depolama hesabınızı yapılandırma

Depolama hesabı oluşturulduktan sonra, uygulamaların hazırlanbildiği bir blob kapsayıcısı oluşturursunuz. Azure portal, uygulamalarınızı depolamak istediğiniz Azure depolama hesabına gidin. **Bloblar**  >  **Ekle kapsayıcı** seçin. 

Kümenizin içindeki kaynaklara genel erişim düzeyi **özel** olarak ayarlanarak güvenli hale getirebilirsiniz. Birden çok şekilde erişim verebilirsiniz:

* [Azure Active Directory](../storage/common/storage-auth-aad-app.md)kullanarak bloblara ve kuyruklara erişim yetkisi verin.
* [Azure Portal Azure RBAC](../storage/common/storage-auth-aad-rbac-portal.md)kullanarak Azure Blob ve kuyruk verilerine erişim izni verin.
* [Paylaşılan erişim imzasını](/rest/api/storageservices/delegate-access-with-shared-access-signature)kullanarak erişim yetkisi verin.

Aşağıdaki ekran görüntüsündeki örnek, Bloblar için anonim okuma erişimi kullanır.

![Blob oluştur][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Depolama hesabınızda uygulamayı hazırlama

Bir uygulamayı dağıtabilmeniz için önce uygulamayı blob Storage 'da aşamalandırmalısınız. Bu öğreticide uygulama paketini el ile oluşturacağız. Bu adımın otomatikleştirilebilir olduğunu aklınızda bulundurun. Daha fazla bilgi için bkz. [bir uygulamayı paketleme](service-fabric-package-apps.md#create-an-sfpkg). 

Bu öğreticide, [Oylama örnek uygulamasını](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)kullanıyoruz.

1. Visual Studio 'da, **Oylama** projesine sağ tıklayın ve ardından **paket**' i seçin.

   ![Paket uygulaması][PackageApplication]  
1. *.\Service-Fabric-DotNet-quickstart\Voting\pkg\Debug* dizinine gidin. İçeriği *Voting.zip* adlı bir dosyaya ZIP. *ApplicationManifest.xml* dosyası ZIP dosyasının kökünde olmalıdır.

   ![ZIP uygulaması][ZipApplication]  
1. . Zip uzantısını *. sfpkg* olarak değiştirmek için dosyayı yeniden adlandırın.

1. Azure portal, depolama hesabınızın **uygulamalar** kapsayıcısında **karşıya yükle**' yi seçin ve ardından **Oylama. sfpkg**' i karşıya yükleyin. 

   ![Uygulama paketini karşıya yükle][UploadAppPkg]

Artık uygulama hazırlanmıştır ve uygulamayı dağıtmak için Kaynak Yöneticisi şablonu oluşturabilirsiniz.

### <a name="create-the-resource-manager-template"></a>Resource Manager şablonu oluşturma

Örnek uygulama, uygulamayı dağıtmak için kullanabileceğiniz [Azure Resource Manager şablonlar](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) içerir. Şablon dosya adları *UserApp.js* ve üzerinde *UserApp.Parameters.js*.

> [!NOTE]
> *UserApp.Parameters.jsdosyadaki* , kümenizin adıyla birlikte güncelleştirilmeleri gerekir.
>
>

| Parametre              | Açıklama                                 | Örnek                                                      | Yorumlar                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Dağıtım yaptığınız kümenin adı | SF-cluster123                                                |                                                              |
| uygulama            | Uygulamanın adı                 | Oylama                                                       |
| sürüm    | Uygulamanın kaynak KIMLIĞI, uygulama türü ve sürümü.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | ApplicationManifest.xml eşleşmelidir                 |
| HizmetAdı            | Hizmetin adı         | VotingWeb                                             | ServiceType biçiminde olmalıdır            |
| serviceTypeName        | Hizmetin tür adı                | VotingWebType                                                    | ServiceManifest.xml eşleşmelidir                 |
| appPackageUrl 'Si          | Uygulamanın BLOB depolama URL 'SI     | https: \/ /servicefabricapps.blob.Core.Windows.net/Apps/Voting.sfpkg | Blob depolamada uygulama paketinin URL 'SI (URL 'YI ayarlama yordamı, makalenin ilerleyen kısımlarında açıklanmıştır) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Uygulamayı dağıtma

Uygulamanızı kümenizi içeren kaynak grubuna dağıtmak için **New-AzResourceGroupDeployment** cmdlet 'ini çalıştırın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Service Fabric uygulamayı Kaynak Yöneticisi kullanarak yükseltme

> [!IMPORTANT]
> ARM JSON tanımıyla dağıtılan herhangi bir hizmetin, karşılık gelen ApplicationManifest.xml dosyasının DefaultServices bölümünden kaldırılması gerekir.


Aşağıdaki nedenlerden biri için zaten Service Fabric bir kümeye dağıtılmış olan bir uygulamayı yükseltebilirsiniz:

* Uygulamaya yeni bir hizmet eklenir. Uygulamaya bir hizmet eklendiğinde, bir hizmet tanımının *service-manifest.xml* ve *application-manifest.xml* dosyalara eklenmesi gerekir. Bir uygulamanın yeni bir sürümünü yansıtmak için, uygulama türü sürümünü 1.0.0 ' den 1.0.1 ' den [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)' de değiştirmeniz gerekir:

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Uygulamaya mevcut bir hizmetin yeni bir sürümü eklenir. Örnek olarak uygulama kodu değişiklikleri ve uygulama türü sürümü ve adı güncelleştirmeleri bulunur. Bu yükseltme için UserApp.Parameters.jsşu şekilde güncelleştirin:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>Uygulama kaynaklarını Sil

Kaynak Yöneticisi içindeki uygulama kaynak modeli kullanılarak dağıtılan bir uygulamayı silmek için:

1. Uygulamanın kaynak KIMLIĞINI almak için [Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet 'ini kullanın:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Uygulama kaynaklarını silmek için [Remove-AzResource](/powershell/module/az.resources/remove-azresource) cmdlet 'ini kullanın:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Sonraki adımlar

Uygulama kaynak modeli hakkında bilgi alın:

* [Service Fabric bir uygulama modelleme](service-fabric-application-model.md)
* [Uygulama ve hizmet bildirimlerini Service Fabric](service-fabric-application-and-service-manifests.md)
* [En iyi Yöntemler: kod olarak altyapı](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Uygulamaları ve Hizmetleri Azure kaynakları olarak yönetme](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png