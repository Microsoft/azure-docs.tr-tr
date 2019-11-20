---
title: Azure veri kutusu ağır cihazına veri kopyalama hizmeti aracılığıyla veri kopyalamak için öğretici | Microsoft Docs
description: Bu öğreticide, Azure veri kutusu ağır cihazınıza veri kopyalama hizmeti aracılığıyla veri kopyalama hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: adc28ea5f74ad16d4387d246ef73618a53ea26e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595759"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Öğretici: Verileri Azure veri kutusu ağır (Önizleme) kopyalamak için veri kopyası hizmeti kullanın

Bu öğreticide, bir ara ana olmadan veri kopyalama hizmetini kullanarak veri alımı açıklar. Veri kopyalama hizmeti yerel olarak Azure veri kutusu yoğun çalışır, ağa bağlı depolama (NAS) cihazınıza SMB üzerinden bağlanır ve verileri veri kutusu ağır kopyalar.

Veri kopyalama hizmeti kullanın:

- Burada Ara konakları kullanılabilir olmayabilir NAS ortamlarda.
- Alımı ve veri yükleme için hafta geçmesi küçük dosyaları ile. Veri kopyalama hizmeti alımı ve karşıya yükleme zamanı küçük dosyaları için önemli ölçüde artırır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Veri kutusu ağır için veri kopyalama

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Bu öğreticiyi tamamladınız: [Azure veri kutusu ağır kümesi](data-box-heavy-deploy-set-up.md).
2. Aldığınız, veri kutusu yoğun ve sipariş durumu Portalı'nda **teslim edildi**.
3. Veri kopyalama için bağlantı kurma şeklinizi kaynak NAS cihaz kimlik bilgileri var.
4. Yüksek hızlı bir ağa bağlı olursunuz. Hızlı kopya hızları için iki 40 GbE bağlantılar (düğüm başına bir adet) paralel olarak yararlanılabilir. 40 GbE bağlantı kullanılabilir değilse, en az iki 10 GbE bağlantılar (düğüm başına bir tane) sahip olmasını öneririz. 

## <a name="copy-data-to-data-box-heavy"></a>Veri kutusu ağır için veri kopyalama

NAS cihaza bağlandıktan sonra sonraki adım verilerinizin kopyalamaktır. Veri kopyalama başlamadan önce aşağıdaki konuları gözden geçirin:

- Veri kopyalama sırasında veri boyutu makalesinde açıklanan boyutu sınırları için uyduğundan emin olun [Azure depolama ve veri kutusu ağır sınırları](data-box-heavy-limits.md).
- Veri kutusu ağır tarafından karşıya yüklenen veriler veri kutusu ağır dışındaki diğer uygulamalar tarafından eşzamanlı olarak yüklenirse, karşıya yükleme işi hataları ve veri bozulmasına neden olabilir.
- Veri kopyalama hizmeti okuma gibi veri değiştirilmekte olduğundan, hatalar veya veri bozulması görebilirsiniz.

Veri kopyalama hizmetini kullanarak verileri kopyalamak için bir iş oluşturmak gerekir:

