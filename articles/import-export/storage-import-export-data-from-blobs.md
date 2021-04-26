---
title: Azure Bloblarından verileri dışarı aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure Bloblarından veri aktarmak için Azure portal ' de dışarı aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875710"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Blob depolamadan verileri dışarı aktarmak için Azure İçeri/Dışarı Aktarma hizmetini kullanma

Bu makalede, Azure Blob depolama alanındaki büyük miktarlarda verileri güvenli bir şekilde aktarmak için Azure Içeri/dışarı aktarma hizmeti 'nin nasıl kullanılacağına ilişkin adım adım yönergeler sağlanmaktadır. Hizmet, Azure veri merkezine boş sürücüler sevk etmeniz gerekir. Hizmet, depolama hesabınızdan sürücülere veri aktarır ve sonra sürücüleri geri gönderir.

## <a name="prerequisites"></a>Önkoşullar

Azure Blob depolama 'dan verileri aktarmak üzere bir dışarı aktarma işi oluşturmadan önce, bu hizmet için aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun.
Şunları yapmanız gerekir:

- Içeri/dışarı aktarma hizmeti için kullanılabilen etkin bir Azure aboneliğine sahip olun.
- En az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın. Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md).
- [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır.
- FedEx/DHL hesabınız olmalıdır. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, ' de Azure Data Box Işlemler ekibine başvurun `adbops@microsoft.com` .
  - Hesap geçerli olmalıdır, bakiyesi olmalıdır ve dönüş teslim özelliklerine sahip olmalıdır.
  - Dışarı aktarma işi için bir izleme numarası oluştur.
  - Her iş ayrı bir izleme numarasına sahip olmalıdır. Aynı izleme numarasına birden fazla işin eklenmesi desteklenmez.
  - Bir taşıyıcı hesabınız yoksa şuraya gidin:
    - [FedEx hesabı oluşturun](https://www.fedex.com/en-us/create-account.html)veya
    - [BIR DHL hesabı oluşturun](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1. Adım: dışarı aktarma işi oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir dışarı aktarma işi oluşturmak için aşağıdaki adımları gerçekleştirin.

1. Oturum açın <https://portal.azure.com/> .
2. **İçeri/dışarı aktarma işleri** için arama yapın.

    ![İçeri/dışarı aktarma işlerini arayın](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **+ Yeni** seçeneğini belirleyin.

    ![Yeni ' yi oluşturmak için + yeni ' yi seçin ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **Temel bilgiler**:

   1. Bir abonelik seçin.
   1. Bir kaynak grubu seçin veya **Yeni oluştur** ' u seçip yeni bir tane oluşturun.
   1. İçeri aktarma işi için açıklayıcı bir ad girin. İşlerinizin ilerlemesini izlemek için adı kullanın.
       * Ad yalnızca küçük harf, sayı ve kısa çizgi içerebilir.
       * Ad bir harfle başlamalı ve boşluk içermemelidir.

   1. **Azure 'Dan dışarı aktar**' ı seçin.

    ![Dışarı aktarma siparişi için temel bilgiler seçenekleri](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Ileri ' yi seçin: devam etmek için **iş ayrıntıları >** .

5. **İş için Ayrıntılar**:

   1. Verilerinizin Şu anda bulunduğu Azure bölgesini seçin.
   1. Verileri dışarı aktarmak istediğiniz depolama hesabını seçin. Konumunuza yakın bir depolama hesabı kullanın.

      Bırakma konumu, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur.

   1. Depolama hesabınızdan boş sürücünüze veya sürücülere aktarılacak blob verilerini belirtin. Aşağıdaki üç yöntemden birini seçin.

      - Depolama hesabındaki **tüm blob verilerini dışa aktarmayı** seçin.

        ![Tümünü dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - **Seçili kapsayıcılar ve Bloblar**' ı seçin ve dışarı aktarılacak kapsayıcıları ve Blobları belirtin. Seçim yöntemlerinden birden fazlasını kullanabilirsiniz. Bir **Ekle** seçeneği belirlendiğinde sağ tarafta seçim dizelerinizi ekleyebileceğiniz bir panel açılır.

        |Seçenek|Açıklama|
        |------|-----------|      
        |**Kapsayıcı Ekle**|Bir kapsayıcıdaki tüm Blobları dışarı aktarın.<br>Kapsayıcı **Ekle**' yi seçin ve her kapsayıcı adını girin.|
        |**Blob ekleme**|Dışarı aktarılacak tek blob 'ları belirtin.<br>**BLOB Ekle**' yi seçin. Ardından, kapsayıcının adından başlayarak, Blobun göreli yolunu belirtin. Kök kapsayıcıyı belirtmek için *$root* kullanın.<br>İşlem sırasında hataları önlemek için, bu ekran görüntüsünde gösterildiği gibi BLOB yollarını geçerli biçimde sağlamanız gerekir. Daha fazla bilgi için bkz. [geçerli blob yolları örnekleri](#examples-of-valid-blob-paths).|
        |**Ön ekler Ekle**|Bir kapsayıcıda benzer adlı kapsayıcı veya benzer şekilde adlandırılmış Bloblar kümesi seçmek için bir ön ek kullanın. Ön ek, kapsayıcı adının ön eki, tüm kapsayıcı adı ya da bir bütün kapsayıcı adı ve ardından blob adının öneki olabilir. |

        ![Seçili kapsayıcıları ve Blobları dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - **BLOB listesi dosyasından dışarı aktar ' ı (XML biçimi)** seçin ve depolama hesabından dışarı aktarılacak Blobların bir yol ve önek listesini IÇEREN bir XML dosyası seçin. XML dosyasını oluşturmanız ve depolama hesabı için bir kapsayıcıda depolamanız gerekir. Dosya boş olamaz.

      > [!IMPORTANT]
      > Dışarı aktarılacak Blobları seçmek için bir XML dosyası kullanırsanız, XML 'nin geçerli yollar ve/veya ön ekler içerdiğinden emin olun. Dosya geçersizse veya belirtilen yollarla eşleşen hiçbir veri yoksa, sıra kısmi verilerle sonlanır veya hiçbir veri dışarı aktarılmaz.

       Bir kapsayıcıya XML dosyası ekleme hakkında bilgi için bkz. [XML dosyası kullanarak sırayı dışarı aktarma](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Blob listesi dosyasından dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Dışarı aktarılacak bir blob veri kopyalama sırasında kullanılıyorsa, Azure Içeri/dışarı aktarma hizmeti Blobun anlık görüntüsünü alır ve anlık görüntüyü kopyalar.

   Ileri ' yi seçin: devam etmek için **sevkıyat >** .

6. **Gönderim** aşamasında:

    - Açılan listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. `adbops@microsoft.com`Kullanmayı planladığınız taşıyıcı ile ilgili bilgilerle birlikte Azure Data Box işlemler ekibine başvurun.
    - Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft bu hesabı, dışa aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için kullanır.
    - Tamamen ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, eyalet/il ve ülke/bölge sağlayın.

        > [!TIP]
        > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

    Devam etmek için **gözden geçir + oluştur** ' u seçin.

7. **İnceleme ve oluşturma** bölümünde:

   1. İşin ayrıntılarını gözden geçirin.
   1. Azure 'a disklerin gönderimi için iş adını ve Azure veri merkezi teslimat adresini bir yere göz önünde yapın.

      > [!NOTE]
      > Diskleri Azure portal belirtilen veri merkezine her zaman gönderin. Diskler yanlış veri merkezine sevk edildiğinde iş işlenmez.

   1. Gizlilik ve kaynak verilerini silme siparişinizin **şartlarını** gözden geçirin. Koşulları kabul ediyorsanız, koşulların altındaki onay kutusunu seçin. Siparişin doğrulanması başladı.

   ![Dışarı aktarma siparişinizi gözden geçirin ve oluşturun](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Doğrulama geçtikten sonra **Oluştur**' u seçin.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal bir dışarı aktarma işi oluşturmak için aşağıdaki adımları kullanın.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Bir iş oluşturma

1. [Az Import-Export](/cli/azure/import-export) uzantısını eklemek için [az Extension Add](/cli/azure/extension#az_extension_add) komutunu kullanın:

    ```azurecli
    az extension add --name import-export
    ```

1. Diskleri alabileceğiniz konumların bir listesini almak için [az Import-Export location List](/cli/azure/import-export/location#az_import_export_location_list) komutunu kullanın:

    ```azurecli
    az import-export location list
    ```

1. Mevcut depolama hesabınızı kullanan bir dışarı aktarma işi oluşturmak için aşağıdaki [az Import-Export Create](/cli/azure/import-export#az_import_export_create) komutunu çalıştırın:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

   Bu iş, Depolama hesabınızdaki tüm Blobları dışa aktarır. Bu değeri **--Export** ile değiştirerek dışarı aktarma için bir blob belirtebilirsiniz:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Bu parametre değeri, kök kapsayıcısında *logo.bmp* adlı blobu dışarı aktarır.

   Ayrıca, bir kapsayıcıda önek kullanarak tüm Blobları seçme seçeneğiniz de vardır. Bu değeri **--Export ile** değiştirin:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Daha fazla bilgi için bkz. [geçerli blob yolları örnekleri](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Dışarı aktarılacak blob veri kopyalama sırasında kullanılıyorsa, Azure Içeri/dışarı aktarma hizmeti Blobun anlık görüntüsünü alır ve anlık görüntüyü kopyalar.

1. Kaynak grubu myierg için tüm işleri görmek üzere [az Import-Export List](/cli/azure/import-export#az_import_export_list) komutunu kullanın:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. İşinizi güncelleştirmek veya işinizi iptal etmek için [az Import-Export Update](/cli/azure/import-export#az_import_export_update) komutunu çalıştırın:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell ' de bir dışarı aktarma işi oluşturmak için aşağıdaki adımları kullanın.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az. ımportexport** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklenmelidir `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Bir iş oluşturma

1. Diskleri alabileceğiniz konumların bir listesini almak için [Get-Azımportexportlocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Mevcut depolama hesabınızı kullanan bir dışarı aktarma işi oluşturmak için aşağıdaki [New-Azımportexport](/powershell/module/az.importexport/new-azimportexport) örneğini çalıştırın:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

   Bu iş, Depolama hesabınızdaki tüm Blobları dışa aktarır. **-Exportbloblistblobpath** değerini değiştirerek dışarı aktarma için bir blob belirtebilirsiniz:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Bu parametre değeri, kök kapsayıcısında *logo.bmp* adlı blobu dışarı aktarır.

   Ayrıca, bir kapsayıcıda önek kullanarak tüm Blobları seçme seçeneğiniz de vardır. **-Exportbloblistblobpath** değerini değiştirin:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Daha fazla bilgi için bkz. [geçerli blob yolları örnekleri](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Dışarı aktarılacak blob veri kopyalama sırasında kullanılıyorsa, Azure Içeri/dışarı aktarma hizmeti Blobun anlık görüntüsünü alır ve anlık görüntüyü kopyalar.

1. Myierg kaynak grubu için tüm işleri görmek için [Get-Azımportexport](/powershell/module/az.importexport/get-azimportexport) cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. İşinizi güncelleştirmek veya işinizi iptal etmek için [Update-Azımportexport](/powershell/module/az.importexport/update-azimportexport) cmdlet 'ini çalıştırın:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>2. Adım: sürücüleri gönderme

İhtiyaç duyduğunuz sürücü sayısını görmüyorsanız, [sürücü sayısını kontrol](#check-the-number-of-drives)edin. Sürücü sayısını biliyorsanız, sürücüleri teslim edin.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4. Adım: diskleri alma

Pano işi tamamladıktan sonra, diskler size gönderilir ve sevkıyatın izleme numarası portalda kullanılabilir.

1. İçe aktarılmış verileri olan sürücüleri aldıktan sonra, sürücülerin kilidini açmak için BitLocker anahtarlarını almanız gerekir. Azure portal dışarı aktarma işine gidin. **İçeri/dışarı aktarma** sekmesine tıklayın.
2. Listeden dışarı aktarma işinizi seçin ve tıklayın. **Şifrelemeye** gidin ve anahtarları kopyalayın.

   ![Dışarı aktarma işi için BitLocker anahtarlarını görüntüle](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Disklerin kilidini açmak için BitLocker anahtarlarını kullanın.

Dışarı aktarma işlemi tamamlanmıştır.

## <a name="step-5-unlock-the-disks"></a>5. Adım: disklerin kilidini açma

Sürücünün kilidini açmak için aşağıdaki komutu kullanın:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Örnek girişe bir örnek aşağıda verilmiştir.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Şu anda işi silebilir veya bırakabilirsiniz. İşler 90 gün sonra otomatik olarak silinir.

## <a name="check-the-number-of-drives"></a>Sürücü sayısını denetleyin

Bu *isteğe bağlı* adım, dışa aktarma işi için gereken sürücü sayısını belirlemenize yardımcı olur. [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sisteminde bu adımı gerçekleştirin.

1. Windows sisteminde [Waımportexport sürüm 1 ' i indirin](https://www.microsoft.com/download/details.aspx?id=42659) .
2. Varsayılan klasöre ayıklayın `waimportexportv1` . Örneğin, `C:\WaImportExportV1`.
3. Yönetici ayrıcalıklarına sahip bir PowerShell veya komut satırı penceresi açın. Dizini sıkıştırılmış klasöre dönüştürmek için aşağıdaki komutu çalıştırın:

   `cd C:\WaImportExportV1`

4. Seçili Bloblar için gereken disk sayısını denetlemek için şu komutu çalıştırın:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametreler aşağıdaki tabloda açıklanmıştır:

    |Komut satırı parametresi|Description|
    |--------------------------|-----------------|
    |**/logdir:**|İsteğe bağlı. Günlük dizini. Ayrıntılı günlük dosyaları bu dizine yazılır. Belirtilmemişse, geçerli dizin günlük dizini olarak kullanılır.|
    |**sn**|Gereklidir. Dışarı aktarma işi için depolama hesabının adı.|
    |**sor**|Yalnızca bir kapsayıcı SAS belirtilmemişse gereklidir. Dışarı aktarma işi için depolama hesabının hesap anahtarı.|
    |**/csas:**|Yalnızca bir depolama hesabı anahtarı belirtilmemişse gereklidir. Dışarı aktarma işine verilecek Blobları listelemek için kapsayıcı SAS.|
    |**/ExportBlobListFile:**|Gereklidir. Dışarı aktarılacak Bloblar için blob yollarının listesini veya blob yolu öneklerini içeren XML dosyasının yolu. `BlobListBlobPath`İçeri/dışarı aktarma hizmetinin REST API [İş yerleştirme](/rest/api/storageimportexport/jobs) işlemindeki öğesinde kullanılan dosya biçimi.|
    |**/DriveSize:**|Gereklidir. Bir dışarı aktarma işi için kullanılacak sürücülerin boyutu; *Örneğin*, 500 GB, 1,5 TB.|

    [Önizleme dışa aktarma komutuna bir örnek](#example-of-previewexport-command)görüntüleyin.

5. Dışa aktarma işi için sevk edilecek sürücüleri okuyup yazverecağınızı kontrol edin.

### <a name="example-of-previewexport-command"></a>PreviewExport komutu örneği

Aşağıdaki örnek, komutunu göstermektedir `PreviewExport` :

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Dışarı aktarma blobu liste dosyası, burada gösterildiği gibi BLOB adlarını ve BLOB öneklerini içerebilir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Azure Içeri/dışarı aktarma aracı, dışarı aktarılacak tüm Blobları listeler ve gerekli ek yükü hesaba katarak bu dosyaları belirtilen boyuttaki sürücülere paketlerinizi hesaplar ve ardından blob 'ları ve sürücü kullanım bilgilerini tahmin etmek için gereken sürücü sayısını tahmin eder.

Bilgi günlüklerinin atlanmasıyla birlikte çıktının bir örneği aşağıda verilmiştir:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Geçerli blob yolları örnekleri

Aşağıdaki tabloda geçerli blob yollarının örnekleri gösterilmektedir:

   | Seçici | Blob yolu | Description |
   | --- | --- | --- |
   | Ile başlar |/ |Depolama hesabındaki tüm Blobları dışa aktarır |
   | Ile başlar |/$root/ |Kök kapsayıcıdaki tüm Blobları dışa aktarır |
   | Ile başlar |/Book |Önek **defteriyle** başlayan herhangi bir kapsayıcıdaki tüm Blobları dışa aktarır |
   | Ile başlar |öğelerini |Kapsayıcı **müziindeki** tüm Blobları dışa aktarır |
   | Ile başlar |/Music/aşk |Ön ek **sevimle** başlayan **kapsayıcıdaki** tüm Blobları dışa aktarır |
   | Eşittir |$root/logo.bmp |Kök kapsayıcısında blob **logo.bmp** dışarı aktarır |
   | Eşittir |Videolar/story.mp4 |Kapsayıcı **videolarında** blob **story.mp4** dışarı aktarır |

## <a name="next-steps"></a>Sonraki adımlar

- [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
- [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)
