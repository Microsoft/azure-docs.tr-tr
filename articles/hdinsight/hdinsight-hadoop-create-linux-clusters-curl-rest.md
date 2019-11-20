---
title: Azure REST API kullanarak Apache Hadoop kümeleri oluşturma-Azure
description: Azure REST API Azure Resource Manager şablonları göndererek HDInsight kümeleri oluşturmayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: hrasheed
ms.openlocfilehash: 963dc71097a1ac53df77f3ab9c804b53597adeb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152002"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Azure REST API kullanarak Apache Hadoop kümeleri oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Resource Manager şablonu ve Azure REST API kullanarak HDInsight kümesi oluşturmayı öğrenin.

Azure REST API, Azure platformunda barındırılan, HDInsight kümeleri gibi yeni kaynaklar oluşturma da dahil olmak üzere yönetim işlemleri gerçekleştirmenize olanak tanır.

> [!NOTE]  
> Bu belgedeki adımlarda, Azure REST API iletişim kurmak için [kıvrımlı (https://curl.haxx.se/)](https://curl.haxx.se/) yardımcı programı kullanılır.

## <a name="create-a-template"></a>Şablon oluşturma

Azure Resource Manager şablonlar, **kaynak grubunu** ve içindeki tüm kaynakları (HDInsight gibi) tanımlayan JSON belgelerdir. Bu şablon tabanlı yaklaşım, HDInsight için ihtiyaç duyduğunuz kaynakları tek bir şablonda tanımlamanızı sağlar.

Aşağıdaki JSON belgesi, SSH kullanıcı hesabının güvenliğini sağlamak için bir parola kullanan Linux tabanlı küme oluşturan [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)şablon ve parametre dosyalarının birleşmesi olur.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Bu örnek, bu belgedeki adımlarda kullanılır. **Parameters** bölümündeki örnek *değerleri* kümenizin değerleriyle değiştirin.

> [!IMPORTANT]  
> Şablon, HDInsight kümesi için varsayılan çalışan düğümü sayısını (4) kullanır. 32 ' den fazla çalışan düğümünü planlıyorsanız, en az 8 çekirdek ve 14 GB RAM içeren bir baş düğüm boyutu seçmeniz gerekir.
>
> Düğüm boyutları ve ilişkili maliyetler hakkında daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

[Azure CLI kullanmaya başlama](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) bölümünde belgelenen adımları izleyin ve `az login` komutunu kullanarak aboneliğinize bağlanın.

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

> [!NOTE]  
> Bu adımlar, [kaynaklara erişmek üzere bir hizmet sorumlusu oluşturmak Için Azure CLI kullanma](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) başlıklı ' ın *parola Ile hizmet sorumlusu oluşturma* bölümünün, desteklenmeyen bir sürümüdür. Bu adımlar, Azure REST API kimlik doğrulaması yapmak için kullanılan bir hizmet sorumlusu oluşturur.

1. Bir komut satırından, Azure aboneliklerinizi listelemek için aşağıdaki komutu kullanın.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Listede, kullanmak istediğiniz aboneliği seçin ve **Subscription_ID** ve __Tenant_ID__ sütunlarını unutmayın. Bu değerleri kaydedin.

2. Azure Active Directory bir uygulama oluşturmak için aşağıdaki komutu kullanın.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    `--display-name`, `--homepage`ve `--identifier-uris` değerlerini kendi değerlerinizle değiştirin. Yeni Active Directory girişi için bir parola girin.

   > [!NOTE]  
   > `--home-page` ve `--identifier-uris` değerlerinin Internet 'te barındırılan gerçek bir Web sayfasına başvurması gerekmez. Benzersiz URI 'Ler olmalıdır.

   Bu komuttan döndürülen değer, yeni uygulamanın __uygulama kimliğidir__ . Bu değeri kaydedin.

3. **Uygulama kimliğini**kullanarak bir hizmet sorumlusu oluşturmak için aşağıdaki komutu kullanın.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Bu komuttan döndürülen değer, __nesne kimliğidir__. Bu değeri kaydedin.

4. **Nesne kimliği** değerini kullanarak hizmet sorumlusuna **sahip** rolünü atayın. Daha önce edindiğiniz **ABONELIK kimliğini** kullanın.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Kimlik doğrulama belirteci al

Bir kimlik doğrulama belirteci almak için aşağıdaki komutu kullanın:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

`$TENANTID`, `$APPID`ve `$PASSWORD` daha önce elde edilen veya kullanılan değerlere ayarlayın.

Bu istek başarılı olursa, bir 200 serisi yanıtı alırsınız ve yanıt gövdesi bir JSON belgesi içerir.

Bu istek tarafından döndürülen JSON belgesi, **access_token**adlı bir öğe içeriyor. **Access_token** değeri, REST API isteklerinde kimlik doğrulaması yapmak için kullanılır.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bir kaynak grubu oluşturmak için aşağıdakileri kullanın.

* Hizmet sorumlusu oluşturulurken alınan abonelik KIMLIĞINE `$SUBSCRIPTIONID` ayarlayın.
* Önceki adımda alınan erişim belirtecine `$ACCESSTOKEN` ayarlayın.
* `DATACENTERLOCATION`, kaynak grubu oluşturmak istediğiniz veri merkezi ile ve içindeki kaynakları ile değiştirin. Örneğin, ' Orta Güney ABD '.
* Bu grup için kullanmak istediğiniz ada `$RESOURCEGROUPNAME` ayarlayın:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Bu istek başarılı olursa, bir 200 serisi yanıtı alırsınız ve yanıt gövdesi, Grup hakkında bilgi içeren bir JSON belgesi içerir. `"provisioningState"` öğesi `"Succeeded"`değerini içerir.

## <a name="create-a-deployment"></a>Dağıtım oluşturma

Şablonu kaynak grubuna dağıtmak için aşağıdaki komutu kullanın.

* Bu dağıtım için kullanmak istediğiniz ada `$DEPLOYMENTNAME` ayarlayın.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Şablonu bir dosyaya kaydettiyseniz, `-d "{ template and parameters}"`yerine aşağıdaki komutu kullanabilirsiniz:
>
> `--data-binary "@/path/to/file.json"`

Bu istek başarılı olursa, bir 200 serisi yanıtı alırsınız ve yanıt gövdesi dağıtım işlemiyle ilgili bilgileri içeren bir JSON belgesi içerir.

> [!IMPORTANT]  
> Dağıtım gönderildi, ancak tamamlanmadı. Dağıtımın tamamlanması için genellikle 15 etrafında birkaç dakika sürebilir.

## <a name="check-the-status-of-a-deployment"></a>Bir dağıtımın durumunu denetleme

Dağıtımın durumunu denetlemek için şu komutu kullanın:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Bu komut, dağıtım işlemi hakkında bilgi içeren bir JSON belgesi döndürür. `"provisioningState"` öğesi dağıtımın durumunu içerir. Bu öğe bir `"Succeeded"`değeri içeriyorsa Dağıtım başarıyla tamamlanır.

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık bir HDInsight kümesini başarıyla oluşturduğunuza göre, kümenizle nasıl çalışacağınızı öğrenmek için aşağıdakileri kullanın.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kümeleri

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](hadoop/hdinsight-use-pig.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kümeleri

* [HDInsight üzerinde Apache Storm için Java topolojileri geliştirme](storm/apache-storm-develop-java-topology.md)
* [HDInsight üzerinde Apache Storm Python bileşenlerini kullanma](storm/apache-storm-develop-python-topology.md)
* [HDInsight üzerinde Apache Storm topolojilerini dağıtma ve izleme](storm/apache-storm-deploy-monitor-topology-linux.md)
