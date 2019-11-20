---
title: Müşteri tarafından yönetilen anahtarları kullanarak bekleyen şifreleme (Önizleme)
titleSuffix: Azure Cognitive Search
description: Azure Key Vault içinde oluşturduğunuz ve yönettiğiniz anahtarlar aracılığıyla Azure Bilişsel Arama Dizin ve eş anlamlı haritalar üzerinde sunucu tarafı şifrelemeyi tamamlar. Bu özellik şu anda genel önizleme aşamasındadır.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 4f78b4b7b38c6e67aa8aebf04e3a8ef0fdbd000f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112929"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault içindeki müşteri tarafından yönetilen anahtarları kullanarak Azure Bilişsel Arama içeriğin geri kalanında şifreleme

> [!IMPORTANT] 
> Rest 'ten şifreleme desteği şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) ve [.net SDK sürüm 8,0-Önizleme](search-dotnet-sdk-migration-version-9.md) bu özelliği sağlar. Şu anda portal desteği yok.

Azure Bilişsel Arama, varsayılan olarak, [hizmet tarafından yönetilen anahtarlarla](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)bekleyen Kullanıcı içeriğini şifreler. Azure Key Vault içinde oluşturduğunuz ve yönettiğiniz anahtarları kullanarak, varsayılan şifrelemeyi ek bir şifreleme katmanıyla birlikte destekleyebilirsiniz. Bu makalede adımlarda adım adım açıklanmaktadır.

Sunucu tarafı şifreleme, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)tümleştirme aracılığıyla desteklenir. Kendi şifreleme anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya şifreleme anahtarları oluşturmak için Azure Key Vault API 'Lerini kullanabilirsiniz. Azure Key Vault, anahtar kullanımını da denetleyebilirsiniz. 

Müşteri tarafından yönetilen anahtarlarla şifreleme, bu nesneler oluşturulduğunda, arama hizmeti düzeyinde değil, dizin veya eş anlamlı eşleme düzeyinde yapılandırılır. Zaten var olan içeriği şifrelenemez. 

Farklı anahtar kasalarından farklı anahtarlar kullanabilirsiniz. Bu, tek bir arama hizmetinin birden çok şifrelenmiş ındexes\eş anlamlı haritalarını barındırabileceği anlamına gelir. Bu, her şifrelenmiş, müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenmeyen ındexes\eş anlamlı eşlemlerle birlikte, her biri bir müşteri tarafından yönetilen farklı 

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki hizmetler bu örnekte kullanılır. 

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu öğretici için ücretsiz bir hizmet kullanabilirsiniz.

+ Aboneliğiniz altında [bir Azure Key Vault kaynağı oluşturun](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) veya mevcut bir kasayı bulun.

+ Yapılandırma görevleri için [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) veya [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) kullanılır.

