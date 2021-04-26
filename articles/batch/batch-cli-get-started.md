---
title: Batch için Azure CLı ile çalışmaya başlama
description: Azure Batch hizmet kaynaklarını yönetmek üzere Azure CLI’daki Batch komutlarına hızlı bir giriş yapın
ms.topic: how-to
ms.date: 07/24/2018
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: befe2f6fe67ce7c2c4bb61153344103ee2aa70cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768442"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Batch kaynaklarını Azure CLI ile yönetme

Azure CLI, Azure kaynaklarını yönetmek için Azure tarafından sunulan komut satırı deneyimidir. MacOS, Linux ve Windows’da kullanılabilir. Azure CLI, Azure kaynaklarını komut satırından yönetmek üzere iyileştirilmiştir. Azure CLI kullanarak Azure Batch hesaplarınızın yanı sıra havuzlar, işler ve görevler gibi kaynakları yönetebilirsiniz. Azure Batch CLI ile Batch API'leri, Azure portalı ve Batch PowerShell cmdlet’leri ile gerçekleştirdiğiniz görevlerin çoğu için betik oluşturabilirsiniz.

Bu makalede Batch ile [Azure CLI sürüm 2.0](/cli/azure) kullanımına genel bakışa yer verilmiştir. Azure ile CLI kullanımı hakkında bilgi için bkz. [Azure CLI kullanmaya başlama](/cli/azure/get-started-with-azure-cli).

## <a name="set-up-the-azure-cli"></a>Azure CLI'yı ayarlama

