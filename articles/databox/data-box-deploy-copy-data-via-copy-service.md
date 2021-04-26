---
title: 'Öğretici: cihazınıza kopyalamak için veri kopyalama hizmetini kullanın'
titleSuffix: Azure Data Box
description: Bu öğreticide, veri kopyalama hizmeti aracılığıyla Azure Data Box cihazınıza veri kopyalamayı öğreneceksiniz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: e664055893bbdef0f7090811b8a160a1b8a4a1fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124057"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Öğretici: verileri Azure Data Box kopyalamak için veri kopyalama hizmetini kullanma (Önizleme)

Bu öğreticide, bir ara ana bilgisayar olmadan veri kopyalama hizmeti kullanılarak verilerin nasıl kullanılacağı açıklanmaktadır. Veri kopyalama hizmeti Microsoft Azure Data Box yerel olarak çalışır, SMB aracılığıyla ağa bağlı depolama (NAS) cihazınıza bağlanır ve verileri Data Box kopyalar.

Veri kopyalama hizmetini kullanın:

- Ara ana bilgisayarların kullanılabilir olabileceği NAS ortamlarında.
- Veri alma ve karşıya yükleme için hafta alan küçük dosyalarla birlikte. Veri kopyası hizmeti, küçük dosyalar için alma ve karşıya yükleme süresini önemli ölçüde geliştirir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Data Box'a veri kopyalama

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Bu öğreticiyi tamamladınız: [Azure Data Box ayarlayın](data-box-deploy-set-up.md).
2. Data Box cihazınızı aldınız ve portaldaki sipariş durumu **teslim edildi**.
3. Veri kopyalama için bağlanacağı kaynak NAS cihazının kimlik bilgileri vardır.
4. Yüksek hızlı bir ağa bağlanırsınız. En az 1 10-Gigabit Ethernet (GbE) bağlantınızın olması önemle önerilir. 10-GbE bağlantı kullanılamıyorsa 1-GbE veri bağlantısı kullanabilirsiniz, ancak kopyalama hızları etkilenecektir.

## <a name="copy-data-to-data-box"></a>Data Box'a veri kopyalama

NAS cihazına bağlandıktan sonra, bir sonraki adım verilerinizi kopyalayacağız. Verileri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

* Verileri kopyalarken, veri boyutunun [Azure depolama ve Data Box sınırları](data-box-limits.md)makalesinde açıklanan boyut sınırlarına uyduğundan emin olun.

* Data Box tarafından karşıya yüklenen veriler Data Box dışındaki diğer uygulamalar tarafından aynı anda karşıya yüklenirse karşıya yükleme-iş hatası ve veri bozulması oluşabilir.

* Veri kopyası hizmeti okurken veri değiştiriliyorsa, verilerin başarısızlıklarını veya bozulmasını görebilirsiniz.

> [!IMPORTANT]
> Data Box'ın verilerinizi Azure Depolama'ya aktardığını onaylayana kadar kaynak verilerinizin bir kopyasına sahip olduğunuzdan emin olun.

Veri kopyalama hizmetini kullanarak veri kopyalamak için bir iş oluşturmanız gerekir:

