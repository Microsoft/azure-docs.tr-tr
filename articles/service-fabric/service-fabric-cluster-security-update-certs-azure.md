---
title: Azure Service Fabric kümesinde sertifikaları yönetme | Microsoft Docs
description: Yeni sertifikaların nasıl ekleneceğini, sertifikanın nasıl aktarılacağını ve bir Service Fabric kümesinden veya bir kümeden sertifika nasıl kaldırılacağını açıklar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: a993c71e362a61b6861e001dfb5d6eca24873293
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903283"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Azure 'da bir Service Fabric kümesi için sertifika ekleme veya kaldırma
Service Fabric X. 509.440 sertifikalarını nasıl kullandığını ve [küme güvenliği senaryolarını](service-fabric-cluster-security.md)nasıl öğrendiğini öğrenmeniz önerilir. Devam etmeden önce, bir küme sertifikasının ne olduğunu ve ne kullanıldığını anlamalısınız.

Azure hizmet dokuları SDK 'sının varsayılan sertifika yükleme davranışı, son tarihi gelecekte olan tanımlı sertifikayı dağıtmaktır ve kullanır; birincil veya ikincil yapılandırma tanımlarından bağımsız olarak. Klasik davranışa geri dönmek önerilmeyen bir Gelişmiş eylemdir ve `Fabric.Code` yapılandırmanızda "Usesecondaryifdaha yeni" ayar parametre değerinin yanlış olarak ayarlanmasını gerektirir.

Service Fabric, istemci sertifikalarına ek olarak, küme oluşturma sırasında sertifika güvenliğini yapılandırırken, birincil ve ikincil olmak üzere iki küme sertifikası belirtmenize olanak tanır. Oluşturma zamanında ayarlama hakkında ayrıntılar için [Portal aracılığıyla bir Azure kümesi oluşturma](service-fabric-cluster-creation-via-portal.md) veya [Azure Resource Manager aracılığıyla bir Azure kümesi oluşturma](service-fabric-cluster-creation-via-arm.md) bölümüne bakın. Oluşturma zamanında yalnızca bir küme sertifikası belirtirseniz, bu, birincil sertifika olarak kullanılır. Küme oluşturulduktan sonra, ikincil olarak yeni bir sertifika ekleyebilirsiniz.

> [!NOTE]
> Güvenli bir küme için, her zaman en az bir geçerli (iptal edilmemiş ve dolmayan) küme sertifikası (birincil veya ikincil), dağıtılan (Aksi takdirde, küme çalışmayı durduruyor) gerekir. Tüm geçerli sertifikaların süresi dolmadan 90 gün önce, sistem düğüm üzerinde bir uyarı izlemesi ve ayrıca bir uyarı durumu olayı oluşturur. Şu anda bu makalede Service Fabric bir e-posta veya başka bir bildirim yok. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Portalı kullanarak ikincil küme sertifikası ekleme
İkincil küme sertifikası Azure portal aracılığıyla eklenemiyor, Azure PowerShell 'i kullanın. İşlem bu belgede daha sonra özetlenmiştir.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Portalı kullanarak bir küme sertifikasını kaldırma
Güvenli bir küme için, her zaman en az bir geçerli (iptal edilmemiş ve dolmayan) sertifikaya ihtiyaç duyarsınız. Gelecekteki süresi dolan tarihin en uzağında dağıtılan sertifika kullanımda olur ve bunu kaldırmak kümenizin çalışmasını durdurur; yalnızca süresi dolan sertifikayı veya Soonest süresi dolan kullanılmamış bir sertifikayı kaldırtığınızdan emin olun.

Kullanılmayan bir küme güvenlik sertifikasını kaldırmak için Güvenlik bölümüne gidin ve kullanılmayan sertifikadaki bağlam menüsünden ' Sil ' seçeneğini belirleyin.

Amaç, birincil olarak işaretlenmiş sertifikayı kaldırmaktır, sonra otomatik rollover davranışını etkinleştirerek, süresi dolan bir tarihi daha sonra birincil sertifikadan daha sonra dağıtmanız gerekir; otomatik geçiş tamamlandıktan sonra birincil sertifikayı silin.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Kaynak Yöneticisi PowerShell kullanarak ikincil sertifika ekleme
> [!TIP]
> Artık [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) cmdlet 'ini kullanarak ikincil sertifika eklemenin daha iyi ve kolay bir yolu vardır. Bu bölümdeki adımların geri kalanını izlemeniz gerekmez.  Ayrıca, [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) cmdlet 'ini kullanırken kümeyi oluşturmak ve dağıtmak için başlangıçta kullanılan şablona ihtiyacınız yoktur.

