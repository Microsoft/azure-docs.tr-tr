---
title: Azure Veri Gezgini Python kitaplığını kullanarak verileri sorgulama
description: Bu makalede, Azure Veri Gezgini Python kullanarak verileri sorgulamayı öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 2c32e67bb2b47a24036a341ea4e1b83037abbaee
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827521"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Azure Veri Gezgini Python kitaplığını kullanarak verileri sorgulama

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini [Python için bir veri istemci kitaplığı](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data) sağlar. Bu kitaplık kodunuzdan verileri sorgulamanıza olanak tanır. Bu makalede, *Yardım kümesi* üzerinde öğrendiğimiz bir tabloya bağlanırsınız. Ardından bu kümedeki tabloyu sorgular ve sonuçları döndürürsünüz.

Bu makale bir [Azure Not defteri](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb)olarak da kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure Active Directory (AAD) üyesi olan bir kuruluş e-posta hesabı

* Geliştirme bilgisayarınıza yüklenmiş [Python](https://www.python.org/downloads/)

## <a name="install-the-data-library"></a>Veri kitaplığını yükleme

*azure-kusto-data*'yı yükleyin.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>İçeri aktarma deyimlerini ve sabitlerini ekleme

Kitaplıktan sınıfları ve ayrıca bir veri analizi kitaplığı olan *pandas*'ı içeri aktarın.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Azure Veri Gezgini uygulamanın kimliğini doğrulamak için AAD kiracı kimliğinizi kullanır. Kiracı kimliğinizi bulmak için aşağıdaki URL'yi kullanın ve *YourDomain* yerine kendi etki alanınızı yazın.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Örneğin, etki alanınız *contoso.com* olduğunda URL şöyle olur: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Sonuçları görmek için bu URL'ye tıklayın; ilk satır aşağıdaki gibidir.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Bu örnekte kiracı kimliği `6babcaad-604b-40ac-a9d7-9fd97c0b779f` değeridir. Bu kodu çalıştırmadan önce AAD_TENANT_ID değerini ayarlayın.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Şimdi bağlantı dizesini hazırlayın. Bu örnekte kümeye erişmek için cihaz kimlik doğrulaması kullanılır. [AAD uygulama sertifikası](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [AAD uygulama anahtarı](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)ve [AAD Kullanıcı ve parolasını](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)da kullanabilirsiniz.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Azure Veri Gezgini'ne bağlanma ve sorgu yürütme

Kümede bir sorgu yürütün ve çıkışı bir veri çerçevesinde depolayın. Bu kod çalıştırıldığında, aşağıdakine benzer bir ileti döndürür: *Oturum açmak https://microsoft.com/devicelogin için, sayfayı açmak üzere bir Web tarayıcısı kullanın ve kimlik doğrulaması yapmak için F3W4VWZDM kodunu girin*. Adımları izleyerek oturum açın, sonra da dönüp bir sonraki kod bloğunu çalıştırın.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>DataFrame'de verileri inceleme

Siz oturum açma bilgilerini girdikten sonra sorgu sonuçları döndürür ve bunlar veri çerçevesinde depolanır. Diğer herhangi bir veri çerçevesinde yaptığınız gibi sonuçlarla çalışabilirsiniz.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

StormEvents tablosunda ilk on sonucu görüyor olmalısınız.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Veri Gezgini Python kitaplığını kullanarak verileri alma](python-ingest-data.md)
