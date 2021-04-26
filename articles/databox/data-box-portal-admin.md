---
title: Azure Data Box, Azure Data Box Heavy Azure portal aracılığıyla yönetme | Microsoft Docs
description: Azure Data Box ve Azure Data Box Heavy yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678645"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box ve Azure Data Box Heavy yönetmek için Azure portal kullanın

Bu makale hem Azure Data Box hem de Azure Data Box Heavy için geçerlidir. Bu makalede Azure Data Box cihazında gerçekleştirilebilecek karmaşık iş akışlarının ve yönetim görevlerinin bazıları açıklanmaktadır. Data Box cihazı Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla yönetebilirsiniz.

Bu makale, Azure portalı kullanarak gerçekleştirebileceğiniz görevlere odaklanmaktadır. Azure portal kullanarak siparişleri yönetin, Data Box cihazı yönetin ve tamamlanana kadar siparişin durumunu izleyin.

## <a name="cancel-an-order"></a>Siparişi iptal etme

Bunu yaptıktan sonra çeşitli nedenlerle bir siparişi iptal etmeniz gerekebilir.

İçeri ve dışarı aktarma siparişleri için, yalnızca işlenmeden önce siparişi iptal edebilirsiniz. Siparişiniz işlendikten ve Data Box cihaz hazırlandıktan sonra siparişi iptal edemezsiniz.

Bir siparişi iptal etmek için aşağıdaki adımları gerçekleştirin.

