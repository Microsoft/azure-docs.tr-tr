---
title: Azure depolama hesabınız için özel bir etki alanı adı yapılandırma | Microsoft Docs
description: Kendi kurallı adınızı (CNAME) blob depolamaya veya bir Azure depolama hesabındaki Web uç noktasına eşlemek için Azure portal kullanın.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 2359befc05bff867a8f8b17943ed67d906ff4971
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534333"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Azure depolama hesabınız için özel bir etki alanı adı yapılandırma

Azure Depolama hesabınızdaki blob verilerine erişmek için özel bir etki alanı yapılandırabilirsiniz. Azure Blob depolama için varsayılan uç nokta  *\<depolama hesabı-adı >. blob. Core. Windows. net*' dir. [Statik Web siteleri özelliğinin](storage-blob-static-website.md)bir parçası olarak oluşturulan Web uç noktasını da kullanabilirsiniz. *Www\.contoso.com*gibi özel bir etki alanı ve alt etki alanını depolama hesabınıza yönelik blob veya Web uç noktasına eşleştirdiğinizde, kullanıcılarınız bu etki alanını Depolama hesabınızdaki blob verilerine erişmek için kullanabilir.

> [!IMPORTANT]
> Azure depolama, özel etki alanları ile HTTPS 'yi henüz yerel olarak desteklemez. Şu anda, [https üzerinden özel etki alanları kullanarak bloblara erişmek için Azure CDN kullanabilirsiniz](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Depolama hesapları Şu anda hesap başına yalnızca bir özel etki alanı destekler. Özel bir etki alanı adını hem Web hem de blob hizmeti uç noktalarına eşleyemezsiniz.
> 
> [!NOTE]
> Eşleme yalnızca alt etki alanları için (ör. www\.contoso.com) çalışır. Web uç noktanızın kök etki alanında (ör. contoso.com) kullanılabilir olmasını istiyorsanız, [Azure CDN uç noktanıza özel bir etki alanı eklemeniz](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain)gerekir.

Aşağıdaki tabloda, *mystorageaccount*adlı bir depolama hesabında bulunan BLOB verileri için birkaç örnek URL gösterilmektedir. Depolama hesabı için kaydedilen özel alt etki alanı *www\.contoso.com*:

| Kaynak türü | Varsayılan URL | Özel etki alanı URL 'SI |
| --- | --- | --- |
| Depolama hesabı | http:\//mystorageaccount.blob.Core.Windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.Core.Windows.net/myContainer/myblob | http:\//www.contoso.com/myContainer/myblob |
| Kök kapsayıcı | http:\//mystorageaccount.blob.Core.Windows.net/myblob veya http:\//mystorageaccount.blob.Core.Windows.net/$root/myblob | http:\//www.contoso.com/myblob veya http:\//www.contoso.com/$root/myblob |
| Web |  http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web/[ındexdoc] veya http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/[ındexdoc] veya http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web veya http:\//mystorageaccount. [ Bölge]. Web. Core. Windows. net/ | http:\//www.contoso.com/$Web veya http:\/\//www.contoso.com/ya da http:/www.contoso.com/$Web/[ındexdoc] veya http\/:/www.contoso.com/[ındexdoc] |

> [!NOTE]  
> Aşağıdaki bölümlerde gösterildiği gibi, blob hizmeti uç noktası için tüm örnekler Web hizmeti uç noktası için de geçerlidir.

## <a name="direct-vs-intermediary-cname-mapping"></a>Doğrudan vs. ara CNAME eşlemesi

Özel etki alanınızı (ör. www\.contoso.com), depolama hesabınızın blob uç noktasına iki şekilde kullanabilirsiniz: 
* Doğrudan CNAME eşlemesini kullanın.
* *Asverify* ara alt etki alanını kullanın.

### <a name="direct-cname-mapping"></a>Doğrudan CNAME eşleme

