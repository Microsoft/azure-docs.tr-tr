---
title: Azure Blob 'larına veri aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure Bloblarına ve Azure Blob 'larına veri aktarmak için Azure portal ' de içeri ve dışarı aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875692"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Azure Blob depolama alanına veri aktarmak için Azure Içeri/dışarı aktarma hizmetini kullanma

Bu makalede, Azure Içeri/dışarı aktarma hizmeti 'ni kullanarak büyük miktarlarda verileri Azure Blob depolamaya güvenli bir şekilde aktarmak için adım adım yönergeler sağlanmaktadır. Azure Bloblarına veri aktarmak için hizmet, verilerinizi içeren şifrelenmiş disk sürücülerinin bir Azure veri merkezine sevk etmeniz gerekir.

## <a name="prerequisites"></a>Önkoşullar

Verileri Azure Blob depolamaya aktarmak üzere bir içeri aktarma işi oluşturmadan önce, bu hizmet için aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun.
Şunları yapmanız gerekir:

* Içeri/dışarı aktarma hizmeti için kullanılabilen etkin bir Azure aboneliğine sahip olun.
* Depolama kapsayıcısı olan en az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın.
  * Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md).
  * Depolama kapsayıcısı hakkında daha fazla bilgi için, [depolama kapsayıcısı oluşturma](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)bölümüne gidin.
