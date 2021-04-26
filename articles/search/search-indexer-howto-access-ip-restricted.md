---
title: Dizin Oluşturucu IP aralıklarına erişime izin ver
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama Dizin Oluşturucu tarafından veri erişimine izin vermek için IP güvenlik duvarı kurallarını yapılandırın.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98757252"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Dizin Oluşturucu bağlantılarına izin vermek için IP güvenlik duvarı kurallarını yapılandırma (Azure Bilişsel Arama)

Azure kaynaklarında, depolama hesapları, Cosmos DB hesapları ve Azure SQL Server 'Lar gibi IP güvenlik duvarı kuralları, yalnızca belirli IP aralıklarından gelen trafiğe verilere erişim izni verir.

Bu makalede, Azure Bilişsel Arama dizin oluşturucularının verilere güvenli bir şekilde erişebilmesi için bir depolama hesabı için Azure portal aracılığıyla IP kurallarının nasıl yapılandırılacağı açıklanır. Azure depolama 'ya özgü olsa da yaklaşım, verilere erişimi güvenli hale getirmek için IP güvenlik duvarı kuralları kullanan diğer Azure kaynakları için de geçerlidir.

> [!NOTE]
> Depolama hesabı için IP güvenlik duvarı kuralları yalnızca depolama hesabı ve arama hizmeti farklı bölgelerde olduğunda geçerlidir. Kurulumlarınız buna izin veriyorsa, [güvenilir hizmet özel durum seçeneğini](search-indexer-howto-access-trusted-service-exception.md)kullanmanızı öneririz.

## <a name="get-the-ip-address-of-the-search-service"></a>Arama hizmetinin IP adresini al

Arama hizmetinizin tam etki alanı adını (FQDN) alın. Bu, şöyle görünecektir `<search-service-name>.search.windows.net` . Azure portal arama hizmetinize arayarak FQDN 'yi bulabilirsiniz.

   ![Hizmet FQDN 'SI al](media\search-indexer-howto-secure-access\search-service-portal.png "Hizmet FQDN 'SI al")

Arama hizmetinin IP adresi, `nslookup` FQDN 'nin bir (veya a) gerçekleştirerek elde edilebilir `ping` . Aşağıdaki örnekte, Azure Storage güvenlik duvarında gelen bir kurala "150.0.0.1" ekleyeceksiniz. Azure depolama hesabına erişebilmek için, arama hizmeti dizin oluşturucunun güvenlik duvarı ayarları güncelleştirildikten sonra 15 dakika kadar sürebilir.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>"Azurecognivesearch" hizmet etiketi için IP adresi aralıklarını al

Ek IP adresleri, dizin oluşturucunun [çok kiracılı yürütme ortamından](search-indexer-securing-resources.md#indexer-execution-environment)kaynaklanan istekler için kullanılır. Bu IP adresi aralığını hizmet etiketinden alabilirsiniz.

Hizmet etiketi için IP adresi aralıkları `AzureCognitiveSearch` [bulma API 'si (Önizleme)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) veya [indirilebilir JSON dosyası](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)aracılığıyla elde edilebilir.

Bu kılavuzda, arama hizmetinin Azure genel bulutu olduğu varsayıldığında, [Azure genel JSON dosyası](https://www.microsoft.com/download/details.aspx?id=56519) indirilmelidir.

   ![JSON dosyasını indir](media\search-indexer-howto-secure-access\service-tag.png "JSON dosyasını indir")

Arama hizmetinin Orta Batı ABD olduğu kabul edildiğinde JSON dosyasından, çok kiracılı Dizin Oluşturucu yürütme ortamı için IP adreslerinin listesi aşağıda listelenmiştir.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

/32 IP adresleri için, "/32" (52.253.133.74/32-> 52.253.133.74) öğesini bırakın, diğerleri de tam olarak kullanılabilir.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>IP adresi aralıklarını IP güvenlik duvarı kurallarına ekleme

Bir depolama hesabının güvenlik duvarı kuralına IP adres aralıklarını eklemenin en kolay yolu Azure portal aracılığıyla yapılır. Portalda depolama hesabını bulun ve **güvenlik duvarları ve sanal ağlar** sekmesine gidin.

   ![Güvenlik Duvarı ve sanal ağlar](media\search-indexer-howto-secure-access\storage-firewall.png "Güvenlik Duvarı ve sanal ağlar")

Daha önce alınan üç IP adresini (1. arama hizmeti IP 'si için 1, `AzureCognitiveSearch` hizmet etiketi için 2) adres aralığında ekleyin ve **Kaydet**' i seçin.

   ![Güvenlik Duvarı IP kuralları](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Güvenlik Duvarı IP kuralları")

Güvenlik duvarı kuralları, güncelleştirildikten sonra 5-10 dakika sürer ve bu, dizin oluşturucularının depolama hesabındaki verilere erişebilmesi gerekir.

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure Storage güvenlik duvarlarını yapılandırma](../storage/common/storage-network-security.md)
- [Cosmos DB için IP güvenlik duvarını yapılandırma](../cosmos-db/how-to-configure-firewall.md)
- [Azure SQL Server için IP güvenlik duvarını yapılandırma](../azure-sql/database/firewall-configure.md)