Bu adımlarda, Kaynak Yöneticisi nasıl çalıştığını ve bir Kaynak Yöneticisi şablonu kullanarak en az bir Service Fabric kümesini nasıl dağıttığınızı ve kümeyi kullanışlı bir şekilde ayarlamak için kullandığınız şablona sahip olduğunuz varsayılır. Ayrıca, JSON kullanmaya rahat bir şekilde yararlanacağınız varsayılır.

> [!NOTE]
> Bir başlangıç noktası olarak veya bir başlangıç noktası olarak izlemek için kullanabileceğiniz örnek bir şablon ve parametre arıyorsanız, bu [Git-deposu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)' ndan indirin. 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Kaynak Yöneticisi şablonunuzu düzenleme

Bunun yanı sıra, örnek 5-VM-1-NodeTypes-Secure_Step2. JSON, yaptığımız tüm düzenlemeleri içerir. örnek, [Git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)deposu ' te kullanılabilir.

**Tüm adımları izlediğinizden emin olun**

1. Kümenizi dağıtmak için kullandığınız Kaynak Yöneticisi şablonunu açın. (Örneği önceki depodan indirdiyseniz, güvenli bir küme dağıtmak için 5-VM-1-NodeTypes-Secure_Step1. JSON kullanın ve ardından bu şablonu açın).

2. Şablonunuzun parametre bölümüne "String" türünde **iki yeni** "seccertificatethumbprint" ve "secCertificateUrlValue" parametresi ekleyin. Aşağıdaki kod parçacığını kopyalayabilir ve şablona ekleyebilirsiniz. Şablonunuzun kaynağına bağlı olarak, bir sonraki adıma geçmek istiyorsanız, bu tanımlanmış olabilir. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. **Microsoft. ServiceFabric/kümeler** kaynağında değişiklik yapma-şablonunuzda "Microsoft. servicefabric/kümeler" kaynak tanımını bulun. Bu tanımın özellikleri altında, aşağıdaki JSON kod parçacığına benzer olması gereken "Certificate" JSON etiketini bulacaksınız:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    "ThumbprintSecondary" yeni etiketini ekleyin ve "[Parameters (' secCertificateThumbprint ')]" değerini verin.  

    Böylece kaynak tanımı aşağıdaki gibi görünmelidir (şablonun kaynağına bağlı olarak, aşağıdaki kod parçacığına tam olarak benzemeyebilir). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    **Sertifikayı almak**istiyorsanız, yeni sertifikayı birincil olarak belirtin ve geçerli birincili ikincil olarak hareket ettirin. Bu, geçerli birincil sertifikanızın yeni sertifikaya tek bir dağıtım adımında geçişine neden olur.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. **Tüm** **Microsoft. COMPUTE/virtualMachineScaleSets** kaynak tanımlarında değişiklik yapın-Microsoft. COMPUTE/virtualMachineScaleSets kaynak tanımını bulun. "VirtualMachineProfile" altındaki "Publisher": "Microsoft. Azure. ServiceFabric" öğesine gidin.

    Service Fabric yayımcı ayarları ' nda şuna benzer bir şey görmeniz gerekir.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Yeni sertifika girdilerini buna ekleyin
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Özellikler şimdi şöyle görünmelidir
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    **Sertifikayı almak**istiyorsanız, yeni sertifikayı birincil olarak belirtin ve geçerli birincili ikincil olarak hareket ettirin. Bu, geçerli sertifikanızın yeni sertifikaya tek bir dağıtım adımında geçişine neden olur.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Özellikler şimdi şöyle görünmelidir    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. **Tüm** **Microsoft. COMPUTE/VirtualMachineScaleSets** kaynak tanımlarında değişiklik yapın-Microsoft. COMPUTE/virtualMachineScaleSets kaynak tanımını bulun. "Oprofıle" altında "vaultCertificates": ("OSProfile") bölümüne gidin. Şuna benzer görünmelidir.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    SecCertificateUrlValue öğesine ekleyin. Aşağıdaki kod parçacığını kullanın:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Sonuçta elde edilen JSON şuna benzer görünmelidir.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Şablonunuzda tüm Nodetypes/Microsoft. COMPUTE/virtualMachineScaleSets kaynak tanımları için 4 ve 5. adımları tekrarlamadığınızdan emin olun. Bunlardan birini kaçırırsanız, sertifika bu sanal makine ölçek kümesine yüklenmez ve kümeniz de dahil olmak üzere kümenizde (kümenin Güvenlik için kullanabileceği geçerli bir sertifika yoksa) öngörülemeyen sonuçlara sahip olursunuz. Daha sonra devam etmeden önce, iki kez kontrol edin.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Şablon dosyanızı, yukarıda eklediğiniz yeni parametreleri yansıtacak şekilde düzenleyin
Daha sonra izlemek için [Git-](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) deposunun örneğini kullanıyorsanız, örnek 5-VM-1-Nodetypes-Secure. PARAMETERS_STEP2. json dosyasında değişiklik yapmaya başlayabilirsiniz 

