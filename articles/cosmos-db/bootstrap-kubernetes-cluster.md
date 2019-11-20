---
title: Azure Cosmos DB ile Azure Kubernetes 'i kullanma
description: Azure 'da Azure Cosmos DB kullanan bir Kubernetes kümesini önyükleme hakkında bilgi edinin (Önizleme)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093721"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure Kubernetes 'i Azure Cosmos DB ile kullanma (Önizleme)

Azure Cosmos DB ' deki etcd API 'SI, Azure Kubernetes için arka uç deposu olarak Azure Cosmos DB kullanmanıza olanak tanır. Azure Cosmos DB, ana düğümün API sunucularının yerel olarak yüklenmiş bir etcd 'ye erişmek gibi Azure Cosmos DB kullanmasına izin veren etcd tel protokolünü uygular. Azure Cosmos DB ' deki etcd API 'SI Şu anda önizlemededir. Kubernetes için yedekleme deposu olarak Azure Cosmos etcd API 'sini kullandığınızda, aşağıdaki avantajları elde edersiniz: 

* Etcd 'yi el ile yapılandırmanız ve yönetmeniz gerekmez.
* Cosmos (tek bölgede% 99,99, birden çok bölgede% 99,999) tarafından garanti edilen, etcd 'nin yüksek kullanılabilirliği.
* Etcd 'nin esnek ölçeklenebilirliği.
* Varsayılan olarak güvenilir & Kurumsal hazır.
* Sektör lideri, kapsamlı SLA 'Lar.

Azure Cosmos DB ' de etcd API 'SI hakkında daha fazla bilgi edinmek için [genel bakış](etcd-api-introduction.md) makalesine bakın. Bu makalede, Azure 'da yerel olarak yüklenmiş ve yapılandırılmış bir etcd yerine [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) kullanan bir Kubernetes kümesini önyüklemek Için [Azure Kubernetes altyapısının](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-Engine) nasıl kullanılacağı gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar

1. [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)'nın en son sürümünü yükler. İşletim sisteminize özel Azure CLı 'yi indirebilir ve yükleme yapabilirsiniz.

