---
title: Sipariş Azure Data Box Disk öğretici | Microsoft Docs
description: Bu öğreticide Azure'a veri aktarma amacıyla Azure Data Box Disk'e kaydolmayı ve sipariş vermeyi öğreneceksiniz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 04ae2589e8abfab4826f2286d5f00869c24442f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057975"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Öğretici: Azure Data Box Disk sıralama

Azure Data Box Disk, şirket içi verilerinizi Azure'a hızlı, kolay ve güvenilir bir şekilde aktarmanızı sağlayan bir hibrit bulut çözümüdür. Verilerinizi Microsoft tarafından sağlanan katı hal sürücülerine (SSD) aktarır ve diskleri geri gönderirsiniz. Bu veriler daha sonra Azure'a yüklenir.

Bu öğreticide Azure Data Box Disk siparişi verme adımları anlatılmaktadır. Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Data Box Disk sipariş etme
> * Siparişi izleme
> * Siparişi iptal etme

## <a name="prerequisites"></a>Ön koşullar

Dağıtmadan önce, Data Box hizmeti ve Data Box Disk için aşağıdaki yapılandırma önkoşullarını doldurun.

### <a name="for-service"></a>Hizmet için

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Cihaz için

Başlamadan önce aşağıdakilerden emin olun:

* Verileri kopyalayabilmeniz için kullanabileceğiniz bir istemci bilgisayarınızın olması gerekir. İstemci bilgisayarınızda:
  * [Desteklenen bir işletim sistemi](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) çalıştırılmalıdır.
  * Windows istemciyse diğer [gerekli yazılımlar](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) yüklenmiş olmalıdır.  

## <a name="order-data-box-disk"></a>Data Box Disk sipariş etme

Oturum açmak için:

* Bu URL 'deki Azure portal: https://portal.azure.com order Data Box disk.
* Ya da bu URL 'de Azure Kamu Portalı: https://portal.azure.us . Daha fazla ayrıntı için [portalı kullanarak Azure Kamu 'Ya bağlanma](../azure-government/documentation-government-get-started-connect-with-portal.md)konusuna gidin.

Data Box Disk sıralamak için aşağıdaki adımları uygulayın.

