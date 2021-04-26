---
title: Azure geçişi sunucu değerlendirmesi ile içeri aktarılan bir CSV dosyası kullanarak şirket içi sunucuları değerlendirme
description: Azure geçişi sunucu değerlendirmesi ' nde içeri aktarılan CSV dosyası kullanarak Azure 'a geçiş için şirket içi sunucuların nasıl keşfedileceğini açıklar
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: c142cae3e96d800488b67da613181d1a91ba5b5b
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713327"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Öğretici: içeri aktarılan bir CSV dosyası kullanarak sunucuları değerlendirme

Azure 'a geçiş sürecinizin bir parçası olarak şirket içi envanterinizi ve iş yüklerinizi keşfedeceksiniz. 

Bu öğreticide, içeri aktarılan bir virgülle ayrılmış değerler (CSV) dosyası kullanarak Azure geçişi: Sunucu değerlendirmesi aracı ile şirket içi makineleri değerlendirme gösterilmektedir. 

Bir CSV dosyası kullanıyorsanız, sunucuları bulup değerlendirmek için Azure geçişi gereci ayarlamanız gerekmez. Dosya içinde paylaştığınız verileri denetleyebilir ve verilerin büyük bölümü isteğe bağlıdır. Bu yöntem şu durumlarda yararlı olur:

- Aleti dağıtmadan önce hızlı bir başlangıç değerlendirmesi oluşturmak istiyorsanız.
- Azure Geçişi aletini kuruluşunuzda dağıtma imkanınız yoksa.
- Şirket içi sunuculara erişim sağlayan kimlik bilgilerini paylaşamıyorsanız.
- Güvenlik kısıtlamaları nedeniyle alet tarafından toplanan verilerin Azure'a gönderilmesini sağlayamıyorsanız.

> [!NOTE]
> CSV dosyası kullanılarak içeri aktarılan sunucuları geçiremezsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure hesabı ayarlama
> * Azure Geçişi projesi oluşturma
> * CSV dosyası hazırlama
> * Dosyayı içeri aktarma
> * Sunucuları değerlendir

