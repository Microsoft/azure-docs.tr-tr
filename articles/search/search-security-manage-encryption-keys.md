---
title: Müşteri tarafından yönetilen anahtarları kullanarak bekleyen şifreleme
titleSuffix: Azure Cognitive Search
description: Azure Key Vault içinde oluşturduğunuz ve yönettiğiniz anahtarları kullanarak Azure Bilişsel Arama Dizin ve eş anlamlı haritalar üzerinde sunucu tarafı şifrelemeyi tamamlar.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813623"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Bilişsel Arama veri şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma

Azure Bilişsel Arama, [hizmet tarafından yönetilen anahtarlarla](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)bekleyen dizinli içeriği otomatik olarak şifreler. Daha fazla koruma gerekiyorsa, Azure Key Vault ' de oluşturduğunuz ve yönettiğiniz anahtarları kullanarak, varsayılan şifrelemeyi ek bir şifreleme katmanıyla birlikte tamamlayabilirsiniz. Bu makale, müşteri tarafından yönetilen anahtar şifrelemesini ayarlama adımlarında size yol gösterir.

Müşteri tarafından yönetilen anahtar şifrelemesi [Azure Key Vault](../key-vault/general/overview.md)bağımlıdır. Kendi şifreleme anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya şifreleme anahtarları oluşturmak için Azure Key Vault API 'Lerini kullanabilirsiniz. Azure Key Vault ile [günlüğe kaydetmeyi etkinleştirirseniz](../key-vault/general/logging.md)anahtar kullanımını da denetleyebilirsiniz.  

Müşteri tarafından yönetilen anahtarlarla şifreleme, bu nesneler oluşturulduğunda tek tek dizinlere veya eş anlamlı haritalara uygulanır ve arama hizmeti düzeyinin kendisi üzerinde belirtilmez. Yalnızca yeni nesneler şifrelenebilir. Zaten var olan içeriği şifrelenemez.

Anahtarların tümünün aynı anahtar kasasında olması gerekmez. Tek bir arama hizmeti, her biri farklı anahtar kasalarında depolanan, her biri kendi müşteri tarafından yönetilen şifreleme anahtarlarıyla şifrelenen birden çok şifrelenmiş dizini veya eş anlamlı haritaları barındırabilirler. Ayrıca, aynı hizmette, müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenmemiş dizinler ve eş anlamlı haritalar da olabilir.

>[!Important]
> Müşteri tarafından yönetilen anahtarlar uygularsanız, Anahtar Kasası anahtarlarının rutin olarak dönmesi ve uygulama gizli dizileri ve kaydı Active Directory için katı yordamları izlediğinizden emin olun. Tüm şifrelenmiş içerikleri, eski olanları silmeden önce yeni gizli dizileri ve anahtarları kullanacak şekilde güncelleştirin. Bu adımı kaçırırsanız, içeriğinizin şifresi çözülemez.

## <a name="double-encryption"></a>Çift şifreleme