İlk ve en basit yöntem, özel etki alanınızı ve alt etki alanınızı doğrudan blob uç noktasına eşleyen bir kurallı ad (CNAME) kaydı oluşturmaktır. CNAME kaydı, bir kaynak etki alanını hedef etki alanına eşleyen bir etki alanı adı sistemi (DNS) özelliğidir. Örneğimizde, kaynak etki alanı kendi özel etki alanınız ve alt etki alanıdır (örneğin,*www\.contoso.com*). Hedef etki alanı, blob hizmeti uç noktanız (örneğin,*mystorageaccount.blob.Core.Windows.net*).

Doğrudan yöntemi "özel etki alanı kaydetme" bölümünde ele alınmıştır.

### <a name="intermediary-mapping-with-asverify"></a>*Asverify* ile ara eşleme

İkinci yöntem de CNAME kayıtlarını kullanır. Ancak kapalı kalma süresini önlemek için öncelikle Azure tarafından tanınan özel bir alt etki alanı kullanır.

Özel etki alanınızı bir blob uç noktasıyla eşleştirmek, etki alanını [Azure Portal](https://portal.azure.com)kaydederken kısa bir süre kapalı kalma süresine neden olabilir. Etki alanı, sıfır kesinti gerektiren bir hizmet düzeyi sözleşmesi (SLA) olan bir uygulamayı destekliyorsa, ara kayıt adımı olarak Azure *asverify* alt etki alanını kullanın. Bu adım, DNS eşlemesi gerçekleştiğinde kullanıcıların etki alanına erişebilmesini sağlar.

Aracı yöntemi, *asverify* alt etki alanını kullanarak özel bir etki alanını kaydetme bölümünde ele alınmıştır.

## <a name="register-a-custom-domain"></a>Özel bir etki alanını kaydetme
Aşağıdaki deyimler uygulanacaksa, bu bölümdeki yordamı kullanarak etki alanını kaydedin:
* Etki alanının kullanıcılarınız için kısa bir süre içinde kullanılamadığına ilişkin hiçbir endişeniz yok.
* Özel etki alanınız Şu anda bir uygulama barındırmıyor. 

Azure Blob depolduğunuz özel bir DNS adını yapılandırmak için Azure DNS kullanabilirsiniz. Daha fazla bilgi için bkz. [Bir Azure hizmeti için özel etki alanı ayarları sağlamak üzere Azure DNS'yi kullanma](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Özel etki alanınız Şu anda herhangi bir kesinti süresi olmayan bir uygulamayı destekliyorsa, *asverify* alt etki alanını kullanarak özel bir etki alanı kaydetme bölümündeki yordamı kullanın.

Özel bir etki alanı adı yapılandırmak için DNS 'de yeni bir CNAME kaydı oluşturun. CNAME kaydı, bir etki alanı adı için bir diğer ad belirtir. Örneğimizde, kendi özel etki alanınızı, depolama hesabınızın BLOB depolama uç noktasıyla eşleştirir.

Etki alanının DNS ayarlarını, etki alanı Kaydedicinizin Web sitesinde genellikle yönetebilirsiniz. Her kaydedici, bir CNAME kaydı belirtmenin benzer ancak biraz farklı bir yöntemine sahiptir, ancak kavram aynıdır. Bazı temel etki alanı kayıt paketleri DNS yapılandırması sunmadığından, CNAME kaydını oluşturabilmeniz için etki alanı kayıt paketinizi yükseltmeniz gerekebilir.

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

1. Menü bölmesinde, **BLOB hizmeti**altında **özel etki alanı**' nı seçin.  
   **Özel etki alanı** bölmesi açılır.

1. Etki alanı Kaydedicinizin Web sitesinde oturum açın ve sonra DNS 'yi yönetme sayfasına gidin.  
   Sayfayı, **etki alanı adı**, **DNS**ya da **ad sunucusu yönetimi**adlı bir bölümde bulabilirsiniz.

1. CNAME'leri yönetme ile ilgili bölümü bulun.  
   Gelişmiş ayarlar sayfasına gitmeniz ve **CNAME**, **diğer ad**veya alt **etki alanlarını**aramanız gerekebilir.

1. Yeni bir CNAME kaydı oluşturun, **www** veya **Fotoğraflar** gibi bir alt etki alanı diğer adı girin (alt etki alanı gereklidir, kök etki alanları desteklenmez) ve ardından bir konak adı belirtin.  
   Ana bilgisayar adı, blob hizmeti uç noktanıza ait. *Biçimi\<mystorageaccount >. blob. Core. Windows. net*, burada *mystorageaccount* , depolama hesabınızın adıdır. Kullanılacak ana bilgisayar adı, [Azure Portal](https://portal.azure.com) **özel etki alanı** bölmesinin öğe #1 görünür. 

1. **Özel etki alanı** bölmesinde, metin kutusuna, alt etki alanı dahil olmak üzere özel etki alanınızın adını girin.  
   Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www*ise, **www\.contoso.com**girin. Alt etki alanı *fotoğraflarınız*ise, **photos.contoso.com**girin.

1. Özel etki alanınızı kaydetmek için **Kaydet**' i seçin.  
   Kayıt başarılı olursa, Portal, depolama hesabınızın başarıyla güncelleştirildiğini size bildirir.

Yeni CNAME kaydınız DNS aracılığıyla yayıldıktan sonra, kullanıcılarınız uygun izinlere sahipse, özel etki alanınızı kullanarak blob verilerini görüntüleyebilirler.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>*Asverify* alt etki alanını kullanarak özel bir etki alanı kaydetme
Özel etki alanınız Şu anda bir SLA 'nın kapalı kalma süresi olmaması gereken bir uygulamayı destekliyorsa, bu bölümdeki yordamı kullanarak özel etki alanınızı kaydedin. Asverify 'tan işaret eden bir CNAME oluşturarak *.\< alt etki alanı >. \<customdomain >* , *asverify.\< storageaccount >. blob. Core. Windows. net*, etki alanınızı Azure 'a önceden kaydedebilirsiniz. Daha sonra, alt *\<etki alanı >\< işaret eden ikinci bir CNAME oluşturabilirsiniz.*  *\<storageaccount >. blob. Core. Windows. net*' e etki alanı > ve ardından özel etki alanı için trafik, blob uç noktanıza yönlendirilir.

*Asverify* alt etki alanı, Azure tarafından tanınan özel bir alt etki alanıdır. Kendi alt etki alanınızı önceden bekleyen bir şekilde, Azure 'un etki alanı için DNS kaydını değiştirmek zorunda kalmadan özel etki alanınızı tanımasına izin verirsiniz. Etki alanı için DNS kaydını değiştirdiğinizde, işlem kesinti olmadan blob uç noktasına eşleştirilir.

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

1. Menü bölmesinde, **BLOB hizmeti**altında **özel etki alanı**' nı seçin.  
   **Özel etki alanı** bölmesi açılır.

1. DNS sağlayıcınızın web sitesinde oturum açın ve sonra DNS 'yi yönetme sayfasına gidin.  
   Sayfayı, **etki alanı adı**, **DNS**ya da **ad sunucusu yönetimi**adlı bir bölümde bulabilirsiniz.

1. CNAME'leri yönetme ile ilgili bölümü bulun.  
   Gelişmiş ayarlar sayfasına gitmeniz ve **CNAME**, **diğer ad**veya alt **etki alanlarını**aramanız gerekebilir.

1. Yeni bir CNAME kaydı oluşturun, asverify **. www** veya **asverify. fotoğraflar**gibi *asverify* alt etki alanını içeren bir alt etki alanı diğer adı belirtin ve ardından bir ana bilgisayar adı girin.  
   Ana bilgisayar adı, blob hizmeti uç noktanıza ait. Biçimi *asverify.\< mystorageaccount >. blob. Core. Windows. net*, burada *mystorageaccount* , depolama hesabınızın adıdır. Kullanılacak ana bilgisayar adı, [Azure Portal](https://portal.azure.com) *özel etki alanı* bölmesinin öğe #2 görünür.

1. **Özel etki alanı** bölmesinde, metin kutusuna, alt etki alanı dahil olmak üzere özel etki alanınızın adını girin.  
   *Asverify*eklemeyin. Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www*ise, **www\.contoso.com**girin. Alt etki alanı *fotoğraflarınız*ise, **photos.contoso.com**girin.

1. **Dolaylı CNAME doğrulaması kullan** onay kutusunu seçin.

1. Özel etki alanınızı kaydetmek için **Kaydet**' i seçin.  
   Kayıt başarılı olursa, Portal, depolama hesabınızın başarıyla güncelleştirildiğini size bildirir. Özel etki alanınız Azure tarafından doğrulandı, ancak etki alanınız için olan trafik henüz depolama hesabınıza yönlendirilmiyor.

1. DNS sağlayıcınızın web sitesine dönün ve ardından alt etki alanınızı blob hizmeti uç noktanızla eşleyen başka bir CNAME kaydı oluşturun.  
   Örneğin, alt etki alanını *www* veya *Fotoğraflar* olarak belirtin ( *asverify*olmadan) ve ana bilgisayar adını  *\<mystorageaccount >. blob. Core. Windows. net*olarak belirtin; burada *mystorageaccount* , depolama hesabı. Bu adımla, özel etki alanınızı kaydetme işlemi tamamlanmıştır.

1. Son olarak, yalnızca bir ara adım olarak gerekli olan *asverify* alt etki alanını içeren yenı oluşturulan CNAME kaydını silebilirsiniz.

Yeni CNAME kaydınız DNS aracılığıyla yayıldıktan sonra, kullanıcılarınız uygun izinlere sahipse, özel etki alanınızı kullanarak blob verilerini görüntüleyebilirler.

## <a name="test-your-custom-domain"></a>Özel etki alanınızı test etme

Özel etki alanının blob hizmeti uç noktanıza eşlendiğinden emin olmak için, depolama hesabınızda ortak kapsayıcıda bir blob oluşturun. Daha sonra, bir Web tarayıcısında aşağıdaki biçimde bir URI kullanarak bloba erişin:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Örneğin, *photos.contoso.com* özel alt etki alanındaki *MyForms* kapsayıcısındaki bir Web formuna erişmek için aşağıdaki URI 'yi kullanabilirsiniz:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Özel bir etki alanı kaydını kaldırma

BLOB depolama uç noktanız için özel bir etki alanı kaydını silmek için aşağıdaki yordamlardan birini kullanın.

### <a name="azure-portal"></a>Azure portal

Özel etki alanı ayarını kaldırmak için şunları yapın:

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

1. Menü bölmesinde, **BLOB hizmeti**altında **özel etki alanı**' nı seçin.  
   **Özel etki alanı** bölmesi açılır.

1. Özel etki alanı adınızı içeren metin kutusunun içeriğini temizleyin.

1. **Kaydet** düğmesini seçin.

Özel etki alanı başarıyla kaldırıldıktan sonra, depolama hesabınızın başarıyla güncelleştirildiğini belirten bir portal bildirimi görürsünüz.

### <a name="azure-cli"></a>Azure CLI

Özel bir etki alanı kaydını kaldırmak için [az Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI komutunu kullanın ve sonra`""` `--custom-domain` bağımsız değişken değeri için boş bir dize () belirtin.

* Komut biçimi:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Komut örneği:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Özel bir etki alanı kaydını kaldırmak için [set-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 'ini kullanın ve sonra`""` `-CustomDomainName` bağımsız değişken değeri için boş bir dize () belirtin.

* Komut biçimi:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Komut örneği:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Sonraki adımlar
* [Özel bir etki alanını Azure Content Delivery Network (CDN) uç noktasına eşleme](../../cdn/cdn-map-content-to-custom-domain.md)
* [HTTPS üzerinden özel etki alanları kullanarak bloblara erişmek için Azure CDN kullanma](storage-https-custom-domain-cdn.md)
* [Azure Blob depolama 'da (Önizleme) statik Web sitesi barındırma](storage-blob-static-website.md)
