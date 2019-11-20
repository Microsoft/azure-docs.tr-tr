---
title: Azure Cosmos DB yerleşik Not defteri komutlarını ve özelliklerini kullanın
description: Azure Cosmos DB yerleşik not defterlerini kullanarak genel işlemleri yapmak için yerleşik komutları ve özellikleri nasıl kullanacağınızı öğrenin.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 4a9bd554e0858024d656dbf35d6fb00995e6f4bd
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672480"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Azure Cosmos DB yerleşik Not defteri komutlarını ve özelliklerini kullanın

Azure Cosmos DB 'daki yerleşik jupi Not defterleri, Azure portal verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makalede, yerleşik Not defteri komutlarının ve özelliklerinin genel işlemleri yapmak için nasıl kullanılacağı açıklanır.

## <a name="install-a-new-package"></a>Yeni bir paket yükler
Azure Cosmos hesaplarınız için Not defteri desteğini etkinleştirdikten sonra, yeni bir not defteri açıp bir paket yükleyebilirsiniz.

Yeni bir kod hücresinde, istenen Python paketiyle değiştirerek ``PackageToBeInstalled`` aşağıdaki kodu ekleyin ve çalıştırın.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Bu paket, Azure Cosmos hesap çalışma alanındaki herhangi bir not defterinden kullanılabilir. 

> [!TIP]
> Not defteriniz için özel bir paket gerekiyorsa, [çalışma alanını sıfırladıktan](#reset-notebooks-workspace)sonra paketler kaldırıladıkça, paketi yüklemek için Not defterinize bir hücre eklemenizi öneririz.  

## <a name="run-a-sql-query"></a>SQL sorgusu çalıştırma

Bir [SQL sorgusunu](sql-query-getting-started.md) hesabınızdaki ``%%sql`` herhangi bir kapsayıcıya karşı çalıştırmak için Magic komutunu kullanabilirsiniz. Sözdizimini kullanın:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Ve ``{database_id}`` ' ``{container_id}`` i, Cosmos hesabınızdaki veritabanı ve kapsayıcının adıyla değiştirin. Ve bağımsız değişkenleri sağlanmazsa, sorgu [varsayılan veritabanı ve kapsayıcıda](#set-default-database-for-queries)yürütülür. ``--container`` ``--database``
- Azure Cosmos DB için geçerli olan herhangi bir SQL sorgusunu çalıştırabilirsiniz. Sorgu metni yeni bir satırda olmalıdır.

Örneğin: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Not ```%%sql?``` defterindeki SQL Magic komutunun Yardım belgelerini görmek için bir hücrede çalıştırın.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Bir Pandas DataFrame 'e SQL sorgusu ve çıkışı çalıştırma

Bir ``%%sql`` sorgunun sonuçlarını bir [Pandas dataframe](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)'e aktarabilirsiniz. Sözdizimini kullanın: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Ve ``{database_id}`` ' ``{container_id}`` i, Cosmos hesabınızdaki veritabanı ve kapsayıcının adıyla değiştirin. Ve bağımsız değişkenleri sağlanmazsa, sorgu [varsayılan veritabanı ve kapsayıcıda](#set-default-database-for-queries)yürütülür. ``--container`` ``--database``
- Sonuçları ``{outputDataFrameVar}`` içerecek dataframe değişkeninin adıyla değiştirin.
- Azure Cosmos DB için geçerli olan herhangi bir SQL sorgusunu çalıştırabilirsiniz. Sorgu metni yeni bir satırda olmalıdır. 

Örneğin:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Sorgular için varsayılan veritabanını ayarla
Not defteri için kullanılacak varsayılan veritabanı ```%%sql``` komutlarını ayarlayabilirsiniz. Veritabanınızın ```{database_id}``` adıyla değiştirin.

```bash
%database {database_id}
```
Not ```%database?``` defterindeki belgeleri görmek için bir hücrede çalıştırın.

## <a name="set-default-container-for-queries"></a>Sorgular için varsayılan kapsayıcıyı ayarla
Not defteri için kullanılacak varsayılan kapsayıcı ```%%sql``` komutlarını ayarlayabilirsiniz. Kapsayıcının ```{container_id}``` adıyla değiştirin.

```bash
%container {container_id}
```
Not ```%container?``` defterindeki belgeleri görmek için bir hücrede çalıştırın.

## <a name="use-the-built-in-python-sdk"></a>Yerleşik Python SDK 'sını kullanma
[SQL API için Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 'nın sürüm 4 ' ü yüklenir ve Cosmos hesabı için Not defteri ortamına dahil edilmiştir.

Herhangi bir SDK işlemini çalıştırmak ``cosmos_client`` için yerleşik örneği kullanın. 

Örneğin:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Bkz. [Python SDK örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Yerleşik Python SDK 'Sı yalnızca SQL (çekirdek) API hesapları için desteklenir. Diğer API 'Ler için, API 'ye karşılık gelen [Ilgili Python sürücüsünü yüklemeniz](#install-a-new-package) gerekir. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Özel bir örneği oluşturma``cosmos_client``
Daha fazla esneklik için, aşağıdakileri yapmak için özel bir örneği ``cosmos_client`` oluşturabilirsiniz:

- [Bağlantı ilkesini](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) özelleştirme
- İşlemleri, farklı bir Cosmos hesabına karşı çalıştırın

Geçerli hesabın bağlantı dizesine ve birincil anahtarına [ortam değişkenleri](#access-the-account-endpoint-and-primary-key-env-variables)aracılığıyla erişebilirsiniz. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Hesap uç noktasına ve birincil anahtar env değişkenlerine erişin
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` Ve``COSMOS_KEY`` ortam değişkenleri yalnızca SQL API için geçerlidir. Diğer API 'Ler için, Cosmos hesabınızdaki **bağlantı dizeleri** veya **anahtarlar** dikey penceresinde uç noktasını ve anahtarı bulun.  

## <a name="reset-notebooks-workspace"></a>Not defteri çalışma alanını Sıfırla
Not defteri çalışma alanını varsayılan ayarlara sıfırlamak için komut çubuğundan **çalışma alanını Sıfırla** ' yı seçin. Bu, tüm özel yüklü paketleri kaldıracak ve jupi sunucusunu yeniden başlatacak. Not defterleriniz, dosyalarınız ve Cosmos kaynaklarınız etkilenmeyecektir.  

![Not defteri çalışma alanını Sıfırla](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Jupyıter Not defterlerinin Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) avantajları hakkında bilgi edinin
- [SQL API Için Python SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) hakkında bilgi edinin