1. Yerel web kullanıcı Arabirimi veri kutusu ağır cihazınızın, Git **Yönet** > **veri kopyalama**.
2. Üzerinde **veri kopyalama** sayfasında **Oluştur**.

    !["Veri Kopyala" sayfasında Oluştur'u seçin](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. İçinde **Yapılandır işi ve başlangıç** iletişim kutusunda, aşağıdaki alanları doldurun:
    
    |Alan                          |Değer    |
    |-------------------------------|---------|
    |**İş adı**                       |Benzersiz bir ad 230'dan az karakter için bir proje. Bu karakterler proje adlarında izin verilmeyen: \<, \>, \|, \?, \*, \\, \:, \/, ve \\\.         |
    |**Kaynak konumu**                |Veri kaynağı biçimde SMB yolunu belirtin: `\\<ServerIPAddress>\<ShareName>` veya `\\<ServerName>\<ShareName>`.        |
    |**Kullanıcı Adı**                       |Kullanıcı adını `\\<DomainName><UserName>` veri kaynağına erişmek için kullanılan biçim. Yerel yönetici bağlanırsa, bunlar açık güvenlik izinleri gerekir. Klasöre sağ tıklayın, **özellikleri** seçip **güvenlik**. Bu, yerel yönetici olarak eklemeniz gerekir **güvenlik** sekmesi.       |
    |**Parola**                       |Veri kaynağına erişmek için parola.           |
    |**Hedef depolama hesabı**    |Liste için verileri yüklemek için hedef depolama hesabını seçin.         |
    |**Hedef türü**       |Listeden hedefi depolama türü seçin: **Blok Blobunda**, **sayfa blobu**, veya **Azure dosyaları**.        |
    |**Hedef kapsayıcı/paylaşma**    |Kapsayıcı adını girin ya da hedef depolama hesabınız için verileri karşıya yüklemek istediğiniz paylaşabilirsiniz. Ad, bir paylaşım adı veya kapsayıcı adı olabilir. Örneğin `myshare` veya `mycontainer` kullanın. Adı biçiminde girebilirsiniz `sharename\directory_name` veya `containername\virtual_directory_name`.        |
    |**Desenle eşleşen dosyaları Kopyala**    | Aşağıdaki iki yoldan dosya adı eşleşen desen girebilirsiniz:<ul><li>**Joker karakter ifade kullanın:** Yalnızca `*` ve `?` joker ifadelerde desteklenir. Örneğin, ifade `*.vhd` sahip tüm dosyaları eşleşen `.vhd` uzantısı. Benzer şekilde, `*.dl?` ya da uzantılı tüm dosyaları eşleşen `.dl` veya ile başlayıp `.dl`, gibi `.dll`. Benzer şekilde, `*foo` adları ile bitemez tüm dosyaları eşleşen `foo`.<br>Bu gibi durumlarda, joker karakter ifadesini doğrudan alanında girebilirsiniz. Varsayılan bir joker karakter ifadesini alanına girdiğiniz değer kabul edilir.</li><li>**Normal ifadeler kullanın:** Normal ifadeler POSIX tabanlı desteklenir. Örneğin, normal ifade `.*\.vhd` sahip tüm dosyaları eşleşecektir `.vhd` uzantısı. Normal ifadeler için sağlamak `<pattern>` doğrudan olarak `regex(<pattern>)`. Normal ifadeler hakkında daha fazla bilgi için Git [normal ifade dili - hızlı başvuru](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Dosya iyileştirmesi**              |Bu özellik etkinleştirildiğinde, dosya 1 MB'tan küçük alımı sırasında paketlenir. Bu paket, küçük dosyaları için veri kopyalama hızlandırır. Dosya sayısı şu ana kadar dizinler sayısını aştığında zaman önemli miktarda de tasarruf sağlar.        |
 
4. Seçin **Başlat**. Girişleri doğrulanır ve doğrulama başarılı olursa, iş başlatır. Bu işin başlatılması birkaç dakika sürebilir.

    !["Yapılandır işi ve Başlangıç" iletişim kutusundan bir proje başlatın](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Belirtilen ayarlarla bir iş oluşturulur. Duraklatma, sürdürme iptal veya bir işi yeniden başlatın. İş adı yanındaki onay kutusunu işaretleyin ve ardından uygun düğmesini seçin.

    !["Veri Kopyala" sayfasında bir iş yönetimi](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Yoğun saatlerde NAS cihazın kaynaklara etkileniyor, bir işi duraklatabilirsiniz:

        !["Veri Kopyala" sayfasında bir iş duraklatılamadı](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Daha sonra yoğun olmayan saatlerinde işin devam edebilir:

        !["Veri Kopyala" sayfasında bir işini sürdürme](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Bir işi dilediğiniz zaman iptal edebilirsiniz:

        !["Veri Kopyala" sayfasında bir işi iptal et](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Bir işi iptal ettiğinizde bir onay gereklidir:

        ![İşi iptal işlemini Onayla](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Bir işi iptal etmek karar verirseniz, zaten kopyalanan veriler silinmez. Data Box cihazınıza kopyalamış olduğunuz tüm verileri silmek için cihazı sıfırlayın.

        ![Bir cihazı Sıfırla](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Büyük dosyaları, yalnızca kısmen iptal ya da bir işi duraklatmak, kopyalanabilir. Bu kısmen kopyalanan dosyalar aynı durumda azure'a yüklenir. İptal etme ya da bir iş duraklatılamadı dosyalarınızı düzgün şekilde kopyalandığını doğrulayın. Vstemplate dosyalarını doğrulamak için SMB paylaşımları arayın ya BOM dosyasını indirin.

    - Bir sorun gibi geçici bir hata nedeniyle başarısız olması durumunda, bir işi yeniden başlatabilirsiniz. Ancak bu gibi bir terminal durumuna ulaştı, bir iş yeniden başlatılamıyor **başarılı** veya **hatalarla tamamlandı**. İş hataları dosya adlandırma veya dosya boyutu sorunlarından kaynaklanıyor olabilir. Bu hatalar kaydedilir, ancak bu tamamlandıktan sonra işi yeniden başlatılamıyor.

        ![Başarısız işi yeniden başlatın](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Bir hatayla karşılaşabilir ve işi yeniden başlatın, hata günlüklerini indir ve hata günlük dosyalarını arayın. Sorunu düzelttikten sonra dosyaları kopyalamak için yeni bir proje oluşturun. Ayrıca [SMB üzerinden dosyaları kopyalama](data-box-deploy-copy-data.md).
    
    - Bu sürümde, bir iş silinemiyor.
    
    - Sınırsız işleri oluşturabilirsiniz, ancak herhangi bir anda yalnızca en fazla 10 projelerin paralel olarak çalıştırılabilir.
    - Varsa **dosya iyileştirmesi** açıktır, paketlenmiş küçük dosyalara kopyalama performansını artırmak için alma. Bu gibi durumlarda, bir paket dosyası (dosya adıyla bir GUID olacaktır) görürsünüz. Bu dosyayı silmeyin. Paketten karşıya yükleme sırasında olacaktır.

6. İş üzerinde devam ederken **veri kopyalama** sayfası:

    - İçinde **durumu** sütun kopyalama işinin durumunu görüntüleyebilirsiniz. Durum olabilir:
        - **Çalıştıran**
        - **Başarısız**
        - **Başarılı oldu**
        - **Duraklatma**
        - **Duraklatıldı**
        - **İptal ediliyor**
        - **İptal edildi**
        - **Hatalarla tamamlandı**
    - İçinde **dosyaları** sütun sayısı ve toplam kopyalanan dosya boyutunu görebilirsiniz.
    - İçinde **işlenen** sütun sayısı ve işlenen dosyaların toplam boyutu görebilirsiniz.
    - İçinde **iş ayrıntıları** sütunundaki **görünümü** iş ayrıntılarını görmek için.
    - Gösterildiği gibi kopyalama işlemi sırasında herhangi bir hata meydana gelirse **# hataları** sütun, Git **hata günlüğü** sütun ve Hata günlüklerini sorun giderme için indirme.

Kopyalama işinin tamamlanmasını bekleyin. Bazı hatalar yalnızca kaydedilir çünkü **Bağlan ve Kopyala** sayfasında, sonraki adıma geçmeden önce kopyalama işinin hatasız tamamlandığını emin olun.

!["Bağlan ve Kopyala" sayfasında hata yok](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Veri bütünlüğünü sağlamak için verileri kopyalanırken bir sağlama toplamı hesaplanan satır içidir. Kopyalama tamamlandıktan sonra seçip **panoyu görüntüle** kullanılan alan ve boş alan Cihazınızda doğrulayın.
    
![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> İkinci düğümü veri kutusu yoğun veri kopyalamak için yönergeleri yineleyin.

Kopyalama işi tamamlandıktan sonra seçebileceğiniz **göndermeye hazırlama**.

>[!NOTE]
> **Göndermeye hazırlama** kopyası işleri devam ederken çalıştırılamaz.

## <a name="next-steps"></a>Sonraki adımlar

Veri kutusu ağır Cihazınızı Microsoft'a gönderin öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure veri kutusu ağır Cihazınızı Microsoft'a gönderin](./data-box-heavy-deploy-picked-up.md)

