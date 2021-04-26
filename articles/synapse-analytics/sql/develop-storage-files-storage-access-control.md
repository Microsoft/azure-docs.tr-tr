---
title: Sunucusuz SQL havuzu için depolama hesabı erişimini denetleme
description: Sunucusuz SQL havuzunun Azure Storage 'a nasıl eriştiği ve Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu için depolama erişimini nasıl denetleyebileceğinizi açıklar.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 266a6c27261107b883fdc0c1cdd274e6345de6db
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483461"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu için depolama hesabı erişimini denetleme

Sunucusuz bir SQL havuzu sorgusu, dosyaları doğrudan Azure Storage 'dan okur. Azure Storage 'daki dosyalara erişim izinleri iki düzeyde denetlenir:
- **Depolama düzeyi** -kullanıcının temeldeki depolama dosyalarına erişim izni olmalıdır. Depolama yöneticiniz, Azure AD Principal 'ın dosyaları okumasına/yazmasına veya depolamaya erişmek için kullanılacak SAS anahtarı oluşturmasına izin verir.
- **SQL hizmet düzeyi** -Kullanıcı [dış tablo](develop-tables-external-tables.md) kullanarak verileri okuma veya işlevi yürütme izni vermiş olmalıdır `OPENROWSET` . [Bu bölümde gerekli izinler](develop-storage-files-overview.md#permissions)hakkında daha fazla bilgi edinin.

Bu makalede, kullanabileceğiniz kimlik bilgileri türleri ve SQL ve Azure AD kullanıcıları için kimlik bilgileri aramasının nasıl yapılacağı açıklanır.

## <a name="storage-permissions"></a>Depolama izinleri

SYNAPSE Analytics çalışma alanındaki sunucusuz bir SQL havuzu Azure Data Lake depolama alanında depolanan dosyaların içeriğini okuyabilir. Bir SQL sorgusunu yürüten bir kullanıcının dosyaları okumasını sağlamak için depolama izinlerini yapılandırmanız gerekir. Dosyalara erişimi etkinleştirmek için üç yöntem vardır>
- **[Rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)** , depolama alanının yerleştirildiği kiracıda BIR Azure AD kullanıcısına bir rol atamanızı sağlar. RBAC rolleri, Azure AD kullanıcılarına atanabilir. Okuyucu `Storage Blob Data Reader` , `Storage Blob Data Contributor` veya rolüne sahip olmalıdır `Storage Blob Data Owner` . Azure Storage 'da veri yazan bir kullanıcının `Storage Blob Data Writer` veya `Storage Blob Data Owner` rolü olmalıdır. `Storage Owner`Rolün bir kullanıcının de olduğunu göstermez `Storage Data Owner` .
- **Access Control listeleri (ACL)** , Azure depolama 'daki dosya ve dizinlerde hassas izin modeli tanımlamanızı sağlar. ACL, Azure AD kullanıcılarına atanabilir. Okuyucular Azure Storage 'daki bir yoldaki dosyayı okumak istiyorlarsa, dosya yolundaki her klasör üzerinde Execute (X) ACL 'SI olmalıdır ve dosyadaki (R) ACL 'yi okur. [Depolama katmanında ACL izinleri ayarlama hakkında daha fazla bilgi edinin](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls)
- **Paylaşılan erişim imzası (SAS)** , bir okuyucunun zaman sınırlı belirteci kullanarak Azure Data Lake depolama üzerindeki dosyalara erişmesine olanak sağlar. Okuyucunun kimlik doğrulamasının Azure AD kullanıcısı olarak da olması gerekmez. SAS belirteci, okuyucuya verilen izinleri ve belirtecin geçerli olduğu süreyi içerir. SAS belirteci, aynı Azure AD kiracısında olması gerekmeyen tüm kullanıcılara zaman kısıtlı erişim için iyi bir seçimdir. SAS belirteci, depolama hesabında veya belirli dizinlerde tanımlanabilir. [Paylaşılan erişim imzalarını kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../../storage/common/storage-sas-overview.md)hakkında daha fazla bilgi edinin.

## <a name="supported-storage-authorization-types"></a>Desteklenen depolama yetkilendirme türleri

Sunucusuz SQL havuzunda oturum açan bir kullanıcının, dosyalar herkese açık değilse Azure Storage 'daki dosyalara erişme ve bunları sorgulama yetkisi olmalıdır. Genel olmayan depolama- [Kullanıcı kimliği](?tabs=user-identity), [paylaşılan erişim Imzası](?tabs=shared-access-signature)ve [yönetilen kimliğe](?tabs=managed-identity)erişmek için üç yetkilendirme türü kullanabilirsiniz.

> [!NOTE]
> **Azure AD geçiş** , bir çalışma alanı oluşturduğunuzda varsayılan davranıştır.

### <a name="user-identity"></a>[Kullanıcı kimliği](#tab/user-identity)

"Azure AD geçişi" olarak da bilinen **Kullanıcı kimliği**, veri erişimini yetkilendirmek için SUNUCUSUZ SQL havuzunda oturum açan Azure AD kullanıcısının kimliğinin kullanıldığı bir yetkilendirme türüdür. Verilere erişmeden önce Azure depolama yöneticisinin Azure AD kullanıcısına izin vermesi gerekir. Aşağıdaki tabloda gösterildiği gibi, SQL kullanıcı türü için de desteklenmez.

> [!IMPORTANT]
> AAD kimlik doğrulama belirteci, istemci uygulamaları tarafından önbelleğe alınmış olabilir. Örneğin, PowerBI tarafından AAD belirteci önbelleğe alınır ve aynı belirteci bir saat için yeniden kullanır. Sorgu yürütmenin ortasında belirtecin süresi dolarsa uzun süren sorgular başarısız olabilir. Sorgunun ortasında süresi dolan AAD erişim belirtecinin neden olduğu sorgu hatalarıyla karşılaşıyorsanız, [yönetilen kimliğe](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) veya [paylaşılan erişim imzasına](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types)geçmeyi düşünün.

Verilerinize erişmek için kimliğinizi kullanabilmeniz için bir Depolama Blobu veri sahibi/katkıda bulunan/okuyucu rolüne sahip olmanız gerekir. Alternatif olarak, dosyalara ve klasörlere erişmek için hassas ACL kuralları belirtebilirsiniz. Bir depolama hesabının sahibi olsanız bile, yine de Depolama Blobu veri rollerinden birine eklemeniz gerekir.
Azure Data Lake Store Gen2 ' de erişim denetimi hakkında daha fazla bilgi edinmek için [Azure Data Lake Storage 2. makalesinde erişim denetimini](../../storage/blobs/data-lake-storage-access-control.md) gözden geçirin.


### <a name="shared-access-signature"></a>[Paylaşılan erişim imzası](#tab/shared-access-signature)

**Paylaşılan erişim imzası (SAS)** , bir depolama hesabındaki kaynaklara temsilci erişimi sağlar. SAS ile müşteri, istemcilere, hesap anahtarlarını paylaşmadan bir depolama hesabındaki kaynaklara erişim izni verebilir. SAS, geçerlilik aralığı, verilen izinler, kabul edilebilir IP adresi aralığı ve kabul edilebilir protokol (https/http) dahil olmak üzere SAS 'si olan istemcilere verdiğiniz erişim türü üzerinde ayrıntılı denetim sağlar.

**Azure portal > depolama hesabı-> paylaşılan erişim imzası-> Izinleri yapılandırma-> SAS ve bağlantı dizesi oluşturma '** ya gıderek bir SAS belirteci alabilirsiniz.

> [!IMPORTANT]
> Bir SAS belirteci oluşturulduğunda, belirtecin başlangıcında bir soru işareti ('? ') içerir. Belirteci sunucusuz SQL havuzunda kullanmak için, bir kimlik bilgisi oluştururken soru işaretini ('? ') kaldırmanız gerekir. Örnek:
>
> SAS belirteci:? ZF = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Bir SAS belirteci kullanarak erişimi etkinleştirmek için, veritabanı kapsamlı veya sunucu kapsamlı kimlik bilgisi oluşturmanız gerekir 


> [!IMPORTANT]
> SAS belirteci ile özel depolama hesaplarına erişemezsiniz. Korumalı depolamaya erişmek için [yönetilen kimliğe](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) veya [Azure AD geçişli](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) kimlik doğrulamasına geçmeyi düşünün.

### <a name="managed-identity"></a>[Yönetilen Kimlik](#tab/managed-identity)

**Yönetilen kimlik** de MSI olarak bilinir. Sunucusuz SQL havuzu için Azure hizmetleri sağlayan Azure Active Directory (Azure AD) özelliğidir. Ayrıca, Azure AD 'de otomatik olarak yönetilen bir kimlik dağıtır. Bu kimlik, Azure Storage 'da veri erişimi isteğine yetki vermek için kullanılabilir.

Verilere erişmeden önce Azure depolama yöneticisinin verilere erişim için yönetilen kimliğe izinler vermesi gerekir. Yönetilen kimliğe izin verilmesi, diğer Azure AD kullanıcıları için izin verme ile aynı şekilde yapılır.

### <a name="anonymous-access"></a>[Anonim erişim](#tab/public-access)

[Anonim erişime izin](../../storage/blobs/anonymous-read-access-configure.md)veren Azure depolama hesaplarına yerleştirilmiş genel kullanıma açık dosyalara erişebilirsiniz.

---

### <a name="supported-authorization-types-for-databases-users"></a>Veritabanları kullanıcıları için desteklenen yetkilendirme türleri

Aşağıdaki tabloda kullanılabilir yetkilendirme türlerini bulabilirsiniz:

| Yetki türü                    | *SQL kullanıcısı*    | *Azure AD kullanıcısı*     |
| ------------------------------------- | ------------- | -----------    |
| [Kullanıcı kimliği](?tabs=user-identity#supported-storage-authorization-types)       | Desteklenmez | Desteklenir      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Desteklenir     | Desteklenir      |
| [Yönetilen Kimlik](?tabs=managed-identity#supported-storage-authorization-types) | Desteklenir | Desteklenir      |

### <a name="supported-storages-and-authorization-types"></a>Desteklenen depolama alanları ve yetkilendirme türleri

Aşağıdaki yetkilendirme ve Azure Depolama türleri birleşimlerini kullanabilirsiniz:

| Yetki türü  | Blob Depolama   | ADLS 1. Nesil        | ADLS 2. Nesil     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Desteklenir\*      | Desteklenmiyor   | Desteklenir\*     |
| [Yönetilen Kimlik](?tabs=managed-identity#supported-storage-authorization-types) | Desteklenir      | Desteklenir        | Desteklenir     |
| [Kullanıcı kimliği](?tabs=user-identity#supported-storage-authorization-types)    | Desteklenir\*      | Desteklenir\*        | Desteklenir\*     |

\* SAS belirteci ve Azure AD kimliği, güvenlik duvarıyla korunmayan depolamaya erişmek için kullanılabilir.


### <a name="querying-firewall-protected-storage"></a>Güvenlik duvarı korumalı depolamayı sorgulama

Güvenlik duvarıyla korunan depolamaya erişirken **Kullanıcı kimliğini** veya **yönetilen kimlik**' i kullanabilirsiniz.

> [!NOTE]
> Depolama üzerindeki güvenlik duvarı özelliği genel önizlemeye sunuldu ve tüm genel bulut bölgelerinde kullanılabilir. 

#### <a name="user-identity"></a>Kullanıcı kimliği

Kullanıcı kimliği aracılığıyla güvenlik duvarıyla korunan depolamaya erişmek için, Azure portal Kullanıcı arabirimi veya PowerShell modülü az. Storage ' u kullanabilirsiniz.
#### <a name="configuration-via-azure-portal"></a>Azure portal aracılığıyla yapılandırma

1. Azure portal 'de depolama hesabınızı arayın.
1. Bölüm ayarları altındaki Ağ ' a gidin.
1. "Kaynak örnekleri" bölümünde, SYNAPSE çalışma alanınız için bir özel durum ekleyin.
1. Kaynak türü olarak Microsoft. SYNAPSE/çalışma alanlarını seçin.
1. Örnek adı olarak çalışma alanınızın adını seçin.
1. Kaydet’e tıklayın.

#### <a name="configuration-via-powershell"></a>PowerShell aracılığıyla yapılandırma

Depolama hesabı güvenlik duvarını yapılandırmak ve SYNAPSE çalışma alanı için bir özel durum eklemek için aşağıdaki adımları izleyin.

1. PowerShell 'i açın veya [PowerShell 'i yükleyip](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Az. Storage 3.4.0 modülünü ve az. SYNAPSE 0.7.0 'yi yükler: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > **3.4.0 sürümünü** kullandığınızdan emin olun. Şu komutu çalıştırarak az. Storage sürümünüzü kontrol edebilirsiniz:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Azure Kiracınıza bağlanın: 
    ```powershell
    Connect-AzAccount
    ```
4. PowerShell 'de değişkenleri tanımlama: 
    - Kaynak grubu adı-bu Azure portal, depolama hesabına genel bakış halinde bulabilirsiniz.
    - Hesap adı-güvenlik duvarı kuralları tarafından korunan depolama hesabının adı.
    - Kiracı KIMLIĞI-bunu, kiracı bilgilerinde Azure Active Directory Azure portal bulabilirsiniz.
    - Çalışma alanı adı-SYNAPSE çalışma alanının adı.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Kaynak kimliğinin RESOURCEID değişkeninin yazdırılması içinde bu şablonla eşleştiğinden emin olun.
    >
    > Daha küçük bir durumda **ResourceGroups** yazmak önemlidir.
    > Bir kaynak kimliği örneği: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Depolama ağı kuralı ekle: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Kuralın depolama hesabınıza uygulandığını doğrulayın: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Yönetilen Kimlik
[Güvenilen Microsoft hizmetlerine Izin vermeniz gerekiyor... ](../../storage/common/storage-network-security.md#trusted-microsoft-services)bu kaynak örneği için [sistem tarafından atanan yönetilen kimliğe](../../active-directory/managed-identities-azure-resources/overview.md) [bir Azure rolünü](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) ayarlama ve açıkça atama. Bu durumda, örnek için erişim kapsamı yönetilen kimliğe atanan Azure rolüne karşılık gelir.

## <a name="credentials"></a>Kimlik bilgileri

Azure depolama 'da bulunan bir dosyayı sorgulamak için sunucusuz SQL havuzu uç noktasının kimlik doğrulama bilgilerini içeren bir kimlik bilgisi olması gerekir. İki tür kimlik bilgisi kullanılır:
- Sunucu düzeyindeki KIMLIK BILGILERI, işlev kullanılarak yürütülen geçici sorgular için kullanılır `OPENROWSET` . Kimlik bilgisi adının depolama URL 'siyle eşleşmesi gerekir.
- VERITABANı KAPSAMLı KIMLIK BILGILERI dış tablolar için kullanılır. Dış tablo `DATA SOURCE` , depolamaya erişmek için kullanılması gereken kimlik bilgileri ile başvurur.

Bir kullanıcının kimlik bilgisini oluşturmasına veya çalıştırmasına izin vermek için yönetici, bir kullanıcıya HERHANGI bir KIMLIK BILGISI izni VEREBILIR/REDDEDEBILIR:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Dış depolamaya erişen veritabanı kullanıcılarının kimlik bilgilerini kullanma izni olmalıdır.

### <a name="grant-permissions-to-use-credential"></a>Kimlik bilgilerini kullanmak için izinleri verme

Kimlik bilgisini kullanmak için, bir kullanıcının `REFERENCES` belirli bir kimlik bilgisi üzerinde izni olması gerekir. Bir `REFERENCES` specific_user için storage_credential izin vermek için şunu yürütün:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Sunucu kapsamlı kimlik bilgisi

Sunucu kapsamlı kimlik bilgileri, SQL oturum açma `OPENROWSET` işlevi `DATA_SOURCE` bir depolama hesabındaki dosyaları okumak zorunda kalmadan çalıştırıldığında kullanılır. Sunucu kapsamlı kimlik bilgisinin adı, Azure depolama 'nın temel URL **'siyle eşleşmelidir (** isteğe bağlı olarak bir kapsayıcı adı gelir). Kimlik bilgisi [Oluştur](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)çalıştırılarak bir kimlik bilgisi eklenir. KIMLIK BILGISI adı bağımsız değişkeni sağlamanız gerekir.

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER`Bağımsız değişken desteklenmiyor.

Sunucu düzeyi KIMLIK BILGISI adı, depolama hesabının (ve isteğe bağlı olarak kapsayıcı) tam yoluyla aşağıdaki biçimde olmalıdır: `<prefix>://<storage_account_path>[/<container_name>]` . Depolama hesabı yolları aşağıdaki tabloda açıklanmıştır:

| Dış veri kaynağı       | Ön ek | Depolama hesabı yolu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Depolama         | https  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>. dfs.core.windows.net              |

Sunucu kapsamlı kimlik bilgileri, aşağıdaki kimlik doğrulama türlerini kullanarak Azure depolama 'ya erişim sağlar:

### <a name="user-identity"></a>[Kullanıcı kimliği](#tab/user-identity)

Azure AD kullanıcıları `Storage Blob Data Owner` , veya rolüne sahip olmaları durumunda Azure Storage 'daki herhangi bir dosyaya erişebilir `Storage Blob Data Contributor` `Storage Blob Data Reader` . Azure AD kullanıcılarının depolama erişimi için kimlik bilgilerine ihtiyacı yoktur. 

SQL kullanıcıları depolama alanına erişmek için Azure AD kimlik doğrulamasını kullanamaz.

### <a name="shared-access-signature"></a>[Paylaşılan erişim imzası](#tab/shared-access-signature)

Aşağıdaki betik, `OPENROWSET` SAS belirtecini kullanarak Azure Storage 'daki herhangi bir dosyaya erişmek için işlev tarafından kullanılabilecek bir sunucu düzeyi kimlik bilgisi oluşturur. Bu kimlik bilgisini, `OPENROWSET` kimlik bilgisi ADıNDAKI URL ile eşleşen Azure depolama 'DA SAS anahtarıyla korunan dosyaları okumak için işlevi yürüten SQL sorumlusunu etkinleştirmek üzere oluşturun.

Gerçek depolama hesabı adınızla birlikte Exchange <*mystorageaccountname*> ve *mystorageaccountcontainername*> gerçek kapsayıcı adıyla <:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

İsteğe bağlı olarak, depolama hesabının yalnızca temel URL 'sini kapsayıcı adı olmadan kullanabilirsiniz.

### <a name="managed-identity"></a>[Yönetilen Kimlik](#tab/managed-identity)

Aşağıdaki betik, `OPENROWSET` çalışma alanı tarafından yönetilen kimlik kullanarak Azure Storage 'daki herhangi bir dosyaya erişmek için işlev tarafından kullanılabilecek bir sunucu düzeyi kimlik bilgisi oluşturur.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

İsteğe bağlı olarak, depolama hesabının yalnızca temel URL 'sini kapsayıcı adı olmadan kullanabilirsiniz.

### <a name="public-access"></a>[Genel erişim](#tab/public-access)

Genel kullanıma açık dosyalara erişime izin vermek için veritabanı kapsamlı kimlik bilgileri gerekli değildir. Azure depolama 'da genel kullanıma açık dosyalara erişmek için [veritabanı kapsamlı kimlik bilgileri olmadan veri kaynağı](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) oluşturun.

---

## <a name="database-scoped-credential"></a>Veritabanı kapsamlı kimlik bilgileri

Veritabanı kapsamlı kimlik bilgileri, herhangi bir asıl çağrı çalıştırıldığında `OPENROWSET` `DATA_SOURCE` veya ortak dosyalara erişlemeyen [dış tablodaki](develop-tables-external-tables.md) verileri seçtiğinde kullanılır. Veritabanı kapsamlı kimlik bilgisinin depolama hesabı adıyla eşleşmesi gerekmez. Bu, depolama konumunu tanımlayan VERI KAYNAĞıNDA açıkça kullanılacaktır.

Veritabanı kapsamlı kimlik bilgileri, aşağıdaki kimlik doğrulama türlerini kullanarak Azure depolama 'ya erişim sağlar:

### <a name="azure-ad-identity"></a>[Azure AD kimliği](#tab/user-identity)

Azure AD kullanıcıları, en az `Storage Blob Data Owner` , veya rolüne sahip olmaları durumunda Azure Storage 'daki herhangi bir dosyaya erişebilir `Storage Blob Data Contributor` `Storage Blob Data Reader` . Azure AD kullanıcılarının depolama erişimi için kimlik bilgilerine ihtiyacı yoktur.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL kullanıcıları depolama alanına erişmek için Azure AD kimlik doğrulamasını kullanamaz.

### <a name="shared-access-signature"></a>[Paylaşılan erişim imzası](#tab/shared-access-signature)

Aşağıdaki betik, kimlik bilgilerinde belirtilen SAS belirtecini kullanarak depolamadaki dosyalara erişmek için kullanılan bir kimlik bilgisi oluşturur. Betik, depolama alanına erişmek için bu SAS belirtecini kullanan bir örnek dış veri kaynağı oluşturur.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Yönetilen Kimlik](#tab/managed-identity)

Aşağıdaki betik, geçerli Azure AD kullanıcısının yönetilen hizmet kimliği olarak taklit etmek için kullanılabilecek veritabanı kapsamlı bir kimlik bilgisi oluşturur. Betik, depolama alanına erişmek için çalışma alanı kimliğini kullanan örnek bir dış veri kaynağı oluşturur.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Veritabanı kapsamlı kimlik bilgisinin, depolama konumunu tanımlayan VERI KAYNAĞıNDA açık olarak kullanılacağı için depolama hesabı adıyla eşleşmesi gerekmez.

### <a name="public-access"></a>[Genel erişim](#tab/public-access)

Genel kullanıma açık dosyalara erişime izin vermek için veritabanı kapsamlı kimlik bilgileri gerekli değildir. Azure depolama 'da genel kullanıma açık dosyalara erişmek için [veritabanı kapsamlı kimlik bilgileri olmadan veri kaynağı](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) oluşturun.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Bu depolama alanına erişmek için hangi kimlik doğrulama yönteminin kullanılacağını belirtmek için, dış veri kaynaklarında veritabanı kapsamlı kimlik bilgileri kullanılır:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Örnekler

### <a name="access-a-publicly-available-data-source"></a>**Genel kullanıma açık bir veri kaynağına erişme**

Genel olarak kullanılabilir veri kaynağına erişen bir tablo oluşturmak için aşağıdaki betiği kullanın.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Veritabanı kullanıcısı, veri kaynağına başvuran dış tablo veya [OPENROWSET](develop-openrowset.md) işlevini kullanarak veri kaynağındaki dosyaların içeriğini okuyabilir:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Kimlik bilgilerini kullanarak bir veri kaynağına erişme**

SAS belirtecini, Kullanıcı Azure AD kimliğini veya çalışma alanının yönetilen kimliğini kullanarak Azure depolama 'ya erişen bir dış tablo oluşturmak için aşağıdaki betiği değiştirin.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Veritabanı kullanıcısı, veri kaynağına başvuran [dış tablo](develop-tables-external-tables.md) veya [OPENROWSET](develop-openrowset.md)  işlevini kullanarak veri kaynağındaki dosyaların içeriğini okuyabilir:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıda listelenen makaleler farklı klasör türlerini, dosya türlerini sorgulamayı ve görünümleri oluşturma ve kullanma hakkında bilgi edinmenize yardımcı olur:

- [Tek CSV dosyasını sorgula](query-single-csv-file.md)
- [Sorgu klasörleri ve birden çok CSV dosyası](query-folders-multiple-csv-files.md)
- [Belirli dosyaları sorgula](query-specific-files.md)
- [Parquet dosyalarını sorgulama](query-parquet-files.md)
- [Görünümleri oluşturma ve kullanma](create-use-views.md)
- [JSON dosyalarını sorgulama](query-json-files.md)
- [Parquet iç içe türlerini sorgulama](query-parquet-nested-types.md)
