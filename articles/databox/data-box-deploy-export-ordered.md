---
title: Azure Data Box verileri dışarı aktarma öğreticisi | Microsoft Docs
description: Dağıtım önkoşullarını ve Azure Data Box verileri dışarı aktarmayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789204"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Öğretici: Azure Data Box için dışarı aktarma siparişi oluşturma

Azure Data Box, verileri Azure 'dan konumunuza taşımanıza olanak sağlayan bir karma çözümdür. Bu öğreticide, Azure Data Box için dışarı aktarma siparişi oluşturma işlemi açıklanmaktadır. Bir dışarı aktarma siparişi oluşturmanın temel nedeni, şirket içi depolamanın tehlikeye alınması ve bir yedeklemenin geri yüklenmesi için olağanüstü durum kurtarma içindir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Dışarı aktarma önkoşulları
> * Dışarı aktarma için bir Data Box sıralama
> * Dışarı aktarma sırasını izleyin
> * Dışarı aktarma sırasını iptal et

## <a name="prerequisites"></a>Önkoşullar

Cihazı sipariş etmeden önce Data Box hizmeti ve cihazı için aşağıdaki yapılandırma önkoşullarını doldurun.

### <a name="for-service"></a>Hizmet için

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Azure Data Box için kullanabileceğiniz mevcut bir kaynak grubunuz olduğundan emin olun.

