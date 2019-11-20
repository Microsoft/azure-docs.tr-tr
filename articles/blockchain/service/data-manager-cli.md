---
title: Blok zincirini yapılandırma Veri Yöneticisi-Azure CLı
description: Azure CLı kullanarak blok zinciri oluşturma ve yönetme Veri Yöneticisi
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 9f408b090db40e5145b424034c39cdba4de14a8f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605911"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Azure CLı kullanarak blok zinciri Veri Yöneticisi yapılandırma

Blok zinciri verilerini yakalamak için Blockzincirini Veri Yöneticisi blok zinciri için yapılandırma bir Azure Event Grid konusuna gönderin.

Bir blok zinciri Veri Yöneticisi örneği yapılandırmak için şunları yapın:

* Blok zinciri Yöneticisi örneği oluşturma
* Azure blok zinciri hizmeti işlem düğümüne giriş oluşturma
* Azure Event Grid konu başlığına çıkış oluşturma
* Blok zinciri uygulaması ekleme
* Örnek başlatma

## <a name="prerequisites"></a>Ön koşullar

* En son [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 'yı yükleyip `az login`kullanarak oturum açtınız.
* [Hızlı başlangıç: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
* [Event Grid konu başlığı](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) oluşturma
* [Azure Event Grid Içindeki olay işleyicileri](../../event-grid/event-handlers.md) hakkında bilgi edinin

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell'i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. İsterseniz [https://shell.azure.com/bash](https://shell.azure.com/bash) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.51 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Örnek Oluştur

Bir blok zinciri Veri Yöneticisi örneği, bir Azure blok zinciri hizmeti işlem düğümünü izler. Örnek, işlem düğümünden tüm ham blok ve ham işlem verilerini yakalar.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parametre | Açıklama |
|-----------|-------------|
| resource-group | Blok zinciri Veri Yöneticisi örneğinin oluşturulacağı kaynak grubu adı. |
| ad | Blok zinciri Veri Yöneticisi örneğinin adı. |
| Kaynak türü | Bir blok zinciri Veri Yöneticisi örneği için kaynak türü **Microsoft. blockzincirde/izleyicileri**. |
| -Full-Object | Özellikler ' in izleyici kaynağı seçeneklerini içerdiğini gösterir. |
| properties | İzleyici kaynağının özelliklerini içeren JSON biçimli dize. , Bir dize veya dosya olarak geçirilebilir.  |

### <a name="create-instance-examples"></a>Örnek oluşturma örnekleri

**Doğu ABD** bölgesinde bir blok zinciri Yöneticisi örneği oluşturmak için JSON yapılandırma örneği.

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Öğe | Açıklama |
|---------|-------------|
| location | İzleyici kaynağının oluşturulacağı bölge |
| properties | İzleyici kaynağı oluşturulurken ayarlanacak Özellikler |

Yapılandırma için JSON dizesi kullanarak *myizleyici* adlı bir blok zinciri veri Yöneticisi örneği oluşturun.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

JSON yapılandırma dosyası kullanarak *myizleyici* adlı bir blok zinciri veri Yöneticisi örneği oluşturun.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Giriş oluştur

Bir giriş blok zinciri Veri Yöneticisi bir Azure blok zinciri hizmeti işlem düğümüne bağlar. Yalnızca işlem düğümüne erişimi olan kullanıcılar bir bağlantı oluşturabilir.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parametre | Açıklama |
|-----------|-------------|
| resource-group | Giriş kaynağının oluşturulacağı kaynak grubu adı. |
| ad | Girişin adı. |
| ad alanı | **Microsoft. Blockzincirde** sağlayıcı ad alanını kullanın. |
| Kaynak türü | Bir blok zinciri Veri Yöneticisi girişi için kaynak türü **giriştir**. |
| üst | Girişin ilişkilendirildiği izleyicinin yolu. Örneğin, **izleyicileri/myizleyici**. |
| -Full-Object | Özelliklerin giriş kaynağı için seçenekleri içerdiğini gösterir. |
| properties | Giriş kaynağının özelliklerini içeren JSON biçimli dize. , Bir dize veya dosya olarak geçirilebilir. |

### <a name="input-examples"></a>Giriş örnekleri

Yapılandırma JSON örneği, \<blok zinciri üye\>bağlı *Doğu ABD* bölgesinde bir giriş kaynağı oluşturmak için kullanılır.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Öğe | Açıklama |
|---------|-------------|
| location | Giriş kaynağının oluşturulacağı bölge. |
| InputType | Azure blok zinciri hizmeti üyesinin defter türü. Şu anda **Ethereum** destekleniyor. |
| resourceId | Girişin bağlandığı işlem düğümü. \<abonelik KIMLIĞI\>, \<kaynak grubu\>ve \<blok zinciri üyesi\>, işlem düğümü kaynağı değerleriyle değiştirin. Giriş, Azure blok zinciri hizmeti üyesinin varsayılan işlem düğümüne bağlanır. |

Yapılandırma için JSON dizesi kullanarak *myizleyici* Için *MyInput* adlı bir giriş oluşturun.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

JSON yapılandırma dosyası kullanarak *myizleyici* Için *MyInput* adlı bir giriş oluşturun.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Çıkış oluştur

Giden bağlantı, blok zinciri verilerini Azure Event Grid gönderir. Blok zinciri verilerini tek bir hedefe gönderebilir veya birden çok hedefe blok zinciri verileri gönderebilirsiniz. Blok zinciri Veri Yöneticisi, belirli bir blok zinciri Veri Yöneticisi örneği için birden çok Event Grid konu giden bağlantısını destekler.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parametre | Açıklama |
|-----------|-------------|
| resource-group | Çıkış kaynağının oluşturulacağı kaynak grubu adı. |
| ad | Çıkışın adı. |
| ad alanı | **Microsoft. Blockzincirde** sağlayıcı ad alanını kullanın. |
| Kaynak türü | Bir blok zinciri için kaynak türü Veri Yöneticisi çıkışı **çıktılar**olur. |
| üst | Çıktının ilişkilendirildiği izleyicinin yolu. Örneğin, **izleyicileri/myizleyici**. |
| -Full-Object | Özellikler, çıkış kaynağı için seçenekler içerir. |
| properties | Çıkış kaynağı için özellikler içeren JSON biçimli dize. , Bir dize veya dosya olarak geçirilebilir. |

### <a name="output-examples"></a>Çıkış örnekleri

Configuration JSON örneği, \<olay Kılavuzu konu başlığı\>adlı bir Event Grid konusuna bağlı *Doğu ABD* bölgesinde bir çıkış kaynağı oluşturmak için kullanılır.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Öğe | Açıklama |
|---------|-------------|
| location | Çıkış kaynağının oluşturulacağı bölge. |
| #B2 | Çıkışın türü. Şu anda **Eventgrid** destekleniyor. |
| resourceId | Çıktının bağlandığı kaynak. \<abonelik KIMLIĞI\>, \<kaynak grubu\>ve \<blok zinciri üyesi\> olay Kılavuzu kaynağı değerleriyle değiştirin. |

JSON yapılandırma dizesi kullanarak bir Event Grid konusuna bağlanan *myizleyici* için *MVU put* adlı bir çıktı oluşturun.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

JSON yapılandırma dosyası kullanarak bir Event Grid konusuna bağlanan *myizleyici* için *MVU put* adlı bir çıktı oluşturun.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Blok zinciri uygulaması ekleme

Bir blok zinciri uygulaması eklerseniz, blok zinciri uygulama için olay ve özellik durumunun kodunu çözer Veri Yöneticisi. Aksi takdirde, yalnızca ham blok ve ham işlem verileri gönderilir. Blok zinciri Veri Yöneticisi sözleşme dağıtıldığında sözleşme adreslerini de bulur. Bir blok zinciri Veri Yöneticisi örneğine birden çok blok zinciri uygulaması ekleyebilirsiniz.


> [!IMPORTANT]
> Şu anda, Solidity [dizi türlerini](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) veya [eşleme türlerini](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) bildiren blok zinciri uygulamaları tam olarak desteklenmez. Bir dizi veya eşleme türü olarak tanımlanan özellikler, *Contractpropertiesmsg* veya *Decodedcontracteventsmsg* iletilerinde kodu çözülür.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parametre | Açıklama |
|-----------|-------------|
| resource-group | Uygulama kaynağının oluşturulacağı kaynak grubu adı. |
| ad | Uygulamanın adı. |
| ad alanı | **Microsoft. Blockzincirde** sağlayıcı ad alanını kullanın. |
| Kaynak türü | Bir blok zinciri Veri Yöneticisi uygulama için kaynak türü **yapıtdır**. |
| üst | Uygulamanın ilişkilendirildiği izleyicinin yolu. Örneğin, **izleyicileri/myizleyici**. |
| -Full-Object | Özellikler, uygulama kaynağı için seçenekler içerir. |
| properties | Uygulama kaynağının özelliklerini içeren JSON biçimli dize. , Bir dize veya dosya olarak geçirilebilir. |

### <a name="blockchain-application-examples"></a>Blok zinciri uygulama örnekleri

Configuration JSON örneği, sözleşme ABı ve bytecode tarafından tanımlanan bir akıllı sözleşmeyi izleyen *Doğu ABD* bölgesinde bir uygulama kaynağı oluşturmak için.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Öğe | Açıklama |
|---------|-------------|
| location | Uygulama kaynağının oluşturulacağı bölge. |
| artifactType | Uygulamanın türü. Şu anda **Ethereumsmartcontract** destekleniyor. |
| Abıfileurl | Akıllı sözleşme ABı JSON dosyası URL 'SI. Sözleşme ABı edinme ve bir URL oluşturma hakkında daha fazla bilgi için bkz. [sözleşme ABI ve bytecode 'U alma](data-manager-portal.md#get-contract-abi-and-bytecode) ve [sözleşme ABı ve bytecode URL 'si oluşturma](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | Akıllı sözleşme tarafından dağıtılan bytecode JSON dosyası URL 'SI. Akıllı sözleşme dağıtılan ByteCode ve bir URL oluşturma hakkında daha fazla bilgi için bkz. [sözleşme ABI ve bytecode 'U alma](data-manager-portal.md#get-contract-abi-and-bytecode) ve [sözleşme ABı ve bytecode URL 'si oluşturma](data-manager-portal.md#create-contract-abi-and-bytecode-url). Note: blok zinciri Veri Yöneticisi **dağıtılan bytecode**'u gerektirir. |
| queryTargetTypes | Yayınlanan ileti türleri. **Contractproperties** 'ı belirleme *contractpropertiesmsg* ileti türünü yayımlar. **ContractEvents** yayım *Decodedcontracteventsmsg* ileti türü belirtiliyor. Note: *Rawblockandtransactionmsg* ve *Rawtransactioncontractcreationmsg* ileti türleri her zaman yayımlanır. |

JSON dizesi tarafından tanımlanan akıllı sözleşmeyi izleyen *myizleyici* için *MyApplication* adlı bir uygulama oluşturun.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

JSON yapılandırma dosyası kullanılarak tanımlanan bir akıllı sözleşmeyi izleyen *myizleyici* için *MyApplication* adlı bir uygulama oluşturun.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Örneği Başlat

Çalışırken, blok zinciri Yöneticisi örneği, tanımlı girişlerden blok zinciri olaylarını izler ve tanımlanan çıkışlara veri gönderir.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametre | Açıklama |
|-----------|-------------|
| action | İzleyiciyi çalıştırmak için **Başlat** 'ı kullanın. |
| ayrılacak | İzleyici kaynak KIMLIĞI. \<abonelik KIMLIĞI\>, \<kaynak grubu\>ve \<Izleyici adı\> izleyici kaynağı değerleriyle değiştirin.|

### <a name="start-instance-example"></a>Örnek örneğini Başlat

*Myizleyici*adlı bir blok zinciri veri Yöneticisi örneğini başlatın.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Örneği durdur

Bir blok zinciri Veri Yöneticisi örneğini durdurun.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametre | Açıklama |
|-----------|-------------|
| action | İzleyiciyi durdurmak için **Durdur** kullanın. |
| ayrılacak | İzleyicinin adı. \<abonelik KIMLIĞI\>, \<kaynak grubu\>ve \<Izleyici adı\> izleyici kaynağı değerleriyle değiştirin. |

### <a name="stop-watcher-example"></a>İzleyici örneğini durdur

*Myizleyici*adlı bir örneği durdurun.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Örneği Sil

Bir blok zinciri Veri Yöneticisi örneğini silin.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametre | Açıklama |
|-----------|-------------|
| resource-group | Silinecek izleyicinin kaynak grubu adı. |
| ad | Silinecek izleyici adı. |
| Kaynak türü | Bir blok zinciri Veri Yöneticisi İzleyicisi için kaynak türü **Microsoft. blockzinciridir/izleyicileri**. |

### <a name="delete-instance-example"></a>Örnek örneğini Sil

Myrg kaynak grubundaki *myizleyici* adlı bir örneği silin.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Sonraki adımlar

Blok zinciri Veri Yöneticisi ve Azure Cosmos DB kullanarak bir blok zinciri işlem iletisi Gezgini oluşturmayı deneyin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Cosmos DB 'a veri göndermek için blok zinciri Veri Yöneticisi kullanma](data-manager-cosmosdb.md)