En son Azure CLI sürümünü [Azure Cloud Shell](../cloud-shell/overview.md)'de çalıştırabilirsiniz. Azure CLI'yı yerel ortamınıza yüklemek için [Azure CLI'yı yükleme](/cli/azure/install-azure-cli) sayfasındaki adımları izleyin.

> [!TIP]
> Hizmet güncelleştirmeleri ve geliştirmelerinden yararlanmak için Azure CLI yüklemenizi sık sık güncelleştirmeniz önerilir.
> 
> 

## <a name="command-help"></a>Komut yardımı

Komuttan sonra `-h` ekleyerek Azure CLI'daki her komut için yardım metni görüntüleyebilirsiniz. Diğer seçenekleri atın. Örnek:

* `az` komutuyla ilgili yardım almak için şunu girin: `az -h`
* CLI’daki tüm Batch komutlarının listesini almak için şunu kullanın: `az batch -h`
* Batch hesabı oluşturmayla ilgili yardım almak için şunu girin: `az batch account create -h`

Emin olmadığınızda herhangi bir Azure CLI komutuyla ilgili yardım almak için `-h` komut satırı seçeneğini kullanın.



Ayrıca [Batch için Azure CLI komutları](/cli/azure/batch) hakkında ayrıntılı bilgi için Azure CLI referans belgelerini inceleyin. 

## <a name="log-in-and-authenticate"></a>Oturum açma ve kimlik doğrulaması

Batch ile Azure CLI kullanmak için oturum açmanız ve kimlik doğrulamasından geçmeniz gerekir. İzlemeniz gereken iki basit adım vardır:

1. **Azure 'da oturum açın.** Azure'da oturum açarak [Batch Management hizmeti](batch-management-dotnet.md) komutları dahil olmak üzere Azure Resource Manager komutlarına erişebilirsiniz.  
2. **Batch hesabınızda oturum açın.** Batch hesabınızda oturum açarak Batch hizmeti komutlarına erişebilirsiniz.   

### <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure'da oturum açmanın birkaç farklı yolu vardır ve hepsi [Azure CLI ile oturum açma](/cli/azure/authenticate-azure-cli) sayfasında ayrıntılı olarak açıklanmıştır:

1. [Etkileşimli olarak oturum açma](/cli/azure/authenticate-azure-cli). Azure CLI komutlarını komut satırından çalıştırmak için etkileşimli olarak oturum açın.
2. [Hizmet sorumlusu Ile oturum açın](/cli/azure/authenticate-azure-cli). Azure CLI komutlarını bir betikten veya uygulamadan çalıştırdığınızda hizmet sorumlusuyla oturum açın.

Bu makalede Azure'da etkileşimli oturum açmayı göstereceğiz. Komut satırına [az login](/cli/azure/reference-index#az_login) yazın:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

`az login` komutu, burada gösterildiği gibi kimlik doğrulaması için kullanabileceğiniz bir belirteç döndürür. Bir web sayfası açmak ve belirteci Azure'a göndermek için yönergeleri izleyin:

![Azure'da oturum açma](./media/batch-cli-get-started/az-login.png)

Örnek kabuk betikleri bölümünde listelenen örnekler de Azure'da etkileşimli olarak oturum açarak Azure CLI oturumunuzu nasıl başlatacağınızı göstermektedir. Oturum açtıktan sonra Batch hesapları, anahtarları, uygulama paketleri ve kotaları dahil olmak üzere Batch Management kaynaklarıyla çalışmak için komutları çağırabilirsiniz.  

### <a name="log-in-to-your-batch-account"></a>Batch hesabınızda oturum açma

Havuzlar, işler ve görevler gibi Batch kaynaklarını yönetmek üzere Azure CLI kullanmak için Batch hesabınızda oturum açıp kimlik doğrulamasından geçmeniz gerekir. Batch hizmetinde oturum açmak için [az batch account login](/cli/azure/batch/account#az_batch_account_login) komutunu kullanın. 

Batch hesabınızla kimlik doğrulamasından geçmek için kullanabileceğiniz iki seçenek vardır:

- **Azure Active Directory (Azure AD) kimlik doğrulaması kullanarak** 

    Azure AD kimlik doğrulamasını kullanmak, Batch ile Azure CLI kullandığınızda varsayılan ve çoğu senaryo için önerilen yöntemdir. 
    
    Önceki bölümde anlatıldığı şekilde Azure'da etkileşimli olarak oturum açtığınızda kimlik bilgileriniz önbelleğe alınır ve Azure CLI bu kimlik bilgilerini kullanarak Batch hesabınızda oturum açabilir. Azure'da hizmet sorumlusu kullanarak oturum açarsanız o kimlik bilgileri de Batch hesabınıza oturum açmak için kullanılır.

    Azure AD 'nin bir avantajı, Azure rol tabanlı erişim denetimi (Azure RBAC) sunuyor. Azure RBAC ile kullanıcının erişimi, hesap anahtarlarına sahip olup olmamaları değil, kendilerine atanan rolüne bağlıdır. Hesap anahtarlarını yönetmek yerine Azure rollerini yönetebilir ve Azure AD 'nin erişim ve kimlik doğrulamasına izin verebilirsiniz.  

     Azure AD ile Batch hesabınızda oturum açmak için [az batch account login](/cli/azure/batch/account#az_batch_account_login) komutunu çağırın: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Paylaşılan anahtar kimlik doğrulamasını kullanarak**

    [Paylaşılan Anahtar kimlik doğrulaması](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) hesap erişim anahtarlarınızı kullanarak Batch hizmeti için Azure CLI komutlarının kimlik doğrulamasından geçmesini sağlar.

    Batch komutlarını otomatikleştirme amacıyla Azure CLI betikleri oluşturuyorsanız Paylaşılan Anahtar kimlik doğrulaması veya Azure AD hizmet sorumlusu kullanabilirsiniz. Bazı senaryolarda Paylaşılan Anahtar ile kimlik doğrulaması, hizmet sorumlusu oluşturmaktan daha kolay olabilir.  

    Paylaşılan Anahtar kimlik doğrulamasını kullanarak oturum açmak için komut satırına `--shared-key-auth` seçeneğini dahil edin:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Örnek kabul betikleri bölümünde listelenen örnekler, hem Azure AD hem de Paylaşılan Anahtar kullanarak Azure CLI ile Batch hesabınızda nasıl oturum açacağınızı göstermektedir.

## <a name="use-azure-batch-cli-extension-commands"></a>Azure Batch CLI uzantısı komutlarını kullanma

Azure Batch CLI uzantısını yükleyerek, Azure CLI'yı Batch işlerini kod yazmadan uçtan uca çalıştırmak için kullanabilirsiniz. Uzantı tarafından desteklenen Batch komutları Azure CLI ile havuz, iş ve görev oluşturmak için JSON şablonlarını kullanmanızı sağlar. Uzantı CLI komutlarını, Batch hesabıyla ilişkilendirilmiş Azure Depolama hesabına işin giriş dosyalarını yüklemek ve bu hesaptan iş çıkış dosyalarını indirmek için de kullanılabilir. Daha fazla bilgi için bkz. [Azure Batch CLI şablonlarını ve dosya aktarımını kullanma](batch-cli-templates.md).

## <a name="script-examples"></a>Betik örnekleri

Ortak görevleri gerçekleştirmek üzere Batch için [CLI betik örneklerini](./scripts/batch-cli-sample-create-account.md) görüntüleyin. Bu örnekler, Batch için Azure CLI’da hesapları, havuzları, işleri ve görevleri oluşturup yönetmeye yönelik kullanılabilir komutların birçoğunu kapsar.

## <a name="json-files-for-resource-creation"></a>Kaynak oluşturmak için JSON dosyaları

Havuzlar ve işler gib Batch kaynakları oluşturduğunuzda parametrelerini komut satırı seçenekleri olarak geçirmek yerine yeni kaynağın yapılandırmasını içeren bir JSON dosyası belirtebilirsiniz. Örnek:

```azurecli
az batch pool create my_batch_pool.json
```

Yalnızca komut satırı seçeneklerini kullanarak çoğu Batch kaynağını oluşturabilirsiniz ancak bazı özellikler, kaynak ayrıntılarını içeren JSON biçimli bir dosya belirtmenizi gerektirir. Örneğin, bir başlatma görevi için kaynak belirtmek istiyorsanız bir JSON dosyası kullanmanız gerekir.

Kaynak oluşturmak için gereken JSON söz dizimi dosyasını bulmak üzere [Batch REST API başvurusu][rest_api] belgelerine bakın. REST API başvurusundaki her bir "Ekle *kaynak türü*" konusu ilgili kaynağı oluşturmak için örnek JSON betikleri içerir. Bu örnek JSON betiklerini Azure CLI ile kullanabileceğiniz JSON dosyası şablonu olarak değerlendirebilirsiniz. Örneğin havuz oluşturmak için JSON söz dizimini görmek istiyorsanız bkz. [Bir hesaba havuz ekleme][rest_add_pool].

Bir JSON dosyasını belirten örnek bir betik için bkz. [Batch ile bir iş ve görevlerini çalıştırma](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Bir kaynak oluştururken JSON dosyası belirtirseniz, komut satırında ilgili kaynak için belirttiğiniz diğer parametreler yok sayılır.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Batch kaynakları için etkili sorgular

Her Batch kaynak türü, Batch hesabınızı sorgulayan ve bu türdeki kaynakları listeleyen bir `list` komutunu destekler. Örneğin, hesabınızdaki havuzları ve bir işteki görevleri listeleyebilirsiniz:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Batch hizmetini bir `list` işlemiyle sorguladığınızda döndürülen veri miktarını sınırlamak için OData yan tümcesi belirtebilirsiniz. Tüm filtreleme sunucu tarafında oluştuğu için yalnızca istediğiniz veriler aktarılır. Liste işlemleri gerçekleştirirken bant genişliğinden (ve böylece zamandan) tasarruf etmek için bu yan tümceleri kullanın.

Aşağıdaki tabloda Batch hizmeti tarafından desteklenen OData yan tümceleri listelenmiştir:

| Yan Tümce | Description |
|---|---|
| `--select-clause [select-clause]` | Her varlık için bir özellik alt kümesi döndürür. |
| `--filter-clause [filter-clause]` | Yalnızca belirtilen OData ifadesiyle eşleşen varlıklar döndürür. |
| `--expand-clause [expand-clause]` | Tek bir temel alınan REST çağrısındaki varlık bilgilerini alır. Expand yan tümcesi şu anda yalnızca `stats` özelliğini desteklemektedir. |

Bir OData yan tümcesinin nasıl kullanılacağını gösteren örnek betik için bkz. [Batch ile bir iş ve görevlerini çalıştırma](./scripts/batch-cli-sample-run-job.md).

OData yan tümceleriyle etkili liste sorguları gerçekleştirme hakkında bilgi için bkz. [Azure Batch hizmetini etkili bir şekilde sorgulama](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Azure CLI sorunlarını giderirken aşağıdaki ipuçları yardımcı olabilir:

* Herhangi bir CLI komutu için **yardım metni** almak üzere `-h` kullanın
* **Ayrıntılı** komut çıktısını görüntülemek için `-v` ve `-vv` kullanın. `-vv` bayrağı eklendiğinde Azure CLI gerçek REST isteklerini ve yanıtlarını görüntüler. Bu anahtarlar tam hata çıktısını görüntülemek için kullanışlıdır.
* `--json` seçeneği ile **komut çıktısını JSON olarak** görüntüleyebilirsiniz. Örneğin, `az batch pool show pool001 --json` seçeneği pool001'in özelliklerini JSON biçiminde gösterir. Bundan sonra bu çıktıyı kopyalayıp bir `--json-file` içinde kullanmak üzere değiştirebilirsiniz (bu makalenin başındaki JSON dosyaları kısmına bakın).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Sonraki adımlar

* Bkz. [Azure CLI belgeleri](/cli/azure).
* [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
* [CLI şablonları ve dosya aktarımı Azure Batch kullanım](batch-cli-templates.md)kodu yazmadan havuzlar, işler ve görevler oluşturmak için Batch şablonlarını kullanma hakkında bilgi edinin.

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: /rest/api/batchservice/
[rest_add_pool]: /rest/api/batchservice/pool/add
