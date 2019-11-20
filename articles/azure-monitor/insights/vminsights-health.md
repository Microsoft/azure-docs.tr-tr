---
title: Azure sanal makinelerinizin durumunu anlayın | Microsoft Docs
description: Bu makalede, VM'ler için Azure İzleyici ile sanal makinelerin ve temel alınan işletim sistemlerinin sistem durumunun nasıl anlaşılması açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 5fd5295e52f0fef5e1432fdb2f81d2ba0e1717e8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109774"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Azure sanal makinelerinizin sistem durumunu anlayın

Azure, izleme alanındaki belirli roller veya görevler için hizmetler içerir, ancak Azure sanal makinelerinde (VM 'Ler) barındırılan işletim sistemlerinin (OSs) ayrıntılı durum perspektiflerini sağlamaz. Azure Izleyici 'yi farklı koşullar için kullanabilseniz de, çekirdek bileşenlerin sistem durumunu veya VM 'lerin genel sistem durumunu modellemek ve göstermek üzere tasarlanmamıştır.

VM'ler için Azure İzleyici sistem durumunu kullanarak bir Windows veya Linux konuk işletim sisteminin kullanılabilirliğini ve performansını etkin bir şekilde izleyebilirsiniz. Sağlık özelliği, anahtar bileşenlerini ve bunların ilişkilerini temsil eden bir model kullanır, bileşen durumunun nasıl ölçülmesi gerektiğini belirten ölçütler sağlar ve sağlıksız bir durum algıladığında uyarı gönderir.

Bir Azure VM 'nin genel sistem durumunu ve temel alınan işletim sistemini görüntülemek, iki perspektiften gözlemlenebilir: doğrudan bir VM 'den veya bir kaynak grubundaki tüm VM 'lerde Azure Izleyici 'den.

Bu makalede, sistem durumu sorunlarını VM'ler için Azure İzleyici sistem durumu özelliği tarafından algılandığında hızlı bir şekilde değerlendirme, araştırma ve çözme işlemlerinin nasıl yapılacağı gösterilir.

VM'ler için Azure İzleyici yapılandırma hakkında daha fazla bilgi için bkz. [VM'ler için Azure izleyici etkinleştirme](vminsights-enable-overview.md).

