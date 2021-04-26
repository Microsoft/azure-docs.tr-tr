---
title: Batch havuzunda otomatik sertifika döndürmeyi etkinleştirme
description: Yönetilen kimliğe ve otomatik olarak yenilenecek bir sertifikaya sahip bir Batch havuzu oluşturabilirsiniz.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962700"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Batch havuzunda otomatik sertifika döndürmeyi etkinleştirme

 Otomatik olarak yenilenecek bir sertifikayla Batch havuzu oluşturabilirsiniz. Bunu yapmak için, havuzunuzun [Azure Key Vault](../key-vault/general/overview.md)' deki sertifikaya erişimi olacak [Kullanıcı tarafından atanan bir yönetilen kimlikle](managed-identity-pools.md) oluşturulması gerekir.

> [!IMPORTANT]
> Kullanıcı tarafından atanan yönetilen kimliklerle Azure Batch havuzlara yönelik destek şu bölgeler için şu anda genel önizlemededir: Batı ABD 2, Orta Güney ABD, Doğu ABD, US Gov Arizona ve US Gov Virginia.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik oluşturma

İlk olarak, [Kullanıcı tarafından atanan yönetilen kimliğinizi](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) Batch hesabınızla aynı kiracıda oluşturun. Bu yönetilen kimliğin aynı kaynak grubunda olması veya aynı abonelikte bile olması gerekmez.

Kullanıcı tarafından atanan yönetilen kimliğin **ISTEMCI kimliğini** aklınızda olduğunuzdan emin olun. Bu değere daha sonra ihtiyacınız olacak.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Azure portal Kullanıcı tarafından atanan yönetilen kimliğin istemci KIMLIĞINI gösteren ekran görüntüsü.":::

## <a name="create-your-certificate"></a>Sertifikanızı oluşturma

Daha sonra, bir sertifika oluşturmanız ve bunu Azure Key Vault eklemeniz gerekir. Henüz bir Anahtar Kasası oluşturmadıysanız, önce bunu yapmanız gerekir. Yönergeler için bkz. [hızlı başlangıç: Azure Key Vault Azure Portal kullanarak bir sertifikayı ayarlama ve alma](../key-vault/certificates/quick-create-portal.md).

Sertifikanızı oluştururken otomatik olarak yenilemek için **ömür eylemi türünü** ayarladığınızdan emin olun ve sertifikanın yenilenmesi gereken gün sayısını belirtin.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Azure portal sertifika oluşturma ekranının ekran görüntüsü.":::

Sertifikanız oluşturulduktan sonra, **gizli dizi tanımlayıcısını** unutmayın. Bu değere daha sonra ihtiyacınız olacak.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Bir sertifikanın gizli tanımlayıcısını gösteren ekran görüntüsü.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Azure Key Vault erişim ilkesi ekleme

Anahtar Kasanızda, Kullanıcı tarafından atanan yönetilen kimliğinizin gizli dizileri ve sertifikalara erişmesine izin veren bir Key Vault erişim ilkesi atayın. Ayrıntılı yönergeler için, bkz. [Azure Portal kullanarak Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliğe sahip bir Batch havuzu oluşturma

[Batch .NET Yönetim Kitaplığı](/dotnet/api/overview/azure/batch#management-library)'nı kullanarak yönetilen kimliğiniz Ile bir Batch havuzu oluşturun. Daha fazla bilgi için bkz. [Batch havuzlarında yönetilen kimlikleri yapılandırma](managed-identity-pools.md).

Aşağıdaki örnek, bir havuz oluşturmak için Batch yönetimi REST API kullanır. Aşağıdaki örnek verileri değiştirerek, için sertifikanızın **gizli tanımlayıcısını** `observedCertificates` ve YÖNETILEN kimliğin **istemci kimliğini** kullandığınızdan emin olun `msiClientId` .

REST API URI'si

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

İstek Gövdesi

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Sertifikayı doğrulama

Sertifikanın başarıyla dağıtıldığını doğrulamak için, işlem düğümünde oturum açın. Aşağıdakine benzer bir çıktı görmeniz gerekir:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.
- [Müşteri tarafından yönetilen anahtarların Kullanıcı tarafından yönetilen kimliklerle](batch-customer-managed-key.md)nasıl kullanılacağını öğrenin.
