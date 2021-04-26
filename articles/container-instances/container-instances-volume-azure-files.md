---
title: Azure dosyaları birimini kapsayıcı grubuna bağlama
description: Azure Container Instances ile devam etmek için bir Azure dosyaları birimini nasıl bağlayacağınızı öğrenin
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763748"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances'ta Azure dosya paylaşımı bağlama

Azure Container Instances varsayılan olarak durum bilgilerini saklamaz. Kapsayıcı yeniden başlatılırsa, kilitlenirse veya durdurulduğunda, tüm durumu kaybedilir. Durum bilgilerinin kapsayıcının ömründen bağımsız olarak kalıcı olmasını sağlamak için dış bir depodan birim bağlamanız gerekir. Bu makalede gösterildiği gibi Azure Container Instances [Azure dosyaları](../storage/files/storage-files-introduction.md)ile oluşturulmuş bir Azure dosya paylaşımından bağlanabilir. Azure dosyaları, Azure depolama 'da barındırılan, sektör standart sunucu Ileti bloğu (SMB) protokolü aracılığıyla erişilebilen, tam olarak yönetilen dosya paylaşımları sunar. Azure Container Instances ile bir Azure dosya paylaşımının kullanılması, Azure sanal makinelerle Azure dosya paylaşımı kullanmaya benzer dosya paylaşımı özellikleri sağlar.

## <a name="limitations"></a>Sınırlamalar

