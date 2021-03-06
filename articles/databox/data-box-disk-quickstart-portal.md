---
title: 'Hızlı başlangıç: Microsoft Azure Data Box Disk | Microsoft Docs'
description: Azure Data Box Disk'inizi Azure portalda hızlıca dağıtmak için bu hızlı başlangıçtan faydalanın
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 241b7c0c07d1fbaa6a43c6be4b264424612f538a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869050"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalını kullanarak Azure Data Box Disk'i dağıtma

Bu hızlı başlangıçta Azure portalı kullanarak Azure Data Box Disk'i dağıtma adımları anlatılmaktadır. Bu adımlar sipariş oluşturma, diskleri alma, paketini açma, bağlama, disklere veri kopyalama ve Azure'a yükleme işlemlerini kapsamaktadır.

Ayrıntılı adım adım dağıtım ve izleme yönergeleri için, [Öğretici: Azure Data Box Disk sipariş etme](data-box-disk-deploy-ordered.md) bölümüne gidin. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true) oluşturun.

::: zone-end

::: zone target="chromeless"

Bu kılavuz, Azure portalında Azure Data Box Disk’i kullanma adımlarında size yol gösterir. Bu kılavuz, aşağıdaki soruların yanıtlarını bulmanıza yardımcı olacaktır.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:

- Aboneliğinizde Azure Data Box hizmetinin etkinleştirildiğinden emin olun. Bu hizmeti aboneliğinizde etkinleştirmek için bkz. [Hizmete kaydolma](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs) adresinden Azure portalında oturum açın.

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Önkoşulları inceleme**: Disk ve kabloların sayısını, işletim sistemini ve diğer yazılımları denetleyin.
> - **Bağlama ve kilidini açma**: Cihazı bağlayın ve verileri kopyalamak için diskin kilidini açın.
> - **Diske veri kopyalama ve doğrulama**: Verileri disklerin içinde önceden oluşturulmuş klasörlerin içine kopyalayın.
> - **Diskleri iade etme**: Diskleri, verilerin depolama hesabınıza yüklendiği Azure veri merkezine iade edin.
> - **Azure’da verileri doğrulama**: Verilerinizi kaynak veri sunucusundan silmeden önce depolama hesabınıza yüklendiğini doğrulayın.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Sipariş verme

### <a name="portal"></a>[Portal](#tab/azure-portal)

Bu adım yaklaşık 5 dakika sürer.

1. Azure portalda yeni bir Azure Data Box kaynağı oluşturun. 
2. Bu hizmetin etkinleştirildiği bir aboneliği seçin ve aktarım türünü **İçeri aktarma** olarak belirleyin. Verilerin bulunduğu **Kaynak ülkeyi** ve veri aktarımı için **Azure hedef bölgesini** seçin.
3. **Data Box Disk**'i seçin. Maksimum çözüm kapasitesi 35 TB olarak belirlenmiştir ve daha büyük veriler için birden fazla disk siparişi oluşturabilirsiniz.  
4. Sipariş ayrıntılarını ve sevkiyat bilgilerini girin. Hizmet bölgenizde kullanılabilir durumdaysa bildirim e-posta adreslerini girin, özeti gözden geçirin ve siparişi oluşturun.

