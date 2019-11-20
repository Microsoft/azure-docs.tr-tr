---
title: Azure Data Box Disk’ten depolama hesabına veri yüklemeyi doğrulama öğreticisi | Microsoft Docs
description: Azure Data Box Disk’ten Azure depolama hesabına yüklenen verilerin nasıl doğrulanacağını öğrenmek için bu öğreticiyi kullanın.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: fc3145ee0b60402026389863b94d21da4b3e4123
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307755"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Öğretici: Azure Data Box Disk’ten veri yüklemesini doğrulama

Bu, serinin son öğreticisidir: Azure Data Box Disk’i dağıtın. Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box Disk'ten verileri silinme

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce [Öğretici: Azure Data Box Disk’i İade Etme](data-box-disk-deploy-picked-up.md) bölümünü tamamladığınızdan emin olun.


## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

Diskler nakliyeciniz tarafından toplandıktan sonra, portaldaki sipariş durumu **Toplandı** olarak güncelleştirilir. Ayrıca bir takip numarası da görüntülenir.

![Diskler toplandı](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Microsoft diski alıp taradığında, iş durumu **Alındı** olarak güncelleştirilir. 

![Diskler alındı](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Diskler Azure veri merkezindeki bir sunucuya bağlandıktan sonra veriler otomatik olarak kopyalanır. Verilerin boyutuna bağlı olarak, kopyalama işleminin tamamlanması birkaç saatten bir güne kadar sürebilir. Kopyalama işinin ilerleme durumunu portalda izleyebilirsiniz.

Kopyalama tamamlandıktan sonra, sipariş durumu **Tamamlandı** olarak güncelleştirilir.

![Veri kopyalama tamamlandı](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Kopyalama hatalarla tamamlanırsa, bkz. [karşıya yükleme hatalarını giderme](data-box-disk-troubleshoot-upload.md).

Kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun. Verileriniz şuralarda olabilir:

- Azure Depolama hesaplarınız. Data Box'a veri kopyaladığınızda, türlerine bağlı olarak bu veriler Azure Depolama hesabınızda aşağıdaki yollardan birine yüklenir.

  - Blok blobları ve sayfa blobları için: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Dosyaları için: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatif olarak Azure portalda Azure depolama hesabınıza gidip oradan ilerleyebilirsiniz.

- Yönetilen disk kaynak gruplarınız. Yönetilen diskler oluştururken, VHD'ler sayfa blobları olarak yüklenir ve ardından yönetilen disklere dönüştürülür. Yönetilen diskler, sipariş oluşturma sırasında belirtilen kaynak gruplarına iliştirilir.

  - Azure'da yönetilen disklere kopyalama işleminiz başarılı olduysa, Azure portalındaki **Sipariş ayrıntıları**’na gidip yönetilen diskler için belirtilen kaynak grubunu not alabilirsiniz.

      ![Sipariş ayrıntılarını görüntüleme](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Belirtilen kaynak grubuna gidin ve yönetilen disklerinizi bulun.

      ![Yönetilen diskler için kaynak grubu](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Bir VHDX veya dinamik/fark kayıt VHD'si kopyaladıysanız, VHDX/VHD bir blok blobu olarak hazırlama depolama hesabına yüklenir. Hazırlama **Depolama hesabı > Bloblar**’a gidin ve sonra uygun kapsayıcıyı seçin: StandardSSD, StandardHDD veya PremiumSSD. VHDX/VHD'ler, hazırlama depolama hesabınızda blok blobları olarak gösterilmelidir.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

Veriler Azure’a yüklendikten sonra, kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun. Verileriniz şuralarda olabilir:

- Azure Depolama hesaplarınız. Data Box'a veri kopyaladığınızda, türlerine bağlı olarak bu veriler Azure Depolama hesabınızda aşağıdaki yollardan birine yüklenir.

    - **Blok blobları ve sayfa blobları için**: https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Azure Dosyalar için**: https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

- Yönetilen disk kaynak gruplarınız. Yönetilen diskler oluştururken, VHD'ler sayfa blobları olarak yüklenir ve ardından yönetilen disklere dönüştürülür. Yönetilen diskler, sipariş oluşturma sırasında belirtilen kaynak gruplarına iliştirilir.

::: zone-end

Verilerin Azure'a yüklendiğini doğrulamak için aşağıdaki adımları uygulayın:

1. Disk siparişinizle ilişkilendirilmiş depolama hesabına gidin.
2. **Blob hizmeti > Bloblara göz atın** seçeneğine gidin. Kapsayıcı listesi gösterilir. *BlockBlob* ve *PageBlob* klasörlerinin altında oluşturduğunuz alt klasöre karşılık olarak, depolama hesabınızda aynı adlı kapsayıcılar oluşturulur.
    Klasör adları Azure adlandırma kurallarına uymuyorsa, Azure'a veri yükleme işlemi başarısız olur.

3. Veri kümesinin tamamının yüklendiğini doğrulamak için, Microsoft Azure Depolama Gezgini'ni kullanın. Data Box Disk siparişine karşılık gelen depolama hesabını ekleyin ve ardından blob kapsayıcılarının listesine bakın. Bir kapsayıcı seçin, **…Diğer** düğmesine tıklayın ve sonra da **Klasör istatistikleri**'ne tıklayın. **Etkinlikler** bölmesinde, blob sayısı ve toplam blob boyutu gibi o klasöre özgü istatistikler görüntülenir. Bayt cinsinden toplam blob boyutu, veri kümesinin boyutuyla eşleşmelidir.

    ![Depolama Gezgini'nde klasör istatistikleri](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk'ten verileri silinme

Kopyalama tamamlandıktan ve siz de verilerin Azure depolama hesabında olduğunu doğruladıktan sonra, diskler NIST standardına uygun olarak güvenle silinir.

::: zone target="docs"

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box Disk konularını öğrendiniz:

> [!div class="checklist"]
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box Disk'ten verileri silinme


Azure portalı yoluyla Data Box Disk'i yönetmeyi öğrenmek için bir sonraki nasıl yapılır makalesine geçin.

> [!div class="nextstepaction"]
> [Azure portalını kullanarak Azure Data Box Disk'i yönetme](./data-box-portal-ui-admin.md)

::: zone-end




