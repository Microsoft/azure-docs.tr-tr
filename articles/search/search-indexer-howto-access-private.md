---
title: Özel bir uç nokta aracılığıyla Dizin Oluşturucu bağlantıları
titleSuffix: Azure Cognitive Search
description: Özel bir uç nokta ile korunan diğer Azure kaynaklarından içeriğe erişmek için Dizin Oluşturucu bağlantılarını yapılandırın.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676510"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Özel bir uç nokta aracılığıyla Dizin Oluşturucu bağlantıları oluşturma

Azure depolama hesapları gibi birçok Azure kaynağı, bir sanal ağ listesinden gelen bağlantıları kabul edecek ve genel bir ağdan kaynaklanan bağlantıları reddedecek şekilde yapılandırılabilir. Azure Bilişsel Arama 'daki verileri indekslemek için bir Dizin Oluşturucu kullanıyorsanız ve veri kaynağınız özel bir ağ üzerinde ise, verilere ulaşmak için giden [Özel uç nokta bağlantısı](../private-link/private-endpoint-overview.md) oluşturabilirsiniz.

Bu Dizin Oluşturucu bağlantı yöntemi aşağıdaki iki gereksinime tabidir:

+ İçerik veya kod sağlayan Azure kaynağının daha önce [Azure özel bağlantı hizmetine](https://azure.microsoft.com/services/private-link/)kayıtlı olması gerekir.

+ Azure Bilişsel Arama hizmeti, temel katmanda veya daha yüksek olmalıdır. Özellik ücretsiz katmanda kullanılamaz. Ayrıca, dizin oluşturucuda bir beceri varsa, katmanın Standart 2 (S2) veya üzeri olması gerekir. Daha fazla bilgi için bkz. [hizmet limitleri](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Paylaşılan özel bağlantı kaynakları yönetim API 'Leri

Azure Bilişsel Arama API 'Leri aracılığıyla oluşturulan güvenli kaynakların özel uç noktaları, *paylaşılan özel bağlantı kaynakları* olarak adlandırılır. Bunun nedeni, [Azure özel bağlantı hizmeti](https://azure.microsoft.com/services/private-link/)ile tümleştirilmiş bir depolama hesabı gibi bir kaynağa "paylaşım" erişiminizin olması nedeniyle oluşur.

Azure Bilişsel Arama, yönetim REST API aracılığıyla Azure Bilişsel Arama Dizin oluşturucudan erişimi yapılandırmak için kullanabileceğiniz bir [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) işlemi sağlar.

Aşağıdaki tabloda *önizlemesi* belirlenmiş olan arama yönetimi API 'sinin (sürüm *2020-08-01-önizleme* veya üzeri) önizleme sürümünü kullanarak bazı kaynaklara özel uç nokta bağlantıları oluşturabilirsiniz. *Önizleme* ataması olmayan kaynaklar önizleme veya genel kullanıma açık API sürümü (*2020-08-01* veya üzeri) ile oluşturulabilir.

Aşağıdaki tabloda Azure Bilişsel Arama giden özel uç noktaları oluşturabileceğiniz Azure kaynakları listelenmektedir. Paylaşılan bir özel bağlantı kaynağı oluşturmak için, **Grup Kimliği** DEğERLERINI, API 'ye yazıldığı şekilde tam olarak girin. Değerler büyük/küçük harfe duyarlıdır.

| Azure kaynağı | Grup Kimliği |
| --- | --- |
| Azure depolama-blob (veya) ADLS Gen 2 | `blob`|
| Azure depolama-tablolar | `table`|
| Azure Cosmos DB-SQL API 'SI | `Sql`|
| Azure SQL Veritabanı | `sqlServer`|
| MySQL için Azure veritabanı (Önizleme) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Işlevleri (Önizleme) | `sites` |

Ayrıca, giden özel uç nokta bağlantılarının desteklendiği Azure kaynaklarını [desteklenen API 'ler listesini](/rest/api/searchmanagement/privatelinkresources/listsupported)kullanarak da sorgulayabilirsiniz.

Bu makalenin geri kalanında, REST API çağrılarını göstermek için [Azure CLI](/cli/azure/) (veya tercih ediyorsanız [Armclient](https://github.com/projectkudu/ARMClient) ) ve [Postman](https://www.postman.com/) (ya da tercih ediyorsanız [kıvrımlı](https://curl.se/) gibi diğer http istemcileri) bir karışımı kullanılır.

> [!NOTE]
> Bu makaledeki örneklerde aşağıdaki varsayımlar temel alınır:
> * Arama hizmetinin adı, _00000000-0000-0000-0000-000000000000_ abonelik kimlikli bir aboneliğin _contoso_ kaynak grubunda bulunan _contoso-arama_' dır. 
> * Bu arama hizmetinin kaynak KIMLIĞI _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Search/searchServices/contoso-Search_' dir.

Örneklerin geri kalanında _contoso-Search_ hizmetinin, dizin oluşturucularının _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Storage/storageAccounts/contoso-Storage_ güvenli depolama hesabından verilere erişebilmeleri için nasıl yapılandırılabileceğini gösterilmektedir.

## <a name="secure-your-storage-account"></a>Depolama hesabınızın güvenliğini sağlama

Depolama hesabını [yalnızca belirli alt ağlardan erişime izin verecek](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)şekilde yapılandırın. Azure portal, bu seçeneği belirleyip kümeyi boş bırakırsanız, sanal ağlardan hiçbir trafiğe izin verilmeyeceği anlamına gelir.

   ![Seçilen ağlara erişime izin verme seçeneğini gösteren "güvenlik duvarları ve sanal ağlar" bölmesinin ekran görüntüsü. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Bir depolama hesabındaki sanal ağı veya IP kısıtlamalarını atlamak için [güvenilir Microsoft hizmet yaklaşımını](../storage/common/storage-network-security.md#trusted-microsoft-services) kullanabilirsiniz. Ayrıca, arama hizmetinin depolama hesabındaki verilere erişmesini sağlayabilirsiniz. Bunu yapmak için bkz. [Güvenilen hizmet özel durumu Ile Azure depolama 'Ya Dizin Oluşturucu erişimi](search-indexer-howto-access-trusted-service-exception.md). 
>
> Ancak, bu yaklaşımı kullandığınızda Azure Bilişsel Arama ile depolama hesabınız arasındaki iletişim, güvenli Microsoft omurga ağı üzerinden depolama hesabının genel IP adresi aracılığıyla yapılır.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>1. Adım: depolama hesabına paylaşılan bir özel bağlantı kaynağı oluşturma

Depolama hesabına giden özel uç nokta bağlantısı oluşturmak üzere Azure Bilişsel Arama istemek için aşağıdaki API çağrısını yapın, örneğin [Azure CLI](/cli/azure/): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

[Armclient](https://github.com/projectkudu/ARMClient)kullanmayı tercih ediyorsanız:

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

İstek gövdesini API temsil eden dosyadaki *create-pe.js* içeriği aşağıdaki gibidir:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`Başarı durumunda bir yanıt döndürülür. Giden özel uç nokta oluşturma işlemi uzun süredir çalışan (zaman uyumsuz) bir işlemdir. Aşağıdaki kaynakların dağıtılmasını içerir:

* Bir durumda özel bir IP adresi ile ayrılmış özel bir uç nokta `"Pending"` . Özel IP adresi, arama hizmeti 'ne özgü özel Dizin Oluşturucu için yürütme ortamının sanal ağına ayrılan adres alanından alınır. Özel uç nokta onaylandıktan sonra Azure Bilişsel Arama 'dan depolama hesabına olan tüm iletişimler özel IP adresinden ve güvenli bir özel bağlantı kanalından kaynaklanır.

* Kaynak türüne göre özel bir DNS bölgesi `groupId` . Bu kaynağı dağıtarak, özel kaynağa yönelik herhangi bir DNS aramasının özel uç noktayla ilişkili IP adresini kullandığından emin olursunuz.

`groupId`Özel uç noktasını oluşturmakta olduğunuz kaynak türü için doğru öğesini belirttiğinizden emin olun. Herhangi bir uyuşmazlık, başarılı olmayan bir yanıt iletisi oluşmasına neden olur.

Tüm zaman uyumsuz Azure işlemlerinde, çağrı aşağıdakine `PUT` `Azure-AsyncOperation` benzer bir üst bilgi değeri döndürür:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

İşlemin durumunu elde etmek için bu URI 'yi düzenli aralıklarla yoklayabilirsiniz. Devam etmeden önce, paylaşılan özel bağlantı kaynağı işleminin durumu bir Terminal durumuna gelinceye kadar beklemeniz önerilir (yani işlemin durumu *başarılı* olur).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Veya ARMClient kullanarak:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Adım 2a: depolama hesabı için özel uç nokta bağlantısını onaylama

> [!NOTE]
> Bu bölümde, depolama için özel bir uç noktanın onay akışında gezinmek üzere Azure portal kullanırsınız. Alternatif olarak, depolama kaynak sağlayıcısı aracılığıyla kullanılabilen [REST API](/rest/api/storagerp/privateendpointconnections) kullanabilirsiniz.
>
> Azure Cosmos DB veya Azure SQL Server gibi diğer sağlayıcılar, Özel uç nokta bağlantılarını yönetmek için benzer depolama kaynak sağlayıcısı API 'Leri sunar.

1. Azure portal, depolama hesabınızın **Özel uç nokta bağlantıları** sekmesini seçin. Zaman uyumsuz işlem başarılı olduktan sonra, önceki API çağrısından gelen istek iletisiyle bir özel uç nokta bağlantısı isteği olmalıdır.

   !["Özel uç nokta bağlantıları" bölmesini gösteren Azure portal ekran görüntüsü.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Azure Bilişsel Arama 'nin oluşturduğu özel uç noktayı seçin. **Özel uç nokta** sütununda, önceki API 'de belirtilen ada göre özel uç nokta bağlantısını belirleyin, **Onayla**' yı seçin ve ardından uygun bir ileti girin. İleti içeriği önemli değildir. 

   Özel uç nokta bağlantısının aşağıdaki ekran görüntüsünde gösterildiği gibi göründüğünden emin olun. Bu durum, portalda güncelleştirilmesini sağlamak için bir veya iki dakika sürebilir.

   !["Özel uç nokta bağlantıları" bölmesinde "onaylanan" durumunu gösteren Azure portal ekran görüntüsü.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Özel uç nokta bağlantısı isteği onaylandıktan sonra, trafik özel uç  nokta üzerinden akar. Özel uç nokta onaylandıktan sonra Azure Bilişsel Arama, kendisi için oluşturulan DNS bölgesinde gerekli DNS bölgesi eşlemelerini oluşturur.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Adım 2b: paylaşılan özel bağlantı kaynağının durumunu sorgulama

Paylaşılan özel bağlantı kaynağının onay sonrasında güncelleştirildiğini onaylamak için, [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)'sini kullanarak durumunu elde edin.

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Veya ARMClient kullanarak:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

`properties.provisioningState`Kaynak `Succeeded` ve `properties.status` ise, `Approved` paylaşılan özel bağlantı kaynağının işlevsel olduğu ve dizin oluşturucunun özel uç nokta üzerinden iletişim kurmak üzere yapılandırılabileceği anlamına gelir.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>3. Adım: Dizin oluşturucuyu özel ortamda çalışacak şekilde yapılandırma

> [!NOTE]
> Özel uç nokta bağlantısı onaylanmadan önce bu adımı gerçekleştirebilirsiniz. Özel uç nokta bağlantısı onaylanana kadar, güvenli bir kaynakla (depolama hesabı gibi) iletişim kurmayı denediği herhangi bir dizin oluşturucu geçici bir hata durumunda sona acaktır. Yeni Dizin oluşturucular oluşturulamaz. Özel uç nokta bağlantısı onaylandıktan hemen sonra, Dizin oluşturucular özel depolama hesabına erişebilir.

1. Güvenli depolama hesabına ve depolama hesabı içinde uygun bir kapsayıcıya işaret eden [bir veri kaynağı oluşturun](/rest/api/searchservice/create-data-source) . Aşağıdaki ekran görüntüsünde bu istek Postman 'da gösterilmektedir.

   ![Postman Kullanıcı arabirimindeki bir veri kaynağının oluşturulmasını gösteren ekran görüntüsü.](media\search-indexer-howto-secure-access\create-ds.png )

1. Benzer şekilde, [bir dizin oluşturun](/rest/api/searchservice/create-index) ve isteğe bağlı olarak, REST API kullanarak [bir beceri oluşturun](/rest/api/searchservice/create-skillset) .

1. Önceki adımda oluşturduğunuz veri kaynağına, dizine ve beceri işaret eden [bir Dizin Oluşturucu oluşturun](/rest/api/searchservice/create-indexer) . Ayrıca, Dizin Oluşturucu yapılandırma özelliğini olarak ayarlayarak, Dizin oluşturucuyu özel yürütme ortamında çalışacak şekilde zorlayın `executionEnvironment` `private` .

   ![Postman Kullanıcı arabirimindeki bir dizin oluşturucunun oluşturulmasını gösteren ekran görüntüsü.](media\search-indexer-howto-secure-access\create-idr.png)

   Dizin Oluşturucu başarıyla oluşturulduktan sonra, Özel uç nokta bağlantısı üzerinden depolama hesabından içerik dizinlemeyi başlamalıdır. Dizin Oluşturucu [durum API](/rest/api/searchservice/get-indexer-status)'sini kullanarak dizin oluşturucunun durumunu izleyebilirsiniz.

> [!NOTE]
> Zaten mevcut dizin oluşturucularınıza sahipseniz, öğesini olarak ayarlayarak [PUT API 'si](/rest/api/searchservice/create-indexer) aracılığıyla güncelleştirebilirsiniz `executionEnvironment` `private` .

## <a name="troubleshooting"></a>Sorun giderme

- Dizin Oluşturucu oluşturma işlemi, "veri kaynağı kimlik bilgileri geçersiz" gibi bir hata iletisiyle başarısız olursa, Özel uç nokta bağlantısının durumu henüz *onaylanmamış* ya da bağlantı işlevsel değildir. Sorunu gidermek için: 
  * [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)'sini kullanarak paylaşılan özel bağlantı kaynağının durumunu alın. Durum *onaylanırsa*, kaynağın konumunu kontrol edin `properties.provisioningState` . Burada durum varsa `Incomplete` , bu, kaynak için temel bağımlılıkların bazılarının ayarlanmayacağı anlamına gelir. `PUT`Paylaşılan özel bağlantı kaynağını yeniden oluşturmak için isteği yeniden verme sorunu çözmelidir. Yeniden onay gerekli olabilir. Sorunun giderildiğini doğrulamak için kaynağın durumunu yeniden kontrol edin.

- Dizin oluşturucuyu özelliğini ayarlamadan oluşturursanız `executionEnvironment` , oluşturma işlemi başarılı olur ancak yürütme geçmişi, dizin oluşturucunun çalıştığını gösterir. Sorunu gidermek için:
   * Yürütme ortamını belirtmek için [Dizin oluşturucuyu güncelleştirin](/rest/api/searchservice/update-indexer) .

- Dizin oluşturucuyu özelliği ayarlamadan oluşturduysanız `executionEnvironment` ve başarıyla çalışırsa, Azure bilişsel arama 'nin yürütme ortamının arama hizmeti 'ne özgü *özel* ortam olduğunu karar verdiği anlamına gelir. Bu, Dizin Oluşturucu tarafından tüketilen kaynaklara, arama hizmetindeki yükün ve diğer faktörlere bağlı olarak değişebilir ve daha sonra başarısız olabilir. Sorunu gidermek için:
  * Daha `executionEnvironment` sonra başarısız olmamasını sağlamak için özelliğini olarak ayarlamanızı öneririz `private` .

[Kotalar ve sınırlar](search-limits-quotas-capacity.md) , kaç tane paylaşılan özel bağlantı kaynağı oluşturulup arama hizmetinin SKU 'suna bağlı olarak belirlenir.

## <a name="next-steps"></a>Sonraki adımlar

Özel uç noktalar hakkında daha fazla bilgi edinin:

- [Özel uç noktalar nelerdir?](../private-link/private-endpoint-overview.md)
- [Özel uç noktalar için gereken DNS yapılandırması](../private-link/private-endpoint-dns.md)