Sipariş oluşturulduktan sonra diskler gönderilmek üzere hazırlanır.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Data Box Disk işi oluşturmak için bu Azure CLI komutlarını kullanın.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. [az group create](/cli/azure/group#az_group_create) komutunu çalıştırarak bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu kullanın:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) komutunu kullanarak bir depolama hesabı oluşturun veya mevcut bir depolama hesabını kullanın:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. SKU DataBoxDisk ile bir Data Box işi oluşturmak için [az databox job create](/cli/azure/databox/job#az_databox_job_create) komutunu çalıştırın:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Bu örnekte olduğu gibi, iletişim adını ve e-postasını değiştirebileceğiniz bir işi güncelleştirmek için [az databox job update](/cli/azure/databox/job#az_databox_job_update) komutunu çalıştırın:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   İş hakkındaki bilgileri almak için [az databox job show](/cli/azure/databox/job#az_databox_job_show) komutunu çalıştırın:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Kaynak grubundaki tüm Data Box işlerini görmek için [az databox job list]( /cli/azure/databox/job#az_databox_job_list) komutunu kullanın:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   İşi iptal etmek için [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) komutunu çalıştırın:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   İşi silmek için [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) komutunu çalıştırın:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Data Box işinin kimlik bilgilerini listelemek için [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) komutunu kullanın:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Sipariş oluşturulduktan sonra cihaz gönderilmek üzere hazırlanır.

---

## <a name="unpack"></a>Paketi açma

Bu adım yaklaşık 5 dakika sürer.

Data Box Disk, UPS Express Box içinde gönderilir. Kutuyu açın ve içinde şunların bulunduğundan emin olun:

- Baloncuklu ambalaja sarılı 1 ile 5 USB disk.
- Disk başına bir bağlantı kablosu.
- İade için sevkiyat etiketi.

## <a name="connect-and-unlock"></a>Bağlama ve kilidini açma

Bu adım yaklaşık 5 dakika sürer.

1. Diski desteklenen bir işletim sisteminin çalıştırıldığı Windows/Linux bilgisayarına bağlamak için, verilen kabloyu kullanın. Desteklenen işletim sistemi sürümleri hakkında daha fazla bilgi için bkz. [Azure Data Box Disk sistem gereksinimleri](data-box-disk-system-requirements.md). 
2. Disk kilidini açmak için:

    1. Azure portalında **Genel > Cihaz Ayrıntıları**'na gidin ve destek anahtarını alın.
    2. İşletim sistemine özgü Data Box Disk kilit açma aracını disklere veri kopyalamak için kullanılacak bilgisayara indirin ve ayıklayın. 
    3. Data Box Disk kilit açma aracını çalıştırın ve destek anahtarını sağlayın. Yeniden takılan tüm diskler için kilit açma aracını tekrar çalıştırın ve destek anahtarını sağlayın. **Disk kilidini açmak için BitLocker iletişim kutusunu veya BitLocker anahtarını kullanmayın.** Disk kilitlerinin açılması hakkında daha fazla bilgi için, [Windows istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)'ya veya [Linux istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)'ya gidin.
    4. Diske araç tarafından atanan sürücü harfi görüntülenir. Disk sürücü harfini not edin. Bu harf sonraki adımlarda kullanılacaktır.

## <a name="copy-data-and-validate"></a>Verileri kopyalama ve doğrulama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna göre değişir.

1. Sürücüde *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* ve *DataBoxDiskImport* klasörleri bulunur. Blok blobu olarak içeri aktarılması gereken verileri sürükleyip *BlockBlob* klasörüne bırakın. Benzer şekilde VHD/VHDX gibi verileri sürükleyip *PageBlob* klasörüne, uygun verileri ise *AzureFile* klasörüne bırakın. Yönetilen disk olarak karşıya yüklemek istediğiniz VHD’leri *ManagedDisk* altındaki bir klasöre kopyalayın.

    *BlockBlob* ve *PageBlob* klasörlerinin altındaki her klasör için Azure depolama hesabında bir kapsayıcı oluşturulur. *AzureFile* altındaki bir alt klasör için dosya paylaşımı oluşturulur.

    *BlockBlob* ve *PageBlob* klasörlerinin altındaki tüm dosyalar Azure Depolama hesabındaki varsayılan `$root` kapsayıcısına kopyalanır. Dosyaları *AzureFile* içindeki bir klasöre kopyalayın. Doğrudan *AzureFile* klasörüne kopyalanan tüm dosyalar başarısız olur ve blok blobu olarak yüklenir.

    > [!NOTE]
    > - Tüm kapsayıcılar, bloblar ve dosyaların [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) uygun olması gerekir. Bu kurallara uyulmaması halinde veriler Azure'a yüklenemez.
    > - Dosya boyutlarının blok blobları için en fazla ~4,75 TiB, sayfa blobları için ~8 TiB ve Azure Dosyalar için ~1 TiB olduğundan emin olun.

2. **(İsteğe bağlı ancak önerilir)** Kopyalama tamamlandıktan sonra dosyaları doğrulamak için en azından *DataBoxDiskImport* klasöründe belirtilen `DataBoxDiskValidation.cmd` dosyasını çalıştırmanız ve 1. seçeneği belirlemeniz önerilir. Ayrıca, sürenin izin verdiği ölçüde, doğrulama için sağlama toplamları da oluşturmak üzere 2. seçeneği kullanmanız önerilir (veri boyutuna bağlı olarak zaman alabilir). Bu adımlar, verileri Azure'a yüklerken oluşan hataların olasılığını en aza indirir.
3. Sürücüyü güvenle kaldırın.

## <a name="ship-to-azure"></a>Azure'a gönderme

Bu adımın tamamlanması yaklaşık 5-7 dakika sürer.

1. Tüm diskleri orijinal paketine yerleştirin. Paketle gönderilen sevkiyat etiketini kullanın. Etiket hasar gördüyse veya kaybolduysa portaldan indirin. **Genel bakış**'a gidin ve komut çubuğundan **Sevkiyat etiketini indirin**'e tıklayın.
2. Kapattığınız paketi kargoya verin.  

Data Box Disk hizmeti bir e-posta bildirimi gönderir ve Azure portalında sipariş durumunu güncelleştirir.

## <a name="verify-your-data"></a>Verilerinizi doğrulama

Bu işlemi tamamlamak için gereken süre verilerinizin boyutuna göre değişir.

1. Data Box Disk, Azure veri merkezi ağına bağlandığında veriler otomatik olarak Azure'a yüklenir.
2. Veri kopyalama işlemi tamamlandığında Azure Data Box hizmeti Azure portaldan bildirim gönderir.
    
    1. Hata günlüklerini kontrol ederek hata olup olmadığını kontrol edin ve gerekli eylemleri gerçekleştirin.
    2. Kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu adımın tamamlanması 2-3 dakika sürer.

Temizlemek için Data Box siparişini iptal edebilir ve ardından silebilirsiniz.

- Data Box siparişini işleme alınmadan önce Azure portaldan iptal edebilirsiniz. Siparişler işleme alındıktan sonra iptal edilemez. Sipariş, tamamlanma aşamasına gelene kadar ilerler.

    Siparişi iptal etmek için **Genel bakış**'a gidin ve komut çubuğundan **İptal**'e tıklayın.  

- Siparişin durumu **Tamamlandı** veya **İptal edildi** olduğunda Azure portaldan silebilirsiniz.

    Siparişi silmek için **Genel bakış**'a gidin ve komut çubuğundan **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure'a veri aktarımı konusunda yardım almak için Azure Data Box Disk'i dağıttınız. Azure Data Box Disk yönetimi hakkında daha fazla bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Azure portalı kullanarak Data Box Disk'i yönetme](data-box-portal-ui-admin.md)

::: zone-end
