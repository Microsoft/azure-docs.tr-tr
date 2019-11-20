---
title: Azure CLı kullanarak Azure blok zinciri hizmetini yönetme
description: Azure CLı ile Azure blok zinciri hizmeti oluşturma ve yönetme
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 4dd58f2542674633f2d5e2a1724adc7934d7f030
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307055"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLı kullanarak Azure blok zinciri hizmetini yönetme

Azure portal ek olarak Azure CLı 'yi kullanarak Azure blok zinciri hizmetinize yönelik blok zinciri üyelerini ve işlem düğümlerini yönetebilirsiniz.

En son [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 'yi yüklediğinizden ve ' de `az login`bir Azure hesabında oturum açtığınızdan emin olun.

Aşağıdaki örneklerde örneği `<parameter names>` kendi değerlerinizle değiştirin.

## <a name="create-blockchain-member"></a>Blok zinciri üyesi oluştur

Örnek, Azure blok zinciri hizmetinde yeni bir konsorsiyumun çekirdek defter protokolünü çalıştıran bir blok zinciri üyesi oluşturur.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **name** | Azure blok zinciri hizmeti blok zinciri üyesini tanımlayan benzersiz bir ad. Ad, genel uç nokta adresi için kullanılır. Örneğin: `myblockchainmember.blockchain.azure.com`. |
| **konum** | Blok zinciri üyesinin oluşturulduğu Azure bölgesi. Örneğin: `eastus`. Kullanıcılarınıza veya diğer Azure uygulamalarınıza en yakın konumu seçin. |
| **Parola** | Üye hesabı parolası. Üye hesabı parolası, blok zinciri üyesinin genel uç noktasında temel kimlik doğrulaması kullanılarak kimlik doğrulaması için kullanılır. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;)|
| **Protocol** | Genel Önizleme, çekirdeğini destekler. |
| **unun** | Katılacak veya oluşturulacak konsorsiyumun adı. |
| **consortiumManagementAccountPassword** | Konsorsiyum yönetimi parolası. Parola, bir konsorsiya katılmak için kullanılır. |
| **ruleName** | Bir IP adresi aralığı için beyaz listeye yönelik kural adı. Güvenlik duvarı kuralları için isteğe bağlı parametre.|
| **Startıpaddress** | Beyaz listeye yönelik IP adresi aralığının başlangıcı. Güvenlik duvarı kuralları için isteğe bağlı parametre. |
| **Endıpaddress değerini** | Beyaz listeye yönelik IP adresi aralığının sonu. Güvenlik duvarı kuralları için isteğe bağlı parametre. |
| **skuName** | Katman türü. Standart için S0 ve temel için B0 kullanın. |

## <a name="change-blockchain-member-password"></a>Blok zinciri üye parolasını değiştir

Örnek, blok zinciri üyesinin parolasını değiştirir.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **name** | Azure blok zinciri hizmeti üyesini tanımlayan ad. |
| **Parola** | Üye hesabı parolası. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve noktalı virgül (;). |

## <a name="create-transaction-node"></a>İşlem düğümü oluştur

Varolan bir blok zinciri üyesinin içinde bir işlem düğümü oluşturun. İşlem düğümleri ekleyerek güvenlik yalıtımını artırabilir ve yükü dağıtabilirsiniz. Örneğin, farklı istemci uygulamaları için bir işlem düğümü uç noktasına sahip olabilirsiniz.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **name** | Yeni işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **konum** | Blok zinciri üyesinin oluşturulduğu Azure bölgesi. Örneğin: `eastus`. Kullanıcılarınıza veya diğer Azure uygulamalarınıza en yakın konumu seçin. |
| **Parola** | İşlem düğümü parolası. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve noktalı virgül (;). |
| **ruleName** | Bir IP adresi aralığı için beyaz listeye yönelik kural adı. Güvenlik duvarı kuralları için isteğe bağlı parametre. |
| **Startıpaddress** | Beyaz listeye yönelik IP adresi aralığının başlangıcı. Güvenlik duvarı kuralları için isteğe bağlı parametre. |
| **Endıpaddress değerini** | Beyaz listeye yönelik IP adresi aralığının sonu. Güvenlik duvarı kuralları için isteğe bağlı parametre.|

## <a name="change-transaction-node-password"></a>İşlem düğümü parolasını değiştir

