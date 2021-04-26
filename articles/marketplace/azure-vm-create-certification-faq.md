---
title: Azure Marketi için sanal makine (VM) sertifikası sorunlarını giderme
description: Azure Marketi için sanal makine (VM) görüntülerini test etme ve sertifika ile ilgili sık karşılaşılan sorunları giderin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mathapli
ms.author: mathapli
ms.date: 01/18/2021
ms.openlocfilehash: 7a9fa4a6f44e88caa6f98d5e1c56adaa065fa29a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558719"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Sanal makine sertifikası sorunlarını giderme

Sanal makine (VM) görüntünüzü Azure Market 'e yayımladığınızda, Azure ekibi, önyüklenebilir, güvenli ve Azure ile uyumlu olduğundan emin olmak için bunu doğrular. VM Görüntünüz yüksek kaliteli testlerin hiçbirini başaramazsa, yayımlanmaz. Sorunu açıklayan bir hata iletisi alırsınız.

Bu makalede, ilgili çözümlerle birlikte VM görüntüsü yayımlama sırasında sık karşılaşılan hata iletileri açıklanmaktadır.

> [!NOTE]
> Bu makale veya geliştirme önerileri hakkında sorularınız varsa, [Iş Ortağı Merkezi desteğiyle](https://aka.ms/marketplacepublishersupport)iletişim kurun.

## <a name="vm-extension-failure"></a>VM Uzantısı hatası

Resminizin VM uzantılarını destekleyip desteklemediğini kontrol edin.

VM uzantılarını etkinleştirmek için:

1. Linux VM 'nizi seçin.
1. **Tanılama ayarları**' na gidin.
1. **Depolama hesabını** güncelleştirerek taban matrislerini etkinleştirin.
1. **Kaydet**’i seçin.

   ![Konuk düzeyinde izlemenin nasıl etkinleştirileceğini görüntüleyen ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-1.png)

VM uzantılarının düzgün etkinleştirildiğini doğrulamak için:

1. VM 'de **VM uzantıları** sekmesini seçin ve ardından **Linux Tanılama uzantısının** durumunu doğrulayın.
1. Sağlama durumunu denetleyin.

   - Durum *sağlama başarılı* olursa, uzantılar test çalışması geçirilir.  
   - Durum *sağlama başarısız* olursa, uzantılar test çalışması başarısız olur ve sağlamlaştırılmış bayrağını ayarlamanız gerekir.

   ![Sağlamanın başarılı olduğunu gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-2.png)

   VM Uzantısı başarısız olursa, etkinleştirmek üzere [ölçümleri ve günlükleri izlemek için bkz. Linux Tanılama uzantısı 'Nı kullanma](../virtual-machines/extensions/diagnostics-linux.md) . VM uzantısının etkinleştirilmesini istemiyorsanız, destek ekibine başvurun ve devre dışı bırakmasına izin vermesini isteyin.

## <a name="vm-provisioning-issue"></a>VM sağlama sorunu

Teklifinizi göndermeden önce VM sağlama sürecini dikkatli bir şekilde izlediğinizden emin olun. VM 'yi sağlamaya yönelik JSON biçimini görüntülemek için bkz. [sanal makine görüntüsünü test](azure-vm-image-test.md)etme.

Sağlama sorunları aşağıdaki başarısızlık senaryolarını içerebilir:

|Senaryo|Hata|Nedeni|Çözüm|
|---|---|---|---|
|1|Geçersiz sanal sabit disk (VHD)|VHD altbilgisinde belirtilen tanımlama bilgisi değeri yanlışsa, VHD geçersiz olarak kabul edilir.|Görüntüyü yeniden oluşturun ve isteği iletin.|
|2|Geçersiz blob türü|Kullanılan blok, bir sayfa türü yerine bir blob türü olduğundan VM sağlama başarısız oldu.|Görüntüyü yeniden oluşturun ve isteği iletin.|
|3|Sağlama zaman aşımı veya düzgün Genelleştirilmiş|VM Genelleştirme ile ilgili bir sorun var.|Yansımayı Genelleştirme ile yeniden oluşturun ve isteği iletin.|
|

> [!NOTE]
> VM Genelleştirme hakkında daha fazla bilgi için bkz.
> - [Linux belgeleri](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows belgeleri](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>VHD belirtimleri

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix tanımlama bilgisi ve diğer VHD belirtimleri

' Conectix ' dizesi, VHD belirtiminin bir parçasıdır. Dosya oluşturucuyu tanımlayan VHD altbilgisinde 8 baytlık tanımlama bilgisi olarak tanımlanır. Microsoft tarafından oluşturulan tüm VHD dosyaları bu tanımlama bilgisine sahiptir.

VHD biçimli bir blob, bu biçimde 512 baytlık bir alt bilgiye sahip olmalıdır:

|Sabit disk altbilgisi alanları|Boyut (bayt)|
|---|---|
Bilgilerinin|8
Özellikler|4
Dosya biçimi sürümü|4
Veri boşluğu|8
Zaman Damgası|4
Oluşturan uygulama|4
Oluşturucu sürümü|4
Oluşturucu konak işletim sistemi|4
Özgün plan|8
Geçerli boyut|8
Disk geometrisi|4
Disk Türü|4
Sağlama|4
Benzersiz Kimlik|16
Kaydedilen durum|1
Ayrılmıştır|427
|

### <a name="vhd-specifications"></a>VHD belirtimleri

Sorunsuz bir yayımlama deneyimi sağlamak için, VHD 'nizin aşağıdaki ölçütlere uyduğundan emin olun:

- Tanımlama bilgisi ' conectix ' dizesini içerir.
- Disk türü düzeltildi.
- VHD 'nin sanal boyutu en az 20 MB 'tır.
- VHD hizalanır. Sanal Boyut 1 MB 'ın katları olmalıdır.
- VHD blob uzunluğu, sanal boyuta ve VHD alt bilgi uzunluğuna (512) eşit.

[VHD belirtimini](https://www.microsoft.com/download/details.aspx?id=23850)indirin.

## <a name="software-compliance-for-windows"></a>Windows için yazılım uyumluluğu

Windows görüntü isteğiniz bir yazılım uyumluluğu sorunu nedeniyle reddedilirse, yüklü SQL Server örneğiyle bir Windows görüntüsü oluşturmuş olabilirsiniz. Bunun yerine, Azure Marketi 'nden ilgili SQL Server sürümü temel görüntüsünü almanız gerekir.

Bilgisayarınızda yüklü SQL Server kendi Windows görüntünüzü oluşturmayın. Azure Marketi 'nden onaylanan SQL Server temel görüntüleri (Enterprise/Standard/Web) kullanın.

Visual Studio 'Yu veya Office lisanslı herhangi bir ürünü yüklemeye çalışıyorsanız, önceki onay için destek ekibine başvurun.

Onaylanan bir taban seçme hakkında daha fazla bilgi için bkz. onaylanan bir temelden [sanal makine oluşturma](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Araç seti test çalışması yürütülemedi

Microsoft sertifika araç seti, test çalışmalarını çalıştırmanıza ve VHD veya görüntünüzün Azure ortamıyla uyumlu olduğunu doğrulamanıza yardımcı olabilir.

[Microsoft sertifika araç setini](azure-vm-image-test.md)indirin.

### <a name="linux-test-cases"></a>Linux test çalışmaları

Aşağıdaki tablo, araç setinin çalışacağı Linux test çalışmalarını listeler. Test doğrulaması, açıklamada belirtilmiştir.

|Senaryo|Test çalışması|Açıklama|
|---|---|---|
|1|Bash geçmişi|VM görüntüsünü oluşturmadan önce bash geçmiş dosyaları temizlenmelidir.|
|2|Linux Aracısı sürümü|Azure Linux Agent 2.2.41 veya üzeri yüklü olmalıdır.|
|3|Gerekli çekirdek parametreleri|Aşağıdaki çekirdek parametrelerinin ayarlandığını doğrular: <br>Konsol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|İşletim sistemi diskinde takas bölümü|Takas bölümlerinin işletim sistemi diskinde oluşturulmadığını doğrular.|
|5|İşletim sistemi diskinde kök bölüm|İşletim sistemi diski için tek bir kök bölüm oluşturun.|
|6|OpenSSL sürümü|OpenSSL sürümü v 0.9.8 veya üzeri olmalıdır.|
|7|Python sürümü|Python sürüm 2,6 veya sonraki bir sürümü önemle önerilir.|
|8|İstemci canlı aralığı|ClientAliveInterval değerini 180 olarak ayarlayın. Uygulama gereksinimi üzerine 30 ' dan 235 ' e ayarlanabilir. Son kullanıcılarınız için SSH 'yi etkinleştirirseniz, bu değerin açıklanacak şekilde ayarlanması gerekir.|
|9|İşletim sistemi mimarisi|Yalnızca 64 bit işletim sistemleri desteklenir.|
|10|Otomatik güncelleştirme|Linux Aracısı otomatik güncelleştirme özelliğinin etkinleştirilip etkinleştirilmediğini belirler.|
|

### <a name="common-test-case-errors"></a>Ortak test çalışması hataları

Test çalışmalarını çalıştırırken görebileceğiniz yaygın hatalar için aşağıdaki tabloya bakın:

| Senaryo | Test çalışması | Hata | Çözüm |
| --- | --- | --- | --- |
| 1 | Linux Aracısı sürüm test çalışması | En düşük Linux Aracısı sürümü 2.2.41 veya üzeri. Bu gereksinim 1 Mayıs 2020 ' den itibaren zorunludur. | Linux Aracısı sürümünü güncelleştirin. 2,241 veya sonraki bir sürümü olmalıdır. Daha fazla bilgi için [Linux Aracısı sürüm güncelleştirme sayfasını](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)ziyaret edin. |
| 2 | Bash geçmiş test çalışması | Gönderilen görüntinizdeki Bash geçmişinin boyutu 1 kilobayt (KB) daha büyükse bir hata oluşur. Bash geçmiş dosyanızın potansiyel olabilecek duyarlı bilgiler içermediğinden emin olmak için Boyut 1 KB 'ye kısıtlıdır. | VHD 'yi başka bir çalışan VM 'ye bağlayarak ve boyutu 1 KB veya daha az olacak şekilde azaltmak için değişiklikler yapın. Örneğin, `.bash` geçmiş dosyalarını silin. |
| 3 | Gerekli çekirdek parametresi test çalışması | Değeri olarak ayarlanmadıysa bu hatayı alırsınız `console` `ttyS0` . Aşağıdaki komutu çalıştırarak kontrol edin: <br /> `cat /proc/cmdline` | Değerini `console` olarak ayarlayın `ttyS0` ve isteği yeniden gönderin. |
| 4 | ClientAlive Interval test çalışması | Araç seti size bu test çalışması için başarısız bir sonuç veriyorsa, için uygun olmayan bir değer vardır `ClientAliveInterval` . | Değerini `ClientAliveInterval` 235 değerinden küçük veya buna eşit olarak ayarlayın ve sonra isteği yeniden gönderin. |
|

### <a name="windows-test-cases"></a>Windows test çalışmaları

Aşağıdaki tabloda, test doğrulamasının bir açıklamasıyla birlikte, araç setinin çalışacağı Windows test çalışmaları listelenmektedir:

|Senaryo |Test çalışmaları|Açıklama|
|---|---|---|
|1|İşletim sistemi mimarisi|Azure yalnızca 64 bitlik işletim sistemlerini destekler.|
|2|Kullanıcı hesabı bağımlılığı|Uygulama yürütme, yönetici hesabına bağlı olmamalıdır.|
|3|Yük devri kümesi|Windows Server Yük Devretme Kümelemesi özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|4|IPV6|IPv6 henüz Azure ortamında desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|5|DHCP|Dinamik ana bilgisayar Yapılandırma Protokolü sunucu rolü henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|6|Hyper-V|Hyper-V sunucu rolü henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|7|Uzaktan erişim|Uzaktan erişim (doğrudan erişim) sunucu rolü henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|8|Rights Management Hizmetleri|Rights Management Hizmetleri. Sunucu rolü henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|9|Windows Dağıtım Hizmetleri|Windows Dağıtım Hizmetleri. Sunucu rolü henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|10|BitLocker Sürücü Şifrelemesi|BitLocker Sürücü Şifrelemesi, işletim sistemi sabit diskinde desteklenmez, ancak veri disklerinde kullanılıyor olabilir.|
|11|Internet Depolama adı sunucusu|Internet Depolama adı sunucusu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|12|Çok Yollu G/Ç|Çok yollu g/ç. Bu sunucu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|13|Ağ Yükü Dengeleme|Ağ Yükü Dengeleme. Bu sunucu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|14|Eş Adı Çözümleme Protokolü|Eş adı çözümleme protokolü. Bu sunucu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|15|SNMP Hizmetleri|Basit Ağ Yönetim Protokolü (SNMP) Hizmetleri özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|16|Windows Internet ad hizmeti|Windows Internet ad hizmeti. Bu sunucu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|17|Kablosuz LAN Hizmeti|Kablosuz LAN Hizmeti. Bu sunucu özelliği henüz desteklenmiyor. Uygulamanın bu özelliğe bağımlı olmaması gerekir.|
|

Önceki test durumlarında tüm hatalarda karşılaşırsanız, çözüm için tablodaki **Açıklama** sütununa bakın. Daha fazla bilgi için destek ekibine başvurun.

## <a name="data-disk-size-verification"></a>Veri diski boyut doğrulaması

Boyut 1023 gigabayttan (GB) büyük olan veri diski istekleri onaylanmayacak. Bu kural hem Linux hem de Windows için geçerlidir.

İsteği 1023 GB 'tan küçük veya buna eşit bir boyutla yeniden gönderin.

## <a name="os-disk-size-validation"></a>İşletim sistemi disk boyutu doğrulaması

İşletim sistemi disk boyutuyla ilgili sınırlamalar için aşağıdaki kurallara bakın. Herhangi bir istek gönderdiğinizde, işletim sistemi disk boyutunun Linux veya Windows için kısıtlama dahilinde olduğunu doğrulayın.

|İşletim Sistemi|Önerilen VHD boyutu|
|---|---|
|Linux|1 GB ila 1023 GB|
|Windows|30 GB ila 250 GB|
|

VM 'Ler temeldeki işletim sistemine erişime izin vermediğinden, VHD boyutunun VHD için yeterince büyük olduğundan emin olun. Diskler kesinti olmadan genişletilebilir değildir. 30 GB ile 50 GB arasında bir disk boyutu kullanın.

|VHD boyutu|Dolu gerçek boyut|Çözüm|
|---|---|---|
|>500 tebibayt (Tib)|yok|Özel durum onayı için destek ekibine başvurun.|
|250-500 TiB|Blob boyutundan >200 Gibibyte (gib) farkı|Özel durum onayı için destek ekibine başvurun.|
|

> [!NOTE]
> Daha büyük disk boyutları daha yüksek maliyetlere sahiptir ve kurulum ve çoğaltma işlemi sırasında gecikmeye neden olur. Bu gecikme ve maliyet nedeniyle, destek ekibi özel durum onayı için gerekçe arayabilir.

## <a name="wannacry-patch-verification-test-for-windows"></a>Windows için WannaCry Patch doğrulama testi

WannaCry virüsü ile ilgili olası bir saldırıyı engellemek için tüm Windows yansıma isteklerinin en son düzeltme ekiyle güncelleştirildiğinden emin olun.

Ya da ' dan görüntü dosyası sürümünü doğrulayabilirsiniz `C:\windows\system32\drivers\srv.sys` `srv2.sys` .

Aşağıdaki tabloda, Windows Server 'ın en düşük düzeltme eki uygulanmış sürümü gösterilmektedir:

|İşletim Sistemi|Sürüm|
|---|---|
|Windows hizmeti 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

> [!NOTE]
> Windows Server 2019 zorunlu sürüm gereksinimlerine sahip değildir.

## <a name="sack-vulnerability-patch-verification"></a>SACK güvenlik düzeltme eki doğrulaması

Bir Linux görüntüsü gönderdiğinizde, isteğiniz çekirdek sürümü sorunları nedeniyle reddedilebilir.

Çekirdeği onaylanan bir sürümle güncelleştirin ve isteği yeniden gönderin. Onaylanan çekirdek sürümünü aşağıdaki tabloda bulabilirsiniz. Sürüm numarası, burada listelenen sayıya eşit veya ondan büyük olmalıdır.

Görüntünüz aşağıdaki çekirdek sürümlerinden biriyle yüklenmezse, doğru düzeltme ekleriyle güncelleştirin. Görüntü, gereken bu düzeltme ekleriyle güncelleştirildikten sonra destek ekibinin gerekli onayını iste:

- CVE-2019-11477
- CVE-2019-11478
- CVE-2019-11479

|İşletim sistemi ailesi|Sürüm|Çekirdek|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151.552| 
||14,04 LTS|4.15.0-1049- \* -Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0-1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19,04|5.0.0-1010|
||19,04|5.3.0-1004|
|RHEL ve sent OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|TBD|
||RHEL-SAP 7,5|TBD|
|SLES|SLES11SP4 (SAP dahil)|3.0.101-108.95.2|
||SAP için SLES12SP1|3.12.74-60.64.115.1|
||SAP için SLES12SP2|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (çekirdek-Azure)|
||SAP için SLES12SP3|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (çekirdek-Azure)|
||SAP için SLES12SP4|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (çekirdek-Azure)|
||SAP için SLES15|4.12.14-5.30.1 (çekirdek-Azure)|
||SLES15SP1|4.12.14-5.30.1 (çekirdek-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK yukarıdaki RHEL 'yi izler|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS kararlı 2079.6.0|4.19.43\*|
||Beta 2135.3.1|4.19.50\*|
||Alpha 2163.2.1|4.19.50\*|
|Debian|JESSIE (güvenlik)|3.16.68-2|
||jessıya arkabağlantı noktaları|4.9.168-1 + deb9u3|
||Esnetme (güvenlik)|4.9.168-1 + deb9u3|
||Deuter GNU/Linux 10 (Buster)|Detem 6.3.0-18 + deb9u1|
||Buster, SID (Esnetme noktaları)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Görüntü boyutu megabayt katları cinsinden olmalıdır

Azure 'daki tüm VHD 'Lerin 1 megabayt (MB) katları ile hizalı bir sanal boyutu olmalıdır. VHD 'niz önerilen sanal boyuta bağlı değilse isteğiniz reddedilebilir.

Ham diskten VHD 'ye dönüştürme yaparken yönergeleri izleyin. Ham disk boyutunun 1 MB 'ın katı olduğundan emin olun. Daha fazla bilgi için bkz. [onaylamayan dağıtımlar Için bilgi](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>VM erişimi reddedildi

VM üzerinde bir test çalışması çalıştırmaya yönelik _erişim engellendi_ sorunu, yetersiz ayrıcalıklara neden olmuş olabilir.

Kendi test çalışmalarının çalıştığı hesap için uygun erişimi etkinleştirmediğinizi denetleyin. Etkin değilse test çalışmalarını çalıştırmak için erişimi etkinleştirin. Erişimi etkinleştirmek istemiyorsanız, kendi test çalışması sonuçlarını destek ekibi ile paylaşabilirsiniz.

Sertifika işlemi için SSH devre dışı görüntüyle isteğinizi göndermek için:

1. Görüntinizdeki [Azure VM 'leri için en son sertifika test aracı](https://aka.ms/AzureCertificationTestTool) 'nı çalıştırın.

2. [Destek bileti](https://aka.ms/marketplacepublishersupport)yükseltin. Araç seti raporunu iliştirdiğinizden ve teklif ayrıntıları sağladığınızdan emin olun:
   - Teklif adı
   - Yayımcı adı
   - Plan KIMLIĞI/SKU 'SU ve sürümü

3. Sertifika isteğinizi yeniden gönderin.

## <a name="download-failure"></a>İndirme hatası

Paylaşılan erişim imzası (SAS) URL 'SI ile VM görüntüsünü indirdiğinizde ortaya çıkan herhangi bir sorun için aşağıdaki tabloya bakın.

|Hata|Nedeni|Çözüm|
|---|---|---|
|Blob bulunamadı|VHD silinmiş ya da belirtilen konumdan taşınmış olabilir.|| 
|Kullanılan blob|VHD başka bir iç işlem tarafından kullanılıyor.|VHD 'yi bir SAS URL 'si ile indirdiğinizde kullanılan bir durumda olmalıdır.|
|Geçersiz SAS URL 'SI|VHD için ilişkili SAS URL 'SI yanlış.|Doğru SAS URL 'sini alın.|
|Geçersiz imza|VHD için ilişkili SAS URL 'SI yanlış.|Doğru SAS URL 'sini alın.|
|HTTP koşullu üstbilgisi|SAS URL 'SI geçersiz.|Doğru SAS URL 'sini alın.|
|Geçersiz VHD adı|VHD adında, yüzde işareti veya tırnak işareti gibi özel karakterlerin mevcut olup olmadığını denetleyin `%` `"` .|Özel karakterleri kaldırarak VHD dosyasını yeniden adlandırın.|
|

## <a name="first-partition-starts-at-1-mb-2048-sectors"></a>İlk bölüm 1 MB (2048 kesim) ile başlar

[Kendi görüntünüzü oluşturuyorsanız](azure-vm-create-using-own-image.md), işletim sistemi diskinin ilk 2048 kesimlerinin (1 MB) boş olduğundan emin olun. Aksi takdirde, yayımlarınız başarısız olur. Bu gereksinim yalnızca işletim sistemi diski (veri diskleri değil) için geçerlidir. Görüntüyü [onaylanan bir tabandan](azure-vm-create-using-approved-base.md)oluşturuyorsanız, bu gereksinimi atlayabilirsiniz.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Boş bir VHD üzerinde 1 MB (2048 kesim, her 512 baytlık bir kesim) bölümü oluşturun

Bu adımlar yalnızca Linux için geçerlidir.

1. Ubuntu, sent OS veya diğer gibi herhangi bir tür Linux sanal makinesi oluşturun. Gerekli alanları doldurup **İleri ' yi seçin: diskler >**.

   ![' Sonraki: diskler komutu ' düğmesine sahip bir sanal makine oluştur sayfasının vurgulandığını gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. VM 'niz için yönetilmeyen bir disk oluşturun.

   Varsayılan değerleri kullanın veya NIC, NSG ve genel IP gibi alanlar için herhangi bir değer belirtin.

   ![Sanal makine akışı oluşturma içindeki ' veri diskleri ' sayfasının ekran görüntüsü resmi.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. VM 'yi oluşturduktan sonra sol bölmedeki **diskler** ' i seçin.

   ![Bir VM için disklerin nasıl seçileceğini gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Bölüm tablosu oluşturmak için VHD 'nizi sanal makinenize veri diski olarak ekleyin.

   1. **Veri diski**  >  **mevcut blobu** Ekle ' yi seçin.

      ![VHD 'nize nasıl veri diski ekleneceğini gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. VHD depolama hesabınızı bulun.
   1. **Kapsayıcı** ' yı seçin ve ardından VHD 'nizi seçin.
   1. **Tamam**’ı seçin.

      ![Yönetilmeyen Disk Ekle sayfasının ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-19.png)

      VHD 'niz, veri diski LUN 0 olarak eklenecektir.

   1. VM’yi yeniden başlatın.

1. VM 'yi yeniden başlattıktan sonra, Putty veya başka bir istemciyi kullanarak VM 'de oturum açın ve `sudo  -i` kök erişimi kazanmak için komutunu çalıştırın.

   ![Sudo-i komutunu gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. VHD 'niz üzerinde bir bölüm oluşturun.

   1. `fdisk /dev/sdb`Komut girin.
   1. VHD 'nizden mevcut bölüm listesini görüntülemek için girin `p` .
   1. `d`VHD 'niz içindeki mevcut tüm bölümleri silmek için girin. Gerekli değilse, bu adımı atlayabilirsiniz.

      ![Mevcut bölümleri silmeye yönelik komutları gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. `n`Yeni bölüm oluşturmak için girin ve `p` (birincil bölüm) öğesini seçin.

   1. _İlk kesim_ değeri olarak 2048 girin. _Son kesime_ varsayılan değer olarak bırakabilirsiniz.

      >[!IMPORTANT]
      >Mevcut veriler 2048 kesim (her 512 baytlık bir kesim) olarak silinir. Yeni bir bölüm oluşturmadan önce VHD 'nin yedeklemesi.

      ![Silinen veriler için komutları ve çıktıyı gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. `w`Bölüm oluşturulmasını onaylamak için yazın.

      ![Bölüm oluşturmaya yönelik komutları gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Komutunu çalıştırarak ve yazarak bölüm tablosunu doğrulayabilirsiniz `n fdisk /dev/sdb` `p` . Bölümün 2048 fark değeriyle oluşturulduğunu görürsünüz.

      ![2048 sapmasını oluşturmaya yönelik komutları gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. VHD 'yi VM 'den ayırın ve VM 'yi silin.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>VHD 'deki mevcut verileri taşıyarak 1 MB (2048 kesim, her 512 baytlık bir kesim) bölümü oluşturun

Bu adımlar yalnızca Linux için geçerlidir.

1. Ubuntu, sent OS veya diğer gibi herhangi bir tür Linux sanal makinesi oluşturun. Gerekli alanları doldurup **İleri ' yi seçin: diskler >**.

   ![' Sonraki: diskler komutu ' düğmesine sahip bir sanal makine oluştur sayfasının vurgulandığını gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. VM 'niz için yönetilmeyen bir disk oluşturun.

   ![Sanal makine akışı oluşturma içindeki ' veri diskleri ' sayfasının ekran görüntüsü resmi.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Varsayılan değerleri kullanın veya NIC, NSG ve genel IP gibi alanlar için herhangi bir değer belirtin.

1. VM 'yi oluşturduktan sonra sol bölmedeki **diskler** ' i seçin.

   ![Bir VM için disklerin nasıl seçileceğini gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Bölüm tablosu oluşturmak için VHD 'nizi sanal makinenize veri diski olarak ekleyin.

   1. Bölüm tablosu oluşturmak için VHD 'nizi sanal makinenize veri diski olarak ekleyin.

   1. **Veri diski**  >  **mevcut blobu** Ekle ' yi seçin.

      ![VHD 'nize nasıl veri diski ekleneceğini gösteren ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. VHD depolama hesabınızı bulun.
   1. **Kapsayıcı** ' yı seçin ve ardından VHD 'nizi seçin.
   1. **Tamam**’ı seçin.

      ![Yönetilmeyen Disk Ekle sayfasının ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-19.png)

      VHD 'niz, veri diski LUN 0 olarak eklenecektir.

   1. VM’yi yeniden başlatın.

1. Putty veya başka bir istemciyle VM 'de oturum açın ve `sudo  -i` kök erişimi kazanmak için komutunu çalıştırın.

   ![Oturum açma ve sudo-i komutunu gösteren Putty istemci komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1` komutunu çalıştırın.

   ![Komutun yürütülmesini gösteren Putty istemcisi komut satırı ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Bu komutun tamamlanması, diskin boyutuna bağlı olduğundan biraz zaman alabilir.

1. VHD 'yi VM 'den ayırın ve VM 'yi silin.


## <a name="default-credentials"></a>Varsayılan kimlik bilgileri

Varsayılan kimlik bilgilerini hiçbir şekilde gönderilen VHD ile göndermeyin. Varsayılan kimlik bilgilerini ekleme, VHD 'nin güvenlik tehditlerine karşı daha savunmasız olmasını sağlar. Bunun yerine, VHD 'YI gönderdiğinizde kendi kimlik bilgilerinizi oluşturun.
  
## <a name="datadisk-mapped-incorrectly"></a>Veri diski yanlış eşlendi

Sıralı olmayan birden fazla veri diski ile bir istek gönderildiğinde, bir eşleme sorunu oluşabilir. Örneğin, üç veri diski için numaralandırma sırası *0, 1, 2* olmalıdır. Diğer herhangi bir sıra, eşleme sorunu olarak kabul edilir.

İsteği veri disklerinin doğru sıralaması ile yeniden gönderin.

## <a name="incorrect-os-mapping"></a>Yanlış işletim sistemi eşlemesi

Bir görüntü oluşturulduğunda, bu, yanlış işletim sistemi etiketine eşlenmiş veya atanmış olabilir. Örneğin, görüntüyü oluştururken işletim sistemi adının bir parçası olarak **Windows** ' u seçtiğinizde, işletim sistemi diski yalnızca Windows ile yüklenmelidir. Aynı gereksinim Linux için de geçerlidir.

## <a name="vm-not-generalized"></a>VM Genelleştirilmiş değil

Azure Marketi 'nden alınan tüm görüntülerin yeniden kullanılabilmesi için işletim sistemi VHD 'sinin genelleştirilmesi gerekir.

- **Linux** için aşağıdaki Işlem BIR Linux sanal makinesini genelleştirir ve ayrı bir VM olarak yeniden dağıtır.

  SSH penceresinde şu komutu girin: `sudo waagent -deprovision+user` .

- **Windows** için, kullanarak Windows görüntülerini genelleştirdiğinizde `sysreptool` .

  Araç hakkında daha fazla bilgi için `sysreptool` bkz. [Sistem Hazırlama (Sysprep) genel bakış](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Veri diski hataları

Veri diskiyle ilgili hatalara yönelik çözümler için aşağıdaki tabloyu kullanın:

|Hata|Nedeni|Çözüm|
|---|---|---|
|`DataDisk- InvalidUrl:`|Bu hata, teklif gönderildiğinde geçersiz bir mantıksal birim numarası (LUN) nedeniyle meydana gelebilir.|Veri diski için LUN numarası sırasının Iş Ortağı Merkezi 'nde olduğunu doğrulayın.|
|`DataDisk- NotFound:`|Bu hata, bir veri diski belirtilen SAS URL 'sinde bulunmadığından meydana gelebilir.|Veri diskinin belirtilen SAS URL 'sinde bulunduğundan emin olun.|

## <a name="remote-access-issue"></a>Uzaktan erişim sorunu

Windows görüntüsü için Uzak Masaüstü Protokolü (RDP) seçeneği etkinleştirilmemişse bu hatayı alırsınız.

Göndermeden önce Windows görüntüleri için RDP erişimini etkinleştirin.

## <a name="bash-history-failed"></a>Bash geçmişi başarısız oldu

Gönderilen görüntinizdeki Bash geçmişinin boyutu 1 kilobayt (KB) daha büyükse bu hatayı görürsünüz. Dosyanın potansiyel olarak duyarlı bilgiler içermeden kısıtlamak için Boyut 1 KB ile kısıtlıdır.

Bash geçmişini silmek için:

1. VM 'yi dağıtın ve Azure portal **komutu Çalıştır** seçeneğini belirleyin.

   ![Sol bölmedeki ' Çalıştır komutu ' seçeneğiyle birlikte Azure portal ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. **Runshellscript** ilk seçeneğini belirleyin ve ardından şu komutu çalıştırın: `cat /dev/null > ~/.bash_history && history -c` .

   ![Azure portal ' komut dosyasını çalıştır ' sayfasının ekran görüntüsü.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Komut başarıyla çalıştıktan sonra, sanal makineyi yeniden başlatın.

1. VM 'yi genelleştirin, görüntü VHD 'sini alın ve VM 'yi durdurun.

1. Genelleştirilmiş görüntüyü yeniden gönderin.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Seçme testleri için VM görüntülerinde özel durum isteme

Yayımcılar, VM sertifikası sırasında gerçekleştirilen birkaç test için özel durumlar isteyebilir. Bir yayımcının isteği desteklemek için kanıt sağladığı nadir durumlarda özel durumlar sunulmaktadır. Sertifika ekibi, her zaman özel durumları reddetme veya onaylama hakkını saklı tutar.

Bu bölümde, yayımcıların bir özel durum istemesi ve bir tane istemesi için genel senaryolar açıklanmaktadır.

### <a name="scenarios-for-exception"></a>Özel durum senaryoları

Yayımcılar genellikle aşağıdaki durumlarda özel durumlar ister:

- **Bir veya daha fazla test çalışması Için özel durum**. Test çalışmaları için özel durumlar istemek üzere [Iş Ortağı Merkezi desteğiyle](https://aka.ms/marketplacepublishersupport) iletişim kurun.

- **Kilitli VM 'ler/kök erişimi yok**. VM 'lerde yüklü olan güvenlik duvarları gibi yazılımlar bulunduğundan, birkaç yayımcıda VM 'Lerin kilitlenmesi gereken senaryolar vardır. Bu durumda, [sertifikalı test aracını](https://aka.ms/AzureCertificationTestTool) indirin ve raporu [iş ortağı merkezi desteği](https://aka.ms/marketplacepublishersupport)' ne gönderebilirsiniz.

- **Özel şablonlar**. Bazı yayımcılar VM 'Leri dağıtmak için özel bir Azure Resource Manager (ARM) şablonu gerektiren VM görüntülerini yayımlar. Bu durumda, sertifika ekibi tarafından doğrulama için kullanılabilmesi için [Iş Ortağı Merkezi desteğiyle](https://aka.ms/marketplacepublishersupport) özel şablonları gönderebilirsiniz.

### <a name="information-to-provide-for-exception-scenarios"></a>Özel durum senaryoları için sağlanacak bilgiler

Senaryolarından biri için bir özel durum istemek üzere [Iş Ortağı Merkezi desteğiyle](https://aka.ms/marketplacepublishersupport) iletişim kurun ve aşağıdaki bilgileri ekleyin:

- **Yayımcı kimliği**. Iş Ortağı Merkezi portalı yayımcı KIMLIĞINIZI yazın.
- **TEKLIF kimliği/adı**. Teklif KIMLIĞINI veya adını girin.
- **SKU/plan kimliği**. VM teklif planı KIMLIĞI veya SKU 'SU yazın.
- **Sürüm**. Özel durum gerektiren VM teklif sürümünü girin.
- **Özel durum türü**. Testler, kilitli VM veya özel şablonlar arasından seçim yapın.
- **Isteğin nedeni**. Özel durum isteğinin sebebini ve test muafiyetleri hakkında bilgi ekleyin.
- **Zaman çizelgesi**. Özel durum için bitiş tarihini girin.
- **Ek**. Ekli önemli bulgu belgeleri:

  - Kilitli VM 'Ler için, test raporunu iliştirin.
  - Özel şablonlar için özel ARM şablonunu ek olarak sağlayın.

  Bu ekleri dahil etmeme başarısız olursa, isteğiniz reddedilir.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Bir güvenlik açığını veya bir VM teklifinde yararlanmaya yönelik bir açığı çözün

Bu bölümde, sanal makine görüntülerinizden biriyle bir güvenlik açığı veya yararlanma bulunduğunda yeni bir VM görüntüsünün nasıl sağlanacağı açıklanmaktadır. Yalnızca Azure Marketi 'Nde yayınlanan Azure VM teklifleri için geçerlidir.

> [!NOTE]
> Son VM görüntüsünü bir plandan kaldıramaz veya bir teklifin son planını satamazsınız.

Aşağıdaki eylemlerden birini yapın:

- Güvenlik açığı bulunan VM görüntüsünü değiştirmek için yeni bir VM görüntünüz varsa bkz. [bir sabıt VM görüntüsü sağlama](#provide-a-fixed-vm-image).
- Bir plandaki tek VM görüntüsünü değiştirmek için yeni bir VM Görüntünüz yoksa veya plan ile işiniz bittiğinde [planı satma durun](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Teklifteki tek VM görüntüsünü değiştirmeyi planlamıyorsanız, [teklifi satmasını durdurmanız](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)önerilir.

### <a name="provide-a-fixed-vm-image"></a>Sabit bir VM görüntüsü sağlama

Bir güvenlik açığı olan veya açıktan yararlanan bir VM görüntüsünü değiştirmek için sabit bir VM görüntüsü sağlamak için:

1. Güvenlik açığı veya açıktan yararlanmak için yeni bir VM görüntüsü sağlayın.
1. VM görüntüsünü güvenlik açığı veya açıktan yararlanmaya kaldırın.
1. Teklifi yeniden yayımlayın.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Güvenlik açığı veya açıktan yararlanmak için yeni bir VM görüntüsü sağlayın

Bu adımları gerçekleştirmek için, eklemek istediğiniz VM görüntüsü için teknik varlıkları hazırlayın. Daha fazla bilgi için bkz. [onaylanan temel kullanarak bir sanal makine oluşturma](azure-vm-create-using-approved-base.md) veya [kendi görüntünüzü kullanarak bir sanal makıne oluşturma](azure-vm-create-using-own-image.md) ve [VM Görüntünüz için SAS URI](azure-vm-get-sas-uri.md)oluşturma.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
1. Sol bölmede, **ticari Market**  >  **genel bakış**' ı seçin.
1. **Teklif diğer adı** sütununda teklifi seçin.
1. **Plana genel bakış** sekmesinde, **ad** sütununda, uygun planı seçin.
1. **Teknik yapılandırma** sekmesinde, **VM GÖRÜNTÜLERI** altında **+ VM görüntüsü Ekle**' yi seçin.

   > [!NOTE]
   > Tek seferde bir plana yalnızca bir VM görüntüsü ekleyebilirsiniz. Birden çok VM görüntüsü eklemek için, sonraki VM görüntüsünü eklemeden önce ilk olanı yayımlayın.

1. Görüntülenen kutulara yeni bir disk sürümü ve sanal makine görüntüsü belirtin.
1. **Taslağı kaydet**'i seçin.

Sonra, VM görüntüsünü güvenlik güvenlik açığıyla birlikte kaldırın.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>VM görüntüsünü güvenlik açığı veya açıktan yararlanmaya kaldırma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2. Sol bölmede, **ticari Market**  >  **genel bakış**' ı seçin.
3. **Teklif diğer adı** sütununda teklifi seçin.
4. **Plana genel bakış** sekmesinde, **ad** sütununda, uygun planı seçin.
5. **Teknik yapılandırma** sekmesinde, **VM görüntüleri** altında, kaldırmak istediğiniz VM görüntüsünün yanındaki VM görüntüsünü **Kaldır**' ı seçin.
6. İletişim kutusunda **devam**' ı seçin.
7. **Taslağı kaydet**'i seçin.

Sonra, teklifi yeniden yayımlayın.

#### <a name="republish-the-offer"></a>Teklifi yeniden yayımlayın

1. **Gözden geçir ve Yayımla '** yı seçin.
2. Sertifika ekibine herhangi bir bilgi sağlamanız gerekiyorsa, **sertifikayı sertifika notları** kutusuna ekleyin.
3. **Yayımla**’yı seçin.

Yayımlama işlemini gerçekleştirmek için bkz. [teklifleri inceleme ve yayımlama](review-publish-offer.md).

### <a name="vm-images-with-limited-access-or-requiring-custom-templates"></a>Sınırlı erişimi olan veya özel şablonlar gerektiren VM görüntüleri

#### <a name="locked-down-or-ssh-disabled-offer"></a>Kilitlenmiş (veya) SSH devre dışı teklifi

  SSH devre dışı bırakılmış (Linux için) veya RDP devre dışı (Windows için) ile yayınlanan görüntüler, kilitli VM 'Ler olarak kabul edilir. Hangi yayımcıların yalnızca birkaç kullanıcı için sınırlı erişime izin verdiğiyle ilgili özel iş senaryoları vardır. Doğrulama denetimleri sırasında, kilitli VM 'Ler belirli sertifika komutlarının yürütülmesine izin vermiyor olmayabilir.


#### <a name="custom-templates"></a>Özel şablonlar

   Genel olarak, tek VM 'ler kapsamında yayınlanan tüm görüntüler dağıtım için standart ARM şablonunu izler. Ancak, VM 'Leri dağıtmada (örn. birden çok NIC (), örneğin, yapılandırılacak bir özelleştirme gerektiren senaryolar vardır.
    
   Aşağıdaki senaryolara (ayrıntılı olmayan) bağlı olarak, yayımcılar VM dağıtmak için özel şablonlar kullanır:

   * VM ek ağ alt ağları gerektirir.
   * ARM şablonuna eklenecek ek meta veriler.
   * ARM şablonunun yürütülmesi için önkoşul olan komutlar.

### <a name="vm-extensions"></a>VM uzantıları   

   Azure sanal makine (VM) uzantıları, dağıtım sonrası yapılandırma ve Azure VM'lerinde otomasyon görevleri sunan küçük uygulamalardır. Örneğin bir sanal makinede yazılım yüklemesi gerekiyorsa, virüsten koruma gerekiyorsa veya içinde bir betik çalıştırılacaksa VM uzantısı kullanılabilir. 

   Linux VM Uzantısı doğrulamaları görüntünün bir parçası olmasını gerektirir:
* Azure Linux Aracısı daha büyük 2.2.41
* 2,8 üzerinde Python sürümü 


Daha fazla bilgi için lütfen [VM uzantısını](../virtual-machines/extensions/diagnostics-linux.md)ziyaret edin.
     
## <a name="next-steps"></a>Sonraki adımlar

- [VM teklifi özelliklerini yapılandırma](azure-vm-create-properties.md)
- [Etkin Market 'teki ödüller](partner-center-portal/marketplace-rewards.md)
- Geliştirme için sorularınız veya geri bildiriminiz varsa, [Iş Ortağı Merkezi desteğiyle](https://aka.ms/marketplacepublishersupport)iletişim kurun.
