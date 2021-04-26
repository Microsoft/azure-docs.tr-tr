---
title: Dağıtım verilerini şifreleme
description: Kapsayıcı örnek kaynaklarınız için kalıcı verilerin şifrelenmesi ve müşterinin yönettiği bir anahtarla verilerin şifrelenmesi hakkında bilgi edinin
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790878"
---
# <a name="encrypt-deployment-data"></a>Dağıtım verilerini şifreleme

Bulutta Azure Container Instances (ACI) kaynaklarını çalıştırırken ACI hizmeti, kapsayıcılarınızla ilgili verileri toplar ve devam ettirir. Bu verileri bulutta kalıcı olduğunda acı otomatik olarak şifreler. Bu şifreleme, kuruluşunuzun güvenlik ve uyumluluk taahhütlerini karşılamasına yardımcı olmak için verilerinizi korur. ACı Ayrıca, bu verileri kendi anahtarınızla şifreleme seçeneği sunarak acı dağıtımlarınızla ilgili veriler üzerinde daha fazla denetim sağlar.

## <a name="about-aci-data-encryption"></a>ACı veri şifrelemesi hakkında 

ACI 'daki veriler, 256 bit AES şifrelemesi kullanılarak şifrelenir ve şifresi çözülür. Tüm ACI dağıtımları için etkinleştirilmiştir ve bu şifrelemeden yararlanmak için dağıtımınızı veya Kapsayıcılarınızı değiştirmeniz gerekmez. Bu, dağıtım, ortam değişkenleri, kapsayıcılarınıza geçilen anahtarlar ve bu dosyaları görmeye devam ettikten sonra kalıcı olan Günlükler hakkında meta veriler içerir. Şifreleme, kapsayıcı grubu performansınızı etkilemez ve şifreleme için ek maliyet yoktur.

## <a name="encryption-key-management"></a>Şifreleme anahtarı yönetimi

Kapsayıcı verilerinizin şifrelenebilmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya şifrelemeyi kendi anahtarlarınız ile yönetebilirsiniz. Aşağıdaki tabloda bu seçenekler karşılaştırılmaktadır: 

|    |    Microsoft tarafından yönetilen anahtarlar     |     Müşteri tarafından yönetilen anahtarlar     |
|----|----|----|
|    **Şifreleme/şifre çözme işlemleri**    |    Azure    |    Azure    |
|    **Anahtar depolama**    |    Microsoft anahtar deposu    |    Azure Key Vault    |
|    **Anahtar döndürme sorumluluğu**    |    Microsoft    |    Müşteri    |
|    **Anahtar erişimi**    |    Yalnızca Microsoft    |    Microsoft, müşteri    |

Belgenin geri kalanı, acı dağıtım verilerinizi anahtarınızla (müşteri tarafından yönetilen anahtar) şifrelemek için gereken adımları ele alır. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Müşteri tarafından yönetilen anahtar ile verileri şifreleme

### <a name="create-service-principal-for-aci"></a>ACI için hizmet sorumlusu oluşturma

İlk adım, [Azure kiracınızın](../active-directory/develop/quickstart-create-new-tenant.md) Azure Container Instances hizmetine izin vermek için atanmış bir hizmet sorumlusu olmasını sağlamaktır. 

> [!IMPORTANT]
> Aşağıdaki komutu çalıştırmak ve başarıyla hizmet sorumlusu oluşturmak için kiracınızda hizmet sorumluları oluşturma izninizin olduğunu doğrulayın.
>

Aşağıdaki CLı komutu, Azure ortamınızda ACI SP 'yi ayarlar:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Bu komutun çalıştırıldığı çıktıda "displayName": "Azure Container Instance Service" ile ayarlanmış bir hizmet sorumlusu gösterilmelidir.

Hizmet sorumlusunu başarıyla oluşturabilmeniz durumunda:
* kiracınızda bunu yapmak için izinleriniz olduğunu doğrulayın
* kiracınızda ACI 'ye dağıtmaya yönelik bir hizmet sorumlusu olup olmadığını kontrol edin. Bunu çalıştırarak `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` ve bunun yerine bu hizmet sorumlusunu kullanarak yapabilirsiniz

### <a name="create-a-key-vault-resource"></a>Key Vault kaynağı oluşturma

[Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md)veya [Azure PowerShell](../key-vault/general/quick-create-powershell.md)kullanarak Azure Key Vault oluşturun.

Anahtar kasanızın özellikleri için aşağıdaki yönergeleri kullanın: 
* Ad: Benzersiz bir ad gereklidir. 
* Abonelik: Bir abonelik seçin.
* Kaynak grubu altında, var olan bir kaynak grubunu seçin ya da yeni oluştur ve bir kaynak grubu adı girin.
* Konum aşağı açılan menüsünde bir konum seçin.
* Diğer seçenekleri varsayılan olarak bırakabilir veya ek gereksinimlere göre seçebilirsiniz.

