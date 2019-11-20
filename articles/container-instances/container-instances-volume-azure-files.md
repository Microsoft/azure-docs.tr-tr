---
title: Azure Container Instances bir Azure dosyaları birimi bağlama
description: Azure Container Instances ile devam etmek için bir Azure dosyaları birimini nasıl bağlayacağınızı öğrenin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 686c20aeb22c16298967aa6f73ee829472f4ea0c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175929"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances Azure dosya paylaşımından bağlama

Varsayılan olarak, Azure Container Instances durumsuz. Kapsayıcı kilitlenirse veya durdurulduğunda, tüm durumu kaybedilir. Kapsayıcının kullanım ömrünün ötesinde durumu kalıcı hale getirmek için bir dış depodan bir birim bağlamanız gerekir. Bu makalede gösterildiği gibi Azure Container Instances [Azure dosyaları](../storage/files/storage-files-introduction.md)ile oluşturulmuş bir Azure dosya paylaşımından bağlanabilir. Azure dosyaları, bulutta sektör standart sunucu Ileti bloğu (SMB) protokolü aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Azure Container Instances ile bir Azure dosya paylaşımının kullanılması, Azure sanal makinelerle Azure dosya paylaşımı kullanmaya benzer dosya paylaşımı özellikleri sağlar.

