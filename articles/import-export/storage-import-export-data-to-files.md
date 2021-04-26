---
title: Azure dosyaları 'na veri aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure dosyalarına veri aktarmak için Azure portal içeri aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 9c13ffc597349cdd2b304889d142ca7c2f89c713
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861544"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Dosyaları'na veri aktarmak için Azure İçeri/Dışarı Aktarma hizmetini kullanma

Bu makalede, Azure Içeri/dışarı aktarma hizmeti 'nin büyük miktarlarda verileri Azure dosyalarına güvenli bir şekilde aktarmak için nasıl kullanılacağı hakkında adım adım yönergeler sağlanmaktadır. Verileri içeri aktarmak için hizmet, verilerinizi içeren desteklenen disk sürücülerinin bir Azure veri merkezine sevk etmeniz gerekir.

Içeri/dışarı aktarma hizmeti, Azure depolama 'ya yalnızca Azure dosyalarını içeri aktarmayı destekler. Azure dosyalarını dışarı aktarma desteklenmiyor.

## <a name="prerequisites"></a>Önkoşullar

Azure dosyalarına veri aktarmaya yönelik bir içeri aktarma işi oluşturmadan önce, aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun. Şunları yapmanız gerekir:

- Içeri/dışarı aktarma hizmeti ile kullanmak için etkin bir Azure aboneliğine sahip olmanız gerekir.
- En az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın. Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md).
- [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır.
- [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sistemine sahiptir.
- Windows sisteminde [Waımportexport sürüm 2](https://aka.ms/waiev2) ' nı indirin. Varsayılan klasöre ayıklayın `waimportexport` . Örneğin, `C:\WaImportExport`.
- FedEx/DHL hesabınız olmalıdır. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, ' de Azure Data Box Işlemler ekibine başvurun `adbops@microsoft.com` .
    - Hesap geçerli olmalıdır, bakiyesi olmalıdır ve dönüş teslim özelliklerine sahip olmalıdır.
    - Dışarı aktarma işi için bir izleme numarası oluştur.
    - Her iş ayrı bir izleme numarasına sahip olmalıdır. Aynı izleme numarasına birden fazla işin eklenmesi desteklenmez.
    - Bir taşıyıcı hesabınız yoksa şuraya gidin:
        - [FedEx hesabı oluşturun](https://www.fedex.com/en-us/create-account.html)veya
        - [BIR DHL hesabı oluşturun](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>1. Adım: sürücüleri hazırlama

Bu adım bir günlük dosyası oluşturur. Günlük dosyası, sürücü seri numarası, şifreleme anahtarı ve depolama hesabı ayrıntıları gibi temel bilgileri depolar.

Sürücüleri hazırlamak için aşağıdaki adımları uygulayın.

1. Windows sistemine SATA bağlayıcıları aracılığıyla disk sürücülerimizi bağlayın.
2. Her sürücüde tek bir NTFS birimi oluşturun. Birime bir sürücü harfi atayın. Bağlama noktalarını kullanmayın.
3. Kök klasördeki *dataset.csv* dosyasını, aracın olduğu yerde değiştirin. Bir dosyayı veya klasörü veya her ikisini de içeri aktarmak istediğinize bağlı olarak, *dataset.csv* dosyasına aşağıdaki örneklere benzer girdiler ekleyin.

   - **Bir dosyayı içeri aktarmak için**: aşağıdaki örnekte kopyalanacak veriler F: sürücüsüdür. Dosya *MyFile1.txt*  , *MyAzureFileshare1* köküne kopyalanır. *MyAzureFileshare1* yoksa, Azure depolama hesabında oluşturulur. Klasör yapısı korunur.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Bir klasörü içeri aktarmak için**: *MyFolder2* altındaki tüm dosyalar ve klasörler yinelemeli olarak FileShare 'e kopyalanır. Klasör yapısı korunur.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     İçeri aktarılan klasörlere veya dosyalara karşılık gelen aynı dosyada birden çok giriş yapılabilir.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     [Veri KÜMESI CSV dosyasını hazırlama](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)hakkında daha fazla bilgi edinin.


4. Kök klasördeki *driveset.csv* dosyasını, aracın olduğu yerde değiştirin. *driveset.csv* dosyasına aşağıdaki örneklere benzer girdiler ekleyin. Sürücünün hazırlanabilmesi için disk listesini doğru bir şekilde seçmesini sağlamak üzere sürücü kümesi dosyasında diskler ve karşılık gelen sürücü harfleri bulunur.

    Bu örnek, iki diskin bağlı olduğunu ve temel NTFS birimlerinin G:\ olduğunu varsayar. ve H:\ oluşturulur. G: zaten şifrelenirken h:\şifrelenmez. Araç, H:\ barındıran diski biçimlendirir ve şifreler yalnızca (ve değil: \) .

   - **Şifrelenmeyen bir disk için**: diskte BitLocker şifrelemesini etkinleştirmek üzere *şifrelemeyi* belirtin.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Zaten şifrelenmiş bir disk için**: *Alreadyencrypted* belirtip BitLocker anahtarını sağlayın.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Birden fazla girişe karşılık gelen aynı dosyada birden çok giriş yapılabilir. [Sürücü KÜMESI CSV dosyasını hazırlama](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)hakkında daha fazla bilgi edinin.

5. `PrepImport`Verileri disk sürücüsüne kopyalama ve hazırlama seçeneğini kullanın. İlk kopyalama oturumunda, dizinleri ve/veya dosyaları yeni bir kopyalama oturumuyla kopyalamak için aşağıdaki komutu çalıştırın:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Aşağıda bir içeri aktarma örneği gösterilmektedir.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Parametresi ile birlikte verdiğiniz bir günlük dosyası `/j:` , komut satırının her çalışması için oluşturulur. Hazırladığınız her sürücü, içeri aktarma işini oluştururken karşıya yüklenmesi gereken bir günlük dosyasına sahiptir. Günlük dosyaları olmayan sürücüler işlenmedi.

    > [!IMPORTANT]
    > - Disk sürücüsü veya günlük dosyası üzerinde disk hazırlama işlemini tamamladıktan sonra verileri değiştirmeyin.

Ek örnekler için, [günlük dosyaları Için örneklere](#samples-for-journal-files)gidin.

## <a name="step-2-create-an-import-job"></a>2. Adım: içeri aktarma işi oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir içeri aktarma işi oluşturmak için aşağıdaki adımları uygulayın.
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

   1. Yukarıdaki [Adım 1: sürücüleri hazırlama](#step-1-prepare-the-drives)sırasında oluşturduğunuz günlük dosyalarını karşıya yükleyin.
   1. Sipariş için hedef Azure bölgesini seçin.
   1. İçeri aktarma için depolama hesabını seçin.

      Açılan konum, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur.

   1. Ayrıntılı bir günlük kaydetmek istemiyorsanız, **' waımportexport ' blob kapsayıcısı seçeneğinde ayrıntılı günlüğü kaydet** ' i temizleyin.


   ![İçeri aktarma işi oluşturma-2. adım](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Ileri ' yi seçin: devam etmek için **sevkıyat >** .

4. **Gönderim** aşamasında:

    1. Açılır listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. `adbops@microsoft.com`Kullanmayı planladığınız taşıyıcı hakkında bilgi edinmek için Azure Data Box işlemler ekibine başvurun.
    1. Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft, içeri aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için bu hesabı kullanır.
    1. Tümü ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, Eyalet/bölge ve ülke/bölge sağlayın.

        > [!TIP]
        > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

    ![İçeri aktarma işi oluşturma-3. adım](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Devam etmek için **gözden geçir + oluştur** ' u seçin.

5. Sipariş özetinde:

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
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
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

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3. Adım: sürücüleri Azure veri merkezine gönderme

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5. Adım: verileri Azure 'a yüklemeyi doğrulama

İşi tamamlamaya göre izleyin. İş tamamlandıktan sonra verilerinizin Azure 'a yüklendiğini doğrulayın. Şirket içi verileri yalnızca karşıya yükleme işleminin başarılı olduğunu doğruladıktan sonra silin.

## <a name="samples-for-journal-files"></a>Günlük dosyaları için örnekler

**Daha fazla sürücü eklemek** için yeni bir sürücü kümesi dosyası oluşturun ve komutu aşağıda gösterildiği gibi çalıştırın.

*Initialdriveset. csv* dosyasında belirtilenden farklı disk sürücülerine sonraki kopyalama oturumları için, yeni bir sürücü kümesi *. csv* dosyası belirtin ve parametreye bir değer girin `AdditionalDriveSet` . **Aynı günlük dosyası** adını kullanın ve **yenı bir oturum kimliği** sağlayın. AdditionalDriveset CSV dosyası biçimi ınitialdriveset biçimiyle aynı.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Aşağıda bir içeri aktarma örneği gösterilmektedir.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Aynı sürücü kümesine ek veri eklemek için, ek dosya/dizin kopyalamak üzere sonraki kopyalama oturumları için Prepımport komutunu kullanın.

*InitialDriveset.csv* dosyasında belirtilen aynı sabit disk sürücülerine sonraki kopyalama oturumları için, **aynı günlük dosyası** adını belirtin ve **Yeni bir oturum kimliği** sağlayın; depolama hesabı anahtarı sağlanması gerekmez.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Aşağıda bir içeri aktarma örneği gösterilmektedir.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Sonraki adımlar

* [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
* [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)