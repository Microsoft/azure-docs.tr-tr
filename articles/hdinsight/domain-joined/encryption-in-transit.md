---
title: Yoldaki Azure HDInsight şifrelemesi
description: Azure HDInsight kümeniz için iletim sırasında şifreleme sağlamaya yönelik güvenlik özellikleri hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946847"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Azure HDInsight için iletim sırasında IPSec şifrelemesi

Bu makalede, Azure HDInsight küme düğümleri arasındaki iletişim için iletim sırasında Şifrelemenin uygulanması ele alınmaktadır.

## <a name="background"></a>Arka Plan

Azure HDInsight, kurumsal verilerinizi güvenli hale getirmek için çeşitli güvenlik özellikleri sunar. Bu çözümler, çevre güvenliği, kimlik doğrulama, yetkilendirme, denetim, şifreleme ve uyumluluk gibi değerler altında gruplandırılır. Şifreleme, hem bekleyen hem de aktarım sırasında verilere uygulanabilir.

Bekleyen şifreleme, Azure depolama hesaplarında sunucu tarafı şifrelemenin yanı sıra HDInsight kümenizin bir parçası olan Azure VM 'lerinde disk şifrelemesi de kapsamına alınmıştır.

HDInsight 'ta geçiş sırasında verilerin şifrelenmesi, küme ağ geçitleri ve [Internet Protokolü güvenliğine (IPSec)](https://wikipedia.org/wiki/IPsec) küme düğümleri arasında erişmek Için [Aktarım KATMANı güvenliği (TLS)](../transport-layer-security.md) ile elde edilir. IPSec isteğe bağlı olarak tüm baş düğümler, çalışan düğümleri, kenar düğümleri, Zookeeper düğümleri ve ağ geçidi ve [kimlik Aracısı](./identity-broker.md) düğümleri arasında etkinleştirilebilir.

## <a name="enable-encryption-in-transit"></a>İletimde şifrelemeyi etkinleştir

### <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak geçiş özelliği etkinken şifreleme ile yeni bir küme oluşturmak için aşağıdaki adımları uygulayın:

1. Normal küme oluşturma işlemini başlatın. İlk küme oluşturma adımları için [Azure Portal kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) bölümüne bakın.
1. **Temel bilgiler** ve **depolama** sekmelerini doldurun. **Güvenlik + ağ** sekmesine geçin.

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Küme oluşturma-güvenlik ve ağ sekmesi.":::

1. **Güvenlik + ağ** sekmesinde, **Transit şifrelemeyi etkinleştir** onay kutusunu seçin.

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Küme oluşturma-geçiş sırasında şifrelemeyi etkinleştirin.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Azure CLı aracılığıyla etkinleştirilen geçişte şifreleme ile küme oluşturma

Geçiş sırasında şifreleme özelliği kullanılarak etkinleştirildi `isEncryptionInTransitEnabled` .

[Örnek bir şablon ve parametre dosyası indirebilirsiniz](https://github.com/Azure-Samples/hdinsight-enterprise-security). Aşağıdaki şablon ve Azure CLı kod parçacığını kullanmadan önce aşağıdaki yer tutucuları doğru değerlerle değiştirin:

| Yer tutucu | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure aboneliğinizin KIMLIĞI |
| `<RESOURCE_GROUP>` | Yeni küme ve depolama hesabının oluşturulmasını istediğiniz kaynak grubu. |
| `<STORAGEACCOUNTNAME>` | Kümeyle kullanılması gereken mevcut depolama hesabı. Ad şu biçimde olmalıdır `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | HDInsight Kümenizin adı. |
| `<PASSWORD>` | SSH ve ambarı panosunu kullanarak kümede oturum açmak için seçtiğiniz parola. |
| `<VNET_NAME>` | Kümenin dağıtılacağı sanal ağ. |

Aşağıdaki kod parçacığı aşağıdaki ilk adımları yapar:

1. Azure hesabınızda oturum açar.
1. Oluşturma işlemlerinin yapılacağı etkin aboneliği ayarlar.
1. Yeni dağıtım etkinlikleri için yeni bir kaynak grubu oluşturur.
1. Yeni bir küme oluşturmak için şablonu dağıtın.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight 'ta Kurumsal güvenliğe genel bakış](hdinsight-security-overview.md)
* [Azure Active Directory kullanıcılarını bir HDInsight kümesiyle eşitler](../disk-encryption.md).
