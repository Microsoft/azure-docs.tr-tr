---
title: Microsoft Azure Data Box Heavy'yi geri gönderme öğreticisi | Microsoft Docs
description: Azure Data Box Heavy’yi Microsoft'a nasıl geri göndereceğinizi öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70240339"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Öğretici: Azure Data Box Heavy'yi iade etme ve Azure'a veri yüklemeyi doğrulama

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Azure Data Box Heavy'yi iade etme ve Azure'a veri yüklemeyi doğrulama

::: zone-end

::: zone target = "docs"

Bu öğretici, Azure Data Box Heavy’nin nasıl iade edileceğini ve yüklenen verileri nasıl doğrulayabileceğinizi anlatır.

Bu öğreticide şu gibi konular hakkında bilgi edineceksiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Göndermeye hazırlama
> * Data Box Heavy'yi Microsoft'a gönderme
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box Heavy'den veri silme

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunlardan emin olun:

- [Öğretici: Verileri Azure Data Box'a kopyalama ve doğrulama](data-box-heavy-deploy-copy-data.md) bölümünü tamamladınız.
- Kopyalama işleri tamamlandı. Kopyalama işleri devam ediyorsa Göndermeye hazırlama işi gerçekleştirilemez.


## <a name="prepare-to-ship"></a>Göndermeye hazırlama

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Göndermeye hazırlama

Göndermeye hazırlama işleminden önce kopyalama işlerinin tamamlandığından emin olun.

1. Yerel web kullanıcı arabirimindeki Göndermeye hazırlama sayfasına gidin ve gönderme hazırlığına başlayın.
2. Cihazı yerel web kullanıcı arabiriminden kapatın. Kabloları cihazdan çıkarın.

Artık cihazınızı geri göndermeye hazırsınız.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Data Box Heavy’yi geri gönderme

1. Cihazın kapalı olduğundan ve tüm kabloların çıkartılmış olduğundan emin olun. 4 güç kablosunu sarın ve cihazın arkasından erişebileceğiniz tepsiye yerleştirin.
2. Cihaz LTL kargosunu ABD’de FedEx ve AB’de DHL ile gönderir

    1. Kargo toplama ile ilgili bilgi almak ve iade gönderim etiketini almak için [Data Box Operasyonları](mailto:DataBoxOps@microsoft.com) birimine ulaşın.
    2. Kargo toplama randevusu almak için gönderi taşıyıcınızın yerel numarasını arayın.
    3. Gönderim etiketinin, gönderinin dış tarafında göze çarpacak şekilde göründüğünden emin olun.
    4. Önceki gönderimlerden kalan eski gönderim etiketlerinin cihazdan çıkarıldığından emin olun.
3. Data Box Heavy nakliyeciniz tarafından toplandıktan ve tarandıktan sonra, portaldaki sipariş durumu **Toplandı** olarak güncelleştirilir. Ayrıca bir takip numarası da görüntülenir.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

Microsoft cihazı alıp taradığında, sipariş durumu **Alındı** olarak güncelleştirilir. Ardından cihaz hasar veya kurcalama belirtileri için fiziksel doğrulama sürecinden geçer.

Doğrulama tamamlandıktan sonra Data Box Heavy, Azure veri merkezindeki ağa bağlanır. Veri kopyalaması otomatik olarak başlar. Verilerin boyutuna bağlı olarak, kopyalama işleminin tamamlanması birkaç saatten bir güne kadar sürebilir. Kopyalama işinin ilerleme durumunu portalda izleyebilirsiniz.

Kopyalama tamamlandıktan sonra, sipariş durumu **Tamamlandı** olarak güncelleştirilir.

Kaynaktan silmeden önce verilerinizin Azure’a yüklendiğinden emin olun. Verileriniz şuralarda olabilir:

- Azure Depolama hesaplarınız. Data Box'a veri kopyaladığınızda, türlerine bağlı olarak bu veriler Azure Depolama hesabınızda aşağıdaki yollardan birine yüklenir.

  - Blok blobları ve sayfa blobları için: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Dosyaları için: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatif olarak Azure portalda Azure depolama hesabınıza gidip oradan ilerleyebilirsiniz.

- Yönetilen disk kaynak gruplarınız. Yönetilen diskler oluştururken, VHD'ler sayfa blobları olarak yüklenir ve ardından yönetilen disklere dönüştürülür. Yönetilen diskler, sipariş oluşturma sırasında belirtilen kaynak gruplarına iliştirilir. 

    - Azure'da yönetilen disklere kopyalama işleminiz başarılı olduysa, Azure portalındaki **Sipariş ayrıntıları**’na gidip yönetilen diskler için belirtilen kaynak gruplarını not alabilirsiniz.

        ![Yönetilen disk kaynak gruplarını tanımlama](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Belirtilen kaynak grubuna gidin ve yönetilen disklerinizi bulun.

        ![Kaynak gruplarına bağlı yönetilen disk](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Bir VHDX veya dinamik/fark kayıt VHD'si kopyaladıysanız, VHDX/VHD bir sayfa blobu olarak hazırlama depolama hesabına yüklenir ancak VHD'nin yönetilen diske dönüştürülmesi başarısız olur. Hazırlama **Depolama hesabı > Bloblar**’a gidin ve sonra uygun kapsayıcıyı seçin: Standart SSD, Standart HDD veya Premium SSD. VHD'ler, hazırlama depolama hesabınıza sayfa blobları olarak yüklenir.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure'a verilerin yüklendiğini doğrulama

Data Box Heavy cihazı, Azure veri merkezi ağına bağlandığında veriler otomatik olarak Azure'a yüklenir. Veri kopyalama işlemi tamamlandığında Data Box hizmeti Azure portalından bildirim gönderir.

- Hata günlüklerini kontrol ederek hata olup olmadığını kontrol edin ve gerekli eylemleri gerçekleştirin.
- Kaynaktan silmeden önce verilerinizin depolama hesaplarında olduğundan emin olun.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Data Box Heavy'den veri silme
 
Veriler Azure'a yüklendikten sonra Data Box disklerindeki veriyi [NIST SP 800-88 Revision 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) uygun şekilde siler. Silme tamamlandıktan sonra [Sipariş geçmişini indirebilirsiniz](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Göndermeye hazırlama
> * Data Box Heavy'yi Microsoft'a gönderme
> * Azure'a verilerin yüklendiğini doğrulama
> * Data Box Heavy'den veri silme

Data Box Heavy’yi yerel web arabirimini kullanarak yönetmeyi öğrenmek için aşağıdaki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Data Box'ı yönetmek için yerel web arabirimini kullanma](./data-box-local-web-ui-admin.md)

::: zone-end