>[!NOTE]
>Kısa süre önce, genel önizleme müşterilerimizden aldığımız geri bildirimlere göre sistem durumu özelliğine yaptığımız [değişiklikleri duyuruyoruz](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . Yaptığımız değişikliklerin sayısı verildiğinde, yeni müşterilerin sistem durumu özelliğini sunmayı durduracağız. Mevcut müşteriler sistem durumu özelliğini kullanmaya devam edebilir. Daha fazla ayrıntı için lütfen [genel kullanım hakkında SSS bölümüne](vminsights-ga-release-faq.md)bakın. 

## <a name="monitoring-configuration-details"></a>İzleme yapılandırması ayrıntıları

Bu bölümde, Azure Windows ve Linux VM 'lerini izlemek için varsayılan durum ölçütleri özetlenmektedir. Tüm durum ölçütleri, sağlıksız bir durum tespit edildiğinde bir uyarı gönderecek şekilde önceden yapılandırılmıştır.

| İzleyici adı | Sıklık (dk) | Geriye doğru alma süresi (dk) | İşleç | Eşik | Durum durumunda uyar | Severity | İş yükü kategorisi | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Mantıksal disk çevrimiçi | 5 | 15 | <> | 1 (doğru) | Kritik | Sev1 | Linux | 
| Mantıksal disk boş alanı | 5 | 15 | < | 200 MB (uyarı)<br> 100 MB (kritik) | Uyarı | Sev1<br> Sev2 | Linux | 
| Mantıksal disk% boş ınomdes | 5 | 15 | < | %5 | Kritik | Sev1 | Linux | 
| Mantıksal disk% boş alan | 5 | 15 | < | %5 | Kritik | Sev1 | Linux | 
| Ağ bağdaştırıcısı durumu | 5 | 15 | <> | 1 (doğru) | Uyarı | Sev2 | Linux | 
| İşletim sistemi kullanılabilir megabayt belleği | 5 | 10 | < | 2,5 MB | Kritik | Sev1 | Linux | 
| Disk Ortalama Disk sn/okuma | 5 | 25 | > | 0,s | Kritik | Sev1 | Linux | 
| Disk Ortalama Disk sn/Aktarım | 5 | 25 | > | 0,s | Kritik | Sev1 | Linux | 
| Disk Ortalama Disk sn/yazma | 5 | 25 | > | 0,s | Kritik | Sev1 | Linux | 
| Disk durumu | 5 | 25 | <> | 1 (doğru) | Kritik | Sev1 | Linux | 
| İşletim sistemi toplam yüzde Işlemci zamanı | 5 | 10 | >= | %95 | Kritik | Sev1 | Linux | 
| Toplam CPU kullanım yüzdesi | 5 | 10 | >= | %95 | Kritik | Sev1 | Windows | 
| Dosya sistemi hatası veya bozulması | 60 | 60 | <> | 4 | Kritik | Sev1 | Windows | 
| Okuma başına ortalama mantıksal disk saniye sayısı | 1 | 15 | > | 0.04 s | Uyarı | Sev2 | Windows | 
| Aktarım başına ortalama mantıksal disk saniye sayısı | 1 | 15 | > | 0.04 s | Uyarı | Sev2 | Windows | 
| Yazma başına ortalama mantıksal disk saniyesi (mantıksal disk) | 1 | 15 | > | 0.04 s | Uyarı | Sev2 | Windows | 
| Geçerli disk sırası uzunluğu (mantıksal disk) | 5 | 60 | >= | 32 | Uyarı | Sev2 | Windows | 
| Mantıksal disk boş alanı (MB) | 15 | 60 | > | 500 MB uyarı<br> 300 MB kritik | Kritik | Sev1<br> Sev2 | Windows | 
| Mantıksal disk boş alanı (%) | 15 | 60 | > | %10 uyarı<br> %5 kritik | Kritik | Sev1<br> Sev2 | Windows |
| Mantıksal disk boş kalma süresi yüzdesi | 15 | 360 | <= | %20 | Uyarı | Sev2 | Windows | 
| Kullanılan bant genişliği yüzdesi okuma | 5 | 60 | >= | %60 | Uyarı | Sev2 | Windows | 
| Kullanılan bant genişliği yüzdesi toplamı | 5 | 60 | >= | %75 | Uyarı | Sev2 | Windows | 
| Kullanılan bant genişliği yüzdesi yazma | 5 | 60 | >= | %60 | Uyarı | Sev2 | Windows | 
| DHCP Istemci hizmeti durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| DNS Istemci hizmeti durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| Windows olay günlüğü hizmeti durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| Windows Güvenlik Duvarı hizmeti sistem durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| RPC hizmet durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| Sunucu hizmeti durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| Windows Uzaktan Yönetimi hizmet durumu | 5 | 12 | <> | 4 (çalışıyor) | Kritik | Sev1 | Windows | 
| Kullanılabilir bellek megabayt | 5 | 10 | < | 100 MB | Kritik | Sev1 | Windows | 
| Boş sistem sayfa tablosu girdileri | 5 | 10 | <= | 5000 | Kritik | Sev1 | Windows | 
| Saniye başına bellek sayfası | 5 | 10 | >= | 5000/sn | Uyarı | Sev1 | Windows | 
| Kullanımdaki kaydedilmiş bellek yüzdesi | 5 | 10 | > | %80 | Kritik | Sev1 | Windows | 
| Aktarım başına ortalama disk saniye sayısı | 1 | 15 | > | 0.04 s | Uyarı | Sev2 | Windows | 
| Yazma başına ortalama disk saniye sayısı | 1 | 15 | > | 0.04 s | Uyarı | Sev2 | Windows | 
| Geçerli disk sırası uzunluğu | 5 | 60 | >= | 32 | Uyarı | Sev2 | Windows | 
| Disk boş kalma süresi yüzdesi | 5 | 60 | >= | %20 | Uyarı | Sev2 | Windows | 

>[!NOTE]
>Geriye doğru arama süresi, en son beş dakika içinde olduğu gibi, geri arama penceresinin ölçüm değerlerini ne sıklıkta denetleyeceğini temsil eder.  

>[!NOTE]
>Sıklık, koşulların karşılanıp karşılanmadığını (her dakika gibi) ölçüm uyarısının ne sıklıkta denetleyeceğini temsil eder.  Sistem durumu ölçütünün yürütüldüğü ve geri dönüş, sistem durumu ölçütünün değerlendirildiği sürenin hızıdır. Örneğin, **sistem durumu ölçütü** değerlendirme süresi 5 dakikalık bir sıklıkta yüzde 95 ' den büyükse ve 15 dakika (3 ardışık değerlendirme döngüsü) için %95 ' den fazla kalırsa durum kriteri değerlendiriliyor.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Oturum açmak için [Azure Portal](https://portal.azure.com)gidin.

## <a name="introduction-to-azure-monitor-for-vms-health"></a>VM'ler için Azure İzleyici durumuna giriş

Tek bir sanal makine veya sanal makine grubu için sistem sağlığı özelliğini kullanmadan önce, bilgilerin nasıl sunulduğunu ve görselleştirmelerin neyi temsil ettiğini anlamak önemlidir.

### <a name="view-health-directly-from-a-vm"></a>Doğrudan bir VM 'den sistem durumunu görüntüleme

Bir Azure VM 'nin durumunu görüntülemek için, VM 'nin sol bölmesindeki **Öngörüler (Önizleme)** seçeneğini belirleyin. VM öngörüleri sayfasında, **sistem durumu** sekmesi varsayılan olarak AÇıKTıR ve VM 'nin sistem durumu görünümünü gösterir.

![Seçilen Azure sanal makinesine VM'ler için Azure İzleyici sistem durumu genel bakışı](./media/vminsights-health/vminsights-directvm-health-01.png)

**Konuk VM sistem durumu** bölümünde, tablo, sanal makine için sistem durumu ölçütlerine göre izlenen performans bileşenlerinin sistem durumu toplamasını ve sağlıksız bileşenler tarafından oluşturulan toplam VM sistem durumu uyarısı sayısını gösterir. Bu bileşenler **CPU**, **bellek**, **disk**ve **ağ**içerir. Konuk VM sistem durumu ' nun yanındaki köşeli çift ayracı genişleterek bileşenlerini görüntüleyin.

![Seçili Azure sanal makinesinin bileşen sistem durumu VM'ler için Azure İzleyici](./media/vminsights-health/vminsights-directvm-health-02.png)

Bileşenin yanındaki durumu seçmek, sistem durumu tanılama deneyimini seçili bileşen bağlamında açar. Bu bileşen, sistem durumunu hesaplamak için hangi sistem durumu ölçütlerinin kullanıldığını açıklayan, bu bileşenin durumunun birleşimini gösterir. Daha fazla bilgi için bkz. [sağlık tanılama ve durum ölçütleriyle çalışma](#health-diagnostics). Uyarılar hakkında daha fazla bilgi için bkz. [Uyarılar](#alerts).

Bir VM için tanımlanan sistem durumu durumları aşağıdaki tabloda açıklanmıştır:

|Simge |Sistem durumu |Anlamı |
|-----|-------------|---------------|
| |Sorunsuz |VM, tanımlı sistem durumu koşullarıdır. Bu durum, algılanan bir sorun olmadığını ve VM 'nin normal şekilde çalıştığını gösterir. Bir üst toplama izleyicisinde, sistem durumu kaydedilir ve alt öğenin en iyi veya en kötü durumunu yansıtır.|
| |Kritik |Durum, bir veya daha fazla kritik sorunun algılandığını belirten tanımlı sistem durumu koşulu içinde değil. Normal işlevleri geri yüklemek için bu sorunlar ele alınmalıdır. Bir üst toplama izleyicisi ile sistem durumu kaydedilir ve alt öğenin en iyi veya en kötü durumunu yansıtır.|
| |Uyarı |Durum, tanımlı sistem durumu için iki eşik arasındadır; burada bir uyarı durumu ve diğeri kritik durumu gösterir (üç sağlık durumu eşikleri yapılandırılabilir) veya kritik olmayan bir sorun olduğunda kritik sorunlara neden olabilir çözümlenmemiş. Bir üst toplama izleyicisinde, bir veya daha fazla alt öğe bir uyarı durumundaysa, üst öğe bir uyarı durumunu yansıtır. Bir alt öğe kritik durumundaysa ve bir uyarı durumunda başka bir alt ise, üst toplama sistem durumunu kritik olarak gösterir.|
| |Bilinmiyor |Durum çeşitli nedenlerle hesaplanamıyor. Aşağıdaki bölümde ek ayrıntılar ve olası çözümler sağlanmaktadır. |

Bilinmeyen bir sistem durumu aşağıdaki sorunlardan kaynaklanıyor olabilir:

- Aracı yeniden yapılandırıldı ve VM'ler için Azure İzleyici etkinleştirildiğinde artık belirtilen çalışma alanına rapor vermez. Aracıyı çalışma alanına rapor verecek şekilde yapılandırmak için bkz. [bir çalışma alanını ekleme veya kaldırma](../platform/agent-manage.md#adding-or-removing-a-workspace).
- VM silindi.
- VM'ler için Azure İzleyici ilişkili çalışma alanı silindi. Premier destek avantajlarınız varsa, çalışma alanını kurtarabilirsiniz. [Premier](https://premier.microsoft.com/) adresine gidin ve bir destek isteği açın.
- Çözüm bağımlılıkları silindi. Log Analytics çalışma alanınızda ServiceMap ve InfrastructureInsights çözümlerini yeniden etkinleştirmek için, [Azure Resource Manager şablonunu](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)kullanarak servicemap çözümünü yeniden yükleyin. Infastructureınsights çözümünü, e-posta vminsights@microsoft.comyeniden yüklemek için. 
- VM kapatıldı.
- Azure VM hizmeti kullanılamıyor veya bakım gerçekleştiriliyor.
- Çalışma alanı [günlük veri veya saklama sınırı](../platform/manage-cost-storage.md) karşılandı.

VM 'nin tüm bileşenlerini, ilişkili sistem durumu ölçütlerini, durum değişikliklerini ve VM ile ilgili bileşenleri izleyerek algılanan diğer sorunları gösteren bir sayfa açmak için **sistem durumu tanılamayı görüntüle** ' yi seçin.

Daha fazla bilgi için bkz. [sağlık tanılama](#health-diagnostics).

**Sistem durumu** bölümünde, bir tablo sistem durumu ölçütlerine göre izlenen performans bileşenlerinin sistem durumu toplamasını gösterir. Bu bileşenler **CPU**, **bellek**, **disk**ve **ağ**içerir. Bir bileşeni seçtiğinizde, bu bileşenin tüm izleme ölçütlerini ve sistem durumunu listeleyen bir sayfa açılır.

Windows çalıştıran bir Azure VM 'sinden sistem durumuna eriştiğinizde, **Çekirdek Hizmetleri sistem durumu**altında ilk beş çekirdekli Windows hizmetlerinin sistem durumu gösterilir. Hizmetlerden herhangi birini seçmek, bu bileşen için sistem durumu ölçütlerini izlemeyi listeleyen bir sayfa açar.

Durum ölçütlerinin adı seçildiğinde özellik bölmesi açılır. Bu bölmede, sistem durumu ölçütlerinde karşılık gelen bir Azure Izleyici uyarısı olup olmadığı dahil olmak üzere yapılandırma ayrıntılarını gözden geçirebilirsiniz.

Daha fazla bilgi için bkz. [sağlık tanılama ve durum ölçütleriyle çalışma](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Birleşik VM perspektifi

Bir kaynak grubundaki tüm VM 'lerinizin sistem durumu koleksiyonunu görüntülemek için, portalda gezinti listesinden **Azure İzleyicisi** ' ni seçin ve ardından **sanal makineler (Önizleme)** öğesini seçin.

![Azure Izleyici 'den VM öngörüleri izleme görünümü](./media/vminsights-health/vminsights-aggregate-health.png)

**Abonelik** ve **kaynak grubu** açılan listelerinde, grup ile ilgili VM 'leri içeren uygun kaynak grubunu seçip bildirilen sistem durumunu görüntüleyin. Seçiminiz yalnızca sistem durumu özelliğine uygulanır ve **performans** ya da **harita** sekmelerini etkilemez.

**Sistem durumu** sekmesi aşağıdaki bilgileri sağlar:

* Kaç tane sanal makine kritik veya sağlıksız durumda, ne kadar sağlıklı veya veri gönderme (bilinmeyen bir durum olarak adlandırılır).
* İşletim sistemine göre kaç VM 'nin, sağlıksız bir durum bildirdiği.
* Sistem durumuna göre kategorilere ayrılmış bir işlemci, disk, bellek veya ağ bağdaştırıcısında algılanan bir sorun nedeniyle kaç sanal makine sağlıksız.
* Çekirdek işletim sistemi hizmetinde algılanan bir sorun nedeniyle sistem durumuna göre sınıflandırılan kaç sanal makine sağlıksız durumda.

**Sistem durumu** SEKMESINDE, VM 'yi izleyen durum ölçütlerine göre algılanan kritik sorunları tanımlayabilir ve uyarı ayrıntılarını ve ilgili Bilgi Bankası makalelerini gözden geçirebilirsiniz. Bu makaleler, sorunların tanılanması ve düzeltilmesi konusunda yardımcı olabilir. Bu önem derecesine göre filtrelenen [tüm uyarılar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sayfasını açmak için tüm önem derecelerinin herhangi birini seçin.

**İşletim sistemi listesine göre VM dağıtımı** , Windows sürümü veya Linux dağıtımı tarafından listelenen VM 'leri kendi sürümüyle birlikte gösterir. Her işletim sistemi kategorisinde VM 'lerin sistem durumuna göre daha fazla ayrılır.

![VM Insights sanal makine dağıtımı perspektifi](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

**VM sayısı**, **kritik**, **Uyarı**, **sağlıklı**veya **bilinmiyor**gibi herhangi bir sütun seçin. **Sanal makineler** sayfasında, seçilen sütunla eşleşen filtrelenmiş sonuçların listesini görüntüleyin.

Örneğin, sürüm 7,5 Red Hat Enterprise Linux çalıştıran tüm VM 'Leri gözden geçirmek için, o işletim sisteminin **VM sayısı** değerini seçin ve bu filtreyle eşleşen VM 'leri ve geçerli sistem durumlarını listeler.

![Red Hat Linux VM 'lerinin örnek toplaması](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

**Durumu göster** onay kutusunu tıklattığınızda, tablodaki filtrelenmiş sonuçlar için sistem durumu döndürülür.  

![Red Hat Linux VM 'lerinin örnek sistem durumu](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Listedeki öğelerden herhangi biri için, seçilen VM için sistem durumunun değerlendirildiğini gösteren sistem durumu tanılamayı başlatmak üzere ilgili sistem durumuna tıklayabilirsiniz. 

**Sanal makineler** sayfasında, sütun **VM adı**altında bir VM adı ' nı seçerseniz, **VM örneği** sayfasına yönlendirilirsiniz. Bu sayfa, seçilen VM 'yi etkileyen uyarılar ve durum ölçütleri sorunları hakkında daha fazla bilgi sağlar. Hangi bileşenlerin sağlıksız olduğunu görmek için sayfanın sol üst köşesindeki **sistem** durumu simgesini seçerek sistem durumu ayrıntılarını filtreleyin. Ayrıca, uyarı önem derecesine göre kategorilere ayrılmamış bir bileşen tarafından oluşturulan VM sistem durumu uyarılarını görüntüleyebilirsiniz.

VM **listesi** görünümünden VM 'nin adını seçin; benzer şekilde, VM 'Den doğrudan **Öngörüler (Önizleme)** SEÇENEĞINI belirlediyseniz da bu sanal makine için **sistem durumu** sayfasını açın.

![Seçili Azure sanal makinesinin VM öngörüleri](./media/vminsights-health/vminsights-directvm-health.png)

**Azure izleyici 'de sanal makineler (Önizleme)** sayfasında VM ve uyarılar için bir toplu sistem durumu gösterilir. Bu sistem durumu önem derecesine göre kategorize edilir ve bu durum, ölçütlere göre sistem durumu sağlıklı durumundan sağlıksız durumuna değiştirildiğinde oluşan VM sistem durumu uyarılarını temsil eder. **Kritik koşuldaki VM 'lerin** seçilmesi, kritik bir sistem durumundaki bir veya daha fazla VM 'nin listesini içeren bir sayfa açar.

VM 'lerden birinin sistem durumunu seçmek, sanal makinenin **sistem durumu tanılama** görünümünü gösterir. Bu görünümde, bir sistem durumu sorununu yansıtan sistem durumu ölçütlerini belirleyebilirsiniz. **Sistem durumu tanılama** sayfası açıldığında, tüm VM bileşenlerini ve bunların geçerli sağlık durumuyla ilişkili sistem durumu ölçütlerini gösterir.

Daha fazla bilgi için bkz. [sağlık tanılama](#health-diagnostics).

**Tüm durum ölçütlerini görüntüle** ' nin seçilmesi, bu özellikle kullanılabilir olan tüm sistem durumu ölçütlerinin listesini gösteren bir sayfa açar. Bilgiler aşağıdaki seçeneklere göre daha fazla filtrelenebilir:

* **Yazın**. Koşulları değerlendirmek ve izlenen bir sanal makinenin genel sistem durumunu toplamak için üç tür durum ölçütü vardır:
    - **Birim**. Bir VM 'nin bazı yönlerini ölçer. Bu durum ölçütleri türü, bileşenin performansını belirleme, yapay bir işlem gerçekleştirmek için bir komut dosyası çalıştırma veya bir hatayı gösteren bir olayı izleme gibi bir performans sayacını kontrol edebilir. Filtre varsayılan olarak Unit olarak ayarlanır.
    - **Bağımlılık**. Farklı varlıklar arasında bir sistem durumu toplaması sağlar. Bu durum ölçütleri, bir varlığın durumunun, başarılı bir işlem için temel aldığı başka bir varlık türünün sistem durumuna bağlı olmasına izin verir.
    - **Toplam**. Benzer sistem durumu ölçütlerinin Birleşik sistem durumunu sağlar. Birim ve bağımlılık durumu ölçütü genellikle bir toplam sistem durumu ölçütü altında yapılandırılır. Bir varlığa hedeflenmiş çok sayıda farklı durum ölçütünün daha iyi bir genel organizasyonunu sağlamaya ek olarak, toplu sistem durumu ölçütü, varlıkların farklı kategorileri için benzersiz bir sistem durumu sağlar.

* **Kategori**. Raporlama amacıyla benzer kriterleri gruplamak için kullanılan sistem durumu ölçütünün türü. Bu kategoriler **kullanılabilirlik** ve **performanslardır**.

Hangi örneklerin sağlıksız olduğunu görmek için, **sağlıksız bileşen** sütununun altında bir değer seçin. Bu sayfada, bir tablo kritik sağlık durumundaki bileşenleri listeler.

## <a name="health-diagnostics"></a>Sistem durumu tanılaması

**Sistem durumu tanılama** sayfası, BIR sanal makinenin sistem durumu modelini görselleştirmenize olanak tanır. Bu sayfa tüm VM bileşenlerini, ilişkili sistem durumu ölçütlerini, durum değişikliklerini ve VM ile ilgili İzlenen bileşenler tarafından tanımlanan diğer önemli sorunları listeler.

![Bir VM için sistem durumu tanılama sayfası örneği](./media/vminsights-health/health-diagnostics-page-01.png)

Aşağıdaki yöntemleri kullanarak sistem durumu tanılamayı başlatın:

* Azure Izleyici 'de toplam VM perspektifinden tüm VM 'Ler için toplu sistem durumuna göre:

    1. **Sistem durumu** sayfasında, **Konuk VM sistem durumu**bölümünde **kritik**, **Uyarı**, **sağlıklı**veya **Bilinmeyen** sistem durumu simgesini seçin.
    2. Filtrelenmiş kategori ile eşleşen tüm VM 'Leri listeleyen sayfaya gidin.
    3. Bu VM 'nin kapsamındaki sistem durumu tanılamayı açmak için **sistem** durumu sütunundaki değeri seçin.

* Azure Izleyici 'deki Birleşik VM perspektifinden işletim sistemine göre. **VM dağıtımı**altında, sütun değerlerinden herhangi birini seçmek **sanal makineler** sayfasını açar ve filtrelenmiş kategori ile eşleşen tabloda bir liste döndürür. **Sistem durumu** sütunu altında değeri seçmek, seçilen VM için sistem durumu tanılamayı açar.
 
* VM'ler için Azure İzleyici **sistem** durumu SEKMESINDEKI Konuk VM 'den, **durum tanılamayı görüntüle**' yi seçerek.

Sistem durumu tanılama, sistem durumu bilgilerini iki kategoride düzenler: kullanılabilirlik ve performans.
 
Mantıksal disk, CPU vb. gibi bir bileşen için tanımlanan tüm durum ölçütleri, iki kategoride filtrelemeden görüntülenebilir. Bu görünümler, ölçütlerin bir bütün olarak bir görünümünde veya **kullanılabilirlik** veya **performans**' ı seçerken sonuçları kategoriye göre filtreleyerek olabilir.

Ayrıca, ölçüt kategorisi **durum ölçütleri** sütununun yanında görünebilirler. Ölçütler seçili kategori ile eşleşmezse, **Seçili Kategori için kullanılabilir bir sistem durumu ölçütü** belirten bir Ileti, **durum ölçütleri** sütununda görüntülenir.

Durum ölçütlerinin durumu, dört türden biri tarafından tanımlanır: **kritik**, **Uyarı**, **sağlıklı**ve **bilinmiyor**. İlk üçü yapılandırılabilir, yani izleyicilerin eşik değerlerini doğrudan **durum ölçütleri** Yapılandırma bölmesinde değiştiremeyeceğiniz anlamına gelir. Bu, Azure Izleyici REST API [Güncelleştirme İzleyicisi işlemi](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)kullanılarak da mümkündür. **Bilinmiyor** yapılandırılamaz ve belirli senaryolar için ayrılmıştır.

**Sistem durumu tanılama** sayfasında üç ana bölüm vardır:

* Bileşen modeli
* Durum Ölçütleri
* Durum değişiklikleri

![Sistem durumu tanılama sayfasının bölümleri](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Bileşen modeli

**Sistem durumu tanılama** sayfasındaki en soldaki sütun **bileşen modelidir**. VM ile ilişkili tüm bileşenler, bu sütunda geçerli sistem durumu ile birlikte görüntülenir.

Aşağıdaki örnekte, bulunan bileşenler **disk**, **mantıksal disk**, **Işlemci**, **bellek**ve **işletim sistemidir**. Bu bileşenlerin birden çok örneği keşfedilir ve bu sütunda görüntülenir.

Örneğin, aşağıdaki şekilde, VM 'nin, sağlıklı durumda olan **C:** ve **D:** olmak üzere iki mantıksal disk örneği olduğunu gösterir:

![Durum tanılamasında sunulan örnek bileşen modeli](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Durum ölçütleri

Sistem durumu tanılama sayfasındaki orta sütun **sistem durumu ölçütlerinde**. VM için tanımlanan sistem durumu modeli hiyerarşik bir ağaçta görüntülenir. Bir sanal makine için sistem durumu modeli, birim ve toplam durum kriterlerinden oluşur.

![Sağlık tanılamasında sunulan örnek durum ölçütleri](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Bir sistem durumu ölçütü, bir eşik değeri, bir varlığın durumu olabilecek izlenen örneğin sistem durumunu ölçer. Bir sistem durumu ölçütünde, daha önce açıklandığı gibi iki veya üç yapılandırılabilir sistem durumu eşiği bulunur. Herhangi bir noktada, sistem durumu ölçütü olası durumlarından yalnızca birinde olabilir.

Sistem durumu modeli, hedefin genel hedefinin ve bileşenlerinin sistem durumunu belirleyen kriterleri tanımlar. Ölçüt hiyerarşisi **sistem durumu tanılama** sayfasındaki **durum ölçütleri** bölümünde gösterilir.

Sistem durumu toplama ilkesi, toplam durum ölçütü yapılandırmasının bir parçasıdır (varsayılan değer en **kötüsü**olarak ayarlanmıştır). Bu özelliğin bir parçası olarak çalışan varsayılan bir sistem durumu ölçütü kümesini, bu makalenin [izleme yapılandırması ayrıntıları](#monitoring-configuration-details) bölümünde bulabilirsiniz.

Tüm sistem durumu ölçütlerinin listesini almak için Azure Izleyici REST API [izleme örnekleri listesini kaynağa göre](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) de kullanabilirsiniz. Bu ölçütler, Azure VM kaynağına karşı çalışan yapılandırma ayrıntılarını içerir.

**Birim** sistem durumu ölçüt türünün yapılandırması, sağ taraftaki üç nokta bağlantısını seçerek değiştirilmesini sağlayabilir. Yapılandırma bölmesini açmak için **Ayrıntıları göster** ' i seçin.

![Bir sistem durumu ölçütü örneği yapılandırma](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Seçili durum ölçütlerinin Yapılandırma bölmesinde, **yazma başına ortalama disk saniye sayısını**kullanırsanız, eşik farklı bir sayısal değerle yapılandırılabilir. Bu, iki durumlu bir izleyiciden, ancak **sağlıklı** olarak yalnızca **Uyarı**olarak değişebilir.

Diğer durum ölçütleri bazen üç durum kullanır, burada uyarı ve kritik durum durumu eşikleri için değeri yapılandırabilirsiniz. Ayrıca, Azure Izleyici REST API [İzleyici yapılandırmasını](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)kullanarak bir eşiği değiştirebilirsiniz.

>[!NOTE]
>Durum ölçütleri yapılandırma değişikliklerinin bir örneğe uygulanması, bunları izlenen tüm örneklere uygular. Örneğin, **disk-1 D:** ' u seçerseniz ve **yazma eşiği başına ortalama disk saniyeleri** değiştirirseniz, değişiklik VM 'de bulunan ve izlenen tüm örneklere uygulanır.


![Birim izleyicisi örneği için bir sistem durumu ölçütü yapılandırma](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Sistem durumu ölçütleri hakkında daha fazla bilgi edinmek istiyorsanız, sorunları, nedenleri ve çözümleri belirlemenize yardımcı olmak üzere Bilgi Bankası makalelerini sunuyoruz. İlgili Bilgi Bankası makalesini görmek için sayfadaki **bilgileri görüntüle** ' yi seçin.

VM'ler için Azure İzleyici sistem durumu ile birlikte sunulan tüm Bilgi Bankası makalelerini gözden geçirmek için bkz. [Azure izleyici sistem durumu bilgileri belgeleri](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Durum değişiklikleri

**Sistem durumu tanılama** sayfasının en sağdaki sütunu **durum değişikdir**. Bu sütunda, durum **ölçütleri** bölümünde seçilen sistem durumu ölçütleriyle ilişkili tüm durum değişiklikleri veya bir VM 'Nin **bileşen modeli** veya **sistem durumu ölçütü** sütunundan seçilmiş olması durumunda VM 'nin durum değişikliği listelenir.

![Durum tanılamasında sunulan örnek durum değişiklikleri](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Aşağıdaki bölümde, sistem durumu ölçütü durumu ve ilişkili zaman gösterilmektedir. Bu bilgiler, sütunun en üstünde yer aldığı en son durumu gösterir.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Bileşen modeli, sistem durumu ölçütü ve durum değişiklikleri sütunları ilişkilendirmesi

Üç sütun birbirleriyle birbirine bağlanır. **Bileşen modeli** sütununda bir örnek seçtiğinizde, **sistem durumu ölçütü** sütunu bu bileşen görünümüne filtrelenir. **Durum değişiklikleri** sütunu, seçilen sistem durumu ölçütlerine göre güncelleştirilir.

![İzlenen örnek ve sonuçları seçme örneği](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Örneğin, *disk-1 d:* **' yi seçerseniz** , **bileşen modeli**altındaki listeden, *disk-1D:* ve **durum değişikliklerinde** durum değişikliği, *disk-1 D:* ' nin kullanılabilirliğine göre değişiklik gösterir.

Güncelleştirilmiş bir sistem durumunu görmek için **yenileme** bağlantısını seçerek sistem durumu tanılama sayfasını yenileyebilirsiniz. Bu görev, önceden tanımlanmış yoklama aralığına göre sistem durumu ölçütünün sistem durumu için bir güncelleştirme varsa, bu görev, en son sistem durumunu bekleyip yansıtmanızı sağlar. **Durum ölçütleri durumu** , seçilen sistem durumuna göre sonuçları kapsamlamanızı sağlayan bir filtredir: sağlıklı, uyarı, kritik, bilinmeyen ve tümü. Sağ üst köşedeki **Son güncelleme** zamanı, sistem durumu tanılama sayfasının en son yenilenme süresini gösterir.

## <a name="alerts"></a>Uyarılar

VM'ler için Azure İzleyici sistem durumu, [Azure uyarıları](../../azure-monitor/platform/alerts-overview.md)ile tümleşir. Algılandığında önceden tanımlanmış ölçütler olduğunda bir uyarı başlatır, sağlıklı durumdan sağlıksız bir duruma değişir. Uyarılar, sev 0 ile sev 4 arası, en yüksek düzeydeki sev 0 ile önem derecesine göre kategorize edilir.

Uyarılar, uyarı tetiklendiğinde sizi bilgilendirmek için bir eylem grubuyla ilişkilendirilmez. Abonelik kapsamında sahip rolüne sahip bir kullanıcının, [uyarıları Yapılandır](#configure-alerts) bölümündeki adımları izleyerek bildirimleri yapılandırması gerekir.

Önem derecesine göre kategorilere ayrılan VM sistem durumu uyarılarının toplam sayısı, **Uyarılar** bölümünün altındaki **sistem durumu** panosunda bulunabilir. Toplam uyarı sayısını veya önem düzeyine karşılık gelen sayıyı seçtiğinizde, **Uyarılar** sayfası açılır ve seçiminizle eşleşen tüm uyarıları listeler.

Örneğin, **sev düzey 1**' e karşılık gelen satırı seçerseniz, aşağıdaki görünümü görürsünüz:

![Tüm önem düzeyi 1 Uyarılardan oluşan örnek](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**Tüm uyarılar** sayfası, yalnızca seçiminizle eşleşen uyarıları göstermek için Kapsamsız değildir. Ayrıca, yalnızca bir VM kaynağı tarafından oluşturulan sistem durumu uyarılarını göstermek için **kaynak türüne** göre filtrelenmiştir. Bu biçim, uygun olmayan bir koşul karşılandığında Azure VM 'nin oluşan uyarı listesini gösterdiği sütun **hedef kaynağı**altındaki Uyarı listesine yansıtılır.

Diğer kaynak türlerinden veya hizmetlerinden gelen uyarıların bu görünüme eklenmesi hedeflenmemiştir. Bu uyarılar, normalde varsayılan Azure Izleyicisi [tüm uyarılar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sayfasından görüntülediğiniz günlük sorgularını veya ölçüm uyarılarını temel alan günlük uyarılarını içerir.

Bu görünümü, sayfanın en üstündeki açılan menülerde bulunan değerler ' i seçerek filtreleyebilirsiniz.

|Sütun |Açıklama |
|-------|------------|
|Abonelik |Bir Azure aboneliği seçin. Yalnızca seçili abonelikteki uyarılar görünüme dahil edilir. |
|Kaynak Grubu |Tek bir kaynak grubu seçin. Yalnızca seçili kaynak grubunda hedefleri olan uyarılar görünüme dahildir. |
|Kaynak türü |Bir veya daha fazla kaynak türü seçin. Varsayılan olarak, yalnızca hedef **sanal makinelerin** uyarıları seçilir ve bu görünüme dahildir. Bu sütun yalnızca bir kaynak grubu belirtilmişse kullanılabilir. |
|Kaynak |Bir kaynak seçin. Yalnızca hedef olarak bu kaynağa sahip olan uyarılar görünüme dahil edilir. Bu sütun yalnızca bir kaynak türü belirtilmişse kullanılabilir. |
|Severity |Bir uyarı önem derecesi seçin veya tüm önem derecelerinin uyarılarını dahil etmek için **Tümü** ' nü seçin. |
|İzleme koşulu |Koşul artık etkin değilse, uyarıları sistem tarafından tetiklenmişse veya çözülebiliyorsa, uyarıları filtrelemek için bir izleyici koşulu seçin. Ya da tüm koşulların uyarılarını dahil etmek için **Tümü** ' nü seçin. |
|Uyarı durumu |Tüm durumların uyarılarını dahil etmek için bir uyarı durumu, **Yeni**, **bildirim**, **kapalı**veya **Tümü** seçin. |
|Hizmeti izle |Tüm hizmetleri dahil etmek için bir hizmet seçin veya **Tümünü** seçin. Bu özellik için yalnızca VM öngörülerinin uyarıları desteklenir.|
|Zaman aralığı| Yalnızca seçilen zaman penceresi içinde tetiklenen uyarılar görüntülenir. Desteklenen değerler son saat, son 24 saat, son 7 gün ve son 30 gündür. |

Bir uyarı seçtiğinizde, **Uyarı ayrıntısı** sayfası görüntülenir. Bu sayfa, uyarının ayrıntılarını sağlar ve durumunu değiştirmenize olanak sağlar.

Uyarıları yönetme hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici kullanarak uyarı oluşturma, görüntüleme ve yönetme](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Portaldan Azure Izleyici 'de sistem durumu ölçütlerine göre yeni uyarılar oluşturma veya mevcut sistem durumu uyarı kurallarını değiştirme Şu anda desteklenmiyor.


![Seçili bir uyarı için Uyarı ayrıntıları bölmesi](./media/vminsights-health/alert-details-pane-01.png)

Bir veya birden çok uyarının uyarı durumunu seçip sol üst köşedeki **tüm uyarılar** sayfasında **Durumu Değiştir** ' i seçerek değiştirebilirsiniz. **Uyarı durumunu değiştir** bölmesindeki durumlardan birini seçin, **Açıklama** alanındaki değişikliğin açıklamasını ekleyin ve ardından değişikliklerinizi kaydetmek için **Tamam** ' ı seçin. Bilgiler doğrulandığında ve değişiklikler uygulandığında, menüdeki **Bildirimler** altında ilerlemeyi izleyin.

### <a name="configure-alerts"></a>Uyarı yapılandırma
Belirli uyarı yönetimi görevlerini Azure portal yönetemezsiniz. Bu görevlerin [Azure izleyici REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)kullanılarak gerçekleştirilmesi gerekir. Daha ayrıntılı şekilde belirtmek gerekirse:

- Durum ölçütlerine yönelik bir uyarıyı etkinleştirme veya devre dışı bırakma
- Durum ölçütleri uyarıları için bildirimleri ayarlama

Her örnek, Windows makinenizde [Armclient](https://github.com/projectkudu/armclient) kullanır. Bu yöntemi bilmiyorsanız bkz. [ARMClient kullanma](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Uyarı kuralını etkinleştirme veya devre dışı bırakma

Belirli bir durum ölçütlerine yönelik bir uyarıyı etkinleştirmek veya devre dışı bırakmak için, **Alertgeneration** özelliği **devre dışı** veya **etkin**bir değerle değiştirilmelidir.

Belirli durum ölçütlerine ilişkin *Monitorıd* 'yi belirlemek için aşağıdaki örnek, **Aktarım başına Logicaldisk\avg disk saniyesi**ölçütlerine ilişkin bu değerin nasıl sorgulanacağını gösterir:

1. Terminal penceresinde **armclient. exe oturum açma**yazın. Bunun yapılması, Azure 'da oturum açmanızı ister.

2. Belirli bir sanal makinede etkin olan tüm sistem durumu ölçütlerini almak ve *Monitortorıd* özelliğinin değerini belirlemek için aşağıdaki komutu girin:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    Aşağıdaki örnek, *armclient Get* komutunun çıkışını gösterir. *Monitorıd*'nin değerini bir yere göz atın. Bu değer, sistem durumu ölçütlerinin KIMLIĞINI belirttiğimiz ve bir uyarı oluşturmak için özelliğini değiştirecek bir sonraki adım için gereklidir.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. *Alertgeneration* özelliğini değiştirmek için aşağıdaki komutu girin:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Özellik değerinin **devre dışı**olarak ayarlandığını doğrulamak için adım 2 ' de kullanılan Al komutunu girin.

#### <a name="associate-an-action-group-with-health-criteria"></a>Eylem grubunu durum ölçütleriyle ilişkilendir

VM'ler için Azure İzleyici sistem durumu, sağlıksız sistem durumu ölçütlerinden uyarılar oluşturulduğunda SMS ve e-posta bildirimlerini destekler. Bildirimleri yapılandırmak için, SMS veya e-posta bildirimleri göndermek üzere yapılandırılmış eylem grubunun adını aklınızda yapın.

>[!NOTE]
>Bu eylem için bildirim almak istediğiniz izlenen her sanal makineye karşı gerçekleştirilmelidir. Bir kaynak grubundaki tüm VM 'lere uygulanmaz.

1. Terminal penceresinde *armclient. exe oturum açma*girin. Bunun yapılması, Azure 'da oturum açmanızı ister.

2. Bir eylem grubunu uyarı kurallarıyla ilişkilendirmek için aşağıdaki komutu girin:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. **Actiongroupresourceıds** özelliğinin değerinin başarıyla güncelleştirildiğinden emin olmak için aşağıdaki komutu girin:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Çıktı aşağıdaki ölçütlere benzer şekilde görünmelidir:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

- Sınırlamaları ve genel VM performansını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md).

- Bulunan uygulama bağımlılıkları hakkında bilgi edinmek için bkz. [VM'ler için Azure izleyici haritasını görüntüleme](vminsights-maps.md).