1. Azure Kubernetes altyapısının [en son sürümünü](https://github.com/Azure/aks-engine/releases) yükler. Farklı işletim sistemlerine yönelik yükleme yönergeleri [Azure Kubernetes motoru](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) sayfasında bulunabilir. Yalnızca bağlı belge ' nin **AKS altyapısı** ' nı yüklemeniz gerekir. İndirdikten sonra ZIP dosyasını ayıklayın.

   Azure Kubernetes altyapısı (**aks-Engine**), Azure 'Da Kubernetes kümeleri için Azure Resource Manager şablonları oluşturur. Aks-Engine 'e giriş, Orchestrator, Özellikler ve aracılar dahil olmak üzere istenen kümeyi tanımlayan bir küme tanımı dosyasıdır. Giriş dosyalarının yapısı, Azure Kubernetes hizmeti için genel API 'ye benzerdir.

1. Azure Cosmos DB ' deki etcd API 'SI Şu anda önizlemededir. Önizleme sürümünü şu adreste kullanmak için kaydolun: https://aka.ms/cosmosetcdapi-signup. Formu gönderdikten sonra, aboneliğiniz Azure Cosmos etcd API 'sini kullanmak için beyaz listeye alınacaktır. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Kümeyi Azure Cosmos DB dağıtma

1. Bir komut istemi penceresi açın ve aşağıdaki komutla Azure 'da oturum açın:

   ```azurecli-interactive
   az login 
   ```

1. Birden fazla aboneliğiniz varsa, Azure Cosmos DB etcd API 'SI için beyaz listeye alınmış aboneliğe geçiş yapın. Aşağıdaki komutu kullanarak gerekli aboneliğe geçiş yapabilirsiniz:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Ardından, Azure Kubernetes kümesi için gereken kaynakları dağıtacağınız yeni bir kaynak grubu oluşturun. Kaynak grubunu "merkezileştirme" bölgesinde oluşturduğunuzdan emin olun. Kaynak grubunun "merkezde ABD" bölgesinde olması zorunlu değildir, ancak Azure Cosmos etcd API 'SI Şu anda yalnızca "merkezileştirme" bölgesinde dağıtılabilir. Bu nedenle, Kubernetes kümesinin Cosmos etcd örneğiyle birlikte bulunması en iyisidir:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Daha sonra, Azure Kubernetes kümesi için aynı kaynak grubunun parçası olan kaynaklarla iletişim kurabilmesi için bir hizmet sorumlusu oluşturun. Azure CLı, PowerShell veya Azure portal kullanarak bir hizmet sorumlusu oluşturabilirsiniz. Bu örnekte, bunu oluşturmak için CLı yapmanız gerekir.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Bu komut, hizmet sorumlusunun ayrıntılarını verir, örneğin:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Sonraki adımlarda bu parametreleri kullanacağınız için, **AppID** ve **Password** alanlarını bir yere göz önünde bulabilirsiniz. 

1. Komut isteminden Azure Kubernetes altyapısının yürütülebilir dosyasının bulunduğu klasöre gidin. Örneğin, komut istemindeki klasöre şu şekilde gidebilirsiniz:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Seçtiğiniz bir metin düzenleyicisini açın ve Azure Kubernetes kümesini Azure Cosmos DB etcd API 'siyle dağıtan bir Kaynak Yöneticisi şablonu tanımlayın. Aşağıdaki JSON tanımını metin düzenleyicinize kopyalayın ve dosyayı şu şekilde `apiModel.json`kaydedin:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   JSON/Cluster tanım dosyasında, Note anahtar parametresi **"Cosmosetcd": true**olur. Bu parametre "masterProfile" özelliklerinde bulunur ve normal etcd yerine Azure Cosmos etcd API 'sini kullanmak için dağıtımı gösterir. 

1. Azure Cosmos DB kullanan Azure Kubernetes kümesini aşağıdaki komutla dağıtın:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes altyapısı, Azure Kubernetes 'in istenen şeklini, boyutunu ve yapılandırmasını özetleyen bir küme tanımı kullanır. Küme tanımı aracılığıyla etkinleştirilemeyen çeşitli özellikler vardır. Bu örnekte, aşağıdaki parametreleri kullanacaksınız:

   * **abonelik kimliği:** Azure Cosmos DB etcd API 'SI etkinleştirilmiş Azure abonelik KIMLIĞI.
   * **istemci kimliği:** Hizmet sorumlusunun uygulama kimliği. `appId` 4. adımda çıkış olarak döndürüldü.
   * **İstemci parolası:** Hizmet sorumlusunun parolası veya rastgele oluşturulmuş bir parola. Bu değer, 4. adımdaki ' password ' parametresinde çıkış olarak döndürüldü. 
   * **DnsPrefix** Bölgeye özgü bir DNS adı. Bu değer, ana bilgisayar adının bir kısmını oluşturacak (örnek değerler-myprod1, hazırlama).
   * **konumuna**  Kümenin dağıtılacağı konum, şu anda yalnızca "merkezileştirmiş" destekleniyor.

   > [!Note]
   > Azure Cosmos etcd API 'SI Şu anda yalnızca "merkezde ABD" bölgesinde dağıtım için kullanılabilir. 
 
   * **api-model:** Şablon dosyasının tam yolu.
   * **zorla üzerine yaz:** Bu seçenek, çıkış dizinindeki mevcut dosyaların otomatik olarak üzerine yazmak için kullanılır.
 
   Aşağıdaki komut, örnek bir dağıtım göstermektedir:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Şablon dağıtımının tamamlanabilmesi birkaç dakika sürer. Dağıtım başarıyla tamamlandıktan sonra, komut isteminde aşağıdaki çıktıyı görürsünüz:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Kaynak grubu artık-sanal makine, Azure Cosmos hesabı (etcd API), sanal ağ, kullanılabilirlik kümesi ve Kubernetes kümesi için gereken diğer kaynaklar gibi kaynakları içerir. 

Azure Cosmos hesabının adı, k8s eklenmiş olan belirtilen DNS ön ekine göre eşleşir. Azure Cosmos hesabınız, **Etcddb** adlı bir veritabanı ve **etcddata**adlı bir kapsayıcı ile otomatik olarak sağlanacak. Kapsayıcı, tüm etcd ile ilgili verileri depolar. Kapsayıcı belirli sayıda istek birimiyle sağlanır ve iş yükünüze göre [aktarım hızını ölçeklendirebilir (artırabilir/azaltabilirsiniz)](scaling-throughput.md) . 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos veritabanı, kapsayıcılar ve öğeleriyle çalışmayı](databases-containers-items.md) öğrenin
* [Sağlanan verimlilik maliyetlerini iyileştirmek](optimize-cost-throughput.md) hakkında bilgi edinin