+ Önizleme REST API çağırmak için [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) ve [Azure bilişsel arama SDK](https://aka.ms/search-sdk-preview) kullanılabilir. Şu anda müşteri tarafından yönetilen şifreleme için portal veya .NET SDK desteği yoktur.

## <a name="1---enable-key-recovery"></a>1-anahtar kurtarmayı etkinleştir

Bu adım isteğe bağlıdır, ancak önemle önerilir. Azure Key Vault kaynağını oluşturduktan sonra, aşağıdaki PowerShell veya Azure CLı komutlarını yürüterek seçili anahtar kasasında **geçici silme** ve **Temizleme korumasını** etkinleştirin:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> Müşteri tarafından yönetilen anahtarlarla şifrelemenin çok doğası nedeniyle, Azure Anahtar Kasası anahtarınız silinirse Azure Bilişsel Arama verilerinizi alamaz. Yanlışlıkla Key Vault anahtar silmeleri nedeniyle oluşan veri kaybını engellemek için, seçili anahtar kasasında geçici silme ve Temizleme korumasını etkinleştirmeniz önemle önerilir. Daha fazla bilgi için bkz. [Azure Key Vault geçici silme](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2-yeni bir anahtar oluşturma

Azure Bilişsel Arama içeriğini şifrelemek için mevcut bir anahtar kullanıyorsanız, bu adımı atlayın.

1. [Azure Portal oturum açın](https://portal.azure.com) ve Anahtar Kasası panosuna gidin.

1. Sol gezinti bölmesinden **anahtarlar** ayarını seçin ve **+ Oluştur/içeri aktar**' a tıklayın.

1. **Anahtar oluştur** bölmesinde, **Seçenekler**listesinden, anahtar oluşturmak için kullanmak istediğiniz yöntemi seçin. Yeni bir anahtar **oluşturabilir** , var olan bir anahtarı **yükleyebilir** veya bir anahtarın yedeğini seçmek için **yedekleme geri yükleme** ' yi kullanabilirsiniz.

1. Anahtarınız için bir **ad** girin ve isteğe bağlı olarak diğer anahtar özelliklerini seçin.

1. Dağıtımı başlatmak için **Oluştur** düğmesine tıklayın.

Anahtar tanımlayıcısını bir yere unutmayın: Bu, anahtar **değer URI 'si**, **anahtar adı**ve **anahtar sürümünden**oluşur. Azure Bilişsel Arama 'de şifrelenmiş bir dizin tanımlamanız için bunlara ihtiyacınız olacaktır.
 
![Yeni bir anahtar kasası anahtarı oluştur](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Yeni bir anahtar kasası anahtarı oluştur")

## <a name="3---create-a-service-identity"></a>3-hizmet kimliği oluşturma

Arama hizmetinize bir kimlik atamak, arama hizmetinize Key Vault erişim izni vermenizi sağlar. Arama hizmetiniz, Azure Anahtar Kasası 'nda kimlik doğrulaması yapmak için kimliğini kullanacaktır.

Azure Bilişsel Arama, kimlik atamanın iki yolunu destekler: yönetilen bir kimlik veya dışarıdan yönetilen bir Azure Active Directory uygulaması. 

Mümkünse, yönetilen bir kimlik kullanın. Bu, arama hizmetinize bir kimlik atamanın en kolay yoludur ve çoğu senaryoda çalışır. Dizinler ve eş anlamlı haritalar için birden çok anahtar kullanıyorsanız veya çözümünüz kimlik tabanlı kimlik doğrulamasını niteleyen dağıtılmış bir mimaride ise, sonda açıklanan Gelişmiş [dışarıdan yönetilen Azure Active Directory yaklaşımını](#aad-app) kullanın makalesini inceleyin.

 Genel olarak, yönetilen bir kimlik, arama hizmetinizin kimlik bilgilerini kodda depolamadan Azure Key Vault kimlik doğrulaması yapmasına olanak sağlar. Bu tür yönetilen kimliğin yaşam döngüsü, yalnızca tek bir yönetilen kimliğe sahip olabilen arama hizmetinizin yaşam döngüsüne bağlıdır. [Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Yönetilen bir kimlik oluşturmak için [Azure portalında oturum açın](https://portal.azure.com) ve arama hizmeti panonuzu açın. 

1. Sol gezinti bölmesindeki **kimlik** ' e tıklayın, durumunu **Açık**olarak değiştirin ve **Kaydet**' e tıklayın.

![Yönetilen bir kimliği etkinleştirme](./media/search-enable-msi/enable-identity-portal.png "Bir kimlik kimliğini etkinleştirme")

## <a name="4---grant-key-access-permissions"></a>4-anahtar erişim izinleri verme

Arama hizmetinizin Key Vault anahtarınızı kullanmasını sağlamak için, arama hizmetinize belirli erişim izinlerini vermeniz gerekir.

Erişim izinleri belirli bir zamanda iptal edilebilir. Bu Anahtar Kasası 'nı kullanan herhangi bir arama hizmeti dizini veya eş anlamlı eşleme, iptal edildikten sonra kullanılamaz hale gelir. Anahtar Kasası erişim izinlerinin daha sonraki bir zamanda geri yüklenmesi, Dizin \ eş anlamlı eşleme erişimini geri yükler. Daha fazla bilgi için bkz. [bir anahtar kasasına güvenli erişim](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Azure Portal oturum açın](https://portal.azure.com) ve Anahtar Kasası genel bakış sayfasını açın. 

1. Sol gezinti bölmesinden **erişim ilkeleri** ayarını seçin ve **+ Yeni Ekle**' ye tıklayın.

   ![Yeni Anahtar Kasası erişim ilkesi Ekle](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Yeni Anahtar Kasası erişim ilkesi Ekle")

1. **Sorumlu Seç** ' e tıklayın ve Azure bilişsel arama hizmetinizi seçin. Yönetilen kimliği etkinleştirdikten sonra adı veya görüntülenen nesne KIMLIĞINE göre arama yapabilirsiniz.

   ![Anahtar Kasası erişim ilkesi sorumlusunu seçin](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Anahtar Kasası erişim ilkesi sorumlusunu seçin")

1. **Anahtar izinleri** ' ne tıklayın ve *Al*, *sarmalama tuşu* ve *sarmalama*anahtarı ' nı seçin. Gerekli izinleri hızlıca seçmek için *Azure Data Lake Storage veya Azure depolama* şablonunu kullanabilirsiniz.

   Azure Bilişsel Arama aşağıdaki [erişim izinleriyle](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)verilmelidir:

   * *Get* -arama hizmetinizin Key Vault anahtarınızın ortak parçalarını almasına izin verir
   * *Anahtar Sarla* -arama hizmetinizin iç şifreleme anahtarını korumak için anahtarınızı kullanmasına izin verir
   * *Anahtar sarmalaması geri al* -arama hizmetinizin iç şifreleme anahtarını sarmalamak için anahtarınızı kullanmasına izin verir

   ![Anahtar Kasası erişim ilkesi anahtar izinlerini seçin](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Anahtar Kasası erişim ilkesi anahtar izinlerini seçin")

1. **Tamam** ' a tıklayın ve erişim Ilkesi değişikliklerini **kaydedin** .

> [!Important]
> Azure Bilişsel Arama şifrelenmiş içerik, belirli bir **sürüme**sahip belirli bir Azure Key Vault anahtarı kullanacak şekilde yapılandırılmıştır. Anahtarı veya sürümü değiştirirseniz, önceki key\version. silinmeden **önce** dizin veya eş anlamlı haritanın yeni key\version kullanacak şekilde güncellenmesi gerekir Bunun başarısız olması, anahtar erişimi kaybolduktan sonra içeriğin şifresini çözemeyecek şekilde dizin veya eş anlamlı haritanın kullanılamamasına neden olur.   

## <a name="5---encrypt-content"></a>5-içerik şifreleyin

Müşteri tarafından yönetilen anahtarla şifrelenen bir dizin veya eş anlamlı eşleme oluşturmak, Azure portal kullanılarak henüz mümkün değildir. Bu tür bir dizin veya eş anlamlı eşleme oluşturmak için Azure Bilişsel Arama REST API kullanın.

Hem dizin hem de eş anlamlı eşleme, anahtarı belirtmek için kullanılan yeni bir üst düzey **encryptionKey** özelliğini destekler. 

Anahtar **Kasası URI**'sini, anahtar **adını** ve Anahtar Kasası anahtarınızın anahtar **sürümünü** kullanarak bir **encryptionKey** tanımı oluşturuyoruz:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Bu Anahtar Kasası ayrıntılarının hiçbiri gizli kabul edilmez ve Azure portal ' deki ilgili Azure Key Vault anahtarı sayfasına göz atarak kolayca alınabilir.

Yönetilen bir kimlik kullanmak yerine Key Vault kimlik doğrulaması için AAD uygulaması kullanıyorsanız, şifreleme anahtarınıza AAD uygulama **erişimi kimlik bilgilerini** ekleyin: 
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

## <a name="example-index-encryption"></a>Örnek: Dizin şifreleme
Burada tek fark, Dizin tanımının bir parçası olarak şifreleme anahtarı ayrıntılarını belirtirken, REST API aracılığıyla yeni dizin oluşturma ayrıntıları [(Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)bulunabilir. 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Artık dizin oluşturma isteğini gönderebilir ve sonra dizini normal olarak kullanmaya başlayabilirsiniz.

## <a name="example-synonym-map-encryption"></a>Örnek: eş anlamlı eşleme şifrelemesi

REST API aracılığıyla yeni bir eş anlamlı eşleme oluşturma ayrıntıları, burada tek fark, eş anlamlı eşleme tanımının bir parçası olarak şifreleme anahtarı ayrıntılarını belirtirken, [eş anlamlı harita oluşturma (Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)' de bulunabilir: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Artık eş anlamlı harita oluşturma isteğini gönderebilir ve normal olarak kullanmaya başlayabilirsiniz.

>[!Important] 
> **EncryptionKey** , mevcut Azure bilişsel arama dizinlerine veya eş anlamlı haritalara eklenemediğinden, üç Anahtar Kasası ayrıntılarının (örneğin, anahtar sürümünü güncelleştirme) her biri için farklı değerler sunarak güncelleştirilmiş olabilir. Yeni bir Key Vault anahtarına veya yeni bir anahtar sürümüne geçiş yaparken, önce anahtarı kullanan tüm Azure Bilişsel Arama dizini veya eş anlamlı Haritası, önceki key\version. silinmeden **önce** yeni key\version kullanacak şekilde güncellenmelidir Bunun başarısız olması, anahtar erişimi kaybolduktan sonra içeriğin şifresini çözemeyeceği için dizin veya eş anlamlı haritanın kullanılamaz hale gelmesine neden olur.   
> Anahtar Kasası erişim izinlerini daha sonra geri yüklemek, içerik erişimini geri yükler.

## <a name="aad-app"></a>Gelişmiş: dışarıdan yönetilen Azure Active Directory uygulaması kullanma

Yönetilen bir kimlik mümkün olmadığında Azure Bilişsel Arama hizmetiniz için güvenlik sorumlusu olan bir Azure Active Directory uygulaması oluşturabilirsiniz. Özellikle, yönetilen bir kimlik şu koşullarda önemli değildir:

* Anahtar kasasına doğrudan arama hizmeti erişim izinleri verilemez (örneğin, arama hizmeti, Azure Key Vault farklı bir Active Directory kiracısında ise).

* Tek bir arama hizmeti, her anahtar kasasının kimlik doğrulaması için **farklı bir kimlik** kullanması gereken farklı bir anahtar kasasından farklı bir anahtar kullanan birden çok şifrelenmiş ındexes\eş anlamlı haritaları barındırmak için gereklidir. Farklı anahtar kasalarını yönetmek için farklı bir kimlik kullanmak bir gereksinim değildir, yukarıdaki yönetilen kimlik seçeneğini kullanmayı düşünün.  

Azure Bilişsel Arama, bu tür topolojilerle uyum sağlamak için, arama hizmetiniz ve Key Vault arasında kimlik doğrulaması için Azure Active Directory (AAD) uygulamalarının kullanımını destekler.    
Portalda bir AAD uygulaması oluşturmak için:

1. [Azure Active Directory uygulaması oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. Şifrelenmiş bir dizin oluşturmak için gerekli olacak şekilde [, uygulama kimliği ve kimlik doğrulama anahtarını alın](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) . **Uygulama kimliği** ve **kimlik doğrulama anahtarı**sağlamanız gereken değerler.

>[!Important]
> Yönetilen bir kimlik yerine kimlik doğrulaması için bir AAD uygulaması kullanmaya karar verirken, Azure Bilişsel Arama tarafından sizin adınıza AAD uygulamanızı yönetmek için yetkilendirilmediğini göz önünde bulundurun ve düzenli aralıklarla, AAD uygulamanızı yönetmek için uygulama kimlik doğrulama anahtarının dönüşü.
> Bir AAD uygulamasını veya kimlik doğrulama anahtarını değiştirirken, bu uygulamayı kullanan tüm Azure Bilişsel Arama dizini veya eş anlamlı Haritası, önceki uygulamayı veya yetkilendirme silinmeden **önce** yeni Application ID\key kullanacak şekilde güncelleştirilmeleri gerekir anahtar ve Key Vault erişimi iptal etmeden önce.
> Bunun başarısız olması, anahtar erişimi kaybolduktan sonra içeriğin şifresini çözemeyeceği için dizin veya eş anlamlı haritanın kullanılamaz hale gelmesine neden olur.   

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik mimarisi hakkında bilginiz varsa, [Azure Güvenlik belgelerini](https://docs.microsoft.com/azure/security/)gözden geçirin ve özellikle bu makalede:

> [!div class="nextstepaction"]
> [Bekleyen veri şifreleme](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