Örnek, işlem düğümü parolasını değiştirir.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Yeni işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **Parola** | İşlem düğümü parolası. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve noktalı virgül (;). |

## <a name="change-consortium-management-account-password"></a>Konsorsiyumun yönetim hesabı parolasını değiştirme

Konsorsiyum yönetim hesabı, konsorsiyum Üyelik yönetimi için kullanılır. Her üye bir konsorsiyum yönetim hesabı tarafından benzersiz bir şekilde tanımlanır ve bu hesabın parolasını aşağıdaki komutla değiştirebilirsiniz.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **name** | Azure blok zinciri hizmeti üyesini tanımlayan ad. |
| **consortiumManagementAccountPassword** | Konsorsiyum yönetimi hesabı parolası. Parola aşağıdaki dört gereksinimden üçünü karşılamalıdır: uzunluğun 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf karakter, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak işareti içinde olması gerekir. (\`), çift tırnak ("), tek tırnak ('), tire (-) ve noktalı virgül (;). |
  
## <a name="update-firewall-rules"></a>Güvenlik duvarı kurallarını güncelleştirme

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **ruleName** | Bir IP adresi aralığı için beyaz listeye yönelik kural adı. Güvenlik duvarı kuralları için isteğe bağlı parametre.|
| **Startıpaddress** | Beyaz listeye yönelik IP adresi aralığının başlangıcı. Güvenlik duvarı kuralları için isteğe bağlı parametre.|
| **Endıpaddress değerini** | Beyaz listeye yönelik IP adresi aralığının sonu. Güvenlik duvarı kuralları için isteğe bağlı parametre.|

## <a name="list-api-keys"></a>API anahtarlarını Listele

API anahtarları, Kullanıcı adı ve parolaya benzer düğüm erişimi için kullanılabilir. Anahtar döndürmeyi desteklemek için iki API anahtarı vardır. API anahtarlarınızı listelemek için aşağıdaki komutu kullanın.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Yeni işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |

## <a name="regenerate-api-keys"></a>API anahtarlarını yeniden oluştur

API anahtarlarınızı yeniden oluşturmak için aşağıdaki komutu kullanın.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Yeni işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **Işareti** | \<KeyValue\> öğesini KEY1 veya key2 ile değiştirin. |

## <a name="delete-a-transaction-node"></a>İşlem düğümünü silme

Örnek, blok zinciri üye işlem düğümünü siler.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Silinecek yeni işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |

## <a name="delete-a-blockchain-member"></a>Blok zinciri üyesini silme

Örnek, blok zinciri üyesini siler.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **name** | Silinecek Azure blok zinciri hizmeti blok zinciri üyesinin adı. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD kullanıcısı için erişim izni verme

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametre | Açıklama |
|---------|-------------|
| **rolü** | Azure AD rolünün adı. |
| **atanan** | Azure AD Kullanıcı KIMLIĞI. Örneğin, `user@contoso.com` |
| **kapsam** | Rol atamasının kapsamı. Bir blok zinciri üyesi ya da işlem düğümü olabilir. |

**Örnek:**

Azure AD kullanıcısı için blok zinciri **üyesine**düğüm erişimi verme:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Örnek:**

Azure AD kullanıcısı için blok zinciri **işlem düğümüne**düğüm erişimi verme:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD grubu veya uygulama rolü için düğüm erişimi verme

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parametre | Açıklama |
|---------|-------------|
| **rolü** | Azure AD rolünün adı. |
| **atane-nesne kimliği** | Azure AD Grup KIMLIĞI veya uygulama KIMLIĞI. |
| **kapsam** | Rol atamasının kapsamı. Bir blok zinciri üyesi ya da işlem düğümü olabilir. |

**Örnek:**

**Uygulama rolü** için düğüm erişimi verme

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD düğüm erişimini kaldırma

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametre | Açıklama |
|---------|-------------|
| **rolü** | Azure AD rolünün adı. |
| **atanan** | Azure AD Kullanıcı KIMLIĞI. Örneğin, `user@contoso.com` |
| **kapsam** | Rol atamasının kapsamı. Bir blok zinciri üyesi ya da işlem düğümü olabilir. |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmeti işlem düğümlerini Azure portal ile yapılandırma](configure-transaction-nodes.md)
