---
title: Özel bir etki alanını Azure Blob depolama uç noktasıyla eşleme
titleSuffix: Azure Storage
description: Özel bir etki alanını bir BLOB depolama alanına veya bir Azure depolama hesabındaki Web uç noktasına eşleyin.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538350"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Özel bir etki alanını Azure Blob depolama uç noktasıyla eşleme

Özel bir etki alanını blob hizmeti uç noktası veya [statik bir Web sitesi](storage-blob-static-website.md) uç noktası ile eşleyebilirsiniz. 

> [!NOTE] 
> Bu eşleme yalnızca alt etki alanları için geçerlidir (örneğin: `www.contoso.com` ). Web uç noktanızın kök etki alanında kullanılabilir olmasını istiyorsanız (örneğin: `contoso.com` ), Azure CDN kullanmanız gerekir. Rehberlik için, bu makalenin [https özellikli özel etki alanı eşleştirme](#enable-https) bölümüne bakın. Özel etki alanınızı kök etki alanını etkinleştirmek için bu makalenin bu bölümüne gittiğinden, HTTPS 'yi etkinleştirmek için bu bölümün içindeki adım isteğe bağlıdır. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Özel bir etki alanını yalnızca HTTP etkin ile eşleme

Bu yaklaşım daha kolaydır, ancak yalnızca HTTP erişimini sağlar. Depolama hesabı HTTPS üzerinden [Güvenli aktarım gerektirecek](../common/storage-require-secure-transfer.md) şekilde yapılandırıldıysa, özel etki ALANıNıZ için HTTPS erişimini etkinleştirmeniz gerekir. 

HTTPS erişimini etkinleştirmek için bu makalenin [https etkin olduğu özel etki alanını eşleme](#enable-https) bölümüne bakın. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Özel bir etki alanını eşleme

> [!IMPORTANT]
> Yapılandırmayı tamamladığınız sırada özel etki alanınız kullanıcılara kısaca kullanılamayacak. Etki alanınız Şu anda sıfır kapalı kalma süresi gerektiren bir hizmet düzeyi sözleşmesi (SLA) olan bir uygulamayı destekliyorsa, DNS eşlemesi gerçekleşirken kullanıcıların etki alanına erişebildiğinden emin olmak için bu makalenin bu makalede yer alan [özel bir etki alanını eşleme Ile eşle](#zero-down-time) bölümünde yer alan adımları izleyin.

Etki alanının kullanıcılarınız için kısa bir süre içinde kullanılamadığına ilişkin endişeleriniz varsa, bu adımları izleyin.

: heavy_check_mark: 1. Adım: depolama uç noktanızın ana bilgisayar adını alın.

: heavy_check_mark: adım 2: etki alanı sağlayıcınızda kurallı ad (CNAME) kaydı oluşturma.

: heavy_check_mark: 3. Adım: özel etki alanını Azure 'a kaydetme. 

: heavy_check_mark: 4. Adım: özel etki alanınızı test etme.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. Adım: depolama uç noktanızın ana bilgisayar adını alın 

Ana bilgisayar adı, protokol tanımlayıcısı ve sondaki eğik çizgi olmayan depolama uç noktası URL 'sidir. 

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

2. Menü bölmesinde, **Ayarlar**' ın altında **Özellikler**' i seçin.  

3. **Birincil blob hizmeti uç noktası** veya **birincil statik Web sitesi uç noktası** değerini bir metin dosyasına kopyalayın. 
  
   > [!NOTE]
   > Data Lake depolama uç noktası desteklenmez (örneğin: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Protokol tanımlayıcısını (örneğin: `HTTPS` ) ve sondaki eğik çizgiyi bu dizeden kaldırın. Aşağıdaki tablo örnekleri içerir.

   | Uç nokta türü |  endpoint | Ana bilgisayar adı |
   |------------|-----------------|-------------------|
   |blob hizmeti  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statik web sitesi  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Bu değeri daha sonra için bir kenara ayarlayın.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2. Adım: etki alanı sağlayıcınızla kurallı ad (CNAME) kaydı oluşturma

Ana bilgisayar adınızı göstermek için bir CNAME kaydı oluşturun. CNAME kaydı, bir kaynak etki alanı adını hedef etki alanı adıyla eşleyen bir etki alanı adı sistemi (DNS) kaydı türüdür.

1. Etki alanı Kaydedicinizin Web sitesinde oturum açın ve sonra DNS ayarlarını yönetme sayfasına gidin.

   Sayfayı, **etki alanı adı**, **DNS** ya da **ad sunucusu yönetimi** adlı bir bölümde bulabilirsiniz.

2. CNAME kayıtlarını yönetme bölümünü bulun. 

   Gelişmiş ayarlar sayfasına gitmeniz ve **CNAME**, **diğer ad** veya alt **etki alanlarını** aramanız gerekebilir.

3. CNAME kaydı oluşturun. Bu kaydın bir parçası olarak, aşağıdaki öğeleri sağlayın: 

   - Veya gibi alt etki alanı diğer adı `www` `photos` . Alt etki alanı gereklidir, kök etki alanları desteklenmez. 
      
   - Bu makalenin önceki kısımlarında yer alan [depolama uç noktanızın ana bilgisayar adını alın](#endpoint) bölümünde elde ettiğiniz ana bilgisayar adı. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3. Adım: özel etki alanınızı Azure 'a kaydetme

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

2. Menü bölmesinde, **BLOB hizmeti** altında **özel etki alanı**' nı seçin.

   > [!NOTE]
   > Hiyerarşik ad alanı özelliği etkin olan hesaplarda bu seçenek görünmez. Bu hesaplar için, bu adımı gerçekleştirmek için PowerShell veya Azure CLı kullanın.

   ![Özel etki alanı seçeneği](./media/storage-custom-domain-name/custom-domain-button.png "Özel etki alanı")

   **Özel etki alanı** bölmesi açılır.

3. **Etki alanı adı** metin kutusuna, alt etki alanı dahil olmak üzere özel etki alanınızın adını girin  
   
   Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

4. Özel etki alanını kaydetmek için **Kaydet** düğmesini seçin.

   CNAME kaydı, etki alanı adı sunucuları (DNS) üzerinden yayıldıktan sonra ve kullanıcılarınız uygun izinlere sahipse, özel etki alanını kullanarak blob verilerini görüntüleyebilirler.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell komutunu çalıştırın

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- `<resource-group-name>`Yer tutucusunu kaynak grubunun adıyla değiştirin.

- `<storage-account-name>`Yer tutucusunu depolama hesabının adıyla değiştirin.

- Yer tutucusunu, alt etki `<custom-domain-name>` alanı dahil olmak üzere özel etki alanınızın adıyla değiştirin.

  Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

CNAME kaydı, etki alanı adı sunucuları (DNS) üzerinden yayıldıktan sonra ve kullanıcılarınız uygun izinlere sahipse, özel etki alanını kullanarak blob verilerini görüntüleyebilirler.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki PowerShell komutunu çalıştırın

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- `<resource-group-name>`Yer tutucusunu kaynak grubunun adıyla değiştirin.

- `<storage-account-name>`Yer tutucusunu depolama hesabının adıyla değiştirin.

- Yer tutucusunu, alt etki `<custom-domain-name>` alanı dahil olmak üzere özel etki alanınızın adıyla değiştirin.

  Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

CNAME kaydı, etki alanı adı sunucuları (DNS) üzerinden yayıldıktan sonra ve kullanıcılarınız uygun izinlere sahipse, özel etki alanını kullanarak blob verilerini görüntüleyebilirler.

---

#### <a name="step-4-test-your-custom-domain"></a>4. Adım: özel etki alanınızı test etme

Özel etki alanının blob hizmeti uç noktanıza eşlendiğinden emin olmak için, depolama hesabınızda ortak kapsayıcıda bir blob oluşturun. Daha sonra, bir Web tarayıcısında aşağıdaki biçimde bir URI kullanarak bloba erişin: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Örneğin, `myforms` *photos.contoso.com* özel alt etki alanındaki kapsayıcıda bir Web formuna erışmek için aşağıdaki URI 'yi kullanabilirsiniz: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Özel bir etki alanını sıfır kapalı kalma süresiyle eşleme

> [!NOTE]
> Etki alanının kullanıcılarınız için kısa bir süre içinde kullanılamadığına ilişkin endişeleriniz varsa, bu makalenin [özel etki alanı eşleme](#map-a-domain) bölümünde yer alan adımları kullanmayı göz önünde bulundurun. Daha az adımla daha basit bir yaklaşımdır.  

Etki alanınız Şu anda sıfır kesinti gerektiren bir hizmet düzeyi sözleşmesi (SLA) olan bir uygulamayı destekliyorsa, DNS eşlemesi gerçekleşirken kullanıcıların etki alanına erişebildiğinden emin olmak için aşağıdaki adımları izleyin. 

: heavy_check_mark: 1. Adım: depolama uç noktanızın ana bilgisayar adını alın.

: heavy_check_mark: adım 2: etki alanı sağlayıcınızla bir ara kurallı ad (CNAME) kaydı oluşturma.

: heavy_check_mark: 3. Adım: özel etki alanını Azure ile önceden kaydedin.

: heavy_check_mark: 4. Adım: etki alanı sağlayıcınızla bir CNAME kaydı oluşturma.

: heavy_check_mark: 5. Adım: özel etki alanınızı test edin.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. Adım: depolama uç noktanızın ana bilgisayar adını alın 

Ana bilgisayar adı, protokol tanımlayıcısı ve sondaki eğik çizgi olmayan depolama uç noktası URL 'sidir. 

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

2. Menü bölmesinde, **Ayarlar**' ın altında **Özellikler**' i seçin.  

3. **Birincil blob hizmeti uç noktası** veya **birincil statik Web sitesi uç noktası** değerini bir metin dosyasına kopyalayın. 

   > [!NOTE]
   > Data Lake depolama uç noktası desteklenmez (örneğin: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Protokol tanımlayıcısını (örneğin: `HTTPS` ) ve sondaki eğik çizgiyi bu dizeden kaldırın. Aşağıdaki tablo örnekleri içerir.

   | Uç nokta türü |  endpoint | Ana bilgisayar adı |
   |------------|-----------------|-------------------|
   |blob hizmeti  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statik web sitesi  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Bu değeri daha sonra için bir kenara ayarlayın.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2. Adım: etki alanı sağlayıcınızla bir ara kurallı ad (CNAME) kaydı oluşturma

Ana bilgisayar adınızı göstermek için geçici bir CNAME kaydı oluşturun. CNAME kaydı, bir kaynak etki alanı adını hedef etki alanı adına eşleyen bir DNS kaydı türüdür.

1. Etki alanı Kaydedicinizin Web sitesinde oturum açın ve sonra DNS ayarlarını yönetme sayfasına gidin.

   Sayfayı, **etki alanı adı**, **DNS** ya da **ad sunucusu yönetimi** adlı bir bölümde bulabilirsiniz.

2. CNAME kayıtlarını yönetme bölümünü bulun. 

   Gelişmiş ayarlar sayfasına gitmeniz ve **CNAME**, **diğer ad** veya alt **etki alanlarını** aramanız gerekebilir.

3. CNAME kaydı oluşturun. Bu kaydın bir parçası olarak, aşağıdaki öğeleri sağlayın: 

   - Veya gibi alt etki alanı diğer adı `www` `photos` . Alt etki alanı gereklidir, kök etki alanları desteklenmez.

     `asverify`Diğer ada alt etki alanı ekleyin. Örneğin: `asverify.www` veya `asverify.photos` .
       
   - Bu makalenin önceki kısımlarında yer alan [depolama uç noktanızın ana bilgisayar adını alın](#endpoint) bölümünde elde ettiğiniz ana bilgisayar adı. 

     Konak adının alt etki alanını ekleyin `asverify` . Örneğin: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3. Adım: özel etki alanınızı Azure ile ön kaydetme

Özel etki alanınızı Azure 'a önceden kaydettiğinizde, Azure 'un etki alanı için DNS kaydını değiştirmek zorunda kalmadan özel etki alanınızı tanımasına izin verirsiniz. Bu şekilde, etki alanı için DNS kaydını değiştirdiğinizde, işlem kesinti olmadan blob uç noktasına eşleştirilir.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

2. Menü bölmesinde, **BLOB hizmeti** altında **özel etki alanı**' nı seçin.

   > [!NOTE]
   > Hiyerarşik ad alanı özelliği etkin olan hesaplarda bu seçenek görünmez. Bu hesaplar için, bu adımı gerçekleştirmek için PowerShell veya Azure CLı kullanın.

   ![Özel etki alanı seçeneği](./media/storage-custom-domain-name/custom-domain-button.png "Özel etki alanı")

   **Özel etki alanı** bölmesi açılır.

3. **Etki alanı adı** metin kutusuna, alt etki alanı dahil olmak üzere özel etki alanınızın adını girin  
   
   Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

4. **Dolaylı CNAME doğrulaması kullan** onay kutusunu seçin.

5. Özel etki alanını kaydetmek için **Kaydet** düğmesini seçin.
  
   Kayıt başarılı olursa, Portal, depolama hesabınızın başarıyla güncelleştirildiğini size bildirir. Özel etki alanınız Azure tarafından doğrulandı, ancak etki alanı sağlayıcınızla bir CNAME kaydı oluşturana kadar etki alanına giden trafik henüz depolama hesabınıza yönlendirilmemiştir. Bunu bir sonraki bölümde yapacaksınız.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell komutunu çalıştırın

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- `<resource-group-name>`Yer tutucusunu kaynak grubunun adıyla değiştirin.

- `<storage-account-name>`Yer tutucusunu depolama hesabının adıyla değiştirin.

- Yer tutucusunu, alt etki `<custom-domain-name>` alanı dahil olmak üzere özel etki alanınızın adıyla değiştirin.

  Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

Etki alanı sağlayıcınızla bir CNAME kaydı oluşturana kadar, etki alanına giden trafik henüz depolama hesabınıza yönlendirilmemiştir. Bunu bir sonraki bölümde yapacaksınız.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki PowerShell komutunu çalıştırın

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- `<resource-group-name>`Yer tutucusunu kaynak grubunun adıyla değiştirin.

- `<storage-account-name>`Yer tutucusunu depolama hesabının adıyla değiştirin.

- Yer tutucusunu, alt etki `<custom-domain-name>` alanı dahil olmak üzere özel etki alanınızın adıyla değiştirin.

  Örneğin, etki alanınız *contoso.com* ve alt etki alanı diğer adınız *www* ise, girin `www.contoso.com` . Alt etki alanı *fotoğraflarınız* ise, girin `photos.contoso.com` .

Etki alanı sağlayıcınızla bir CNAME kaydı oluşturana kadar, etki alanına giden trafik henüz depolama hesabınıza yönlendirilmemiştir. Bunu bir sonraki bölümde yapacaksınız.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4. Adım: etki alanı sağlayıcınızla CNAME kaydı oluşturma

Ana bilgisayar adınızı göstermek için geçici bir CNAME kaydı oluşturun.

1. Etki alanı Kaydedicinizin Web sitesinde oturum açın ve sonra DNS ayarlarını yönetme sayfasına gidin.

   Sayfayı, **etki alanı adı**, **DNS** ya da **ad sunucusu yönetimi** adlı bir bölümde bulabilirsiniz.

2. CNAME kayıtlarını yönetme bölümünü bulun. 

   Gelişmiş ayarlar sayfasına gitmeniz ve **CNAME**, **diğer ad** veya alt **etki alanlarını** aramanız gerekebilir.

3. CNAME kaydı oluşturun. Bu kaydın bir parçası olarak, aşağıdaki öğeleri sağlayın: 

   - Veya gibi alt etki alanı diğer adı `www` `photos` . Alt etki alanı gereklidir, kök etki alanları desteklenmez.
      
   - Bu makalenin önceki kısımlarında yer alan [depolama uç noktanızın ana bilgisayar adını alın](#endpoint-2) bölümünde elde ettiğiniz ana bilgisayar adı. 

#### <a name="step-5-test-your-custom-domain"></a>5. Adım: özel etki alanınızı test etme

Özel etki alanının blob hizmeti uç noktanıza eşlendiğinden emin olmak için, depolama hesabınızda ortak kapsayıcıda bir blob oluşturun. Daha sonra, bir Web tarayıcısında aşağıdaki biçimde bir URI kullanarak bloba erişin: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Örneğin, `myforms` *photos.contoso.com* özel alt etki alanındaki kapsayıcıda bir Web formuna erışmek için aşağıdaki URI 'yi kullanabilirsiniz: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Özel etki alanı eşlemesini kaldırma

Özel bir etki alanı eşlemesini kaldırmak için özel etki alanı kaydını silin. Aşağıdaki yordamlardan birini kullanın.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.

2. Menü bölmesinde, **BLOB hizmeti** altında **özel etki alanı**' nı seçin.  
   **Özel etki alanı** bölmesi açılır.

3. Özel etki alanı adınızı içeren metin kutusunun içeriğini temizleyin.

4. **Kaydet** düğmesini seçin.

Özel etki alanı başarıyla kaldırıldıktan sonra, depolama hesabınızın başarıyla güncelleştirildiğini belirten bir portal bildirimi görürsünüz.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Özel bir etki alanı kaydını kaldırmak için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 'ini kullanın ve sonra `""` bağımsız değişken değeri için boş bir dize () belirtin `-CustomDomainName` .

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

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Özel bir etki alanı kaydını kaldırmak için [az Storage Account Update](/cli/azure/storage/account) CLI komutunu kullanın ve sonra `""` bağımsız değişken değeri için boş bir dize () belirtin `--custom-domain` .

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
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>HTTPS özellikli özel bir etki alanını eşleme

Bu yaklaşım, daha fazla adım içerir, ancak HTTPS erişimini de mümkün kılar. 

Kullanıcıların HTTPS kullanarak blob veya Web içeriğinize erişmesine gerek yoksa, bu makalenin [yalnızca http özellikli bir özel etki alanını eşleme](#enable-http) bölümüne bakın. 

1. Blob veya Web uç noktanıza [Azure CDN](../../cdn/cdn-overview.md) etkinleştirin. 

   BLOB depolama uç noktası için bkz. [Azure Storage hesabını Azure CDN Ile tümleştirme](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Statik bir Web sitesi uç noktası için bkz. [Azure CDN bir statik Web sitesini tümleştirme](static-website-content-delivery-network.md).

2. [Azure CDN içeriğini özel bir etki alanıyla eşleyin](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Azure CDN özel bir etki alanında https 'Yi etkinleştirin](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Statik Web sitenizi güncelleştirdiğinizde, CDN uç noktası ' nı temizleyerek CDN uç sunucularında önbelleğe alınmış içeriği temizlemeyi unutmayın. Daha fazla bilgi için bkz. [Azure CDN uç noktasını temizleme](../../cdn/cdn-purge-endpoint.md).

4. Seçim Aşağıdaki kılavuzu gözden geçirin:

   * [Azure CDN Ile paylaşılan erişim imzası (SAS) belirteçleri](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Azure CDN Ile http-to-https yönlendirmesi](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Azure CDN Ile BLOB depolama kullanılırken fiyatlandırma ve faturalandırma](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Blob depolamada statik Web sitesi barındırma hakkında bilgi edinin](storage-blob-static-website.md)