1. Data Box cihazınızın yerel Web Kullanıcı arabiriminde,   >  **kopyalama verilerini** Yönet ' e gidin.
2. **Verileri Kopyala** sayfasında **Oluştur**' u seçin.

    !["Verileri Kopyala" sayfasında oluştur ' u seçin.](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. **İşi ve başlatmayı yapılandır** iletişim kutusunda aşağıdaki alanları doldurun:
    
    |Alan                          |Değer    |
    |-------------------------------|---------|
    |**İş adı**                       |İş için 230 karakterden daha az benzersiz bir ad. İş adında Bu karakterlere izin verilmez: \<, \> ,,, \| \? \* , \\ , \: , \/ ve \\\.         |
    |**Kaynak konumu**                |Veri kaynağına ilişkin SMB yolunu şu biçimde belirtin: `\\<ServerIPAddress>\<ShareName>` veya `\\<ServerName>\<ShareName>` .        |
    |**Kullanıcı adı**                       |`\\<DomainName><UserName>`Veri kaynağına erişmek için biçimdeki Kullanıcı adı. Bir yerel yönetici bağlanıyorsa, açık güvenlik izinleri gerekecektir. Klasöre sağ tıklayın, **Özellikler** ' i seçin ve ardından **güvenlik**' i seçin. Bu, **güvenlik** sekmesine yerel yönetici eklemesi gerekir.       |
    |**Parola**                       |Veri kaynağına erişmek için parola.           |
    |**Hedef depolama hesabı**    |Verileri listeden karşıya yüklemek için hedef depolama hesabını seçin.         |
    |**Hedef türü**       |Listeden hedef depolama türünü seçin: **Blok Blobu**, **Sayfa Blobu** veya **Azure dosyaları**.        |
    |**Hedef kapsayıcı/paylaşma**    |Hedef depolama hesabınızda veri yüklemek istediğiniz kapsayıcının veya paylaşımın adını girin. Ad bir paylaşma adı veya kapsayıcı adı olabilir. Örneğin `myshare` veya `mycontainer` kullanın. Adı veya biçiminde de girebilirsiniz `sharename\directory_name` `containername\virtual_directory_name` .        |
    |**Dosya düzeniyle eşleşen dosya Kopyala**    | Dosya adı eşleştirme modelini aşağıdaki iki şekilde girebilirsiniz:<ul><li>**Joker karakter Ifadesi kullanın:** Yalnızca `*` ve `?` Joker ifadelerinde desteklenir. Örneğin, ifadesi `*.vhd` uzantısına sahip olan tüm dosyalarla eşleşir `.vhd` . Benzer şekilde, `*.dl?` uzantısı olan `.dl` veya ile başlayan tüm dosyalarla eşleşir `.dl` `.dll` . Benzer şekilde, `*foo` adları ile biten tüm dosyalarla eşleşir `foo` .<br>Joker karakter ifadesini alana doğrudan girebilirsiniz. Varsayılan olarak, alana girdiğiniz değer joker karakter ifadesi olarak değerlendirilir.</li><li>**Normal Ifadeleri kullan:** POSIX tabanlı normal ifadeler desteklenir. Örneğin, normal ifade `.*\.vhd` uzantısına sahip olan tüm dosyalarla eşleşir `.vhd` . Normal ifadeler için `<pattern>` doğrudan olarak ' ı belirtin `regex(<pattern>)` . Normal ifadeler hakkında daha fazla bilgi için bkz. [normal ifade dili-hızlı başvuru](/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Dosya iyileştirme**              |Bu özellik etkinleştirildiğinde 1 MB 'den küçük dosyalar alma sırasında paketlenmiştir. Bu paketleme, küçük dosyalar için veri kopyasının hızlanmasını hızlandırır. Ayrıca dosya sayısının dizin sayısını aştığında önemli miktarda zaman kazandırır.        |
 
4. **Başlat**'ı seçin. Girişler onaylanır ve doğrulama başarılı olursa iş başlatılır. İşin başlatılması birkaç dakika sürebilir.

    !["İşi Yapılandır ve Başlat" iletişim kutusundan bir iş başlatın](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Belirtilen ayarlara sahip bir iş oluşturulur. Bir işi duraklatabilir, sürdürebilir, iptal edebilir veya yeniden başlatabilirsiniz. İş adının yanındaki onay kutusunu işaretleyin ve ardından uygun düğmeyi seçin.

    !["Veri kopyalama" sayfasında bir işi yönetme](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Yoğun saatlerde NAS cihazının kaynaklarını etkilediğinden bir işi duraklatabilirsiniz:

        !["Veri kopyalama" sayfasında bir işi duraklatma](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        İşi yoğun olmayan saatlerde daha sonra sürdürebilirsiniz:

        !["Veri kopyalama" sayfasında bir işi sürdürür](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - İstediğiniz zaman bir işi iptal edebilirsiniz:

        !["Verileri kopyalama" sayfasında bir işi iptal etme](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Bir işi iptal ettiğinizde, bir onay gereklidir:

        ![İş iptalini onayla](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Bir işi iptal etme kararı verirseniz, zaten kopyalanmış olan veriler silinmez. Data Box cihazınıza kopyaladığınız tüm verileri silmek için, cihazı sıfırlayın.

        ![Bir cihazı sıfırlama](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Bir işi iptal edebilir veya duraklatabilirsiniz, büyük dosyalar yalnızca kısmen kopyalanabilir. Bu kısmen kopyalanan dosyalar Azure 'a aynı duruma yüklenir. Bir işi iptal ettiğinizde veya duraklattığınızda, dosyalarınızın düzgün şekilde kopyalandığından emin olun. Dosyaları doğrulamak için, SMB paylaşımlarına bakın veya ürün reçetesi dosyasını indirin.

    - Ağ hatası gibi geçici bir hata nedeniyle başarısız olduysa bir işi yeniden başlatabilirsiniz. Ancak, **başarılı** veya **hatalarla tamamlanan** bir Terminal durumuna ulaştıysa bir işi yeniden başlatmazsanız. Dosya adlandırma veya dosya boyutu sorunlarından dolayı iş hataları olabilir. Bu hatalar günlüğe kaydedilir, ancak işlem tamamlandıktan sonra yeniden başlatılamaz.

        ![Başarısız işi yeniden Başlat](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Bir hata yaşarsanız ve işi yeniden başlatmazsanız, hata günlüklerini indirin ve günlük dosyalarındaki hatayı bulun. Sorunu düzelttikten sonra, dosyaları kopyalamak için yeni bir iş oluşturun. Ayrıca [, dosyaları SMB üzerinden de kopyalayabilirsiniz](data-box-deploy-copy-data.md).
    
    - Bu sürümde, bir işi silemezsiniz.
    
    - Sınırsız iş oluşturabilirsiniz, ancak herhangi bir anda yalnızca en fazla 10 işi paralel olarak çalıştırabilirsiniz.
    - **Dosya iyileştirmesi** açık ise, küçük dosyalar kopyalama performansını iyileştirmek için alma sırasında paketlenmiştir. Bu durumlarda, paketlenmiş bir dosya görürsünüz (dosya adı olarak bir GUID olacaktır). Bu dosyayı silmeyin. Karşıya yükleme sırasında paketi açılır.

6. İş devam ederken, **verileri Kopyala** sayfasında:

    - **Durum** sütununda kopyalama işinin durumunu görüntüleyebilirsiniz. Durum şu olabilir:
        - **Çalışma**
        - **Başarısız**
        - **Başarılı**
        - **Duraklatılıyor**
        - **Duraklatıldı**
        - **İptal Ediliyor**
        - **İptal edildi**
        - **Hatalarla tamamlandı**
    - **Dosyalar** sütununda, kopyalanan dosyaların sayısını ve toplam boyutunu görebilirsiniz.
    - **İşlenen** sütununda, işlenen dosyaların sayısını ve toplam boyutunu görebilirsiniz.
    - **İş ayrıntıları sütununda,** iş ayrıntılarını görmek için **görüntüle** ' yi seçin.
    - Kopyalama işlemi sırasında, **# Errors** sütununda gösterildiği gibi herhangi bir hata oluşursa **hata günlüğü** sütununa gidin ve sorun giderme için hata günlüklerini indirin.

Kopyalama işinin bitmesini bekleyin. Bazı hatalar yalnızca **Bağlan ve Kopyala** sayfasında günlüğe kaydedildiği için, sonraki adıma geçmeden önce kopyalama işinin hatasız şekilde bittiğinden emin olun.

!["Bağlan ve Kopyala" sayfasında hata yok](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Veri bütünlüğünü sağlamak için, veriler kopyalanırken bir sağlama toplamı satır içi olarak hesaplanır. Kopyalama işlemi tamamlandıktan sonra, cihazınızdaki kullanılan alanı ve boş alanı doğrulamak için **panoyu görüntüle** ' yi seçin.
    
![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Kopyalama işi tamamlandıktan sonra **göndermeye hazırlama**' yi seçebilirsiniz.

>[!NOTE]
> Kopyalama işleri devam ederken **göndermeye hazırlama** çalıştırılamaz.

## <a name="next-steps"></a>Sonraki adımlar

Data Box cihazınızı Microsoft 'a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Data Box cihazınızı Microsoft 'a gönderin](./data-box-deploy-picked-up.md)