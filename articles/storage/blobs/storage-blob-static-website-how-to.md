---
title: Azure depolama 'da statik bir Web sitesi barındırma
description: Azure Storage GPv2 hesabındaki bir kapsayıcıdan doğrudan statik içerik (HTML, CSS, JavaScript ve resim dosyaları) sunma hakkında bilgi edinin.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008510"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure depolama 'da statik bir Web sitesi barındırma

[Genel amaçlı v2](../common/storage-account-create.md) veya [blockblobstorage](storage-blob-create-account-block-blob.md) hesabındaki bir kapsayıcıdan doğrudan statik IÇERIK (HTML, CSS, JavaScript ve resim dosyaları) hizmeti sağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](storage-blob-static-website.md).

Bu makalede Azure portal, Azure CLı veya PowerShell kullanarak statik Web sitesi barındırmayı nasıl etkinleştireceğinizi gösterilmektedir.

## <a name="enable-static-website-hosting"></a>Statik Web sitesi barındırmayı etkinleştir

Statik Web sitesi barındırma, depolama hesabında etkinleştirmeniz gereken bir özelliktir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Başlamak için [Azure Portal](https://portal.azure.com/) oturum açın.

2. Depolama hesabınızı bulun ve hesaba genel bakış ' ı görüntüleyin.

3. Statik Web siteleri için yapılandırma sayfasını göstermek üzere **statik Web sitesi** ' ni seçin.

4. Depolama hesabı için statik Web sitesi barındırmayı etkinleştirmek üzere **etkin** ' i seçin.

5. **Dizin belgesi adı** alanında, bir varsayılan dizin sayfası (örneğin: *index.html*) belirtin. 

   Varsayılan dizin sayfası, bir Kullanıcı statik Web sitenizin köküne gittiğinde görüntülenir.  

6. **Hata belgesi yolu** alanında, varsayılan bir hata sayfası (örneğin: *404.html*) belirtin. 

   Varsayılan hata sayfası, bir Kullanıcı statik Web sitenizde mevcut olmayan bir sayfaya gitmeye çalıştığında görüntülenir.

7. **Kaydet**’e tıklayın. Azure portal artık statik Web sitesi uç noktanızı görüntülüyor. 

    ![Depolama hesabı için statik Web sitesi barındırmayı etkinleştirme](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

[Azure Command-Line arabirimi 'ni (CLI)](/cli/azure/)kullanarak statik Web sitesi barındırmayı etkinleştirebilirsiniz.

1. İlk olarak, [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

3. Statik Web sitesi barındırmayı etkinleştirin.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

   * `<error-document-name>`Yer tutucusunu, sitenizde mevcut olmayan bir sayfa istediğinde, kullanıcılara görüntülenecek olan hata belgesinin adıyla değiştirin.

   * `<index-document-name>`Yer tutucusunu Dizin belgesinin adıyla değiştirin. Bu belge genellikle "index.html" dir.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Azure PowerShell modülünü kullanarak statik Web sitesi barındırmayı etkinleştirebilirsiniz.

1. Bir Windows PowerShell komut penceresi açın.

2. Azure PowerShell modülünüzün az 0,7 veya sonraki bir sürümü olduğunu doğrulayın.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

3. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

4. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

5. Kullanmak istediğiniz depolama hesabını tanımlayan depolama hesabı bağlamını alın.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

6. Statik Web sitesi barındırmayı etkinleştirin.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * `<error-document-name>`Yer tutucusunu, sitenizde mevcut olmayan bir sayfa istediğinde, kullanıcılara görüntülenecek olan hata belgesinin adıyla değiştirin.

   * `<index-document-name>`Yer tutucusunu Dizin belgesinin adıyla değiştirin. Bu belge genellikle "index.html" dir.

---

## <a name="upload-files"></a>Dosyaları karşıya yükleme 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Bu yönergeler, Azure portal görüntülenen Depolama Gezgini sürümünü kullanarak dosyaları karşıya yükleme işleminin nasıl yapılacağını gösterir. Ancak, Azure portal dışında çalışan [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) sürümünü de kullanabilirsiniz. Hesabınızın **$Web** kapsayıcısına dosya yükleyebileceğiniz [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI veya herhangi bir özel uygulama kullanabilirsiniz. Visual Studio Code kullanarak dosyaları karşıya yükleyen adım adım bir öğretici için bkz. [öğretici: blob depolamada statik bir Web sitesi barındırma](./storage-blob-static-website-host.md).

1. **Depolama Gezgini (önizleme)** öğesini seçin.

2. **BLOB kapsayıcıları** düğümünü genişletin ve **$Web** kapsayıcısını seçin.

3. Dosyaları karşıya yüklemek için **karşıya yükle** düğmesini seçin.

   ![Dosyaları karşıya yükleme](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Tarayıcının dosya içeriğini görüntülemesini istiyorsanız, bu dosyanın içerik türünün olarak ayarlandığından emin olun `text/html` . 

   ![İçerik türlerini denetle](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Depolama Gezgini, bu özelliği, gibi `text/html` yaygın olarak tanınan uzantılar için otomatik olarak ayarlar `.html` . Ancak, bazı durumlarda bunu kendiniz ayarlamanız gerekecektir. Bu özelliği ' ye ayarlamazsanız tarayıcı, `text/html` kullanıcılardan içeriği işlemek yerine dosyayı indirmesini ister. Bu özelliği ayarlamak için, dosyaya sağ tıklayın ve ardından **Özellikler**' e tıklayın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nesneleri bir kaynak dizininden *$Web* kapsayıcısına yükleyin.

Bu örnekte Azure Cloud Shell oturumundan komut çalıştırdığınız varsayılır.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Tarayıcı, kullanıcıların içerikleri işlemek yerine dosyayı indirmesini isterse, `--content-type 'text/html; charset=utf-8'` komuta ekleyebilirsiniz. 

* `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

* `<source-path>`Yer tutucusunu, karşıya yüklemek istediğiniz dosyaların konumunun bir yolu ile değiştirin.

> [!NOTE]
> Azure CLı 'nin konum yüklemesini kullanıyorsanız, yerel bilgisayarınızdaki herhangi bir konumun yolunu kullanabilirsiniz (örneğin: `C:\myFolder` .
>
> Azure Cloud Shell kullanıyorsanız, Cloud Shell görünür bir dosya paylaşımıyla başvurulmalıdır. Bu konum, bulut paylaşımının kendisi veya Cloud Shell oluşturduğunuz var olan bir dosya paylaşımının dosya paylaşımıdır. Bunu nasıl yapacağınızı öğrenmek için bkz. [Azure Cloud Shell dosyaları kalıcı hale](../../cloud-shell/persisting-shell-storage.md)getirme.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nesneleri bir kaynak dizininden *$Web* kapsayıcısına yükleyin.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Tarayıcı, kullanıcıların içerikleri işlemek yerine dosyayı indirmesini isterse, `-Properties @{ ContentType = "text/html; charset=utf-8";}` komuta ekleyebilirsiniz.

* `<path-to-file>`Yer tutucu değerini, karşıya yüklemek istediğiniz dosyanın tam yolu ile değiştirin (örneğin: `C:\temp\index.html` ).

* `<blob-name>`Yer tutucu değerini, sonuçta elde edilen Blobun vermek istediğiniz adla değiştirin (örneğin: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Web sitesi URL 'sini bulma

Sitenizin sayfalarını bir tarayıcıdan, Web sitesinin genel URL 'sini kullanarak görüntüleyebilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Depolama hesabınızın firmaya Genel Bakış sayfasının yanında görünen bölmede, **statik Web sitesi**' ni seçin. Sitenizin URL 'SI **birincil uç nokta** alanında görüntülenir.

![Azure depolama statik Web siteleri ölçümleri ölçümü](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki komutu kullanarak statik Web sitenizin genel URL 'sini bulun:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

* `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki komutu kullanarak, kullanarak statik Web sitenizin genel URL 'sini bulun:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

* `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Statik Web sitesi sayfalarında ölçümleri etkinleştirme

Ölçümleri etkinleştirdikten sonra, **$Web** kapsayıcısındaki dosyalardaki trafik istatistikleri ölçüm panosunda raporlanır.

1. Depolama hesabı menüsünün Izleyici bölümünde **ölçümler** ' **e** tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Ölçümler bağlantısı](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Ölçüm verileri farklı ölçüm API 'Lerine bağlanarak oluşturulur. Portal yalnızca verileri döndüren üyelere odaklanmak için yalnızca belirli bir zaman çerçevesinde kullanılan API üyelerini görüntüler. Gerekli API üyesini seçebildiğinizden emin olmak için ilk adım zaman çerçevesini genişletmelidir.

2. Zaman çerçevesi düğmesine tıklayın, bir zaman dilimi seçin ve ardından **Uygula**' ya tıklayın.

   ![Azure depolama statik Web siteleri ölçümleri zaman aralığı](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. *Ad alanı* açılan listesinden **BLOB** ' u seçin.

   ![Azure depolama statik Web siteleri ölçümleri ad alanı](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ardından **Çıkış** ölçümünü seçin.

   ![Azure Storage statik Web siteleri çıkış ölçümünü gösteren ekran görüntüsü.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *Toplama* seçicideki **Toplam** ' u seçin.

   ![Azure depolama statik Web siteleri ölçümleri toplama](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. **Filtre Ekle** düğmesine tıklayın ve *özellik* seçicideki **API adı** ' nı seçin.

   ![Azure depolama statik Web siteleri ölçümleri API adı](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Ölçüm raporunu doldurmak için *değerler* seçicisindeki **getwebcontent** seçeneğinin yanındaki kutuyu işaretleyin.

   ![Azure depolama statik Web siteleri ölçümleri GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > **Getwebcontent** onay kutusu yalnızca söz konusu API üyesinin belirli bir zaman çerçevesinde kullanılması durumunda görünür. Portal yalnızca verileri döndüren üyelere odaklanmak için yalnızca belirli bir zaman çerçevesinde kullanılan API üyelerini görüntüler. Bu listede belirli bir API üyesini bulamıyorsanız, zaman çerçevesini genişletin.

## <a name="next-steps"></a>Sonraki adımlar

* Statik Web siteniz ile özel bir etki alanı yapılandırmayı öğrenin. Bkz. [özel bir etki alanını Azure Blob depolama uç noktası Ile eşleme](storage-custom-domain-name.md).