> [!IMPORTANT]
> Bir acı Dağıtım şablonunu şifrelemek için müşteri tarafından yönetilen anahtarlar kullanılırken, anahtar kasasında aşağıdaki iki özellik ayarlanmalıdır, geçici silme ve Temizleme işlemi yapmanız önerilir. Bu özellikler varsayılan olarak etkinleştirilmez, ancak yeni veya var olan bir anahtar kasasında PowerShell veya Azure CLı kullanılarak etkinleştirilebilir.

### <a name="generate-a-new-key"></a>Yeni bir anahtar oluştur 

Anahtar kasanızın oluşturulduktan sonra Azure portal içindeki kaynağa gidin. Kaynak dikey penceresinin sol gezinti menüsünde, Ayarlar ' ın altında **anahtarlar**' a tıklayın. "Anahtarlar" görünümünde, yeni bir anahtar oluşturmak için "oluştur/Al" düğmesine tıklayın. Bu anahtar için herhangi bir benzersiz ad ve gereksinimlerinize göre diğer tercihleri kullanın. 

![Yeni bir anahtar oluştur](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Erişim ilkesini ayarla

ACı hizmetinin anahtarınıza erişmesine izin vermek için yeni bir erişim ilkesi oluşturun.

* Anahtarınız oluşturulduktan sonra Anahtar Kasası kaynak dikey pencerenize geri dönün, Ayarlar ' ın altında, **erişim ilkeleri**' ne tıklayın.
* Anahtar kasanızın "erişim Ilkeleri" sayfasında, **erişim Ilkesi Ekle**' ye tıklayın.
* Anahtar *izinlerini* , **Get** ve **Unwrap anahtar** ![ kümesi anahtar izinlerini içerecek şekilde ayarlayın](./media/container-instances-encrypt-data/set-key-permissions.png)
* *Asıl seçin* Için **Azure Container Instance hizmeti** ' ni seçin.
* Alt kısımdaki **Ekle** 'ye tıklayın 

Erişim ilkesinin artık anahtar kasasının erişim ilkelerinde gösterilmesi gerekir.

![Yeni erişim ilkesi](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON dağıtım şablonunuzu değiştirme

> [!IMPORTANT]
> Dağıtım verilerinin, müşteri tarafından yönetilen bir anahtarla şifrelenmesi, şu anda kullanıma sunulan en son API sürümünde (2019-12-01) kullanılabilir. Dağıtım şablonunuzda bu API sürümünü belirtin. Bu sorunla karşılaşırsanız lütfen Azure desteğine ulaşın.

Anahtar Kasası anahtarı ve erişim ilkesi kurulduktan sonra, aşağıdaki özellikleri acı dağıtım şablonunuza ekleyin. Öğreticide bir şablonla acı kaynaklarını dağıtma hakkında daha fazla bilgi edinin [: Kaynak Yöneticisi şablonu kullanarak çok kapsayıcılı bir grup dağıtın](./container-instances-multi-container-group.md). 
* Altında `resources` , `apiVersion` olarak ayarlayın `2019-12-01` .
* Dağıtım şablonunun kapsayıcı grubu özellikleri bölümünde, `encryptionProperties` aşağıdaki değerleri içeren bir ekleyin:
  * `vaultBaseUrl`: anahtar kasaınızın DNS adı, portalda Anahtar Kasası kaynağının genel bakış dikey penceresinde bulunabilir
  * `keyName`: daha önce oluşturulan anahtarın adı
  * `keyVersion`: anahtarın geçerli sürümü. Bu, anahtarın kendine tıklanarak bulunabilir (Anahtar Kasası kaynağınızın ayarlar bölümünde "anahtarlar" altında)
* Kapsayıcı grubu özellikleri altında, `sku` değerine sahip bir özellik ekleyin `Standard` . `sku`ÖZELLIĞI API sürüm 2019-12-01 ' de gereklidir.

Aşağıdaki şablon kod parçacığında dağıtım verilerini şifrelemek için bu ek özellikler gösterilmektedir:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Aşağıda, öğreticideki şablondan uyarlanan tam bir şablon verilmiştir [: Kaynak Yöneticisi şablonu kullanarak çok kapsayıcılı bir grup dağıtın](./container-instances-multi-container-group.md). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Kaynaklarınızı dağıtın

Şablon dosyasını masaüstünüzde oluşturup düzenlediyseniz, dosyayı içine sürükleyerek Cloud Shell dizininize yükleyebilirsiniz... 

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Birkaç saniye içinde Azure’dan bir ilk yanıt almanız gerekir. Dağıtım tamamlandıktan sonra ACI hizmeti tarafından kalıcı hale gelen tüm veriler, verdiğiniz anahtarla şifrelenir.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