1.  **Genel bakış > İptal**'e gidin.

    ![Bir siparişin Genel Bakış sekmesinde iptal komutu](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Sipariş iptal etme nedenini belirtin.  

    ![Siparişi iptal et iletişim kutusu](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Sipariş iptal edildikten sonra portaldaki durumu **İptal edildi** olarak görüntülenir.

## <a name="clone-an-order"></a>Siparişi kopyalama

Kopyalama belirli durumlarda kullanışlıdır. Örneğin, bazı verileri aktarmak için Data Box kullandınız. Daha fazla veri üretilmeden bu verileri Azure 'a aktarmak için başka bir Data Box cihaz kullanmanız gerekir. Bu durumda, yalnızca aynı siparişi kopyalayabilirsiniz.

İçeri Aktarma sırasını kopyalamak için aşağıdaki adımları gerçekleştirin.

1.  **Genel bakış > Kopyala**'ya gidin. 

    ![Bir siparişin Genel Bakış sekmesinde kopyalama komutu](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  Siparişin tüm ayrıntıları aynı şekilde korunur. Siparişin adı, özgün siparişin adına *-Kopya* eklenerek oluşturulur. Gizlilik bilgilerini gözden geçirdiğinizi onaylamak için onay kutusunu seçin. **Oluştur**’a tıklayın.

Kopya sipariş birkaç dakikada oluşturulur ve portal yeni siparişi gösterecek şekilde güncelleştirilir.


## <a name="delete-order"></a>Siparişi silme

Tamamlanan siparişleri silmek isteyebilirsiniz. Siparişte adınız, adresiniz ve iletişim bilgileriniz gibi kişisel bilgileriniz yer alır. Sipariş silindiğinde bu kişisel bilgiler de silinir.

Yalnızca tamamlanan veya iptal edilen siparişleri silebilirsiniz. Siparişi silmek için aşağıdaki adımları gerçekleştirin.

1. **Tüm kaynaklar**'a gidin. Siparişinizi arayın.

2. Silmek istediğiniz siparişe tıklayın ve **Genel bakış**'a gidin. Komut çubuğundan **Sil**'e tıklayın.

    ![Bir siparişin Genel Bakış sekmesinde Sil komutu](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Siparişi silme işlemini onaylamanız istendiğinde siparişin adını girin. **Sil**'e tıklayın.

## <a name="download-shipping-label"></a>Sevkiyat etiketini indirme

Data Box E-mürekkep görünümü çalışmıyorsa ve iade sevkiyat etiketini görüntülemediğinizde sevkiyat etiketini indirmeniz gerekebilir. Data Box Heavy üzerinde E-mürekkep görüntülenmez, bu nedenle bu iş akışı Data Box Heavy için uygulanmaz.

Sevkiyat etiketini indirmek için aşağıdaki adımları gerçekleştirin.

1.  **Genel bakış > Sevkiyat etiketi indir** bölümüne gidin. Bu seçenek yalnızca cihaz gönderildikten sonra kullanılabilir. 

    ![Sevkiyat etiketini indirme](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Aşağıda gösterilen iade sevkiyat etiketi indirilir. Etiketi kaydedin ve yazdırın. Etiketi katlamalı ve cihazdaki açık manşon içine ekleyin. Etiketin görünür olduğundan emin olun. Cihazdaki önceki gönderimden kalan tüm çıkartmaları sökün.

    ![Örnek sevkiyat etiketi](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Teslimat adresini düzenleme

Siparişi verdikten sonra teslimat adresini düzenlemeniz gerekebilir. Bu işlem yalnızca cihaz yola çıkana kadar kullanılabilir. Cihaz yola çıktıktan sonra bu seçenek kullanılamaz.

Siparişi düzenlemek için aşağıdaki adımları gerçekleştirin.

1. **Sipariş ayrıntıları > Teslimat adresini düzenle**'ye gidin.

    ![Sipariş ayrıntılarında sevkiyat adresini Düzenle komutu](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Gönderim adresini düzenleyin ve doğrulayın ve sonra değişiklikleri kaydedin.

    ![Sevkiyat adresini Düzenle iletişim kutusu](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Bildirim ayrıntılarını düzenleme

Sıra durumu e-postalarını alan kullanıcıları değiştirmeniz gerekebilir. Örneğin cihaz teslim edildiğinde veya alındığında bir kullanıcının bilgilendirilmesi gerekebilir. Verilerin kaynaktan silinmeden önce Azure depolama hesabında olduğunu doğrulayabilmeleri için, veri kopyalama işlemi tamamlandığında başka bir kullanıcının bilgilendirilmesi gerekebilir. Bu gibi durumlarda bildirim ayrıntılarını düzenleyebilirsiniz.

Bildirim ayrıntılarını düzenlemek için aşağıdaki adımları gerçekleştirin.

1. **Sipariş ayrıntıları > Bildirim ayrıntılarını düzenle**'ye gidin.

    ![Bildirim ayrıntılarını Düzenle komutu sıralı Ayrıntılar](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Bu sayfada bildirim ayrıntılarını düzenleyebilir ve yaptığınız değişiklikleri kaydedebilirsiniz.
 
    ![Bildirim ayrıntılarını Düzenle iletişim kutusu](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Sipariş geçmişi indirme

Data Box siparişiniz tamamlandıktan sonra cihaz disklerindeki veriler silinir. Cihaz temizleme tamamlandığında, sipariş geçmişini Azure portalında indirebilirsiniz.

Sipariş geçmişini indirmek için aşağıdaki adımları uygulayın.

1. Data Box siparişinizde **Genel Bakış**'a gidin. Siparişin tamamlandığından emin olun. Sipariş ve cihaz temizleme tamamlandıysa, **Sipariş ayrıntıları**'na gidin. **Sipariş geçmişi indirme** seçeneği bulunur.

    ![Sipariş geçmişi indirme](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. **Sipariş geçmişini indir**'e tıklayın. İndirilen geçmiş, taşıyıcı İzleme günlüklerinin bir kaydını içerir. Data Box Heavy bir cihazdaki iki düğüme karşılık gelen iki günlük kümesi olacaktır. Bu günlüğün en altına giderseniz, aşağıdakilere giden bağlantıları görebilirsiniz:
    
   - **Günlükleri Kopyala** -Data Box veri kopyalama sırasında Azure depolama hesabınıza (içeri aktarma sırası) veya depolama hesabınızdan Data Box (dışarı aktarma sırasına) veri kopyalama sırasında hatalı bir dosya listesini alın.
   - **Denetim günlükleri** -Data Box Azure veri merkezi dışında olduğunda Data Box nasıl güçlendirin ve bu bilgisayardaki paylaşımlara erişme hakkında bilgi içerir.
   - **İçeri aktarma sırasındaki bom dosyaları** - **göndermeye hazırlama** sırasında indirebileceğiniz ve dosya adlarına, dosya boyutlarına ve dosya sağlama toplamlarını içeren dosya (dosya bildirimi olarak da bilinir) listesini içerir.
   - **Dışarı aktarma sırasında ayrıntılı Günlükler** -veriler Azure depolama hesaplarından Data Box kopyalanırken dosya adları, dosya boyutları ve sağlama toplamı hesaplamasına sahip dosyaların listesini içermelidir.

   Aşağıda, bir içeri aktarma sırasındaki sipariş geçmişinden oluşan bir örnek verilmiştir.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Daha sonra depolama hesabınıza gidebilir ve kopyalama günlüklerini görüntüleyebilirsiniz.

   ![Depolama hesabı için kopyalama günlükleri](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Ayrıca Denetim günlüklerini ve bom dosyalarını içeren gözetim günlüklerinin zincirini görüntüleyebilirsiniz.

   ![Depolama hesabı için gözetim günlüklerinin Zinciri](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Sipariş durumunu görüntüleme

Portalda cihaz durumu değiştiğinde bir e-posta ile bildirilir.

### <a name="statuses-for-import-order"></a>İçeri aktarma sırası için durumlar

İçeri aktarma siparişi için durumlar aşağıda verilmiştir.

|Sipariş durumu |Description |
|---------|---------|
|Sipariş edildi     | Sipariş başarıyla oluşturuldu. <br>Cihaz kullanılabilir durumdaysa Microsoft tarafından gönderilecek cihaz belirlenir ve cihaz hazırlanır. <br> Cihaz hemen kullanılamıyorsa, cihaz kullanılabilir hale geldiğinde sıra işlenir. Siparişin işleme alınması birkaç gün ile birkaç ay sürebilir. Sipariş 90 gün içinde karşılanmıyorsa, sipariş iptal edilir ve size bildirilir.         |
|İşlendi     | Siparişin işlenmesi tamamlandı. Siparişiniz uyarınca cihaz gönderilmek üzere veri merkezinde hazırlanır.         |
|Yola çıktı     | Sipariş sevk edildi. Gönderiyi takip etmek için portalda, siparişinizde görüntülenen takip kimliğini kullanın.        |
|Teslim Edildi     | Gönderim, belirtilen adrese teslim edildi.        |
|Teslim alındı     |İade gönderiniz teslim alındı ve kurye tarafından tarandı.         |
|Alındı     | Cihazınız alındı ve Azure veri merkezinde tarandı. <br> Gönderi incelendikten sonra cihaz karşıya yüklemesi başlar.      |
|Veri kopyalama     | Veri kopyalama işlemi devam ediyor. Azure portal’da siparişinizin kopyalama ilerleme durumunu takip edin. <br> Veri kopyalama işlemi tamamlanana kadar bekleyin. |
|Tamamlandı       |Sipariş başarıyla tamamlandı.<br> Şirket içi verilerini sunuculardan silmeden önce verilerinizin Azure’a kopyalandığından emin olun.         |
|Hatalarla tamamlandı| Veri kopyalama tamamlandı ancak kopyalama sırasında hatalar oluştu. <br> Azure portalda belirtilen yolu kullanarak kopyalama günlüklerini gözden geçirin. [Karşıya yükleme hatalarla tamamlandığında kopyalama günlüklerine örneklere](./data-box-logs.md#upload-completed-with-errors)bakın.   |
|Uyarılarla tamamlandı| Veri kopyalama tamamlandı, ancak verileriniz değiştirildi. Veriler, dosya veya blob adlarını değiştirerek düzeltilen kritik olmayan BLOB veya dosya adı hatalarıyla karşılaştı. <br> Azure portalda belirtilen yolu kullanarak kopyalama günlüklerini gözden geçirin. Verilerdeki değişikliklere bir göz önünde bir dikkat edin. [Karşıya yükleme uyarılarla tamamlandığında, kopyalama günlüklerine örneklere](./data-box-logs.md#upload-completed-with-warnings)bakın.   |
|İptal edildi            |Sipariş iptal edildi. <br> Siparişi iptal ettiniz ya da bir hata oluştuysa, hizmet sırayı iptal etmiş olursunuz. Sipariş 90 gün içinde karşılanmıyorsa, sipariş da iptal edilir ve size bildirilir.     |
|Temizleme | Cihaz sürücülerindeki veriler silinir. Cihaz temizleme; sipariş geçmişi Azure portalından indirilmeye hazır olduğunda tamamlanmış olarak değerlendirilir.|

### <a name="statuses-for-export-order"></a>Dışarı aktarma sırası için durumlar

Dışarı aktarma siparişi için durumlar aşağıda verilmiştir.

|Sipariş durumu |Description |
|---------|---------|
|Sipariş edildi     | Dışarı aktarma sırası başarıyla yerleştirildi. <br>Cihaz kullanılabilir durumdaysa Microsoft tarafından gönderilecek cihaz belirlenir ve cihaz hazırlanır. <br> Cihaz o anda mevcut değilse sipariş cihaz mevcut olduğunda işleme alınır. Siparişin işleme alınması birkaç gün ile birkaç ay sürebilir. Sipariş 90 gün içinde gerçekleştirilemiyorsa sipariş iptal edilir ve bu size bildirilir.         |
|İptal edildi            |Sipariş iptal edildi. <br> Siparişi iptal etmiş olursunuz (yalnızca sipariş işlenmeden önce iptal edebilirsiniz) ya da bir hata ile karşılaşıldıktan sonra hizmet siparişi iptal etmiş olursunuz. Sipariş 90 gün içinde gerçekleştirilemiyorsa sipariş iptal edilir ve bu size bildirilir.     |
|İşlendi     | Siparişin işlenmesi tamamlandı. Siparişiniz uyarınca, cihaz veri merkezindeki veri kopyalama için hazırlanır. Cihaz paylaşımları oluşturulur.         |
|Veri kopyalama devam ediyor     | Belirtilen Azure depolama hesaplarından cihaza veri kopyalama işlemi devam ediyor. Azure portal’da siparişinizin kopyalama ilerleme durumunu takip edin. <br> Veri kopyalama işlemi tamamlanana kadar bekleyin. |
|Kopyalama tamamlandı     | Belirtilen Azure depolama hesaplarından cihaza veri kopyalama işlemi tamamlanmıştır. Ayrıntılı günlük dosyası (Eğer seçenek sırasıyla etkinleştirilmişse) ve depolama hesabınızda bir kopyalama günlüğü oluşturulur. Ayrıntılı günlük, cihaza kopyalanan tüm dosyalar (ad, yol, hesaplama sağlama toplamı) hakkındaki bilgileri içerir. Kopyalama günlüğü, herhangi bir hata nedeniyle kopyalanamayan bir dosya listesi dahil olmak üzere kopyalama işleminin özetini içerir. <br> Depolama hesabı verileri olduğu gibi kalır. |
|Kopyalama hatalarla tamamlandı| Veri kopyalama tamamlandı ancak kopyalama sırasında hatalar oluştu. <br> Azure portal belirtilen yolu kullanarak Azure Storage hesabındaki kopyalama günlüklerini gözden geçirin. [İndirme işlemi hatalarla tamamlandığında, günlükleri kopyalama örneklerine](./data-box-logs.md#upload-completed-with-errors)bakın.   |
|Kopyalama uyarılarla tamamlandı| Azure depolama hesabından veri kopyalama tamamlandı, ancak veriler kritik olmayan hatalara sahipti. <br> Azure portalda belirtilen yolu kullanarak kopyalama günlüklerini gözden geçirin. Kritik olmayan hataları bir yere dikkat edin. [İndirme, uyarılarla tamamlandığında günlükleri kopyalama örneklerine](./data-box-logs.md#upload-completed-with-warnings)bakın.   |
|Kopyalama hatalarla başarısız oldu| Azure depolama hesabından veri kopyalama başarısız oldu ve sıra sonlandırıldı. Bir cihaz gönderilmeyecektir. <br> Azure portal belirtilen yolu kullanarak Azure Storage hesabındaki kopyalama günlüklerini gözden geçirin. [İndirme işlemi hata ile başarısız olduğunda günlükleri kopyalama örneklerine](./data-box-logs.md#upload-completed-with-errors)bakın.   |
|Yola çıktı     |Sipariş sevk edildi. Gönderiyi takip etmek için portalda, siparişinizde görüntülenen takip kimliğini kullanın.        |
|Teslim Edildi     |Gönderim, belirtilen adrese teslim edildi.        |
|Teslim alındı     |İade gönderiniz teslim alındı ve kurye tarafından tarandı.         |
|Alındı     | Cihazınız alındı ve Azure veri merkezinde tarandı. <br> Sevkiyat denetlenir.      |
|Tamamlandı           |Sipariş tamamlanmıştır.     |
|Temizleme | Cihaz sürücülerindeki veriler silinir. Cihaz temizleme; sipariş geçmişi Azure portalından indirilmeye hazır olduğunda tamamlanmış olarak değerlendirilir.|

> [!NOTE]
> Azure depolama hesaplarından verileri dışarı aktarmaya yönelik kopyalama işi hatalar veya uyarılarla Data Box tamamlanırsa, cihaz yine de gönderilir. Yalnızca kopyalama hatası söz konusu olduğunda, sipariş sonlandırılır ve cihaz teslim edilmez.


Otomatik olarak yönetilen kargo kullanıyorsanız, kopyalama işlemi tamamlandıktan sonra ve cihazı almadan önce aşağıdaki durumları görürsünüz (önceki tabloda bahsedilen durumlar yerine):

|Sipariş durumu |Description |
|---------|---------|
|Azure veri merkezinde toplama için hazırlanma      |Cihaz, Azure veri merkezinde hazırlanmaya uygun.        |
|Teslim alındı    |Cihazı tamamladınız.         |
|Azure veri merkezinde almaya hazırlanma     |Cihaz, Azure veri merkezinde alınmak üzere hazırlanmıştı.        |
|Alındı     |Cihaz, Azure veri merkezinde alındı.      |





## <a name="next-steps"></a>Sonraki adımlar

- [Data Box ve Data Box Heavy sorunlarını giderme](data-box-troubleshoot.md)hakkında bilgi edinin.