* Verilerini dışarı aktarmak istediğiniz Azure depolama hesabınızın, [Data Box Için desteklenen depolama hesapları](data-box-system-requirements.md#supported-storage-accounts)Ile desteklenen depolama hesabı türlerinden biri olduğundan emin olun.

### <a name="for-device"></a>Cihaz için

Başlamadan önce aşağıdakilerden emin olun:

* Veri merkezi ağına bağlı bir konak bilgisayarınız olmalıdır. Azure Data Box verileri bu bilgisayara kopyalayacaksınız. [Azure Data Box sistem gereksinimleri](data-box-system-requirements.md) altında açıklandığı gibi konak bilgisayarınızın desteklenen bir işletim sistemini çalıştırması gerekir.

* Veri merkezinizin yüksek hızlı ağı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10-GbE bağlantı yoksa, 1-GbE veri bağlantısı kullanılabilir ancak kopyalama hızları bundan etkilenir.

## <a name="order-data-box-for-export"></a>Dışarı aktarma için Data Box sipariş

Cihaz sipariş etmek için Azure portalında aşağıdaki adımları izleyin.

1. Microsoft Azure kimlik bilgilerini kullanarak şu URL’de oturum açın: [https://portal.azure.com](https://portal.azure.com).

2. **+ Kaynak oluştur**’u seçin ve *Azure Data Box* araması yapın. **Azure Data Box**’ı seçin.

   ![Kaynak oluşturma](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. **Oluştur**’u seçin.

   ![Azure Data Box kaynağı oluşturma](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Azure Data Box hizmetin bölgenizde kullanılabilir olup olmadığını denetleyin. Aşağıdaki bilgileri girin veya seçin ve sonra **Uygula**'yı seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Aktarım türü     | **Azure 'A aktar**' ı seçin.        |
    |Abonelik     | Data Box hizmeti için bir EA, CSP veya Azure sponsorluk aboneliği seçin. <br> Abonelik fatura hesabınıza bağlıdır.       |
    |Kaynak grubu     |    Var olan bir kaynak grubunu seçin. <br> Kaynak grubu, birlikte yönetilebilen ve ya dağıtılabilen kaynaklardan oluşan mantıksal kapsayıcıdır.         |
    |Kaynak Azure bölgesi    |    Verilerinizin Şu anda bulunduğu Azure bölgesini seçin.         |
    |Hedef ülke     |     Cihazı teslim etmek istediğiniz ülkeyi seçin.        |

   ![Data Box ayarlarınızı seçin](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. **Data Box**'ı seçin. Tek bir sipariş için kullanılabilir maksimum kapasite 80 TB 'tır. Daha büyük veri boyutları için birden fazla sipariş oluşturabilirsiniz.

   ![Data Box kapasiteyi seçin](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. **Sırasıyla**, **temel** sipariş ayrıntılarını belirtin. Aşağıdaki bilgileri girin veya seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Abonelik     | Abonelik, önceki seçiminize göre otomatik olarak doldurulur.|
    |Kaynak grubu | Daha önce seçtiğiniz kaynak grubu. |
    |Dışarı aktarma sırası adı     |  Siparişi takip etmek için kullanılacak kolay bir ad girin. <br> Ad harf, rakam ve tirelerden oluşan 3-24 karakter arası uzunlukta olabilir. <br> Ad bir harf veya sayıyla başlamalı ve bitmelidir.      |

    ![Dışarı aktarma siparişi temelleri](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Ileri ' yi seçin: devam etmek için **veri seçimi** .

7. **Veri seçimi**' nde **depolama hesabı ekle ve dışarı aktarma türü**' nü seçin.

    ![Depolama hesabı ve dışarı aktarma türü Ekle](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. **Dışarı aktarma seçeneğini belirleyin**' de dışarı aktarma seçeneği ayrıntılarını belirtin. Aşağıdaki bilgileri girin veya seçin ve **Ekle**' yi seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Depolama hesabı     | Verileri dışarı aktarmak istediğiniz Azure depolama hesabı. |
    |Dışarı aktarma türü     | **Tüm nesnelerden** dışarı aktarılacak verilerin türünü BELIRTIR ve **XML dosyası kullanır**.<ul><li> **Tüm nesneler** -Işin, **aktarım seçenekleri** için seçiminize bağlı olarak tüm verileri dışa aktarıp aktarcağını belirtir.</li><li> **XML dosyası kullan** – depolama hesabından dışarı aktarılacak blob 'lar ve/veya dosyalar için bir dizi yol ve önek IÇEREN bir XML dosyasını belirtir. XML dosyasının seçili depolama hesabının kapsayıcısında olması gerekir ve dosya paylaşımları arasından seçim şu anda desteklenmiyor. Dosyanın boş olmayan bir. xml dosyası olması gerekir.</li></ul>        |
    |Aktarım seçenekleri     |  **Tümünü Seç**, **Tüm Bloblar** ve **tüm dosyalar**'daki veri aktarımı seçeneklerini belirtir. <ul><li> **Tümünü Seç** -tüm Blobların ve Azure dosyalarının verildiğini belirtir. Yalnızca Blobları (BLOB depolama hesabı) destekleyen bir depolama hesabı kullanıyorsanız, **tüm dosyalar** seçeneği seçilemeyecektir.</li><li> **Tüm Bloblar** -yalnızca blok ve sayfa bloblarının aktarıldığını belirtir.</li><li> **Tüm dosyalar** -tüm dosyaların blob 'lar hariç verildiğini belirtir. Sahip olduğunuz depolama hesabının türü (GPv1 ve GPv2, Premium Storage veya blob Storage), dışarı aktarmak istediğiniz veri türlerini belirler. Daha fazla bilgi için bkz. [dışa aktarma Için desteklenen depolama hesapları](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Ayrıntılı günlük Ekle     | Başarıyla aktarılmış tüm dosyaların listesini içeren ayrıntılı bir günlük dosyası isteyip istemediğinizi belirtir.        |

    > [!NOTE]
    >
    > **Dışarı aktarma türü** AYARı için **XML dosyası kullan** ' ı seçerseniz, XML 'nin geçerli yollar ve/veya ön ekler içerdiğinden emin olmanız gerekir. XML dosyası oluşturmanız ve sağlamanız gerekir.  Dosya geçersizse veya belirtilen yollarla eşleşen hiçbir veri yoksa, sıra kısmi verilerle sonlanır veya hiçbir veri dışarı aktarılmaz.

    Bir kapsayıcıya XML dosyası ekleme hakkında bilgi için bkz. [XML dosyası kullanarak sırayı dışarı aktarma](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Dışarı aktarma seçeneğini belirleyin](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   XML girişi örneğini görmek için bkz. [Sample XML Input](data-box-deploy-export-ordered.md#sample-xml-file)

9. **Veri seçiminde** ayarlarınızı gözden geçirin ve ileri ' yi seçin. devam etmek için **güvenlik>** .

   ![Dışarı aktarma sırası, veri seçimi](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    **Güvenlik** ekranı, kendi şifreleme anahtarınızı kullanmanıza olanak sağlar ve çift şifrelemeyi kullanmayı seçebilirsiniz.

    **Güvenlik** ekranındaki tüm ayarlar isteğe bağlıdır. Herhangi bir ayarı değiştirmezseniz varsayılan ayarlar uygulanır.

    ![Data Box alma sırası sihirbazının güvenlik ekranı](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Yeni kaynağınız için kilit açma geçiş anahtarını korumak üzere kendi müşteri tarafından yönetilen anahtarınızı kullanmak istiyorsanız, **şifreleme türü**' ni genişletin.

    Azure Data Box için müşteri tarafından yönetilen bir anahtarın yapılandırılması isteğe bağlıdır. Data Box, varsayılan olarak, kilit açma geçiş anahtarını korumak için Microsoft tarafından yönetilen bir anahtar kullanır.

    Müşterinin yönettiği bir anahtar, cihazdaki verilerin nasıl şifrelendiğini etkilemez. Anahtar yalnızca cihaz kilidini açma geçiş anahtarını şifrelemek için kullanılır.

    Müşteri tarafından yönetilen bir anahtar kullanmak istemiyorsanız, 16. adıma atlayın.

    ![Şifreleme türü ayarlarını gösteren güvenlik ekranı](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Anahtar türü olarak **müşteri tarafından yönetilen anahtar** ' ı seçin. Sonra **bir anahtar kasası ve anahtar seçin** öğesini seçin.
   
    ![Güvenlik ekranı, müşteri tarafından yönetilen anahtar ayarları](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. **Azure Key Vault anahtarı seç** ekranında, abonelik otomatik olarak doldurulur.

    - **Anahtar Kasası** için, açılan listeden var olan bir anahtar kasasını seçebilirsiniz.

      ![Azure Key Vault ekranından anahtar seçin](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Yeni bir Anahtar Kasası oluşturmak için **Yeni oluştur** ' u de seçebilirsiniz. **Anahtar Kasası oluştur** ekranında, kaynak grubunu ve bir Anahtar Kasası adını girin. **Geçici silme** ve **Temizleme korumasının** etkinleştirildiğinden emin olun. Diğer tüm varsayılanları kabul edin ve **gözden geçir + oluştur**' u seçin.

      ![Yeni Azure Key Vault ayarları oluşturma](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Anahtar kasanızın bilgilerini gözden geçirin ve **Oluştur**' u seçin. Anahtar Kasası oluşturma işleminin tamamlanabilmesi için birkaç dakika bekleyin.

      ![Yeni Azure Key Vault incelemesi ekranı](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. **Azure Key Vault anahtar seçin** ekranında, anahtar kasasında varolan bir anahtarı seçebilirsiniz.

    ![Azure Key Vault varolan anahtarı seç](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Yeni bir anahtar oluşturmak istiyorsanız **Yeni oluştur**' u seçin. Bir RSA anahtarı kullanmanız gerekir. Boyut, 2048 veya daha büyük olabilir. Yeni anahtarınız için bir ad girin, diğer varsayılanları kabul edin ve **Oluştur**' u seçin.

      ![Yeni bir anahtar seçeneği oluştur](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Anahtar kasasında anahtar oluşturulduğunda size bildirilir.

14. Kullanılacak anahtarın **sürümünü** seçin ve ardından **Seç**' i seçin.

      ![Anahtar Kasası 'nda yeni anahtar oluşturuldu](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Yeni bir anahtar sürümü oluşturmak istiyorsanız **Yeni oluştur**' u seçin.

    ![Yeni bir anahtar sürümü oluşturmak için bir iletişim kutusu açın](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    **Yeni anahtar oluştur** ekranında yeni anahtar sürümü için ayarlar ' ı seçin ve **Oluştur**' u seçin.

    ![Yeni bir anahtar sürümü oluştur](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    **Güvenlik** ekranındaki **şifreleme türü** ayarları, anahtar Kasanızı ve anahtarınızı gösterir.

    ![Müşteri tarafından yönetilen anahtar için anahtar ve Anahtar Kasası](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Bu kaynağa erişimi yönetmek için kullanacağınız bir kullanıcı kimliği seçin. **Kullanıcı kimliği Seç ' i** seçin. Sağdaki bölmede, kullanılacak aboneliği ve yönetilen kimliği seçin. Ardından **Seç** seçeneğini belirleyin.

    Kullanıcı tarafından atanan yönetilen kimlik, birden fazla kaynağı yönetmek için kullanılabilecek tek başına bir Azure kaynağıdır. Daha fazla bilgi için bkz. [yönetilen kimlik türleri](../active-directory/managed-identities-azure-resources/overview.md).  

    Yeni bir yönetilen kimlik oluşturmanız gerekiyorsa, [Azure Portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme, silme veya rol atama](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)bölümündeki yönergeleri izleyin.
    
    ![Kullanıcı kimliği seçin](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    Kullanıcı kimliği, **şifreleme türü** ayarlarında gösterilir.

    **Şifreleme türü** ayarlarını şimdi daraltabilirsiniz.

    ![Şifreleme türü ayarlarında gösterilen seçili kullanıcı kimliği](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Yazılım tabanlı çift şifrelemeyi etkinleştirmek istiyorsanız, **çift şifrelemeyi (yüksek güvenlikli ortamlar için)** genişletin ve **sıra Için çift şifrelemeyi etkinleştir**' i seçin. 

    Yazılım tabanlı şifreleme, Data Box verilerin AES-256 bit şifrelemesine ek olarak gerçekleştirilir.

    > [!NOTE]
    > Bu seçeneğin etkinleştirilmesi, sipariş işleme ve veri kopyalamanın daha uzun sürmesiyle yapılır. Siparişinizi oluşturduktan sonra bu seçeneği değiştiremezsiniz.

    ![Veri kutusu içeri aktarma için güvenlik ekranı, Çift şifreleme](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Ileri ' yi seçin: devam etmek için **kişi ayrıntıları** .

11. **İletişim ayrıntılarında**, sevkıyat bilgilerinizi girmek Için **+ Sevkiyat Adresi Ekle** ' yi seçin.

    ![Sevkiyat Adresi Ekle](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. **Sevkiyat Adresi Ekle**' de, şirketinizin adını, adını ve posta adresini ve geçerli bir telefon numarasını belirtin. **Doğrula**'yı seçin. Hizmet, teslimat adresinde hizmetin kullanılabilirlik durumunu doğrular. Hizmet belirtilen teslimat adresinde kullanılabilir durumdaysa bu konuda bir bildirim gönderilir.

    ![Sevkiyat adresini doğrula](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Kendi kendine yönetilen aktarmanın kullanılabildiği bir bölgede sıralama yapıyorsanız, bu seçeneği belirleyebilirsiniz. Kendi kendine yönetilen kargo hakkında daha fazla bilgi için bkz. [otomatik olarak yönetilen gönderi kullanma](data-box-portal-customer-managed-shipping.md).

13. Sevkiyat ayrıntıları başarıyla doğrulandıktan sonra **Sevkiyat Adresi Ekle** ' yi seçin.

14. **Kişi ayrıntıları**' nda, Sevkiyat adresinizi ve e-posta adresinizi gözden geçirin. Hizmet, belirtilen e-posta adreslerine sipariş durumundaki güncelleştirmelerle ilgili bilgi gönderir.

    Grup yöneticisinin ayrılması durumunda da bildirim almaya devam etmek için bir grup e-postası kullanmanız önerilir.

    ![Kişi ayrıntıları](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. **İleri ' yi seçin: gözden geçir + düzen>**. Sipariş oluşturma işlemine devam etmek için hüküm ve koşulları kabul etmelisiniz.

16. **Sipariş**'i seçin. Siparişin oluşturulması birkaç dakika sürer.

    ![Siparişi Kaydet](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>XML dosyası kullanarak siparişi dışarı aktar

**XML dosyası kullan**' ı seçerseniz, dışarı aktarmak istediğiniz belirli kapsayıcıları ve Blobları (sayfa ve blok) belirtebilirsiniz. XML dosyanızı biçimlendirmek için [örnek XML dosya tablosu](#sample-xml-file) belirtimlerini izlemeniz gerekir. Aşağıdaki adımlarda, verilerinizi dışarı aktarmak için bir XML dosyasının nasıl kullanılacağı gösterilmektedir:

1. **Dışarı aktarma türü** için **XML dosyası kullan**' ı seçin. Bu, dışarı aktarmak istediğiniz belirli blob 'ları ve Azure dosyalarını belirten XML dosyasıdır. XML dosyasını eklemek için, **BIR XML dosyası seçmek üzere buraya tıklayın ' ı** seçin.

     ![Dışarı aktarma seçeneğini belirleyin, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Kapsayıcı oluşturmak için **+ kapsayıcı** ' yı seçin.

    ![Dışarı aktarma seçeneğini, kapsayıcıları seçin](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Azure portal sağ tarafında açılan **yeni kapsayıcı** sekmesinde kapsayıcı için bir ad ekleyin. Ad küçük harf olmalıdır ve sayılar ve tireler '-' içeremez. Ardından aşağı açılan liste kutusundan **ortak erişim düzeyini** seçin. Başkalarının verilerinize erişmesini engellemek için **özel (anonim olmayan erişim)** seçeneğini seçmenizi öneririz. Kapsayıcı erişim düzeyleri hakkında daha fazla bilgi için bkz. [kapsayıcı erişim izinleri](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Dışarı aktarma seçeneğini, yeni kapsayıcı ayarlarını seçin](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. **Oluştur**’u seçin.

   ![Dışarı aktarma seçeneğini belirleyin, yeni kapsayıcı oluşturun.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Kapsayıcınız başarıyla oluşturulursa şu iletiyi alırsınız:

   ![Kapsayıcı başarıyla oluşturuldu](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Oluşturduğunuz kapsayıcıyı seçin ve üzerine çift tıklayın.

   ![Kapsayıcı ayrıntılarını görüntüle](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Kapsayıcıya çift tıklamak kapsayıcı özellikleri görünümünü getirir. Artık, dışarı aktarmak istediğiniz Bloblar ve/veya Azure dosyaları listenizi içeren XML dosyanızı eklemek (veya taramak) istiyorsunuz. **Karşıya Yükle**’yi seçin.

   ![Blobu kapsayıcıya yükle](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. XML dosyasını kapsayıcıya başarıyla eklediniz. Yalnızca bu XML 'de belirttiğiniz Bloblar ve Azure dosyaları verilecek.

   ![XML dosyası kapsayıcıya eklendi](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Siparişi izleme

Siparişi verdikten sonra durumunu Azure portalından takip edebilirsiniz. Data Box siparişiniz ' ne gidin ve durumu görüntülemek için **genel bakış** ' a gidin. Portalda siparişin durumu **Sipariş edildi** olarak görünür.

Cihaz hazırlığı tamamlandığında, veri kopyalama işlemi seçili depolama hesaplarından başlar. Portal, **veri kopyalama sürüyor** durumunda sırayı gösterir.

![Dışarı aktarma sırası Data Box, veri kopyalama sürüyor](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box, verileri kaynak depolama hesabından kopyalar. Veri kopyalama işlemi tamamlandıktan sonra, Data Box kilitlenir ve Portal **kopyalama tamamlandı** durumunda sırayı gösterecektir.

![Dışarı aktarma sırası Data Box, veri kopyalama Tamam](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Cihaz kullanılamıyorsa bir bildirim alırsınız. Cihaz varsa, Microsoft gönderilecek cihazı belirler ve gönderimi hazırlar. Cihaz hazırlığı sırasında aşağıdaki eylemler gerçekleşir:

* Cihazla ilişkili her depolama hesabı için SMB paylaşımları oluşturulur.
* Her paylaşım için, kullanıcı adı veya parola gibi erişim kimlik bilgileri oluşturulur.
* Cihaz kilitli ve yalnızca cihaz kilidi açma parolası kullanılarak erişilebilir. Parolayı almak için Azure portal hesabınızda oturum açmanız ve **cihaz ayrıntıları**' nı seçmeniz gerekir.

Microsoft daha sonra cihazınızı bir bölgesel taşıyıcı aracılığıyla hazırlar ve dağıtır. Cihaz gönderildikten sonra bir takip numarası iletilir. Portalda sipariş durumu **Yola çıktı** olarak değişir.

![Data Box dışarı aktarma siparişi gönderildi](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Kendi kendine yönetilen kargo seçilmişse, cihaz veri merkezinden çekilmeye hazırsa sonraki adımlarla bir e-posta bildirimi alırsınız. Kendi kendine yönetilen kargo hakkında daha fazla bilgi için bkz. [kendi kendine yönetilen sevkıyat](data-box-portal-customer-managed-shipping.md).

![Kendi kendine yönetilen sevkıyat çekme için hazırlanıyor](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Siparişi iptal etme

Bu sırayı iptal etmek için Azure portal, **Genel Bakış ' a** gidin ve komut çubuğundan **iptal** ' i seçin.

Sipariş yerleştirdikten sonra, siparişi işlemeye başlamadan önce herhangi bir noktada iptal edebilirsiniz.

İptal edilen bir siparişi silmek için **genel bakış** ' a gidin ve komut çubuğundan **Sil** ' i seçin.

## <a name="sample-xml-file"></a>Örnek XML dosyası

Aşağıdaki XML, **XML dosyası kullan** seçeneğini belirlediğinizde dışa aktarma sırasının kullandığı bir blob adı, blob ön eki ve Azure dosyası örneği gösterir:

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

XML dosyalarına göre bazı önemli noktaları:

* XML etiketleri büyük/küçük harfe duyarlıdır ve yukarıdaki örnekte belirtilen şekilde tam olarak eşleşmelidir.
* Açılış ve kapanış etiketlerinin eşleşmesi gerekir.
* Hatalı XML etiketleri veya biçimlendirme veri dışa aktarma hatasına neden olabilir.
* Blob ve/veya dosya ön eki geçersizse veri verilmeyecek.

### <a name="examples-of-valid-blob-paths"></a>Geçerli blob yolları örnekleri

Aşağıdaki tabloda geçerli blob yollarının örnekleri gösterilmektedir:

   | Seçici | Blob yolu | Description |
   | --- | --- | --- |
   | İle başlar |/ |Depolama hesabındaki tüm Blobları dışa aktarır |
   | İle başlar |/$root/ |Kök kapsayıcıdaki tüm Blobları dışa aktarır |
   | İle başlar |/containers |Önek **kapsayıcılarıyla** başlayan herhangi bir kapsayıcıdaki tüm Blobları dışa aktarır |
   | İle başlar |/Container-Name/ |Kapsayıcı kapsayıcısındaki tüm Blobları dışarı aktarır **-adı** |
   | İle başlar |/Container-Name/prefix |Kapsayıcı kapsayıcısında tüm Blobları dışarı aktarır-önek **önekiyle** başlayan **ad** |
   | Eşittir |$root/logo.bmp |Kök kapsayıcısında blob **logo.bmp** dışarı aktarır |
   | Eşittir |8tbpageblob/mydata.txt |Blob **mydata.txt** , **8tbpageblob** kapsayıcısında dışarı aktarır |

## <a name="sample-log-files"></a>Örnek günlük dosyaları

Bu bölümde, dışa aktarma sırasında oluşturulan örnek günlük dosyaları sağlanır. Hata günlükleri otomatik olarak oluşturulur. Ayrıntılı günlük dosyası oluşturmak için, dışarı aktarma sırasını yapılandırırken Azure portal **ayrıntılı günlüğü içer** ' i seçmeniz gerekir.
Kopyalama ve ayrıntılı Günlükler hakkında daha fazla bilgi için bkz. [kayıtları kopyalama](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
>
> * Dışarı aktarma önkoşulları
> * Dışarı aktarma için bir Data Box sıralama
> * Dışarı aktarma sırasını izleyin
> * Dışarı aktarma sırasını iptal et

Data Box’ınızı ayarlama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box’ınızı ayarlama](./data-box-deploy-set-up.md)