Kaynak Yöneticisi şablonu parametre dosyanızı düzenleyin, secCertificateThumbprint ve secCertificateUrlValue için iki yeni parametreyi ekleyin. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Şablonu Azure 'a dağıtma

- Şimdi şablonunuzu Azure 'a dağıtmaya hazırsınız. Bir Azure PS sürüm 1 + komut istemi açın.
- Azure hesabınızda oturum açın ve belirli Azure aboneliğini seçin. Bu, birden fazla Azure aboneliğine erişimi olan katlara yönelik önemli bir adımdır.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Dağıtmadan önce şablonu test edin. Kümenizin Şu anda dağıtıldığı kaynak grubunu kullanın.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Şablonu kaynak grubunuza dağıtın. Kümenizin Şu anda dağıtıldığı kaynak grubunu kullanın. New-AzResourceGroupDeployment komutunu çalıştırın. Varsayılan değer **artımlı**olduğundan, bu modu belirtmeniz gerekmez.

> [!NOTE]
> Modu, tamamlanacak şekilde ayarlarsanız, şablonunuzda olmayan kaynakları yanlışlıkla silebilirsiniz. Bu nedenle, bu senaryoda kullanmayın.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Aynı PowerShell 'in doldurulmuş bir örneği aşağıda verilmiştir.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Dağıtım tamamlandıktan sonra, yeni sertifikayı kullanarak kümenize bağlanın ve bazı sorgular gerçekleştirin. Yapabiliyorsanız. Daha sonra eski sertifikayı silebilirsiniz. 

Kendinden imzalı bir sertifika kullanıyorsanız, bunları yerel Trustedkişilerim sertifika deponuza aktarmayı unutmayın.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Hızlı başvuru için, güvenli bir kümeye bağlanma komutu aşağıda verilmiştir 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Hızlı başvuru için, küme durumunu almak üzere komut şöyledir

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>İstemci sertifikalarını kümeye dağıtma.

Sertifikaların bir anahtar kasasından düğümlere dağıtılmasını sağlamak için yukarıdaki adımlarda özetlenen adımları kullanabilirsiniz. Yalnızca tanımlamanız ve farklı parametreler kullanmanız gerekir.


## <a name="adding-or-removing-client-certificates"></a>Istemci sertifikaları ekleme veya kaldırma

Küme sertifikalarına ek olarak, bir Service Fabric kümesinde yönetim işlemleri gerçekleştirmek için istemci sertifikaları ekleyebilirsiniz.

İki tür istemci sertifikası ekleyebilirsiniz-yönetici veya salt okunurdur. Bunlar daha sonra, kümedeki yönetici işlemlerine ve sorgu işlemlerine erişimi denetlemek için kullanılabilir. Varsayılan olarak, küme sertifikaları izin verilen yönetici sertifikaları listesine eklenir.

herhangi bir sayıda istemci sertifikası belirtebilirsiniz. Her ekleme/silme işlemi Service Fabric kümesinde bir yapılandırma güncelleştirmesine neden olur


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Portal aracılığıyla istemci sertifikaları ekleme-yönetici veya salt okuma

1. Güvenlik bölümüne gidin ve güvenlik bölümünün üstündeki ' + Authentication ' düğmesini seçin.
2. ' Kimlik doğrulaması Ekle ' bölümünde ' kimlik doğrulama türü '-' salt okunurdur ' veya ' admin Client ' seçeneklerini belirleyin
3. Şimdi yetkilendirme yöntemini seçin. Bu, konu adı veya parmak izi kullanarak bu sertifikaya bakmak gerekip gerekmediğini Service Fabric belirtir. Genel olarak, konu adının yetkilendirme yöntemini kullanmak iyi bir güvenlik uygulaması değildir. 

![Istemci sertifikası Ekle][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Portalı kullanarak Istemci sertifikalarını silme-yönetici veya salt okuma

Küme güvenliği için kullanılan ikincil sertifikayı kaldırmak için Güvenlik bölümüne gidin ve belirli bir sertifikadaki bağlam menüsünden ' Sil ' seçeneğini belirleyin.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesine uygulama sertifikaları ekleme

Uygulamalarınız için kullandığınız bir sertifikayı kümenize dağıtmak için [Bu örnek PowerShell betiğine](scripts/service-fabric-powershell-add-application-certificate.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar
Küme yönetimi hakkında daha fazla bilgi için şu makaleleri okuyun:

* [Küme yükseltme işlemini ve beklentilerini Service Fabric](service-fabric-cluster-upgrade.md)
* [İstemciler için rol tabanlı erişim kurulumu](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