> [!NOTE]
> Azure dosya paylaşımının bağlanması şu anda Linux kapsayıcılarıyla kısıtlıdır. [Genel bakışta](container-instances-overview.md#linux-and-windows-containers)geçerli platform farklarını bulun.
>
> Bir Azure dosya paylaşımının bir kapsayıcı örneğine bağlanması, Docker [bind bağlama](https://docs.docker.com/storage/bind-mounts/)ile benzerdir. Dosya veya dizinlerin bulunduğu bir kapsayıcı dizinine bir paylaşımın bağladığınızda, bu dosya veya dizinlerin bağlama tarafından gizlenerek ve kapsayıcının çalıştırıldığı sırada erişilebilir olmadığı farkında olun.
>

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşma oluşturma

Azure Container Instances bir Azure dosya paylaşımının kullanılmadan önce, oluşturmanız gerekir. Dosya paylaşımının ve paylaşımın kendisini barındırmak üzere bir depolama hesabı oluşturmak için aşağıdaki betiği çalıştırın. Depolama hesabı adının genel olarak benzersiz olması gerekir, bu nedenle betik temel dizeye rastgele bir değer ekler.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Depolama kimlik bilgilerini al

Azure Container Instances bir Azure dosya paylaşımının bir birim olarak bağlanması için, üç değere sahip olmanız gerekir: depolama hesabı adı, paylaşma adı ve depolama erişim anahtarı.

Yukarıdaki betiği kullandıysanız, depolama hesabı adı $ACI _PERS_STORAGE_ACCOUNT_ADı değişkeninde depolandı. Hesap adını görmek için şunu yazın:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Paylaşma adı zaten biliniyor (yukarıdaki betikte *Şu* şekilde tanımlanır), bu nedenle her şey, aşağıdaki komut kullanılarak bulunan depolama hesabı anahtarıdır:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Kapsayıcı dağıtma ve bağlama birimi-CLı

Azure CLı kullanarak bir Azure dosya paylaşımının bir kapsayıcıya birim olarak bağlanması için [az Container Create][az-container-create]ile kapsayıcıyı oluştururken Share ve Volume bağlama noktasını belirtin. Önceki adımları izlediyseniz, bir kapsayıcı oluşturmak için aşağıdaki komutu kullanarak daha önce oluşturduğunuz paylaşımdan bağlama yapabilirsiniz:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

@No__t-0 değeri, kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Komutu çalıştırdığınızda bir **DNS adı etiketi** alırsanız, Yukarıdaki komutta değeri güncelleştirin.

## <a name="manage-files-in-mounted-volume"></a>Bağlı birimdeki dosyaları yönetme

Kapsayıcı başlatıldıktan sonra, belirttiğiniz bağlama yolundaki Azure dosya paylaşımında küçük metin dosyaları oluşturmak için Microsoft [aci-hellofshare][aci-hellofiles] Image aracılığıyla dağıtılan basit Web uygulamasını kullanabilirsiniz. Web uygulamasının tam etki alanı adını (FQDN) [az Container Show][az-container-show] komutuyla edinin:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Uygulamayı kullanarak metin kaydettikten sonra, dosya paylaşımında yazılmış dosyayı almak ve denetlemek için [Microsoft Azure Depolama Gezgini][storage-explorer] gibi bir aracı [Azure Portal][portal] veya bir araç kullanabilirsiniz.

## <a name="deploy-container-and-mount-volume---yaml"></a>Kapsayıcı dağıtma ve bağlama birimi-YAML

Ayrıca, bir kapsayıcı grubu dağıtabilir ve bir birimi Azure CLı ve [YAML şablonuyla](container-instances-multi-container-yaml.md)bir kapsayıcıya bağlayabilirsiniz. YAML şablonuna göre dağıtmak, birden çok kapsayıcıdan oluşan kapsayıcı grupları dağıtıldığında tercih edilen yöntemdir.

Aşağıdaki YAML şablonu, `aci-hellofiles` görüntüsüyle oluşturulmuş bir kapsayıcı içeren bir kapsayıcı grubunu tanımlar. Azure *dosya paylaşımının bulunduğu* kapsayıcı, daha önce bir birim olarak oluşturulur. Burada, dosya paylaşımının barındırmasını barındıran depolama hesabı için ad ve depolama anahtarı ' nı girin. 

CLı örneğinde olduğu gibi, `dnsNameLabel` değeri kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Gerekirse YAML dosyasındaki değeri güncelleştirin.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

YAML şablonuyla dağıtmak için önceki YAML 'yi `deploy-aci.yaml` adlı bir dosyaya kaydedin, sonra [az Container Create][az-container-create] komutunu `--file` parametresiyle yürütün:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Kapsayıcı dağıtma ve birim bağlama-Kaynak Yöneticisi

CLı ve YAML dağıtımına ek olarak, bir kapsayıcı grubu dağıtabilir ve bir Azure [Kaynak Yöneticisi şablonu](/azure/templates/microsoft.containerinstance/containergroups)kullanarak bir kapsayıcıya bir birim bağlayabilirsiniz.

İlk olarak, `volumes` dizisini şablonun kapsayıcı grubu `properties` bölümünde doldurun. 

Ardından, birimi bağlamak istediğiniz her bir kapsayıcı için, kapsayıcı tanımının `properties` bölümünde `volumeMounts` dizisini doldurun.

Aşağıdaki Kaynak Yöneticisi şablonu, `aci-hellofiles` görüntüsüyle oluşturulmuş bir kapsayıcı içeren bir kapsayıcı grubunu tanımlar. Azure *dosya paylaşımının bulunduğu* kapsayıcı, daha önce bir birim olarak oluşturulur. Burada, dosya paylaşımının barındırmasını barındıran depolama hesabı için ad ve depolama anahtarı ' nı girin. 

Önceki örneklerde olduğu gibi, `dnsNameLabel` değeri kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Gerekirse şablondaki değeri güncelleştirin.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
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
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
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
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Kaynak Yöneticisi şablonuyla dağıtmak için önceki JSON 'ı `deploy-aci.json` adlı bir dosyaya kaydedin, sonra [az Group Deployment Create][az-group-deployment-create] komutunu `--template-file` parametresiyle yürütün:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Birden çok birim bağlama

Bir kapsayıcı örneğinde birden fazla birimi bağlamak için bir [Azure Resource Manager şablonu](/azure/templates/microsoft.containerinstance/containergroups) veya YAML dosyası kullanarak dağıtmanız gerekir. Bir şablon veya YAML dosyası kullanmak için, `volumes` dizisini şablonun `properties` bölümünde doldurarak paylaşma ayrıntılarını sağlayın ve birimleri tanımlayın. 

Örneğin, *Share1* ve *Share2* adlı iki Azure dosya paylaşımını depolama hesabı *mystorageaccount*içinde oluşturduysanız, bir kaynak yöneticisi şablonunda `volumes` dizisi aşağıdakine benzer şekilde görünür:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ardından, birimleri bağlamak istediğiniz kapsayıcı grubundaki her bir kapsayıcı için, kapsayıcı tanımının `properties` bölümünde `volumeMounts` dizisini doldurun. Örneğin, bu iki birimi bağlar, daha önce tanımlanan *myvolume1* ve *myvolume2*.

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure Container Instances bir emptyDir birimi bağlama](container-instances-volume-emptydir.md)
* [Azure Container Instances bir gitRepo birimi bağlama](container-instances-volume-gitrepo.md)
* [Azure Container Instances bir gizli birimi bağlama](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create