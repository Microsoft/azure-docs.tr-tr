---
title: Apache Kafka REST proxy-Azure HDInsight
description: Azure HDInsight üzerinde Kafka REST proxy kullanarak Apache Kafka işlemleri yapmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: bc3cbe5d0d7cf5e5a78112ae5df63ebb88a97f5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864848"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST proxy kullanarak Azure HDInsight 'ta Apache Kafka kümeleriyle etkileşim kurma

Kafka REST proxy, HTTPS üzerinden bir REST API aracılığıyla Kafka kümeniz ile etkileşim kurmanıza olanak sağlar. Bu eylem, Kafka istemcilerinizin sanal ağınızın dışında olabileceği anlamına gelir. İstemciler, Kafka kitaplıklarına güvenmek yerine Kafka kümesine basit, güvenli HTTPS çağrıları yapabilir. Bu makalede, REST proxy etkin Kafka kümesinin nasıl oluşturulacağı gösterilmektedir. Ayrıca REST proxy 'ye nasıl çağrı yapılacağını gösteren örnek bir kod sağlar.

## <a name="rest-api-reference"></a>REST API başvurusu

Kafka REST API tarafından desteklenen işlemler için bkz. [HDInsight Kafka Rest Proxy API başvurusu](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Arka Plan

:::image type="content" source="./media/rest-proxy/rest-proxy-architecture.png" alt-text="Kafka REST ara sunucu tasarımı" border="false":::

API tarafından desteklenen işlemlerin tam belirtimi için [Apache Kafka Rest Proxy API 'sine](/rest/api/hdinsight-kafka-rest-proxy)bakın.

### <a name="rest-proxy-endpoint"></a>REST proxy uç noktası

REST proxy ile HDInsight Kafka kümesi oluşturma, kümeniz için yeni bir genel uç nokta oluşturur ve bu, Azure portal HDInsight kümesi **özelliklerinde** bulabilirsiniz.

### <a name="security"></a>Güvenlik

Kafka REST proxy 'sine erişim Azure Active Directory güvenlik grupları ile yönetilir. Kafka kümesini oluştururken REST uç noktası erişimi olan Azure AD güvenlik grubunu belirtin. REST proxy 'sine erişmesi gereken Kafka istemcileri, Grup sahibi tarafından bu gruba kaydedilmelidir. Grup sahibi portal veya PowerShell aracılığıyla kaydedebilir.

REST proxy uç noktası istekleri için, istemci uygulamaların bir OAuth belirteci alması gerekir. Belirteç, güvenlik grubu üyeliğini doğrulamak için kullanılır. Aşağıdaki bir OAuth belirtecinin nasıl alınacağını gösteren bir [istemci uygulaması örneği](#client-application-sample) bulun. İstemci uygulaması, HTTPS isteğindeki OAuth belirtecini REST ara sunucusuna geçirir.

> [!NOTE]
> AAD güvenlik grupları hakkında daha fazla bilgi için bkz. [Azure Active Directory grupları kullanarak uygulama ve kaynak erişimini yönetme](../../active-directory/fundamentals/active-directory-manage-groups.md). OAuth belirteçlerinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [oauth 2,0 kod verme akışını kullanarak Azure Active Directory Web uygulamalarına erişimi yetkilendirme](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Ağ güvenlik grupları ile Kafka REST proxy
Kendi VNet 'nizi getirip ağ güvenlik grupları ile ağ trafiğini denetlemeniz durumunda, bağlantı noktası 443 ' e ek olarak **9400** numaralı bağlantı noktasında **gelen** trafiğe izin verin. Bu, Kafka REST proxy sunucusuna ulaşılacağından emin olur.

## <a name="prerequisites"></a>Önkoşullar

1. Bir uygulamayı Azure AD'ye kaydedin. Kafka REST proxy ile etkileşimde bulunmak için yazdığınız istemci uygulamaları, Azure 'da kimlik doğrulaması yapmak için bu uygulamanın KIMLIĞINI ve parolasını kullanacaktır.

1. Bir Azure AD güvenlik grubu oluşturun. Azure AD 'ye kaydettiğiniz uygulamayı, grubun bir **üyesi** olarak güvenlik grubuna ekleyin. Bu güvenlik grubu, REST proxy ile etkileşime girmesine izin verilen uygulamaları denetlemek için kullanılacaktır. Azure AD grupları oluşturma hakkında daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Grubun **güvenlik** tür olduğunu doğrulayın.
    :::image type="content" source="./media/rest-proxy/rest-proxy-group.png" alt-text="Güvenlik grubu" border="true":::

    Uygulamanın grubun üyesi olduğunu doğrulayın.
    :::image type="content" source="./media/rest-proxy/rest-proxy-membergroup.png" alt-text="Üyeliği denetle" border="true":::

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST proxy etkinken bir Kafka kümesi oluşturma

Aşağıdaki adımlar Azure portal kullanır. Azure CLı kullanan bir örnek için bkz. [Azure CLI kullanarak Apache Kafka Rest proxy kümesi oluşturma](tutorial-cli-rest-proxy.md).

1. Kafka kümesi oluşturma iş akışı sırasında, **güvenlik + ağ** sekmesinde, **Kafka Rest proxy 'yi etkinleştir** seçeneğini işaretleyin.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png" alt-text="Ekran görüntüsü, güvenlik + ağ seçiliyken H D Insight Cluster oluştur sayfasını gösterir." border="true":::

1. **Güvenlik grubu seç**' e tıklayın. Güvenlik grupları listesinden REST proxy 'sine erişimi olmasını istediğiniz güvenlik grubunu seçin. Uygun güvenlik grubunu bulmak için arama kutusunu kullanabilirsiniz. Alttaki **Seç** düğmesine tıklayın.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png" alt-text="Ekran görüntüsü, bir güvenlik grubu seçme seçeneğiyle H D Insight Cluster oluştur sayfasını gösterir." border="true":::

1. [Azure Portal kullanarak Azure HDInsight 'ta Apache Kafka kümesi oluşturma](./apache-kafka-get-started.md)bölümünde açıklandığı gibi kümenizi oluşturmak için kalan adımları izleyin.

1. Küme oluşturulduktan sonra, Kafka REST proxy URL 'sini kaydetmek için küme özelliklerine gidin.

     :::image type="content" source="./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png" alt-text="REST proxy URL 'sini görüntüle" border="true":::

## <a name="client-application-sample"></a>İstemci uygulaması örneği

Kafka kümenizdeki REST proxy ile etkileşim kurmak için aşağıdaki python kodunu kullanabilirsiniz. Kod örneğini kullanmak için şu adımları izleyin:

1. Örnek kodu Python yüklü bir makineye kaydedin.
1. Yürüterek gerekli Python bağımlılıklarını yükler `pip3 install msal` .
1. Kod bölümünü değiştirin **Bu özellikleri yapılandırın** ve ortamınız için aşağıdaki özellikleri güncelleştirin:

    |Özellik |Açıklama |
    |---|---|
    |Kiracı Kimliği|Aboneliğinizin bulunduğu Azure kiracısı.|
    |İstemci Kimliği|Güvenlik grubuna kaydettiğiniz uygulamanın KIMLIĞI.|
    |İstemci Gizli Anahtarı|Güvenlik grubuna kaydettiğiniz uygulamanın gizli anahtarı.|
    |Kafkarest_endpoint|[Dağıtım bölümünde](#create-a-kafka-cluster-with-rest-proxy-enabled)açıklandığı gibi kümeye genel bakış ' daki **Özellikler** sekmesinden bu değeri alın. Aşağıdaki biçimde olmalıdır: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Komut satırından, öğesini yürüterek Python dosyasını yürütün `sudo python3 <filename.py>`

Bu kod aşağıdaki eylemi yapar:

1. Azure AD 'den bir OAuth belirteci getirir.
1. Kafka REST proxy 'sine nasıl istek yapılacağını gösterir.

Python 'da OAuth belirteçleri alma hakkında daha fazla bilgi için bkz. [Python AuthenticationContext sınıfı](/python/api/adal/adal.authentication_context.authenticationcontext). `topics`Kafka Rest proxy 'si aracılığıyla oluşturulmamış veya silinmediği sürece bir gecikme görebilirsiniz. Bu gecikme, önbellek yenileme nedeniyle oluşur. Üretici API 'sinin **değer** alanı geliştirilmiştir. Artık JSON nesnelerini ve serileştirilmiş formları kabul eder.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Bir kıvrımlı komutu kullanarak REST proxy için Azure 'dan bir belirteç alma hakkında başka bir örnek aşağıda bulabilirsiniz. **Belirteç alınırken, belirtilen için gerekli olduğunu unutmayın `scope=https://hib.azurehdinsight.net/.default` .**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Sonraki adımlar

* [Kafka REST Proxy API başvuru belgeleri](/rest/api/hdinsight-kafka-rest-proxy/)
