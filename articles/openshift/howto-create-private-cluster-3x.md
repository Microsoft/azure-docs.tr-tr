---
title: Azure Red Hat Openshıft 3,11 ile özel küme oluşturma | Microsoft Docs
description: Azure Red Hat OpenShift 3,11 ile özel bir küme oluşturma
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/02/2020
keywords: Aro, OpenShift, özel küme, Red Hat
ms.openlocfilehash: 28a21635da770a990a21e0da2a8034c754f4f7aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633249"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Azure Red Hat OpenShift 3,11 ile özel bir küme oluşturma

> [!IMPORTANT]
> Azure Red Hat Openshıft 3,11, 30 Haziran 2022 tarihinde kullanımdan kaldırılacaktır. Yeni Azure Red Hat OpenShift 3,11 kümelerinin oluşturulmasına yönelik destek, 30 Kasım 2020 ile devam eder. Aşağıdaki kullanımdan kaldırma, kalan Azure Red Hat Openshıft 3,11 kümeleri güvenlik açıklarını engellemek için kapatılacak.
> 
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:arofeedback@microsoft.com).

Özel kümeler aşağıdaki avantajları sağlar:

* Özel kümeler, genel bir IP adresindeki küme denetim düzlemi bileşenlerini (API sunucuları gibi) kullanıma sunmaz.
* Özel bir kümenin sanal ağı, müşteriler tarafından yapılandırılabilir ve ExpressRoute ortamları dahil diğer sanal ağlarla eşlemeye izin vermek için ağ ayarlamanıza olanak sağlar. Ayrıca, iç hizmetlerle tümleştirilecek şekilde sanal ağda özel DNS yapılandırabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki yapılandırma kod parçacığında yer alan alanlar yenidir ve küme yapılandırmanıza eklenmelidir. `managementSubnetCidr` küme sanal ağı dahilinde olmalıdır ve Azure tarafından kümeyi yönetmek için kullanılır.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Özel bir küme aşağıda belirtilen örnek betikler kullanılarak dağıtılabilir. Küme dağıtıldıktan sonra, `cluster get` komutu yürütün ve `properties.FQDN` OPENSHIFT API sunucusunun özel IP adresini belirleme özelliğini görüntüleyin.

Küme sanal ağı, değişiklik yapabilmeniz için izinlerle oluşturulur. Daha sonra, gereksinimlerinize göre gereken sanal ağa (ExpressRoute, VPN, sanal ağ eşlemesi) erişmek için ağ ayarlayabilirsiniz.

Küme sanal ağındaki DNS kullanır ' ı değiştirirseniz, `properties.RefreshCluster` `true` VM 'lerin yeniden oluşturulabilir olması için özelliği olarak ayarlanmış şekilde kümede bir güncelleştirme yapmanız gerekir. Bu güncelleştirme, yeni nameservers 'ın seçmesine izin verir.

## <a name="sample-configuration-scripts"></a>Örnek yapılandırma betikleri

Özel kümenizi ayarlamak ve dağıtmak için bu bölümdeki örnek betikleri kullanın.

### <a name="environment"></a>Ortam

Aşağıdaki ortam değişkenlerini kendi değerlerinizi kullanarak girin.

> [!NOTE]
> `eastus2`Bu, şu anda özel kümeler için desteklenen konum olduğu için olarak ayarlanmalıdır.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>Üzerinde private-cluster.js

Yukarıda tanımlanan ortam değişkenlerini kullanarak, özel küme etkin bir örnek küme yapılandırması aşağıda verilmiştir.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Özel küme dağıtma

Özel kümenizi yukarıdaki örnek betiklerle yapılandırdıktan sonra, özel kümenizi dağıtmak için aşağıdaki komutu çalıştırın.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Sonraki adımlar

OpenShift konsoluna erişme hakkında bilgi edinmek için bkz. [Web Konsolu](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)Kılavuzu.
