---
title: Azure HDInsight küme düğümlerinin ana bilgisayar adlarını alma
description: Azure HDInsight küme düğümlerinin konak adlarını ve FQDN adını alma hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111492"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Küme düğümlerinin konak adlarını bulma

HDInsight kümesi Genel DNS clustername.azurehdinsight.net ile oluşturulur. Tek tek düğümlere SSH gönderdiğinizde veya aynı özel sanal ağda bulunan küme düğümlerine bağlantı ayarlarsanız, küme düğümlerinin ana bilgisayar adını veya tam etki alanı adlarını (FQDN) kullanmanız gerekir.

Bu makalede, küme düğümlerinin ana bilgisayar adlarını nasıl alabileceğinizi öğreneceksiniz. Ambarı web kullanıcı arabiriminden el ile veya REST API ambarı üzerinden otomatik olarak alabilirsiniz.

> [!WARNING]
> Küme düğümlerinin ana bilgisayar adlarını getirmek için lütfen aşağıdaki önerilen yaklaşımları kullanın. Ana bilgisayar adındaki sayıların sırada garanti edilmez ve HDInsight, ana bilgisayar adı biçimini yayın yenilemesi olan VM 'lerle hizalanacak şekilde değiştirebilir. Bu, bugün mevcut olan herhangi bir adlandırma kuralına bağımlılığı almaz. 
>

Konak adlarını, ambarı Kullanıcı arabirimi veya ambarı REST API aracılığıyla alabilirsiniz. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Konak adlarını ambarı Web kullanıcı arabiriminden al
Düğümde SSH oluştururken konak adlarını almak için, ambarı Web Kullanıcı arabirimini kullanabilirsiniz. Ambarı Web UI Konakları görünümü adresindeki HDInsight kümenizde bulunur; `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` burada `CLUSTERNAME` kümenizin adıdır.

![-Konak------------UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Konak adlarını ambarı REST API al
Otomasyon betikleri oluştururken, konaklara bağlantı yapmadan önce konak adlarını almak için ambarı REST API kullanabilirsiniz. Ana bilgisayar adındaki sayıların sırada garanti edilmez ve HDInsight, ana bilgisayar adı biçimini yayın yenileme ile VM 'lerle hizalanacak şekilde değiştirebilir. Bu, bugün mevcut olan herhangi bir adlandırma kuralına bağımlılığı almaz. 

Kümedeki düğümlerin FQDN 'sini alma hakkında bazı örnekler aşağıda verilmiştir. REST API ambarı hakkında daha fazla bilgi için bkz [. Apache ambarı 'nı kullanarak HDInsight kümelerini yönetme REST API](.\hdinsight-hadoop-manage-ambari-rest-api.md)

Aşağıdaki örnek, JSON yanıt belgesini ayrıştırmak ve yalnızca konak adlarını göstermek için [JQ](https://stedolan.github.io/jq/) veya [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) ' u kullanır.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```