* Azure dosya paylaşımlarını yalnızca Linux kapsayıcılarına bağlayabilirsiniz. [Genel bakışta](container-instances-overview.md#linux-and-windows-containers)Linux ve Windows kapsayıcı grupları için özellik desteğinin farkları hakkında daha fazla bilgi için bkz..
* Azure dosya paylaşma birimi bağlama, Linux kapsayıcısı 'nın *kök* olarak çalıştırılmasını gerektirir.
* Azure dosya paylaşma birimi bağlama, CIFS desteğiyle sınırlıdır.

> [!NOTE]
> Bir Azure dosya paylaşımının bir kapsayıcı örneğine bağlanması, Docker [bind bağlama](https://docs.docker.com/storage/bind-mounts/)ile benzerdir. Dosya veya dizinlerin bulunduğu bir kapsayıcı dizinine bir paylaşılan dizin bağlarsanız, bağlama dosya ve dizinleri gizler, bu da kapsayıcı çalışırken erişilemez hale gelir.
>

> [!IMPORTANT]
> Kapsayıcı gruplarını bir Azure sanal ağına dağıtıyorsanız, Azure depolama hesabınıza bir [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) eklemeniz gerekir.

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma

Azure Container Instances ile bir Azure dosya paylaşımı kullanmak için önce bunu oluşturmanız gerekir. Dosya paylaşımının ve paylaşımın kendisini barındırmak üzere bir depolama hesabı oluşturmak için aşağıdaki betiği çalıştırın. Depolama hesabı adının genel olarak benzersiz olması gerektiğinden, betik temel dizeye rastgele bir değer ekler.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Depolama kimlik bilgilerini alma

Bir Azure dosya paylaşımını Azure Container Instances'a birim olarak bağlamak için üç değere sahip olmanız gerekir: depolama hesabı adı, paylaşım adı ve depolama erişim anahtarı.

* **Depolama hesabı adı** -önceki betiği kullandıysanız, depolama hesabı adı değişkende depolanmıştı `$ACI_PERS_STORAGE_ACCOUNT_NAME` . Hesap adını görmek için şunu yazın:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Paylaşma adı** -bu değer zaten biliniyor ( `acishare` önceki betikte olarak tanımlanır)

* **Depolama hesabı anahtarı** -bu değer aşağıdaki komut kullanılarak bulunabilir:

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

`--dns-name-label`Değer, kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Komutu çalıştırdığınızda bir **DNS adı etiketi** alırsanız, Yukarıdaki komutta değeri güncelleştirin.

## <a name="manage-files-in-mounted-volume"></a>Bağlı birimdeki dosyaları yönetme

Kapsayıcı başlatıldıktan sonra, belirttiğiniz bağlama yolundaki Azure dosya paylaşımında küçük metin dosyaları oluşturmak için Microsoft [aci-hellofshare][aci-hellofiles] Image aracılığıyla dağıtılan basit Web uygulamasını kullanabilirsiniz. Web uygulamasının tam etki alanı adını (FQDN) [az Container Show][az-container-show] komutuyla edinin:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Uygulamayı kullanarak metin kaydettikten sonra, dosyayı veya dosya paylaşımında yazılmış dosyaları almak ve denetlemek için [Microsoft Azure Depolama Gezgini][storage-explorer] gibi bir aracı [Azure Portal][portal] kullanabilirsiniz.

## <a name="deploy-container-and-mount-volume---yaml"></a>Kapsayıcı dağıtma ve bağlama birimi-YAML

Ayrıca, bir kapsayıcı grubu dağıtabilir ve bir birimi Azure CLı ve [YAML şablonuyla](container-instances-multi-container-yaml.md)bir kapsayıcıya bağlayabilirsiniz. YAML şablonuna göre dağıtmak, birden çok kapsayıcıdan oluşan kapsayıcı gruplarını dağıtmada tercih edilen bir yöntemdir.

Aşağıdaki YAML şablonu, görüntüyle oluşturulmuş bir kapsayıcı grubunu tanımlar `aci-hellofiles` . Azure *dosya paylaşımının bulunduğu* kapsayıcı, daha önce bir birim olarak oluşturulur. Burada, dosya paylaşımının barındırmasını barındıran depolama hesabı için ad ve depolama anahtarı ' nı girin. 

CLı örneğinde olduğu gibi, `dnsNameLabel` değer kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Gerekirse YAML dosyasındaki değeri güncelleştirin.

```yaml
apiVersion: '2019-12-01'
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

YAML şablonuyla dağıtmak için, önceki YAML 'yi adlı bir dosyaya kaydedin `deploy-aci.yaml` ve ardından parametresiyle [az Container Create][az-container-create] komutunu yürütün `--file` :

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Kapsayıcı dağıtma ve birim bağlama-Kaynak Yöneticisi

CLı ve YAML dağıtımına ek olarak, bir kapsayıcı grubu dağıtabilir ve bir Azure [Kaynak Yöneticisi şablonu](/azure/templates/microsoft.containerinstance/containergroups)kullanarak bir kapsayıcıya bir birim bağlayabilirsiniz.

Önce, `volumes` diziyi şablonun kapsayıcı grubu `properties` bölümünde doldurun. 

Ardından, birimi bağlamak istediğiniz her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

Aşağıdaki Kaynak Yöneticisi şablonu, görüntüyle oluşturulmuş bir kapsayıcı grubunu tanımlar `aci-hellofiles` . Azure *dosya paylaşımının bulunduğu* kapsayıcı, daha önce bir birim olarak oluşturulur. Burada, dosya paylaşımının barındırmasını barındıran depolama hesabı için ad ve depolama anahtarı ' nı girin. 

Önceki örneklerde olduğu gibi, `dnsNameLabel` değerin kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olması gerekir. Gerekirse şablondaki değeri güncelleştirin.

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
      "apiVersion": "2019-12-01",
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

Kaynak Yöneticisi şablonuyla dağıtmak için, önceki JSON 'ı adlı bir dosyaya kaydedin `deploy-aci.json` , ardından şu parametreyle [az Deployment Group Create][az-deployment-group-create] komutunu yürütün `--template-file` :

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Birden çok birim bağlama

Bir kapsayıcı örneğinde birden fazla birimi bağlamak için, bir [Azure Resource Manager şablonu](/azure/templates/microsoft.containerinstance/containergroups), YAML dosyası veya başka bir programlama yöntemi kullanarak dağıtmanız gerekir. Bir şablon veya YAML dosyası kullanmak için, `volumes` dosyanın bölümündeki diziyi doldurarak paylaşma ayrıntılarını sağlayın ve birimleri tanımlayın `properties` . 

Örneğin, depolama hesabı *Mystorageaccount* içinde *Share1* ve *Share2* adlı iki Azure dosya paylaşımı oluşturduysanız, `volumes` Kaynak Yöneticisi şablondaki dizi aşağıdakine benzer şekilde görünür:

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

Sonra, kapsayıcı grubundaki birimleri bağlamak istediğiniz her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun. Örneğin, bu iki birimi bağlar, daha önce tanımlanan *myvolume1* ve *myvolume2*.

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
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
