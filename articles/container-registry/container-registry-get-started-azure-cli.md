---
title: "Özel Docker kapsayıcısı kayıt defteri oluşturma - Azure CLI| Microsoft Docs"
description: "Azure CLI 2.0 ile Docker kapsayıcısı kayıt defterleri oluşturmaya ve yönetmeye başlayın"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e37a3194bb65ccf3bb6168a2f456902a9c48edc5
ms.lasthandoff: 03/28/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Azure CLI 2.0’ı özel bir Docker kapsayıcı kayıt defteri oluşturma
Linux, Mac veya Windows bilgisayarınızdan bir kapsayıcı kayıt defteri oluşturmak ve ayarlarını yönetmek için [Azure CLI 2.0](https://github.com/Azure/azure-cli)’daki komutları kullanın. Ayrıca, [Azure portalını](container-registry-get-started-portal.md) kullanarak veya Kapsayıcı Kayıt Defteri [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) ile programlı olarak da kapsayıcı kayıt defterleri oluşturup yönetebilirsiniz.


* Arka plan ve kavramlar için, bkz: [genel bakış](container-registry-intro.md)
* Kapsayıcı Kayıt Defteri CLI komutlarıyla (`az acr` komutları) ilgili yardım almak için herhangi bir komuta `-h` parametresini geçirin.


## <a name="prerequisites"></a>Ön koşullar
* **Azure CLI 2.0**: CLI 2.0’ı yüklemek ve kullanmaya başlamak için, bkz. [yükleme yönergeleri](/cli/azure/install-azure-cli). `az login` komutunu çalıştırarak Azure aboneliğinizde oturum açın. Daha fazla bilgi için, bkz: [CLI 2.0’a başlangıç](/cli/azure/get-started-with-azure-cli).
* **Kaynak grubu**: Kapsayıcı kayıt defteri oluşturmadan önce bir [kaynak grubu](../azure-resource-manager/resource-group-overview.md#resource-groups) oluşturun veya mevcut bir kaynak grubunu kullanın. Kaynak grubunun Container Kayıt Defteri hizmetinin [kullanılabilir](https://azure.microsoft.com/regions/services/) olduğu bir konumda olduğundan emin olun. CLI 2.0’ı kullanarak bir kaynak grubu oluşturmak için bkz. [CLI 2.0 referansı](/cli/azure/group).
* **Depolama hesabı** (isteğe bağlı): Kapsayıcı kayıt defterini aynı konumda yedeklemek için standart bir Azure [depolama hesabı](../storage/storage-introduction.md) oluşturun. `az acr create` ile kayıt defteri oluştururken bir depolama hesabı belirtmezseniz komut sizin için bir depolama hesabı oluşturur. CLI 2.0’ı kullanarak bir depolama hesabı oluşturmak için, bkz. [CLI 2.0 referansı](/cli/azure/storage/account). Premium Depolama şu anda desteklenmemektedir.
* **Hizmet sorumlusu** (isteğe bağlı): CLI ile bir kayıt defteri oluşturduğunuzda, kayıt defteri varsayılan olarak erişim için ayarlanmaz. Gereksinimlerinize bağlı olarak mevcut bir Azure Active Directory hizmet sorumlusunu bir kayıt defterine atayabilir (veya bir hizmet sorumlusu oluşturup atayabilir) ya da kayıt defterinin yönetici kullanıcı hesabını etkinleştirebilirsiniz. Bu makalenin sonraki bölümlerine bakın. Kayıt defteri erişimi hakkında daha fazla bilgi için bkz. [Kapsayıcı kayıt defteri ile kimlik doğrulama](container-registry-authentication.md).

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma
Bir kapsayıcı kayıt defteri oluşturmak için `az acr create` komutunu çalıştırın.

> [!TIP]
> Bir kayıt defteri oluştururken yalnızca harf ve sayı içeren genel olarak benzersiz bir üst düzey etki alanı adı sağlayın. Örneklerdeki kayıt defterinin adı `myRegistry1`, ancak bunu kendi bulduğunuz benzersiz bir adla değiştirin.
>
>

Aşağıdaki komutta, olabildiğince az parametre kullanılarak Orta Güney ABD konumundaki `myResourceGroup` kaynak grubunda `myRegistry1` kapsayıcı kayıt defteri oluşturulmuştur:

```azurecli
az acr create -n myRegistry1 -g myResourceGroup -l southcentralus
```

* `--storage-account-name` isteğe bağlıdır. Belirtilmezse, kayıt defteri adını ve belirtilen kaynak grubundaki bir zaman damgasını içeren adla bir depolama hesabı oluşturulur.

Çıkış aşağıdakine benzer:

![az acr create output](./media/container-registry-get-started-azure-cli/acr_create.png)


Özellikle şunlara dikkat edin:

* `id` - Aboneliğinizdeki kayıt defterinin tanımlayıcısı; bir hizmet sorumlusu atamak istiyorsanız buna ihtiyacınız vardır.
* `loginServer` - [Kayıt defterinde oturum açmak için](container-registry-authentication.md) belirttiğiniz tam ad. Bu örnekte ad `myregistry1.exp.azurecr.io`’dur (tamamı küçük harflerle).

## <a name="assign-a-service-principal"></a>Hizmet sorumlusu atama
Bir kayıt defterine Azure Active Directory hizmet sorumlusu atamak için CLI 2.0 komutlarını kullanın. Bu örneklerdeki hizmet sorumlusuna Sahip rolü atanmış olsa da isterseniz [başka roller](../active-directory/role-based-access-control-configure.md) atayabilirsiniz.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Hizmet sorumlusu oluşturma ve kayıt defterine erişim atama
Aşağıdaki komutta, yeni bir hizmet sorumlusuna `--scopes` parametresiyle geçirilen kayıt defteri tanımlayıcısı için Sahip rolü erişimi atanır. `--password` parametresi ile güçlü bir parola belirtin.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Mevcut bir hizmet sorumlusunu atama
Zaten bir hizmet sorumlunuz varsa ve buna kayıt defteri için Sahip rolü erişimi atamak istiyorsanız aşağıdaki örneğe benzer bir komut çalıştırın. `--assignee` parametresini kullanarak asıl uygulama kimliğini geçirirsiniz:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Yönetici kimlik bilgilerini yönetme
Her kapsayıcı kayıt defteri için otomatik olarak bir yönetici hesabı oluşturulur ve varsayılan olarak devre dışı bırakılır. Aşağıdaki örneklerde kapsayıcı kayıt defteriniz için yönetici kimlik bilgilerini yönetmeye yönelik `az acr` CLI komutları gösterilmiştir.

### <a name="obtain-admin-user-credentials"></a>Yönetici kullanıcı kimlik bilgileri edinme
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Mevcut bir kayıt defteri için yönetici kullanıcıyı etkinleştirme
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Mevcut bir kayıt defteri için yönetici kullanıcıyı devre dışı bırakma
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Görüntüleri ve etiketleri listeleme
Bir depodaki görüntüleri ve etiketleri sorgulamak için `az acr` CLI komutlarını kullanın.

> [!NOTE]
> Container Kayıt Defteri şu anda görüntü ve etiket sorgulamak için `docker search` komutunu desteklememektedir.


### <a name="list-repositories"></a>Depoları listeleme
Aşağıdaki örnekte, bir kayıt defterindeki depolar JSON (JavaScript Nesne Gösterimi) biçiminde listelenmiştir:

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Etiketleri listeleme
Aşağıdaki örnekte, **samples/nginx** deposundaki etiketler JSON biçiminde listelenmiştir:

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Sonraki adımlar
* [Docker CLI’yı kullanarak ilk görüntünüzü itme](container-registry-get-started-docker-cli.md)