1. Portalın sol üst köşesinde **+ Kaynak oluştur**'a tıklayın ve *Azure Data Box* aratın. **Azure Data Box**'a tıklayın.

   ![Azure Data Box arama 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. **Oluştur**’a tıklayın.

3. Data Box'ın bölgenizde kullanılabilir olup olmadığını kontrol edin. Aşağıdaki bilgileri girin veya seçin ve sonra **Uygula**'ya tıklayın.

    ![Data Box Disk seçeneğini belirleyin](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Ayar|Değer|
    |---|---|
    |Abonelik|Data Box hizmetinin etkinleştirildiği aboneliği seçin.<br> Abonelik fatura hesabınıza bağlıdır. |
    |Aktarım türü| Azure'e İçeri Aktarma|
    |Kaynak ülke/bölge | Verilerinizin bulunduğu ülkeyi/bölgeyi seçin.|
    |Hedef Azure bölgesi|Verileri aktarmak istediğiniz Azure bölgesini seçin.|
  
4. **Data Box Disk**'i seçin. Çözümün 5 disklik tek bir sipariş için maksimum kapasitesi 35 TB olarak belirlenmiştir. Daha büyük veri boyutları için birden fazla sipariş oluşturabilirsiniz.

     ![Data Box Disk seçenek 2 ' yi seçin](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. **Sipariş** bölümünde **Sipariş ayrıntıları**'nı belirtin. Aşağıdaki bilgileri girin veya seçin.

    |Ayar|Değer|
    |---|---|
    |Adı|Siparişi takip etmek için kullanılacak kolay bir ad girin.<br> Ad harf, rakam ve tirelerden oluşan 3-24 karakter arası uzunlukta olabilir. <br> Ad bir harf veya sayıyla başlamalı ve bitmelidir. |
    |Kaynak grubu| Var olan bir taneyi kullanın veya yenisini oluşturun. <br> Kaynak grubu, birlikte yönetilebilen ve ya dağıtılabilen kaynaklardan oluşan mantıksal kapsayıcıdır. |
    |Hedef Azure bölgesi| Depolama hesabınız için bir bölge seçin.<br> Şu anda depolama hesapları ABD, Batı ve Kuzey Avrupa, Kanada ve Avustralya’nın tüm bölgelerinde desteklenmektedir. |
    |TB cinsinden tahmini veri boyutu| TB cinsinden tahmini veri boyutunu girin. <br>Microsoft, veri boyutuna uygun sayıda 8 TB boyuta sahip SSD'ler (7 TB kullanılabilir kapasite) gönderir. <br>5 diskin maksimum kullanılabilir kapasitesi 35 TB olacaktır. |
    |Disk geçiş anahtarı| **Azure tarafından oluşturulan geçiş anahtarı yerine özel anahtar kullanın** seçeneğini işaretlerseniz disk geçiş anahtarını sağlayın. <br> En az bir sayısal ve bir özel karakter içeren 12 ile 32 karakterlik alfasayısal bir anahtar sağlayın. İzin verilen karakterler: `@?_+`. <br> Bu seçeneği atlayabilir ve disklerinizin kilidini açmak için Azure tarafından oluşturulan destek anahtarını kullanabilirsiniz.|
    |Depolama hedefi     | Depolama hesabı veya yönetilen diskler ya da her ikisini seçin. <br> Belirtilen Azure bölgesine bağlı olarak, var olan bir depolama hesabının filtrelenmiş listesinden bir depolama hesabı seçin. Data Box Disk, yalnızca 1 depolama hesabıyla bağlanabilir. <br> Dilerseniz yeni bir **Genel amaçlı v1**, **Genel amaçlı v2** veya **Blob depolama hesabı** da oluşturabilirsiniz. <br>Sanal ağları olan depolama hesapları desteklenir. Data Box hizmetinin güvenli depolama hesaplarıyla çalışmasına izin vermek için, depolama hesabı ağ güvenlik duvarı ayarları içinden güvenilen hizmetleri etkinleştirin. Daha fazla bilgi için bkz. [Azure Data Box güvenilir hizmet olarak ekleme](../storage/common/storage-network-security.md#exceptions).|

    Depolama hesabı depolama hedefi olarak kullanılıyorsa aşağıdaki ekran görüntüsünü görürsünüz:

    ![Depolama hesabı için Data Box Disk sıralaması](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Şirket içi VHD 'lerden yönetilen diskler oluşturmak için Data Box Disk kullanıyorsanız, aşağıdaki bilgileri de sağlamanız gerekir:

    |Ayar  |Değer  |
    |---------|---------|
    |Kaynak grubu     | Şirket içi VHD 'lerden yönetilen diskler oluşturmayı düşünüyorsanız yeni bir kaynak grubu oluşturun. Yalnızca Data Box hizmeti tarafından yönetilen disk için Data Box Disk sırada oluşturulduysa, mevcut bir kaynak grubunu kullanın. <br> Yalnızca bir kaynak grubu desteklenir.|

    ![Yönetilen disk için Data Box Disk sıralaması](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Yönetilen diskler için belirtilen depolama hesabı, hazırlama depolama hesabı olarak kullanılır. Data Box hizmeti, VHD 'leri hazırlama depolama hesabına yükler ve ardından bunları yönetilen disklere dönüştürür ve kaynak gruplarına gider. Daha fazla bilgi için bkz. [Azure'a veri yüklemeyi doğrulama](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

6. **İleri**’ye tıklayın.

    ![Sipariş ayrıntılarını belirtin](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. **Teslimat adresi** sekmesinde adınızı, soyadınızı, şirket adını, posta adresini ve geçerli bir telefon numarası girin. **Adresi doğrula**'ya tıklayın. Hizmet, teslimat adresinde hizmetin kullanılabilirlik durumunu doğrular. Hizmet belirtilen teslimat adresinde kullanılabilir durumdaysa bu konuda bir bildirim gönderilir.

   Sipariş işlendikten sonra bir e-posta bildirimi alırsınız. Kendi kendine yönetilen kargo seçeneğini belirlediyseniz bkz. otomatik olarak [yönetilen gönderi kullanma](data-box-disk-portal-customer-managed-shipping.md).

    ![Teslimat adresi belirtme](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. **Bildirim ayrıntıları** sayfasında e-posta adresi belirtin. Hizmet, belirtilen e-posta adreslerine sipariş durumundaki güncelleştirmelerle ilgili bilgi gönderir.

    Grup yöneticisinin ayrılması durumunda da bildirim almaya devam etmek için bir grup e-postası kullanmanız önerilir.

9. **Özet** sayfasında sipariş, iletişim, bildirim ve gizlilik koşullarıyla ilgili bilgileri gözden geçirin. Gizlilik koşullarını kabul ettiğinizi belirten kutuyu işaretleyin.

10. **Sipariş**'e tıklayın. Siparişin oluşturulması birkaç dakika sürer.

## <a name="track-the-order"></a>Siparişi izleme

Siparişi verdikten sonra durumunu Azure portalından takip edebilirsiniz. Siparişinize gidin ve durumunu görüntülemek için **Genel bakış** sayfasını inceleyin. Portalda işin durumu **Sipariş edildi** olarak görünür.

![Data Box Disk durumu, sipariş verildi](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

Diskler kullanılabilir durumda değilse bir bildirim gönderilir. Diskler kullanılabilir durumdaysa Microsoft tarafından gönderilecek diskler belirlenir ve disk paketi hazırlanır. Diskler hazırlanırken şu eylemler gerçekleştirilir:

* Diskler AES-128 BitLocker şifrelemesi kullanılarak şifrelenir.  
* Diskler yetkisiz erişimi önlemek için kilitlenir.
* Bu işlem sırasında disklerin kilidini açan destek anahtarı oluşturulur.

Disklerin hazırlanması tamamlandığında portalda sipariş durumu **İşlenen** olarak değişir.

Microsoft ardından disklerinizi hazırlar ve bölgeye uygun gönderim şirketine teslim eder. Diskler gönderildikten sonra bir takip numarası iletilir. Portalda sipariş durumu **Yola çıktı** olarak değişir.

## <a name="cancel-the-order"></a>Siparişi iptal etme

Bu siparişi iptal etmek için Azure portalında **Genel bakış**'a gidin ve komut çubuğundan **İptal**'e tıklayın.

Yalnızca diskler sipariş edildiğinde iptal edebilir ve sipariş sevkiyat için işlenir. Sipariş işleme alındıktan sonra iptal edemezsiniz.

![Siparişi iptal etme](media/data-box-disk-deploy-ordered/cancel-order1.png)

İptal edilen bir siparişi silmek için **Genel bakış**'a gidin ve komut çubuğundan **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
>
> * Data Box Disk sipariş etme
> * Siparişi izleme
> * Siparişi iptal etme

Data Box Disk'inizi ayarlama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box Diskinizi ayarlama](./data-box-disk-deploy-set-up.md)