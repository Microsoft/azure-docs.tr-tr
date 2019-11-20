---
title: Azure Service Fabric ağı uygulama gizli dizilerini yönetme | Microsoft Docs
description: Service Fabric bir kafes uygulamasını güvenli bir şekilde oluşturup dağıtabilmeniz için uygulama gizli dizilerini yönetin.
services: service-fabric-mesh
keywords: gizli dizi
author: athinanthny
ms.author: atsenthi
ms.date: 4/2/2019
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: ef3f04437aca7b6ad9aab8806d54e65d00159d87
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036165"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Service Fabric kafes uygulama gizli dizilerini yönetme
Service Fabric ağ, Azure kaynakları olarak gizli dizileri destekler. Service Fabric bir ağ parolası, depolama bağlantı dizeleri, parolalar veya güvenli bir şekilde depolanması ve aktarılması gereken diğer değerler gibi herhangi bir hassas metin bilgisi olabilir. Bu makalede, gizli dizileri dağıtmak ve korumak için Service Fabric Güvenli Depolama Hizmeti nasıl kullanılacağı gösterilmektedir.

Bir kafes uygulama gizli dizisi aşağıdakilerden oluşur:
* Metin gizli dizileri depolayan bir kapsayıcı olan **gizlilikler** kaynağı. **Gizli** dizileri kaynağı içinde bulunan gizli dizileri, güvenli bir şekilde depolanır ve iletilir.
* **Gizlilikler** kaynak kapsayıcısında depolanan bir veya daha fazla **gizli dizi/değer** kaynağı. Her **gizli dizi/değer** kaynağı bir sürüm numarasıyla ayırt edilir. **Gizli dizi/değer** kaynağının bir sürümünü değiştiremezsiniz, yalnızca yeni bir sürüm ekleyebilirsiniz.

Gizli dizileri yönetmek aşağıdaki adımlardan oluşur:
1. Inlinedvalue Kind ve SecretsStoreRef contentType tanımlarını kullanarak bir Azure kaynak modeli YAML veya JSON dosyasında bir ağ **Parolaları** kaynağı bildirin.
2. Ağ **gizli dizileri/değerleri** kaynaklarını bir Azure kaynak modeli YAML veya **gizli** dizi dosyasında (1. ADıMDAN) depolanacak olan JSON dosyasına bildirin.
3. Kafes uygulamasını, kafes gizli değerlerine başvuracak şekilde değiştirin.
4. Gizli değerleri kullanmak için kafes uygulamasını dağıtın veya yuvarlama.
5. Güvenli Depolama Hizmeti yaşam döngüsü yönetimi için Azure "az" CLı komutlarını kullanın.

## <a name="declare-a-mesh-secrets-resource"></a>Ağ parolaları kaynağı bildirme
Bir ağ parolaları kaynağı bir Azure kaynak modeli JSON veya YAML dosyasında ınlinedvalue türü tanımı kullanılarak belirtilir. Ağ gizli dizileri kaynağı Güvenli Depolama Hizmeti kaynak gizli dizileri destekler. 
>
Aşağıda, bir JSON dosyasında ağ parolaları kaynaklarının nasıl bildirilelebir bir örnek verilmiştir:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Aşağıda, bir YAML dosyasında ağ parolaları kaynaklarının nasıl bildirilebir bir örnek verilmiştir:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Ağ gizli dizileri/değerleri kaynaklarını bildirin
Ağ parolaları/değerler kaynakları, önceki adımda tanımlanan ağ parolaları kaynaklarına bağımlıdır.

"Resources" bölümü "Value:" ve "Name:" alanları arasındaki ilişki ile ilgili olarak: "Name:" dizesinin bir iki nokta ile ayrılmış ikinci bölümü, bir gizli anahtar için kullanılan sürüm numarasıdır ve iki nokta üst üste ait olan ara gizlilik değeriyle eşleşmelidir bağımlılık. Örneğin, öğesi ```name: mysecret:1.0```için sürüm numarası 1,0, ad ```mysecret``` ise daha önce tanımlanan ```"value": "mysecret"```ile aynı olmalıdır.

>
Aşağıda, bir JSON dosyasında ağ gizli dizileri/değerleri kaynaklarının nasıl bildirilelelebir örnektir:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Aşağıda, bir YAML dosyasında ağ gizli dizileri/değerleri kaynaklarının nasıl bildirilelebir örnektir:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Kafes uygulamasını, kafes gizli değerlerine başvuracak şekilde değiştirin
Service Fabric ağ uygulamalarının Güvenli Depolama Hizmeti gizli değerleri kullanabilmesi için aşağıdaki iki dizeyi farkında olması gerekir:
1. Microsoft. Servicefabrickafes/gizlilikler. Name, dosyanın adını içerir ve gizli dizi değerini düz metin olacak şekilde içerecektir.
2. "Fabric_SettingPath" Windows veya Linux ortam değişkeni, Güvenli Depolama Hizmeti gizli dizi değerleri içeren dosyaların erişilebilir olacağı dizin yolunu içerir. Bu, sırasıyla Linux ile barındırılan kafes uygulamaları için Windows ile barındırılan ve "/var/Settings" için "C:\Settings".

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Gizli değerleri kullanmak için bir ağ uygulaması için sıralı yükseltme dağıtın veya kullanın
Gizli dizileri ve/veya sürümlenmiş gizli dizileri/değerleri oluşturma, kaynak modeli tarafından tanımlanan dağıtımlar ile sınırlıdır. Bu kaynakları oluşturmanın tek yolu, **az kafesdeployment** komutuyla aşağıdaki gibi bir kaynak modeli JSON veya YAML dosyası geçirmektir:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Güvenli Depolama Hizmeti yaşam döngüsü yönetimi için Azure CLı komutları

### <a name="create-a-new-secrets-resource"></a>Yeni bir gizli dizi kaynağı oluşturma
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
**Şablon dosyası** ya da **şablon-URI** (her ikisi değil) geçirin.

Örneğin:
- az kafes Deployment Create--c:\MyMeshTemplates\SecretTemplate1.txt
- az kafes Deployment Create--https:\//www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Gizli dizi göster
Gizli anahtar açıklamasını döndürür (ancak değeri değil).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Gizli dizi silme

- Gizli dizi, bir kafes uygulaması tarafından başvurulduğu sırada silinemez.
- Gizli dizi kaynağını silme tüm gizli dizileri/kaynaklar sürümlerini siler.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Abonelikte gizli dizileri Listele
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Kaynak grubundaki gizli dizileri listeleme
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Tüm gizli dizi sürümlerini listeleme
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Gizli sürüm değerini göster
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Gizli sürüm değerini Sil
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Sonraki adımlar 
Service Fabric ağ hakkında daha fazla bilgi edinmek için genel bakışı okuyun:
- [Service Fabric kafese bakış](service-fabric-mesh-overview.md)