> [!NOTE]
> Öğreticiler senaryo denemek için en hızlı yolu gösterir ve mümkün olan yerlerde varsayılan seçenekleri kullanır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Tek bir CSV dosyasında ve bir Azure geçişi projesinde en fazla 20.000 sunucu ekleyebilirsiniz. 
- CSV dosyasında belirtilen işletim sistemi adları, [desteklenen adları](#supported-operating-system-names)içermelidir ve bu adlara uymalıdır.


## <a name="prepare-an-azure-user-account"></a>Azure Kullanıcı hesabı hazırlama

Bir Azure geçişi projesi oluşturmak için şunları içeren bir hesap gerekir:
- Azure aboneliğinde katkıda bulunan veya sahip izinleri.
- Azure Active Directory uygulamaları kaydetme izinleri.

Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz. Abonelik sahibi değilseniz, izinleri aşağıdaki şekilde atamak için sahibiyle birlikte çalışın:

1. Azure portal, "abonelikler" araması yapın ve **Hizmetler** altında **abonelikler**' i seçin.

    ![Azure aboneliğini aramak için arama kutusu](./media/tutorial-discover-import/search-subscription.png)

2. **Abonelikler** sayfasında, Azure geçişi projesi oluşturmak istediğiniz aboneliği seçin. 
3. Abonelikte **erişim denetimi (IAM)**  >  **erişimi denetle**' yi seçin.
4. **Erişimi denetle**' de ilgili Kullanıcı hesabını arayın.
5. **Rol ataması Ekle**' de **Ekle**' yi seçin.

    ![Erişimi denetlemek ve rol atamak için bir kullanıcı hesabı arayın](./media/tutorial-discover-import/azure-account-access.png)

6. **Rol ataması Ekle**' de, katkıda bulunan veya sahip rolünü seçin ve hesabı (örneğimizde azmigrateuser) seçin. Sonra **Kaydet**'i seçin.

    ![Hesaba rol atamak için rol ataması Ekle sayfasını açar](./media/tutorial-discover-import/assign-role.png)

7. Portalda, kullanıcılar için arama yapın ve **Hizmetler** altında **Kullanıcılar**' ı seçin.
8. **Kullanıcı ayarları**' nda, Azure AD kullanıcılarının uygulamaları kaydedebildiğini doğrulayın (varsayılan olarak **Evet** ' e ayarlanır).

    ![Kullanıcıların Active Directory uygulamalar kaydedebildiğini Kullanıcı ayarlarında doğrula](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Proje ayarlama

Yoksa yeni bir Azure geçişi projesi ayarlayın.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel bakış** bölümünde **proje oluştur**' u seçin.
5. **Proje oluştur**' da Azure aboneliğinizi ve kaynak grubunuzu seçin. Yoksa, bir kaynak grubu oluşturun.
6. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin. [Kamu](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government)için desteklenen coğrafi lıkları gözden geçirin.

   ![Proje adı ve bölgesi için kutular](./media/tutorial-discover-import/new-project.png)  
    > [!Note]
    > Özel uç nokta bağlantısına sahip bir Azure geçişi projesi oluşturmak için **Gelişmiş** yapılandırma bölümünü kullanın. [Daha fazla bilgi edinin](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity)

7. **Oluştur**’u seçin.
8. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin.

**Azure geçişi: Sunucu değerlendirmesi** Aracı, varsayılan olarak yeni projeye eklenir.

![Varsayılan olarak eklenen sunucu değerlendirmesi aracını gösteren sayfa](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>CSV dosyasını hazırlama

CSV şablonunu indirin ve içine sunucu bilgilerini ekleyin.

### <a name="download-the-template"></a>Şablonu indirme

1. **Geçiş hedefleri** > **Sunucular** > **Azure Geçişi: Sunucu Değerlendirmesi** bölümünde **Bul**'u seçin.
2. **Makineleri bul** bölümünde **CSV kullanarak içeri aktar**'ı seçin.
3. CSV şablonunu indirmek için **İndir**'i seçin. Alternatif olarak [buradan doğrudan indirebilirsiniz](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![CSV şablonunu indirin](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Sunucu bilgilerini ekleyin

Sunucu verilerini toplayın ve CSV dosyasına ekleyin.

- Verileri toplamak için şirket içi sunucu yönetiminde kullandığınız VMware vSphere veya yapılandırma yönetimi veritabanınız (CMDB) gibi araçlarda bulunan verileri dışarı aktarabilirsiniz.
- Örnek verileri gözden geçirmek için [örnek dosyayı](https://go.microsoft.com/fwlink/?linkid=2108405) indirin.

Aşağıdaki tabloda doldurulacak dosya alanları özetlenmiştir:

**Alan adı** | **Zorunlu** | **Ayrıntılar**
--- | --- | ---
**Sunucu adı** | Yes | Tam etki alanı adını (FQDN) belirtmeniz önerilir.
**IP adresi** | Hayır | Sunucunun adresi.
**Çekirdekler** | Yes | Sunucu için ayrılmış olan işlemci çekirdeği sayısı.
**Bellek** | Yes | Sunucu için ayrılmış olan toplam RAM miktarı (MB).
**İşletim sistemi adı** | Yes | Sunucunun işletim sistemi. <br/> Değerlendirme aracı, [bu listede](#supported-operating-system-names) yer alan adlarla eşleşen veya onları içeren işletim sistemi adlarını tanır.
**İşletim sistemi sürümü** | Hayır | Sunucunun işletim sistemi sürümü.
**İşletim sistemi mimarisi** | Hayır | Sunucunun işletim sistemi mimarisi <br/> Geçerli değerler: x64, x86, amd64, 32-bit veya 64-bit
**Disk sayısı** | Hayır | Her diskle ilgili ayrıntılı bilgi sağlanmışsa gerekli değildir.
**Disk 1 boyutu**  | Hayır | Maksimum disk boyutu (GB).<br/>Şablona [sütun ekleyerek](#add-multiple-disks) daha fazla diskin ayrıntılarını ekleyebilirsiniz. En fazla sekiz disk ekleyebilirsiniz.
**Disk 1 okuma işlemi/saniye** | Hayır | Saniyede gerçekleştirilen disk okuma işlemi sayısı.
**Disk 1 yazma işlemi/saniye** | Hayır | Saniyede gerçekleştirilen disk yazma işlemi sayısı.
**Disk 1 okuma aktarım hızı** | Hayır | Diskten bir saniyede okunan veri miktarı (MB/sn).
**Disk 1 yazma aktarım hızı** | Hayır | Diske bir saniyede yazılan veri miktarı (MB/sn).
**CPU kullanım yüzdesi** | Hayır | Kullanılan CPU yüzdesi.
**Bellek kullanım yüzdesi** | Hayır | Kullanılan RAM yüzdesi.
**Toplam disk okuma işlemi/saniye** | Hayır | Saniye başına disk okuma işlemi.
**Toplam disk yazma işlemi/saniye** | Hayır | Saniye başına disk yazma işlemi.
**Toplam disk okuma aktarım hızı** | Hayır | Diskten MB/saniye cinsinden okunan veriler.
**Toplam disk yazma aktarım hızı** | Hayır | Diske yazılan veriler MB/saniye cinsinden.
**Ağa gelen aktarım hızı** | Hayır | Sunucu tarafından alınan veri miktarı (MB/sn).
**Ağdan giden aktarım hızı** | Hayır | Sunucu tarafından gönderilen veri miktarı (MB/sn).
**Üretici yazılımı türü** | Hayır | Sunucunun üretici yazılımı. Değerler "BIOS" veya "UEFI" olabilir.
**MAC adresi**| Hayır | Sunucunun MAC adresi.


### <a name="add-operating-systems"></a>İşletim sistemi ekleme

Değerlendirme, belirli işletim sistemi adlarını tanır. Belirttiğiniz adların [desteklenen adlar listesinde](#supported-operating-system-names) yer alan girişlerle tam olarak eşleşmesi gerekir.

### <a name="add-multiple-disks"></a>Birden çok disk ekleme

Şablonda ilk disk için varsayılan alanlar verilmiştir. Benzer sütunlar ekleyerek en fazla sekiz disk ekleyebilirsiniz.

Örneğin ikinci disk için tüm alanları belirtmek isterseniz şu sütunları ekleyin:

- Disk 2 boyutu
- Disk 2 okuma işlemi/saniye
- Disk 2 yazma işlemi/saniye
- Disk 2 okuma aktarım hızı
- Disk 2 yazma aktarım hızı


## <a name="import-the-server-information"></a>Sunucu bilgilerini içeri aktarma

CSV şablonuna bilgi ekledikten sonra, CSV dosyasını sunucu değerlendirmesi içine aktarın.

1. Azure Geçişi'nin **Makineleri bul** bölümünde doldurduğunuz şablonu seçin.
2. **İçeri aktar**'ı seçin.
3. İçeri aktarma durumu gösterilir.
    - Durum alanında uyarılar görünürse bunları düzeltebilir veya düzeltmeden devam edebilirsiniz.
    - Değerlendirmenin doğruluğunu artırmak için uyarıları dikkate alarak sunucu bilgilerini düzeltmeniz önerilir.
    - Uyarıları görüntülemek ve düzeltmek için **Uyarı ayrıntılarını indir (CSV)** öğesini seçin. Bunu yaptığınızda CSV dosyası uyarılarla birlikte indirilir. Uyarıları inceleyin ve sorunları gerektiği gibi giderin.
    - Durumda hata görünürse ve içeri aktarma işlemi **Başarısız** olursa içeri aktarma işlemini gerçekleştirmek için ilgili hataları mutlaka gidermeniz gerekir:
        1. Hata bilgilerinin yer aldığı CSV dosyasını indirin.
        1. Hataları gözden geçirin ve gerektiği gibi düzeltin. 
        1. Değiştirdiğiniz dosyayı yeniden karşıya yükleyin.
4. İçeri aktarma durumu **Tamamlandı** olduğunda sunucu bilgileri içeri aktarılmış demektir. İçeri aktarma işleminin tamamlanmamış gibi görünmediğini yenileyin.

## <a name="update-server-information"></a>Sunucu bilgilerini güncelleştirme

Aynı **Sunucu adı** değerini kullanarak yeniden veri içeri aktardığınızda sunucu bilgilerini güncelleştirmiş olursunuz. **Sunucu adı** alanını değiştiremezsiniz. Şu an için sunucuları silme işlemi desteklenmez.

## <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulma işlemi sonrasında sunucuların Azure portalında göründüğünü doğrulamak için:

1. Azure Geçişi panosunu açın.
2. **Azure Geçişi - Sunucular** > **Azure Geçişi: Sunucu Değerlendirmesi** sayfasında **Bulunan sunucuların** sayısını gösteren simgeyi seçin.
3. **İçeri aktarma tabanlı** sekmesini seçin.



## <a name="supported-operating-system-names"></a>Desteklenen işletim sistemi adları

CSV 'de belirtilen işletim sistemi adları içermesi ve eşleşmesi gerekir. Aksi takdirde, bunları değerlendiremeyeceksiniz. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>macOS X 10<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Çevre sistemleri eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure geçişi projesi oluşturma 
> * İçeri aktarılan bir CSV dosyası kullanan sunucular bulundu. Şimdi, [Azure VM 'Lerine VMware VM geçişi](./tutorial-assess-vmware-azure-vm.md)için bir değerlendirme çalıştırın.