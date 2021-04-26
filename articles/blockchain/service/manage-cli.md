---
title: Azure CLI kullanarak Azure Blok Zinciri Hizmeti’ni yönetme
description: Azure CLı ile Azure blok zinciri hizmetini yönetme
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 55df56274aa5baa946b60c27cf49723d59c928a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865936"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI kullanarak Azure Blok Zinciri Hizmeti’ni yönetme

Azure portal ek olarak Azure CLı 'yi kullanarak Azure blok zinciri hizmetinize yönelik blok zinciri üyelerini ve işlem düğümlerini yönetebilirsiniz.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ediyorsanız bkz. [Azure CLI 'yı yüklemek](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

1. Oturum açın.

    CLI’nın yerel yüklemesini kullanıyorsanız [az login](/cli/azure/reference-index#az_login) komutunu kullanarak oturum açın.

    ```azurecli
    az login
    ```

    Terminalinizde görüntülenen adımları uygulayarak kimlik doğrulama işlemini tamamlayın.

1. Azure CLI uzantısını yükleyin.

    Azure CLI için uzantı başvurularıyla çalışırken önce uzantıyı yüklemeniz gerekir.  Azure CLI uzantıları, henüz temel CLI’nın parçası olarak gönderilmeyen deneysel ve ön sürüm komutlarına erişmenize olanak sağlar.  Güncelleştirme ve kaldırma da dahil olmak üzere uzantılar hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile uzantıları kullanma](/cli/azure/azure-cli-extensions-overview).

    Aşağıdaki komutu çalıştırarak [Azure blok zinciri hizmeti uzantısını](/cli/azure/blockchain) yükler:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Blok zinciri üyesi oluştur

Örnek, Azure blok zinciri hizmetinde yeni bir konsorsiyumun çekirdek defter protokolünü çalıştıran [bir blok zinciri üyesi oluşturur](/cli/azure/blockchain/member#az_blockchain_member_create) .

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **ada** | Azure blok zinciri hizmeti blok zinciri üyesini tanımlayan benzersiz bir ad. Ad, genel uç nokta adresi için kullanılır. Örneğin, `myblockchainmember.blockchain.azure.com`. |
| **konumuna** | Blok zinciri üyesinin oluşturulduğu Azure bölgesi. Örneğin, `eastus`. Kullanıcılarınıza veya diğer Azure uygulamalarınıza en yakın konumu seçin. Özellikler, bazı bölgelerde kullanılamayabilir. |
| **parola** | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın. Parolanın aşağıdaki dört gereksinimin üç gereksinimini karşılaması gerekir: Uzunluk 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak ( \` ), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;)) arasında olmalıdır.|
| **Protocol** | Blok zinciri protokolü. Şu anda *çekirdek* protokol destekleniyor. |
| **unun** | Katılacak veya oluşturulacak konsorsiyumun adı. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md). |
| **Konsorsiyum yönetimi-hesap-parola** | Konsorsiyum hesabı parolası üye hesap parolası olarak da bilinir. Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız. |
| **isteyin** | Katman türü. *Standart* veya *temel*. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Ayrıca, blok Veri Yöneticisi Zinciri kullanıyorsanız veya yüksek hacimli özel işlemler gönderiyorsanız *Standart* katmanı da kullanmanız gerekir. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Blok zinciri üye parolalarını veya güvenlik duvarı kurallarını değiştirme

Örnek [bir blok zinciri üyesinin](/cli/azure/blockchain/member#az_blockchain_member_update)parolasını, konsorsiyum yönetim parolasını ve güvenlik duvarı kuralını güncelleştirir.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **ada** | Azure blok zinciri hizmeti üyesini tanımlayan ad. |
| **parola** | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın. Parolanın aşağıdaki dört gereksinimin üç gereksinimini karşılaması gerekir: Uzunluk 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak ( \` ), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;)) arasında olmalıdır.|
| **Konsorsiyum yönetimi-hesap-parola** | Konsorsiyum hesabı parolası üye hesap parolası olarak da bilinir. Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız. |
| **Güvenlik Duvarı-kurallar** | IP izin verilenler listesi için başlangıç ve bitiş IP adresi. |

## <a name="create-transaction-node"></a>İşlem düğümü oluştur

Varolan bir blok zinciri üyesinin içinde [bir işlem düğümü oluşturun](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create) . İşlem düğümleri ekleyerek güvenlik yalıtımını artırabilir ve yükü dağıtabilirsiniz. Örneğin, farklı istemci uygulamaları için bir işlem düğümü uç noktasına sahip olabilirsiniz.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. |
| **konumuna** | Blok zinciri üyesinin Azure bölgesi. |
| **üye adı** | Azure blok zinciri hizmeti üyesini tanımlayan ad. |
| **parola** | İşlem düğümü için parola. İşlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın. Parolanın aşağıdaki dört gereksinimin üç gereksinimini karşılaması gerekir: Uzunluk 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak ( \` ), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;)) arasında olmalıdır.|
| **ada** | İşlem düğümü adı. |

## <a name="change-transaction-node-password"></a>İşlem düğümü parolasını değiştir

Örnek, [işlem düğümü parolasını güncelleştirir](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **üye adı** | Azure blok zinciri hizmeti üyesini tanımlayan ad. |
| **parola** | İşlem düğümü için parola. İşlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın. Parolanın aşağıdaki dört gereksinimin üç gereksinimini karşılaması gerekir: Uzunluk 12 & 72 karakter, 1 küçük harf karakter, 1 büyük harf, 1 sayı ve 1 özel karakter (sayı işareti (#), yüzde (%), virgül (,), yıldız (*), arka tırnak ( \` ), çift tırnak ("), tek tırnak ('), tire (-) ve semicolumn (;)) arasında olmalıdır.|
| **ada** | İşlem düğümü adı. |

## <a name="list-api-keys"></a>API anahtarlarını Listele

API anahtarları, Kullanıcı adı ve parolaya benzer düğüm erişimi için kullanılabilir. Anahtar döndürmeyi desteklemek için iki API anahtarı vardır. [API anahtarlarınızı listelemek](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key)için aşağıdaki komutu kullanın.

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **ada** | Azure blok zinciri hizmeti blok zinciri üyesinin adı |

## <a name="regenerate-api-keys"></a>API anahtarlarını yeniden oluştur

[API anahtarlarınızı yeniden oluşturmak](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key)için aşağıdaki komutu kullanın.

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **ada** | Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **Işareti** | \<keyValue\>KEY1, key2 veya her ikisiyle de değiştirin. |

## <a name="delete-a-transaction-node"></a>İşlem düğümünü silme

Örnek, [blok zinciri üye işlem düğümünü siler](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **üye adı** | Silinecek işlem düğümü adını da içeren Azure blok zinciri hizmeti blok zinciri üyesinin adı. |
| **ada** | Silinecek işlem düğümü adı. |

## <a name="delete-a-blockchain-member"></a>Blok zinciri üyesini silme

Örnek, [blok zinciri üyesini siler](/cli/azure/blockchain/member#az_blockchain_member_delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parametre | Açıklama |
|---------|-------------|
| **kaynak grubu** | Azure blok zinciri hizmeti kaynaklarının mevcut olduğu kaynak grubu adı. |
| **ada** | Silinecek Azure blok zinciri hizmeti blok zinciri üyesinin adı. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD kullanıcısı için erişim izni verme

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametre | Açıklama |
|---------|-------------|
| **rolü** | Azure AD rolünün adı. |
| **atanan** | Azure AD Kullanıcı KIMLIĞI. Örneğin, `user@contoso.com` |
| **kapsam** | Rol atamasının kapsamı. Bir blok zinciri üyesi ya da işlem düğümü olabilir. |

**Örnek:**

Azure AD kullanıcısı için blok zinciri **üyesine** düğüm erişimi verme:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Örnek:**

Azure AD kullanıcısı için blok zinciri **işlem düğümüne** düğüm erişimi verme:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD grubu veya uygulama rolü için düğüm erişimi verme

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
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
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD düğüm erişimini kaldırma

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametre | Açıklama |
|---------|-------------|
| **rolü** | Azure AD rolünün adı. |
| **atanan** | Azure AD Kullanıcı KIMLIĞI. Örneğin, `user@contoso.com` |
| **kapsam** | Rol atamasının kapsamı. Bir blok zinciri üyesi ya da işlem düğümü olabilir. |

## <a name="next-steps"></a>Sonraki adımlar

[Azure blok zinciri hizmeti işlem düğümlerini Azure Portal nasıl yapılandıracağınızı](configure-transaction-nodes.md)öğrenin.