* [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır.
* [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sistemine sahiptir.
* Windows sisteminde BitLocker 'ı etkinleştirin. Bkz. [BitLocker 'ı etkinleştirme](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* Windows sisteminde [en son Waımportexport sürüm 1 ' i indirin](https://www.microsoft.com/download/details.aspx?id=42659) . Aracının en son sürümünde, BitLocker anahtarı ve güncelleştirilmiş kilit açma modu özelliği için bir dış koruyucunun kullanılmasına izin veren güvenlik güncelleştirmeleri vardır.

  * Varsayılan klasöre ayıklayın `waimportexportv1` . Örneğin, `C:\WaImportExportV1`.
* FedEx/DHL hesabınız olmalıdır. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, ' de Azure Data Box Işlemler ekibine başvurun `adbops@microsoft.com` .
  * Hesap geçerli olmalıdır, bakiyesi olmalıdır ve dönüş teslim özelliklerine sahip olmalıdır.
  * Dışarı aktarma işi için bir izleme numarası oluştur.
  * Her iş ayrı bir izleme numarasına sahip olmalıdır. Aynı izleme numarasına birden fazla işin eklenmesi desteklenmez.
  * Bir taşıyıcı hesabınız yoksa şuraya gidin:
    * [FedEx hesabı oluşturun](https://www.fedex.com/en-us/create-account.html)veya
    * [BIR DHL hesabı oluşturun](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1. Adım: sürücüleri hazırlama

Bu adım bir günlük dosyası oluşturur. Günlük dosyası, sürücü seri numarası, şifreleme anahtarı ve depolama hesabı ayrıntıları gibi temel bilgileri depolar.

Sürücüleri hazırlamak için aşağıdaki adımları gerçekleştirin.

1. Windows sistemine SATA bağlayıcıları aracılığıyla disk sürücülerinizi bağlayın.
2. Her sürücüde tek bir NTFS birimi oluşturun. Birime bir sürücü harfi atayın. Bağlama noktalarını kullanmayın.
3. NTFS biriminde BitLocker şifrelemesini etkinleştirin. Windows Server sistemi kullanıyorsanız, [Windows server 2012 R2 'de BitLocker 'ı etkinleştirme](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)bölümündeki yönergeleri kullanın.
4. Verileri şifrelenmiş birime kopyalama. Sürükle ve bırak veya Robocopy ya da böyle bir kopyalama aracını kullanın. Bir günlük (*. jrn*) dosyası, aracı çalıştırdığınız klasörde oluşturulur.

   Sürücü kilitliyse ve sürücünün kilidini açmanız gerekiyorsa, kullanım durumunuza bağlı olarak kilit açma adımları farklı olabilir.

   * Önceden şifrelenen bir sürücüye veri eklediyseniz (Waımportexport aracı şifreleme için kullanılmazsa) sürücünün kilidini açmak için açılan pencerede BitLocker anahtarını (belirttiğiniz sayısal bir parola) kullanın.

   * Waımportexport aracı tarafından şifrelenen bir sürücüye veri eklediyseniz, sürücünün kilidini açmak için aşağıdaki komutu kullanın:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Yönetici ayrıcalıklarına sahip bir PowerShell veya komut satırı penceresi açın. Dizini sıkıştırılmış klasöre dönüştürmek için aşağıdaki komutu çalıştırın:

    `cd C:\WaImportExportV1`
6. Sürücünün BitLocker anahtarını almak için aşağıdaki komutu çalıştırın:

    `manage-bde -protectors -get <DriveLetter>:`
7. Diski hazırlamak için aşağıdaki komutu çalıştırın. **Veri boyutuna bağlı olarak, disk hazırlığı birkaç saat sürebilir.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Bir günlük dosyası, aracı çalıştırdığınız klasörde oluşturulur. Diğer iki dosya da oluşturulur *. bir. xml* dosyası (aracı çalıştırdığınız klasör) ve bir *drive-manifest.xml* dosyası (verilerin bulunduğu klasör).

    Kullanılan parametreler aşağıdaki tabloda açıklanmıştır:

    |Seçenek  |Açıklama  |
    |---------|---------|
    |/j     |. Jrn uzantılı günlük dosyasının adı. Her sürücü için bir günlük dosyası oluşturulur. Günlük dosyası adı olarak disk seri numarasını kullanmanızı öneririz.         |
    |/id     |Oturum KIMLIĞI. Komutun her örneği için benzersiz bir oturum numarası kullanın.      |
    |/t:     |Sevk edilecek diskin sürücü harfi. Örneğin, sürücü `D` .         |
    |/BK:     |Sürücünün BitLocker anahtarı. Çıktısından alınan sayısal parola `manage-bde -protectors -get D:`      |
    |/srcdir:     |Sevk edilecek diskin sürücü harfi ve ardından `:\` . Örneğin, `D:\`.         |
    |/dstdir:     |Azure depolama 'daki Hedef kapsayıcının adı.         |
    |/blobtype:     |Bu seçenek, verileri içe aktarmak istediğiniz Blobların türünü belirtir. Blok Blobları için blob türü `BlockBlob` ve sayfa Blobları için `PageBlob` .         |
    |/skipwrite:     | Kopyalanması gereken yeni bir veri olmadığını ve diskteki mevcut verilerin hazırlandığını belirtir.          |
    |/enablecontentmd5:     |Etkinleştirildiğinde, MD5 'nin hesaplanmasını ve `Content-md5` her Blobun için özellik olarak ayarlandığını sağlar. Bu seçeneği yalnızca, `Content-md5` verileri Azure 'a yükledikten sonra kullanmak istiyorsanız kullanın. <br> Bu seçenek, veri bütünlüğü denetimini etkilemez (varsayılan olarak gerçekleşir). Ayar, buluta veri yükleme süresini artırır.          |
8. Yüklenmesi gereken her disk için önceki adımı tekrarlayın. Komut satırının her çalışması için, belirtilen ada sahip bir günlük dosyası oluşturulur.

    > [!IMPORTANT]
    > * Günlük dosyası ile birlikte, `<Journal file name>_DriveInfo_<Drive serial ID>.xml` aracın bulunduğu klasörde de bir dosya oluşturulur. . Xml dosyası, günlük dosyası çok büyük olduğunda bir iş oluşturulurken günlük dosyası yerine kullanılır.
   > * Portalın izin verdiği en büyük günlük dosyası boyutu 2 MB 'tır. Günlük dosyası bu sınırı aşarsa bir hata döndürülür.

## <a name="step-2-create-an-import-job"></a>2. Adım: içeri aktarma işi oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir içeri aktarma işi oluşturmak için aşağıdaki adımları gerçekleştirin.

1. Oturum açın https://portal.azure.com/ .
2. **İçeri/dışarı aktarma işleri** için arama yapın.

   ![İçeri/dışarı aktarma işlerinde ara](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **+ Yeni** seçeneğini belirleyin.

   ![Yeni ' yi seçerek yeni bir ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **Temel bilgiler**:

   1. Bir abonelik seçin.
   1. Bir kaynak grubu seçin veya **Yeni oluştur** ' u seçip yeni bir tane oluşturun.
   1. İçeri aktarma işi için açıklayıcı bir ad girin. İşlerinizin ilerlemesini izlemek için adı kullanın.
      * Ad yalnızca küçük harf, sayı ve kısa çizgi içerebilir.
      * Ad bir harfle başlamalı ve boşluk içermemelidir.

   1. **Azure 'A aktar**' ı seçin.

    ![İçeri aktarma işi oluşturma-1. adım](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Ileri ' yi seçin: devam etmek için **iş ayrıntıları >** .

5. **İş için Ayrıntılar**:

   1. Yukarıdaki [Adım 1: sürücüleri hazırlama](#step-1-prepare-the-drives)sırasında oluşturduğunuz günlük dosyalarını karşıya yükleyin. `waimportexport.exe version1`Kullanıldıysa, hazırladığınız her sürücü için bir dosyayı karşıya yükleyin. Günlük dosyası boyutu 2 MB 'yi aşarsa, `<Journal file name>_DriveInfo_<Drive serial ID>.xml` günlük dosyası ile oluşturulan öğesini de kullanabilirsiniz.
   1. Sipariş için hedef Azure bölgesini seçin.
   1. İçeri aktarma için depolama hesabını seçin.
      
      Açılan konum, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur.
   1. Ayrıntılı bir günlük kaydetmek istemiyorsanız, **' waımportexport ' blob kapsayıcısı seçeneğinde ayrıntılı günlüğü kaydet** ' i temizleyin.

   ![İçeri aktarma işi oluşturma-2. adım](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Ileri ' yi seçin: devam etmek için **sevkıyat >** .

6. **Gönderim** aşamasında:

   1. Açılan listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. `adbops@microsoft.com`Kullanmayı planladığınız taşıyıcı ile ilgili bilgilerle birlikte Azure Data Box işlemler ekibine başvurun.
   1. Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft, içeri aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için bu hesabı kullanır. Hesap numaranız yoksa bir [FedEx](https://www.fedex.com/us/oadr/) veya [DHL](https://www.dhl.com/) taşıyıcı hesabı oluşturun.
   1.  Tümü ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, Eyalet/bölge ve ülke/bölge sağlayın.

       > [!TIP]
       > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

   ![İçeri aktarma işi oluşturma-3. adım](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Devam etmek için **gözden geçir + oluştur** ' u seçin.

7. Sipariş özetinde:

   1. **Koşulları** gözden geçirin ve "belirtilen tüm bilgilerin doğru olduğunu kabul ediyorum ve hüküm ve koşulları kabul ediyorum" seçeneğini belirleyin. Daha sonra doğrulama gerçekleştirilir.
   1. Özette belirtilen iş bilgilerini gözden geçirin. Disklerin Azure 'a geri sevk edilmesi için iş adı ve Azure veri merkezi teslimat adresini bir yere göz önünde yapın. Bu bilgiler daha sonra sevkiyat etiketinde kullanılır.
   1. **Oluştur**’u seçin.

     ![İçeri aktarma işi oluşturma-4. adım](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı 'de bir içeri aktarma işi oluşturmak için aşağıdaki adımları kullanın.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Bir iş oluşturma

1. [Az Import-Export](/cli/azure/import-export) uzantısını eklemek için [az Extension Add](/cli/azure/extension#az_extension_add) komutunu kullanın:

    ```azurecli
    az extension add --name import-export
    ```

1. Var olan bir kaynak grubunu kullanabilir veya bir tane oluşturabilirsiniz. Kaynak grubu oluşturmak için [az group create](/cli/azure/group#az_group_create) komutunu çalıştırın:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Mevcut bir depolama hesabını kullanabilir veya bir tane oluşturabilirsiniz. Bir depolama hesabı oluşturmak için [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu çalıştırın:

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. Diskleri sevk etmek için kullanabileceğiniz konumların bir listesini almak için [az Import-Export location List](/cli/azure/import-export/location#az_import_export_location_list) komutunu kullanın:

    ```azurecli
    az import-export location list
    ```

1. Bölgenize ait konumları almak için [az Import-Export konumunu göster](/cli/azure/import-export/location#az_import_export_location_show) komutunu kullanın:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. İçeri aktarma işi oluşturmak için aşağıdaki [az Import-Export Create](/cli/azure/import-export#az_import_export_create) komutunu çalıştırın:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

1. Myierg kaynak grubu için tüm işleri görmek üzere [az Import-Export List](/cli/azure/import-export#az_import_export_list) komutunu kullanın:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. İşinizi güncelleştirmek veya işinizi iptal etmek için [az Import-Export Update](/cli/azure/import-export#az_import_export_update) komutunu çalıştırın:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell içinde bir içeri aktarma işi oluşturmak için aşağıdaki adımları kullanın.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az. ımportexport** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklenmelidir `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Bir iş oluşturma

1. Var olan bir kaynak grubunu kullanabilir veya bir tane oluşturabilirsiniz. Bir kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Mevcut bir depolama hesabını kullanabilir veya bir tane oluşturabilirsiniz. Bir depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Diskleri sevk etmek için kullanabileceğiniz konumların bir listesini almak için [Get-Azımportexportlocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. `Get-AzImportExportLocation` `Name` Bölgenize ait konumları almak için cmdlet 'ini parametresiyle birlikte kullanın:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Aşağıdaki [New-Azımportexport](/powershell/module/az.importexport/new-azimportexport) örneğini çalıştırarak bir içeri aktarma işi oluşturun:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

1. Myierg kaynak grubu için tüm işleri görmek için [Get-Azımportexport](/powershell/module/az.importexport/get-azimportexport) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. İşinizi güncelleştirmek veya işinizi iptal etmek için [Update-Azımportexport](/powershell/module/az.importexport/update-azimportexport) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>3. adım (Isteğe bağlı): müşteri tarafından yönetilen anahtarı yapılandırın

Sürücülere yönelik BitLocker anahtarlarınızı korumak için Microsoft tarafından yönetilen anahtarı kullanmak istiyorsanız bu adımı atlayın ve sonraki adıma gidin. BitLocker anahtarını korumak üzere kendi anahtarınızı yapılandırmak için, [Azure Portal Azure içeri/dışarı aktarma Azure Key Vault ile müşteri tarafından yönetilen anahtarları yapılandırma](storage-import-export-encryption-key-portal.md)içindeki yönergeleri izleyin.

## <a name="step-4-ship-the-drives"></a>4. Adım: sürücüleri teslim etme

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6. Adım: verileri Azure 'a yüklemeyi doğrulama

İşi tamamlamaya göre izleyin. İş tamamlandıktan sonra verilerinizin Azure 'a yüklendiğini doğrulayın. Şirket içi verileri yalnızca karşıya yükleme işleminin başarılı olduğunu doğruladıktan sonra silin.

## <a name="next-steps"></a>Sonraki adımlar

* [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
* [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)