1 Ağustos 2020 ve belirli bölgelerde oluşturulan hizmetler için, müşteri tarafından yönetilen anahtar şifrelemenin kapsamı, şu anda şu bölgelerde bulunan [tam çift şifrelemeyi](search-security-overview.md#double-encryption)elde eden geçici diskler içerir: 

+ Batı ABD 2
+ Doğu ABD
+ Orta Güney ABD
+ US Gov Virginia
+ US Gov Arizona

Farklı bir bölge veya 1 Ağustos 'Tan önce oluşturulmuş bir hizmet kullanıyorsanız, yönetilen anahtar şifrelemesi, hizmet tarafından kullanılan geçici diskler hariç yalnızca veri diskiyle sınırlıdır.

## <a name="prerequisites"></a>Önkoşullar

Bu senaryoda aşağıdaki araçlar ve hizmetler kullanılır.

+ [Azure bilişsel arama](search-create-service-portal.md) , [faturalandırılabilir katmanda](search-sku-tier.md#tier-descriptions) (herhangi bir bölgedeki temel veya üstü).
+ [Azure Key Vault](../key-vault/general/overview.md), [Azure Portal](../key-vault//general/quick-create-portal.md), [Azure CLI](../key-vault//general/quick-create-cli.md)veya [Azure PowerShell](../key-vault//general/quick-create-powershell.md)kullanarak Anahtar Kasası oluşturabilirsiniz. Azure Bilişsel Arama ile aynı abonelikte. Anahtar kasasında **geçici silme** ve **Temizleme koruması** etkinleştirilmiş olmalıdır.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Bir tane yoksa, [Yeni bir kiracı kurun](../active-directory/develop/quickstart-create-new-tenant.md).

Şifrelenmiş nesneyi oluşturabileceğiniz bir arama uygulamasına sahip olmanız gerekir. Bu koda, bir Anahtar Kasası anahtarına ve Active Directory kayıt bilgilerine başvurabileceksiniz. Bu kod, çalışan bir uygulama veya [C# kod örneği DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)gibi prototip kodu olabilir.

> [!TIP]
> Şifreleme anahtarı parametresi içeren dizin ve eş anlamlı eşlemeler oluşturan REST API 'Leri çağırmak için [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)veya [Azure PowerShell](./search-get-started-powershell.md)kullanabilirsiniz. Şu anda dizinlere veya eş anlamlı haritalara anahtar eklemek için bir portal desteği yoktur.

## <a name="1---enable-key-recovery"></a>1-anahtar kurtarmayı etkinleştir

Müşteri tarafından yönetilen anahtarlarla şifrelemenin doğası nedeniyle, Azure Anahtar Kasası anahtarınız silinirse hiç kimse verilerinizi alamaz. Yanlışlıkla Key Vault anahtar silmeleri nedeniyle oluşan veri kaybını engellemek için, anahtar kasasında geçici silme ve Temizleme korumasının etkinleştirilmesi gerekir. Geçici silme varsayılan olarak etkindir, bu nedenle yalnızca kasıtlı olarak devre dışı bırakılmışsa sorunlarla karşılaşırsınız. Temizleme koruması varsayılan olarak etkin değildir, ancak Bilişsel Arama ' de müşteri tarafından yönetilen anahtar şifrelemesi için gereklidir. Daha fazla bilgi için bkz. [geçici-silme](../key-vault/general/soft-delete-overview.md) ve [Temizleme koruması](../key-vault/general/soft-delete-overview.md#purge-protection) genel bakış.

Portal, PowerShell veya Azure CLı komutlarını kullanarak her iki özelliği de ayarlayabilirsiniz.

### <a name="using-azure-portal"></a>Azure portalını kullanma

1. [Azure Portal oturum açın](https://portal.azure.com) ve Anahtar Kasası genel bakış sayfasını açın.

1. **Genel bakış** sayfasında, **temel parçalar** altında, **geçici silme** ve **Temizleme korumasını** etkinleştirin.

### <a name="using-powershell"></a>PowerShell’i kullanma

1. `Connect-AzAccount`Azure kimlik bilgilerinizi kurmak için ' i çalıştırın.

1. Anahtar kasanıza bağlanmak ve geçerli bir adla değiştirmek için aşağıdaki komutu çalıştırın `<vault_name>` :

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault, geçici silme etkin ile oluşturulur. Kasanızda devre dışıysa, aşağıdaki komutu çalıştırın:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Temizleme korumasını etkinleştir:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Güncelleştirmelerinizi kaydedin:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

+ [Azure CLI yüklemeniz](/cli/azure/install-azure-cli)varsa, gerekli özellikleri etkinleştirmek için aşağıdaki komutu çalıştırabilirsiniz.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-Key Vault bir anahtar oluşturma

Azure Key Vault ' de bir anahtarınız zaten varsa bu adımı atlayın.

1. [Azure Portal oturum açın](https://portal.azure.com) ve Anahtar Kasası genel bakış sayfasını açın.

1. Sol taraftaki **tuşları** seçin ve **+ Oluştur/içeri aktar**' ı seçin.

1. **Anahtar oluştur** bölmesinde, **Seçenekler** listesinden, anahtar oluşturmak için kullanmak istediğiniz yöntemi seçin. Yeni bir anahtar **oluşturabilir** , var olan bir anahtarı **yükleyebilir** veya bir anahtarın yedeğini seçmek için **yedekleme geri yükleme** ' yi kullanabilirsiniz.

1. Anahtarınız için bir **ad** girin ve isteğe bağlı olarak diğer anahtar özelliklerini seçin.

1. Dağıtımı başlatmak için **Oluştur** ' u seçin.

1. Anahtar tanımlayıcısını bir yere, anahtar **değer URI 'sinden**, **anahtar adından** ve **anahtar sürümünden** oluşur. Azure Bilişsel Arama 'de şifrelenmiş bir dizin tanımlamak için tanımlayıcıya ihtiyacınız olacaktır.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Yeni bir anahtar kasası anahtarı oluştur":::

## <a name="3---register-an-app-in-active-directory"></a>3-Active Directory bir uygulamayı kaydetme

1. [Azure Portal](https://portal.azure.com), aboneliğiniz için Azure Active Directory kaynağını bulun.

1. Sol tarafta, **Yönet** altında, **uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.

1. Kayda, belki de arama uygulaması adına benzer bir ad verin. **Kaydet**’i seçin.

1. Uygulama kaydı oluşturulduktan sonra uygulama KIMLIĞINI kopyalayın. Bu dizeyi uygulamanıza sağlamanız gerekir. 

   [Dotnethowtoencryptionusingcmk](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)aracılığıyla adımladıysanız, bu değeri dosyadaki **appsettings.js** yapıştırın.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Temel bileşenler bölümündeki uygulama KIMLIĞI":::

1. Ardından, sol taraftaki **sertifikalar & parolaları** ' nı seçin.

1. **Yeni istemci gizli dizisi**’ni seçin. Gizli dizi için bir görünen ad verin ve **Ekle**' yi seçin.

1. Uygulama gizli dizesini kopyalayın. Örnek içinde adımlarken, bu değeri dosyadaki **appsettings.js** yapıştırın.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Uygulama gizli dizisi":::

## <a name="4---grant-key-access-permissions"></a>4-anahtar erişim izinleri verme

Bu adımda Key Vault bir erişim ilkesi oluşturacaksınız. Bu ilke, müşteri tarafından yönetilen anahtarınızı kullanmak için Active Directory izinle kaydettiğiniz uygulamayı verir.

Erişim izinleri belirli bir zamanda iptal edilebilir. Bu Anahtar Kasası 'nı kullanan herhangi bir arama hizmeti dizini veya eş anlamlı eşleme, iptal edildikten sonra kullanılamaz hale gelir. Anahtar Kasası erişim izinlerinin daha sonraki bir zamanda geri yüklenmesi, Dizin \ eş anlamlı eşleme erişimini geri yükler. Daha fazla bilgi için bkz. [bir anahtar kasasına güvenli erişim](../key-vault/general/security-features.md).

1. Hala Azure portal, Anahtar Kasası **genel bakış** sayfasını açın. 

1. Sol taraftaki **erişim ilkelerini** seçin ve **+ erişim ilkesi Ekle**' yi seçin.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Yeni Anahtar Kasası erişim ilkesi Ekle":::

1. **Sorumlu Seç** ' i seçin ve Active Directory kaydettiğiniz uygulamayı seçin. Adına göre arama yapabilirsiniz.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Anahtar Kasası erişim ilkesi sorumlusunu seçin":::

1. **Anahtar izinler**' de *Al*, *anahtar kaydırmayı kaldır* ve *anahtarı sarmalama*' i seçin.

1. **Gizli izinler**' de *Al*' ı seçin.

1. **Sertifika izinleri**' nde *Al*' ı seçin.

1. **Ekle** ' yi ve ardından **Kaydet**' i seçin.

> [!Important]
> Azure Bilişsel Arama şifrelenmiş içerik, belirli bir **sürüme** sahip belirli bir Azure Key Vault anahtarı kullanacak şekilde yapılandırılmıştır. Anahtarı veya sürümü değiştirirseniz, önceki key\version. silinmeden **önce** dizin veya eş anlamlı haritanın yeni key\version kullanacak şekilde güncellenmesi gerekir Bunun başarısız olması, anahtar erişimi kaybolduktan sonra içeriğin şifresini çözemeyecek şekilde dizin veya eş anlamlı haritanın kullanılamamasına neden olur.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-içerik şifreleyin

Bir dizin, veri kaynağı, Beceri, Dizin Oluşturucu veya eş anlamlı eşleme üzerinde müşteri tarafından yönetilen bir anahtar eklemek için [arama REST API](/rest/api/searchservice/) veya SDK 'sını kullanmanız gerekir. Portal, eş anlamlı haritalar veya şifreleme özellikleri sunmaz. Geçerli bir API dizinleri kullandığınızda, veri kaynakları, becerileri, Dizin oluşturucular ve eş anlamlı eşlemeler en üst düzey bir **encryptionKey** özelliğini destekler.

Bu örnek, Azure Key Vault ve Azure Active Directory değerleri ile REST API kullanır:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Bu Anahtar Kasası ayrıntılarının hiçbiri gizli kabul edilmez ve Azure portal ' deki ilgili Azure Key Vault anahtarı sayfasına göz atarak kolayca alınabilir.

## <a name="example-index-encryption"></a>Örnek: Dizin şifreleme

[Create INDEX Azure Bilişsel Arama REST API](/rest/api/searchservice/create-index)kullanarak şifrelenmiş bir dizin oluşturun. `encryptionKey`Kullanılacak şifreleme anahtarını belirtmek için özelliğini kullanın.
> [!Note]
> Bu Anahtar Kasası ayrıntılarının hiçbiri gizli kabul edilmez ve Azure portal ' deki ilgili Azure Key Vault anahtarı sayfasına göz atarak kolayca alınabilir.

## <a name="rest-examples"></a>REST örnekleri

Bu bölümde, şifrelenmiş bir dizin ve eş anlamlı eşleme için tam JSON gösterilmektedir

### <a name="index-encryption"></a>Dizin şifreleme

Burada tek fark, Dizin tanımının bir parçası olarak şifreleme anahtarı ayrıntılarını belirten [Dizin oluşturma (REST API)](/rest/api/searchservice/create-index)yolunda yeni bir REST API dizin oluşturma ayrıntılarından bulunabilir:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Artık dizin oluşturma isteğini gönderebilir ve sonra dizini normal olarak kullanmaya başlayabilirsiniz.

### <a name="synonym-map-encryption"></a>Eş anlamlı eşleme şifrelemesi

[Eş anlamlı eşleme oluşturma Azure Bilişsel Arama REST API](/rest/api/searchservice/create-synonym-map)kullanarak şifrelenmiş bir eş anlamlı eşleme oluşturun. `encryptionKey`Kullanılacak şifreleme anahtarını belirtmek için özelliğini kullanın.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Artık eş anlamlı harita oluşturma isteğini gönderebilir ve normal olarak kullanmaya başlayabilirsiniz.

## <a name="example-data-source-encryption"></a>Örnek: veri kaynağı şifrelemesi

[Veri kaynağını oluştur (Azure Bilişsel Arama REST API)](/rest/api/searchservice/create-data-source)kullanarak şifrelenmiş bir veri kaynağı oluşturun. `encryptionKey`Kullanılacak şifreleme anahtarını belirtmek için özelliğini kullanın.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Artık veri kaynağı oluşturma isteğini gönderebilir ve normal olarak kullanmaya başlayabilirsiniz.

## <a name="example-skillset-encryption"></a>Örnek: beceri şifreleme

[Create beceri Azure Bilişsel Arama REST API](/rest/api/searchservice/create-skillset)kullanarak şifrelenmiş bir beceri oluşturun. `encryptionKey`Kullanılacak şifreleme anahtarını belirtmek için özelliğini kullanın.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Artık beceri oluşturma isteğini gönderebilir ve normal olarak kullanmaya başlayabilirsiniz.

## <a name="example-indexer-encryption"></a>Örnek: Dizin Oluşturucu şifreleme

[Create Indexer Create Azure Bilişsel Arama REST API](/rest/api/searchservice/create-indexer)kullanarak şifrelenmiş bir Dizin Oluşturucu oluşturun. `encryptionKey`Kullanılacak şifreleme anahtarını belirtmek için özelliğini kullanın.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Artık Dizin Oluşturucu oluşturma isteğini gönderebilir ve normal olarak kullanmaya başlayabilirsiniz.

>[!Important]
> `encryptionKey`Mevcut arama dizinlerine veya eş anlamlı haritalara eklenemediğinden, üç Anahtar Kasası ayrıntılarının (örneğin, anahtar sürümünü güncelleştirme) her biri için farklı değerler sağlanarak güncelleştirilebilirler. Yeni bir Key Vault anahtarına veya yeni bir anahtar sürümüne geçiş yaparken, önce anahtarı kullanan herhangi bir arama dizini veya eş anlamlı eşleme, önceki key\version. silinmeden **önce** yeni key\version kullanacak şekilde güncellenmelidir Bunun başarısız olması, anahtar erişimi kaybolduktan sonra içeriğin şifresini çözemeyeceği için dizin veya eş anlamlı haritanın kullanılamaz hale gelmesine neden olur. Anahtar Kasası erişim izinlerinin daha sonraki bir zamanda geri yüklenmesi, içerik erişimini geri yükler.

## <a name="simpler-alternative-trusted-service"></a>Daha basit alternatif: Güvenilen hizmet

Kiracı yapılandırmasına ve kimlik doğrulama gereksinimlerine bağlı olarak, bir Anahtar Kasası anahtarına erişmenin daha basit bir yaklaşımını uygulayabilirsiniz. Bir Active Directory uygulaması oluşturmak ve kullanmak yerine, sistem tarafından yönetilen bir kimliği etkinleştirerek bir arama hizmetini güvenilir bir hizmet yapabilirsiniz. Ardından, Anahtar Kasası anahtarına erişmek için, güvenilir arama hizmetini, AD ile kaydedilmiş bir uygulama yerine bir güvenlik ilkesi olarak kullanacaksınız.

Bu yaklaşım, uygulama kaydı ve uygulama gizli dizileri için adımları atlamanızı sağlar ve yalnızca Anahtar Kasası bileşenleri (URI, kasa adı, anahtar sürümü) için bir şifreleme anahtarı tanımını basitleştirir.

Genel olarak, yönetilen bir kimlik kimlik bilgilerini kodda (ApplicationSecret veya ApplicationSecret) depolamadan Azure Key Vault kimlik doğrulaması yapmasına olanak sağlar. Bu tür yönetilen kimliğin yaşam döngüsü, yalnızca tek bir yönetilen kimliğe sahip olabilen arama hizmetinizin yaşam döngüsüne bağlıdır. Yönetilen kimliklerin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

1. Arama hizmetinizi güvenilir bir hizmet yapın.
   ![Sistem tarafından atanan yönetilen kimliği aç](./media/search-managed-identities/turn-on-system-assigned-identity.png "Sistem tarafından atanan yönetilen kimliği aç")

1. Azure Key Vault bir erişim ilkesi ayarlarken, ilke olarak güvenilir arama hizmetini seçin (AD-kayıtlı uygulama yerine). Erişim anahtarı izinleri verme adımında belirtildiği gibi aynı izinleri (birden fazla alma, SARMALAMA, GERI saral) atayın.

1. Active Directory özelliklerini atladığında, Basitleştirilmiş bir oluşturma kullanın `encryptionKey` .

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Bu basitleştirilmiş yaklaşımı benimsemeye engel olacak koşullar şunlardır:

+ Anahtar kasasına doğrudan arama hizmeti erişim izinleri verilemez (örneğin, arama hizmeti, Azure Key Vault farklı bir Active Directory kiracısında ise).

+ Tek bir arama hizmeti, her anahtar kasasının kimlik doğrulaması için **farklı bir kimlik** kullanması gereken farklı bir anahtar kasasından farklı bir anahtar kullanan birden çok şifrelenmiş ındexes\eş anlamlı haritaları barındırmak için gereklidir. Bir arama hizmeti yalnızca tek bir yönetilen kimliğe sahip olabileceğinden, birden çok kimlik için bir gereksinim, senaryonuza yönelik Basitleştirilmiş yaklaşımı ayırt edebilir.  

## <a name="work-with-encrypted-content"></a>Şifrelenmiş içerikle çalışma

Müşteri tarafından yönetilen anahtar şifrelemesi sayesinde, ek şifreleme/şifre çözme işi nedeniyle hem dizin oluşturma hem de sorgular için gecikme fark edeceksiniz. Azure Bilişsel Arama şifreleme etkinliğini günlüğe almaz, ancak Anahtar Kasası günlüğü aracılığıyla anahtar erişimini izleyebilirsiniz. Anahtar Kasası yapılandırmasının bir parçası olarak [günlük kaydını etkinleştirmenizi](../key-vault/general/logging.md) öneririz.

Anahtar dönüşünün zaman içinde oluşması beklenmektedir. Anahtarları her döndürdüğünüzde, bu sırayı izlemeniz önemlidir:

1. [Bir dizin veya eş anlamlı eşleme tarafından kullanılan anahtarı belirleme](search-security-get-encryption-keys.md).
1. [Anahtar Kasası 'nda yeni bir anahtar oluşturun](../key-vault/keys/quick-create-portal.md), ancak özgün anahtarı kullanılabilir olarak bırakın.
1. Yeni değerleri kullanmak için bir dizin veya eş anlamlı haritadaki [encryptionKey özelliklerini güncelleştirin](/rest/api/searchservice/update-index) . Yalnızca bu özellikle oluşturulan nesneler, farklı bir değer kullanacak şekilde güncelleştirilemeyebilir.
1. Anahtar kasasındaki önceki anahtarı devre dışı bırakın veya silin. Yeni anahtarın kullanıldığını doğrulamak için anahtar erişimini izleyin.

Performans nedenleriyle, arama hizmeti anahtarı birkaç saate kadar önbelleğe alır. Anahtarı yeni bir sağlamadan devre dışı bırakır veya silerseniz, sorgular önbelleğin süresi dolana kadar geçici olarak çalışmaya devam edecektir. Ancak, arama hizmeti içeriğin şifresini çözemeyecek şekilde şu iletiyi alırsınız: "erişim yasak. Kullanılan sorgu anahtarı iptal edilmiş olabilir, lütfen yeniden deneyin. " 

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik mimarisi hakkında bilginiz varsa, [Azure Güvenlik belgelerini](../security/index.yml)gözden geçirin ve özellikle bu makalede:

> [!div class="nextstepaction"]
> [Bekleyen veri şifreleme](../security/fundamentals/encryption-atrest.md)