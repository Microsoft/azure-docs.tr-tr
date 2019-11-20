---
title: Azure geçişi sorunlarını giderme | Microsoft Docs
description: Azure geçişi hizmetindeki bilinen sorunlara genel bir bakış ve sık karşılaşılan hatalara yönelik sorun giderme ipuçları sağlar.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: hamusa
ms.openlocfilehash: 468c87e176cc61c48ba4caabd1c5a26f94d5fb5b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970645"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Geçişi sorunlarını giderme

[Azure geçişi](migrate-services-overview.md) , değerlendirme ve geçiş için bir araç merkezi ve üçüncü taraf bağımsız yazılım SATıCıSı (ISV) teklifleri sunar. Bu makale, Azure geçişi, Azure geçişi sunucu değerlendirmesi ve Azure geçişi sunucu geçişi ile ilgili sorunları gidermenize yardımcı olur.


## <a name="find-a-project"></a>Proje bulma

Azure geçişi 'nin [iki sürümü](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) vardır.


Azure geçişi projesini geçerli Azure geçişi sürümünde oluşturduysanız şunları yapın:

1. [Azure Portal](https://portal.azure.com) **Azure geçişi**' ni arayın.
2. Azure 'da Pano geçirme > **sunucularında**sağ üst köşedeki **Değiştir** ' i seçin.

    ![Mevcut bir Azure geçişi projesine geç](./media/troubleshooting-general/switch-project.png)

3. Uygun aboneliği ve Azure geçişi projesini seçin.


Projeyi Azure geçişi 'nin önceki sürümünde oluşturduysanız, şunları yapın:

1. [Azure Portal](https://portal.azure.com) **Azure geçişi**' ni arayın.
2. Azure geçişi panosunda, önceki sürümde bir proje oluşturduysanız, eski projelere başvuran bir başlık görüntülenir. Başlığı seçin.

    ![Mevcut projelere erişin](./media/troubleshooting-general/access-existing-projects.png)

3. Eski proje listesini gözden geçirin.


## <a name="create-additional-projects"></a>Ek projeler oluşturma

Yeni bir Azure geçişi projesi oluşturun ve şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) **Azure geçişi**' ni arayın.
2. Azure 'a Pano geçirme > **sunucularında**sağ üst köşedeki **Değiştir** ' i seçin.

   ![Azure geçişi projesini değiştirme](./media/troubleshooting-general/switch-project.png)

3. Yeni bir proje oluşturmak için **buraya tıklayın ' ı**seçin.

   ![İkinci bir Azure geçişi projesi oluşturma](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Desteklenen coğrafi lıkları gözden geçirin

[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) ve [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)için desteklenen coğrafi lıkları gözden geçirin.

## <a name="delete-projectsworkspaces"></a>Projeleri/çalışma alanlarını sil

Azure geçişi projelerini ve Log Analytics çalışma alanlarını silerken şunları unutmayın:

- Bir Azure geçişi projesini sildiğinizde, proje *ve* bulunan makineler hakkındaki meta veriler silinir.
- Sunucu değerlendirmesi aracına bir Log Analytics çalışma alanı eklediyseniz, çalışma alanı otomatik olarak silinmez.
- Aynı Log Analytics çalışma alanı birden çok senaryo için kullanılabilir.
- Log Analytics çalışma alanını silmek istiyorsanız, bunu el ile yapmanız gerekir.


### <a name="delete-a-project"></a>Projeyi silme

Geçerli Azure geçişi sürümündeki bir projeyi silmek için:

1. Projenin oluşturulduğu Azure kaynak grubunu açın.
2. Kaynak grubu sayfasında **gizli türleri göster**' i seçin.
3. Silmek istediğiniz geçişi projeyi seçin. Kaynak türü Microsoft. Migrate/migrateprojects olur ve onu siler.

Azure geçişi 'nin eski sürümündeki bir projeyi silmek için:

1. Projenin oluşturulduğu Azure kaynak grubunu açın.
2. Silmek istediğiniz geçişi projeyi seçin. Kaynak türü geçiş projem olur ve onu siler.


### <a name="delete-a-workspace"></a>Çalışma alanını silme

Projeye bağlı Log Analytics çalışma alanına gidin.
* Azure geçişi projesini sildiyseniz, **Essentials** > **Sunucu değerlendirmesi**içindeki çalışma alanının bağlantısını bulabilirsiniz.
       ![LA çalışma alanı @ no__t-1

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Hata "Isteklerin Kullanıcı kimliği üst bilgilerini içermesi gerekir"

Proje oluştururken bu hata, kuruluşun Azure Active Directory (Azure AD) kiracısına erişiminizin olmadığını gösterebilir.

- Bir Azure AD kiracısına ilk kez eklenişinizde kiracıya katılması için bir e-posta davetiyesi alırsınız.
- Kiracıya başarıyla eklemek için daveti kabul edin.
    - E-postayı göremiyorsanız kiracıya erişimi olan bir kullanıcıyla iletişim kurun ve [daveti](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) size yeniden göndermesini isteyin.
    - Davet e-postasını aldıktan sonra açın ve daveti kabul etmek için bağlantıyı seçin. Sonra, Azure portal oturumunuzu kapatın ve yeniden oturum açın. (tarayıcının yenilenmesi çalışmaz.) Daha sonra geçiş projesi oluşturmaya başlayabilirsiniz.


## <a name="error-invalid-ovf-manifest-entry"></a>Hata "geçersiz OVF manifest entry"

"Belirtilen bildirim dosyası geçersiz: geçersiz OVF manifest entry" hatasını alırsanız şunları yapın:

1. Azure geçişi gereç OVA dosyasının karma değerini denetleyerek doğru şekilde indirildiğini doğrulayın. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Karma değeri eşleşmiyorsa, OVA dosyasını yeniden indirin ve dağıtımı yeniden deneyin.
2. Dağıtım hala başarısız olursa ve OVF dosyasını dağıtmak için VMware vSphere istemcisini kullanıyorsanız, vSphere Web istemcisi aracılığıyla dağıtmayı deneyin. Dağıtım hala başarısız olursa, farklı bir Web tarayıcısı kullanmayı deneyin.
3. VSphere Web istemcisini kullanıyorsanız ve vCenter Server 6,5 veya 6,7 ' de dağıtmaya çalışıyorsanız, OVA 'yı doğrudan ESXi konağına dağıtmayı deneyin:
   - Web istemcisi (https://<*ana BILGISAYAR IP adresi*>/UI) Ile ESXi konağına doğrudan (vCenter Server yerine) bağlanın.
   - **Ana** > **envanterinde** **Dosya** > **ovf şablonu dağıt**' ı seçin. OVA 'ya gidin ve dağıtımı doldurun.
4. Dağıtım hala başarısız olursa Azure geçiş desteği 'ne başvurun.

## <a name="appliance-cant-connect-to-the-internet"></a>Gereç internet 'e bağlanamıyor

Gereç makinesi bir proxy 'nin arkasındaysa bu durum oluşabilir.

- Proxy 'ye ihtiyaç duyuyorsa yetkilendirme kimlik bilgilerini sağladığınızdan emin olun.
- Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu URL 'Leri izin verilenler listesine ekleyin:

    - [VMware değerlendirmesi URL 'Leri](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Hyper-V değerlendirmesi için URL 'Ler](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [VMware aracısız geçişinin URL 'Leri](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [VMware Aracısı tabanlı geçiş için URL 'ler](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Hyper-V geçişi için URL 'Ler](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- İnternet 'e bağlanmak için bir kesintiye uğratan ara sunucu kullanıyorsanız, [Bu adımları](https://docs.microsoft.com/azure/migrate/concepts-collector)kullanarak proxy SERTIFIKASıNı gereç sanal makinesine aktarın.

## <a name="errordatetime-synchronization"></a>Hata: Tarih/saat eşitleme

Tarih ve saat eşitleme (802) ile ilgili bir hata, sunucu saatinin beş dakikadan uzun bir süredir geçerli zamandan eşitlenmemiş olabileceğini gösterir. Toplayıcı VM 'sinin saat saatini Şu anki saatle eşleşecek şekilde değiştirin:

1. VM 'de bir yönetici komut istemi açın.
2. Saat dilimini denetlemek için **w32tm/tz komutunu**çalıştırın.
3. Saati eşitleme için **w32tm/resync**komutunu çalıştırın.


## <a name="error-unabletoconnecttoserver"></a>Hata: UnableToConnectToServer

Bu bağlantı hatası alırsanız, vCenter Server *ServerName*. com: 9443 öğesine bağlanamadıysanız. Hata ayrıntıları, https://*ServerName*. com: 9443/SDK ' da iletiyi kabul edebilecek bir uç nokta dinleme olmadığını gösterir.

- En son gereç sürümünü çalıştırıp çalıştırmadığını denetleyin. Değilseniz, gereci [en son sürüme](https://docs.microsoft.com/azure/migrate/concepts-collector)yükseltin.
- Sorun hala en son sürümde gerçekleşirse, Gereç belirtilen vCenter Server adını çözemeyebilir veya belirtilen bağlantı noktası yanlış olabilir. Varsayılan olarak, bağlantı noktası belirtilmemişse, toplayıcı 443 numaralı bağlantı noktası numarasına bağlanmayı dener.

    1. Gereci *ServerName*. com ' a ping yapın.
    2. 1\. adım başarısız olursa, IP adresini kullanarak vCenter Server 'a bağlanmayı deneyin.
    3. VCenter Server bağlanmak için doğru bağlantı noktası numarasını belirler.
    4. VCenter Server çalışır olduğunu doğrulayın.


## <a name="error-appliance-might-not-be-registered"></a>Hata: gereç kaydettirilmemiş olabilir

- Hata 60052, "gereci kaydetmek için kullanılan Azure hesabının izinleri yetersizse," gereç Azure geçişi projesine başarıyla kaydedilmemiş olabilir "hatası oluşur.
    - Gereci kaydetmek için kullanılan Azure Kullanıcı hesabının abonelikte en az katkıda bulunan izinleri olduğundan emin olun.
    - Gerekli Azure rolleri ve izinleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .
- Hata 60039, "gereç, kayıt başarısız olursa, kayıt başarısız olursa, Gereç kayıt işlemi için kullanılan Azure geçişi projesi bulunamadığı için," gereç Azure geçiş projesi 'ne başarıyla kaydettirilmemiş "olabilir
    - Azure portal ve projenin kaynak grubunda mevcut olup olmadığını kontrol edin.
    - Proje yoksa, kaynak grubunuzda yeni bir Azure geçişi projesi oluşturun ve gereci yeniden kaydedin. Yeni bir proje oluşturmayı [öğrenin](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) .

## <a name="error-key-vault-management-operation-failed"></a>Hata: Key Vault Yönetimi işlemi başarısız oldu

60030 veya 60031 hatası alırsanız, "bir Azure Key Vault yönetim işlemi başarısız oldu", şunları yapın:
- Gereci kaydetmek için kullanılan Azure Kullanıcı hesabının abonelikte en az katkıda bulunan izinleri olduğundan emin olun.
- Hesabın hata iletisinde belirtilen anahtar kasasına erişimi olduğundan emin olun ve işlemi yeniden deneyin.
- Sorun devam ederse, Microsoft desteği 'ne başvurun.
- Gerekli Azure rolleri ve izinleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .

## <a name="fix-discovery-couldnt-be-initiated"></a>Çözüm: bulma başlatılamadı

Hata 60028: "bir hata nedeniyle bulma başlatılamadı. Belirtilen konaklar veya kümeler listesi için işlem başarısız oldu "VM bilgilerine erişirken veya alırken bir sorun nedeniyle hatada listelenen konaklarda bulma işleminin başlatılamayacağını gösterir. Ana bilgisayarların geri kalanı başarıyla eklendi.

- Hatada listelenen Konakları, **ana bilgisayar Ekle** seçeneğini kullanarak yeniden ekleyin.
- Doğrulama hatası varsa, hataları onarmak için düzeltme kılavuzunu gözden geçirin ve sonra **bulmayı Kaydet ve Başlat** seçeneğini tekrar deneyin.

## <a name="fix-azure-ad-operation-failed-60025"></a>Çözüm: Azure AD işlemi başarısız oldu (60025)

Hata 60025: "Azure AD işlemi başarısız oldu. Azure AD uygulaması oluşturulurken veya güncelleştirilirken oluşan hata oluştu "bulmayı başlatmak için kullanılan Azure Kullanıcı hesabı gereci kaydetmek için kullanılan hesaptan farklı olduğunda gerçekleşir. Aşağıdakilerden birini yapın:

- Keşfi başlatan kullanıcı hesabının gereci kaydetmek için kullanılan ile aynı olduğundan emin olun.
- Bulma işleminin başarısız olduğu Kullanıcı hesabına Azure Active Directory Uygulama erişim izinleri sağlayın.
- Azure geçişi projesi için önceden oluşturulan kaynak grubunu silin. Yeniden başlamak için başka bir kaynak grubu oluşturun.
- Azure Active Directory Uygulama izinleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .


## <a name="discovered-vms-not-in-portal"></a>Bulunan VM 'Ler portalda yok

**Sunucu değerlendirmesi** ve **sunucu geçişinin** **bulma işlemini devam ettiğinden**, ancak henüz portalda VM 'leri görmemesi için bulmayı başlatırsanız, aşağıdakilere göz atın:

- Gereci bulmayı başlattıktan sonra, VMware VM keşfi 15 dakika boyunca ve Hyper-V VM keşfi için eklenen her konak için iki dakika sürer.
- Bu bekleme sürelerinden sonra bile **bulma devam ediyor** ' i görmeye devam ederseniz, **sunucular** sekmesinde **Yenile** ' yi seçin. Bu, **Sunucu değerlendirmesi** ve **sunucu geçişi**içindeki keşfedilen sunucuların sayısını göstermelidir.


## <a name="deleted-vms-in-the-portal"></a>Portalda silinen VM 'Ler

Şirket içi ortamınızı sürekli tespit eden bir gereç dağıttıysanız, ancak silinen VM 'Ler portalda gösterilmeye devam ediyorsa, aşağıdakileri unutmayın:  

- Gereç tarafından toplanan bulgu verilerinin portalda yansıtılması 30 dakikaya kadar sürer.
- 30 dakikadan sonra güncel bilgileri görmüyorsanız, bu adımları izleyerek verileri yenileyin:

    1. **Sunucular** > **Azure sunucu değerlendirmesini geçir**bölümünde **genel bakış**' ı seçin.
    2. **Yönet**' in altında **Aracı durumu** ' yi seçin.
    3. **Aracıyı Yenile**' yi seçin.
    1. Yenileme işleminin tamamlanmasını bekleyin. Şimdi güncel bilgileri görmeniz gerekir.

## <a name="vm-information-isnt-in-the-portal"></a>VM bilgileri portalda yok

- Gereç tarafından toplanan bulgu verilerinin portalda yansıtılması 30 dakikaya kadar sürer.
- 30 dakikadan sonra güncel bilgileri görmüyorsanız, bu adımları izleyerek verileri yenileyin:

    1. **Sunucular** > **Azure sunucu değerlendirmesini geçir**bölümünde **genel bakış**' ı seçin.
    2. **Yönet**' in altında **Aracı durumu** ' yi seçin.
    3. **Aracıyı Yenile**' yi seçin.
    1. Yenileme işleminin tamamlanmasını bekleyin. Şimdi güncel bilgileri görmeniz gerekir.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Çözüm: konağa veya kümeye bağlanılamıyor

Hata 50004: "sunucu adı çözümlenemediği için bir konağa veya kümeye bağlanılamıyor. WinRM hata kodu: gereç için Azure DNS hizmeti, verdiğiniz küme veya ana bilgisayar adını çözümleyemezse, 0x803381B9 "gerçekleşebilir.

- Kümede bu hatayı görürseniz, küme FQDN 'SI.
- Ayrıca, bir kümedeki konaklar için bu hatayı görebilirsiniz. Bu, gerecin kümeye bağlanabildiğini, ancak kümenin FQDN olmayan ana bilgisayar adlarını döndürdüğünü gösterir. Bu hatayı çözmek için, IP adresi ve ana bilgisayar adlarının eşlemesini ekleyerek gereç üzerindeki Hosts dosyasını güncelleştirin:
    1. Not defteri 'Ni yönetici olarak açın.
    2. C:\Windows\System32\Drivers\etc\hosts dosyasını açın.
    3. IP adresini ve ana bilgisayar adını bir satıra ekleyin. Bu hatayı gördüğünüz her bir konak veya küme için tekrarlayın.
    4. Hosts dosyasını kaydedin ve kapatın.
    5. Gereç Yönetimi uygulamasını kullanarak gerecin konaklara bağlanıp bağlanamayacağını denetleyin. 30 dakika sonra, Azure portal bu konaklar için en son bilgileri görmeniz gerekir.



## <a name="fix-assessment-readiness"></a>Değerlendirme hazırlığını çözme

Değerlendirme hazırlık sorunlarını aşağıdaki şekilde giderin:

**Konuda** | **Onar**
--- | ---
Desteklenmeyen önyükleme türü | Azure, EFı önyükleme türü olan VM 'Leri desteklemez. Geçiş çalıştırmadan önce önyükleme türünü BIOS 'a dönüştürmeniz önerilir. <br/><br/>Bu sanal makinelerin geçişini yönetmek için Azure geçişi sunucu geçişini kullanabilirsiniz. Geçiş sırasında VM 'nin önyükleme türünü BIOS 'a dönüştürür.
Koşullu olarak desteklenen Windows işletim sistemi | İşletim sistemi destek son tarihini geçti ve [Azure 'da destek](https://aka.ms/WSosstatement)için özel bir destek SÖZLEŞMESINE (CSA) ihtiyaç duyuyor. Azure 'a geçiş yapmadan önce yükseltmeyi göz önünde bulundurun.
Desteklenmeyen Windows işletim sistemi | Azure yalnızca [Seçili Windows işletim sistemi sürümlerini](https://aka.ms/WSosstatement)destekler. Azure 'a geçiş yapmadan önce makineyi yükseltmeyi düşünün.
Koşullu olarak onaylama Linux işletim sistemi | Azure yalnızca [Seçili Linux işletim sistemleri sürümlerini](../virtual-machines/linux/endorsed-distros.md)onaylar. Azure 'a geçiş yapmadan önce makineyi yükseltmeyi düşünün.
Hazırlanmamış Linux işletim sistemi | Makine Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. Azure 'a geçiş yapmadan önce, [onaylı bir Linux sürümüne](../virtual-machines/linux/endorsed-distros.md) yükseltmeyi düşünün.
Bilinmeyen işletim sistemi | VM 'nin işletim sistemi vCenter Server içinde "Other" olarak belirtilmiştir. Bu davranış, Azure geçişi 'nin VM 'nin Azure 'un hazır olduğunu doğrulamasını engeller. Makineyi geçirmeden önce işletim sisteminin Azure tarafından [desteklendiğinden](https://aka.ms/azureoslist) emin olun.
Desteklenmeyen bit sürümü | 32 bitlik bir işletim sistemi olan VM 'Ler Azure 'da önbaşlatılabilir, ancak Azure 'a geçmeden önce 64 bit 'e yükseltmeniz önerilir.
Microsoft Visual Studio aboneliği gerektirir | Makine, yalnızca bir Visual Studio aboneliği aracılığıyla desteklenen bir Windows istemci işletim sistemi çalıştırıyor.
Gerekli depolama performansı için VM bulunamadı | Makine için gereken depolama performansı (saniye başına giriş/çıkış işlemi [ıOPS] ve üretilen iş), Azure VM desteğini aşıyor. Geçişten önce makinenin depolama gereksinimlerini azaltın.
Gerekli ağ performansı için VM bulunamadı | Makine için gereken ağ performansı (ın/out) Azure VM desteğini aşıyor. Makinenin ağ gereksinimlerini azaltın.
Belirtilen konumda VM bulunamadı | Geçişten önce farklı bir hedef konum kullanın.
Bir veya daha fazla uygun olmayan disk | VM 'ye bağlı bir veya daha fazla disk, Azure gereksinimlerini karşılamıyor. A<br/><br/> Azure geçişi: Sunucu değerlendirmesi Şu anda Ultra SSD diskleri desteklememektedir ve Premium yönetilen diskler (32 TB) için disk sınırlarına göre diskleri değerlendirir.<br/><br/> SANAL makineye bağlı her disk için, diskin boyutunun < 64 TB (Ultra SSD diskler tarafından desteklenir) olduğundan emin olun.<br/><br/> Değilse, Azure 'a geçmeden önce disk boyutunu azaltın veya Azure 'da birden çok disk kullanın ve daha yüksek depolama sınırları almak için [bunları birlikte toplayın](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) . Her disk için gereken performansın (ıOPS ve aktarım hızı) Azure [yönetilen sanal makine diskleri](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)tarafından desteklendiğinden emin olun.
Bir veya daha fazla uygun olmayan ağ bağdaştırıcısı. | Kullanılmayan ağ bağdaştırıcılarını geçişten önce makineden kaldırın.
Disk sayısı sınırı aşıyor | Kullanılmayan diskleri geçişten önce makineden kaldırın.
Disk boyutu sınırı aşıyor | Azure geçişi: Sunucu değerlendirmesi Şu anda Ultra SSD diskleri desteklemez ve değerlendirir disk sınırlarına (32 TB) dayalı diskleri destekler.<br/><br/> Ancak Azure, 64 TB 'a kadar olan diskleri destekler (Ultra SSD diskleri tarafından desteklenir). Geçiş işleminden önce diskleri 64 TB 'den daha az bir değere küçültün veya Azure 'da birden çok disk kullanın ve daha yüksek depolama sınırları almak için [bunları birlikte toplayın](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) .
Belirtilen konumda disk yok | Geçirmeden önce diskin hedef konumunda olduğundan emin olun.
Belirtilen artıklık için disk kullanılamıyor | Disk, değerlendirme ayarlarında tanımlanan artıklık depolama türünü kullanmalıdır (varsayılan olarak LRS).
Bir iç hata nedeniyle disk uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Gerekli çekirdek ve bellek içeren VM bulunamadı | Azure uygun bir VM türü bulamadı. Geçirmeden önce şirket içi makinenin bellek ve çekirdek sayısını azaltın.
Bir iç hata nedeniyle VM uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Bir iç hata nedeniyle bir veya daha fazla disk için uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Bir iç hata nedeniyle bir veya daha fazla ağ bağdaştırıcısı için uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM 'Leri "koşullu olarak hazırlanıyor"

Sunucu değerlendirmesi, sunucu değerlendirmesinde bilinen bir boşluk nedeniyle Linux sanal makinelerini "koşullu olarak hazırlanıyor" olarak işaretler.

- Bu Aralık, BT 'nin şirket içi VM 'lerde yüklü Linux IŞLETIM sisteminin ikincil sürümünü algılamasını önler.
- Örneğin, RHEL 6,10 için şu anda sunucu değerlendirmesi, işletim sistemi sürümü olarak yalnızca RHEL 6 ' ı algılar.
-  Azure, Linux 'un yalnızca belirli sürümlerini sağladığından, Linux VM 'Leri Şu anda sunucu değerlendirmesi için koşullu olarak kullanılabilir olarak işaretlendi.
- [Azure Linux desteğini](https://aka.ms/migrate/selfhost/azureendorseddistros)inceleyerek, ŞIRKET içi VM 'de çalışan Linux Işletim sisteminin Azure 'da olup olmadığını belirleyebilirsiniz.
-  Doğrulanan dağıtımı doğruladıktan sonra bu uyarıyı yoksayabilirsiniz.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure SKU 'Ları şirket içi boyutlandırmayı aşıyor

Azure geçişi sunucu değerlendirmesi, Azure VM SKU 'Larını, değerlendirme türüne göre geçerli şirket içi ayırmadan daha fazla çekirdek ve bellek ile önerebilir:


- VM SKU 'SU önerisi, değerlendirme özelliklerine bağlıdır.
- Bu, sunucu değerlendirmesi: *performans tabanlı*veya *Şirket içi olarak*gerçekleştirdiğiniz değerlendirme türünden etkilenir.
- Performans tabanlı değerlendirmelere yönelik sunucu değerlendirmesi, şirket içi sanal makinelerinize yönelik doğru hedef VM SKU 'sunu belirlemede şirket içi VM 'lerin (CPU, bellek, disk ve ağ kullanımı) kullanım verilerini dikkate alır. Ayrıca, etkin kullanımı belirlerken bir rahatlık faktörü ekler.
- Şirket içi boyutlandırma için performans verileri göz önünde bulundurulmaz ve hedef SKU, şirket içi ayırmaya göre önerilir.

Bunun önerileri nasıl etkileyebileceğini göstermek için bir örnek alalım:

Dört çekirdekli ve sekiz GB bellek içeren,% 50 CPU kullanımı ve% 50 bellek kullanımı ve belirli bir rahatlık 1,3 faktörü içeren bir şirket içi VM sunuyoruz.

-  Değerlendirme **Şirket içinde**ise, 4 çekirdek ve 8 GB bellek Içeren BIR Azure VM SKU 'su önerilir.
- Değerlendirme performansı temel alıyorsa, etkin CPU ve bellek kullanımına 50 (4 çekirdek * 1,3 = 2,6 çekirdekler 50 ve 8 GB bellek * 1,3 = 5,3-GB bellek) göre, dört çekirdekli sanal makine SKU 'SU (en yakın desteklenen çekirdek sayısı) ve sekiz GB bellek (en yakın) bellek boyutu) önerilir.
- Değerlendirme boyutlandırma hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md#sizing) .

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure disk SKU 'Ları Şirket içinden daha büyük

Azure geçişi sunucu değerlendirmesi, değerlendirme türüne göre daha büyük bir disk önerebilir.
- Sunucu değerlendirmesinde disk boyutlandırma iki değerlendirme özelliklerine bağlıdır: boyutlandırma ölçütleri ve depolama türü.
- Boyutlandırma ölçütü **performans tabanlıdır**ve depolama türü **Otomatik**olarak ayarlandıysa, diskin IOPS ve aktarım hızı değerleri, hedef disk türü (Standart HDD, standart SSD veya Premium) tanımlanırken kabul edilir. Daha sonra disk türünden bir disk SKU 'SU önerilir ve öneri, şirket içi diskin boyut gereksinimlerini dikkate alır.
- Boyutlandırma ölçütü **performans tabanlıdır**ve depolama türü **Premium**ise, şirket içi diskin IOPS, verimlilik ve Boyut gereksinimlerine bağlı olarak Azure 'DA BIR Premium disk SKU 'su önerilir. Aynı mantık, boyutlandırma ölçütleri **Şirket içinde** olduğunda ve depolama türü **Standart HDD**, **Standart SSD**veya **Premium**olduğunda disk boyutlandırmayı gerçekleştirmek için kullanılır.

Örnek olarak, 32 GB bellek içeren bir şirket içi diskiniz varsa, ancak diskin toplanmış okuma ve yazma ıOPS değeri 800 ıOPS ise, sunucu değerlendirmesi bir Premium disk önerir (daha yüksek ıOPS gereksinimleri nedeniyle) ve ardından r 'yi destekleyebilen bir disk SKU 'SU önerir rekli ıOPS ve boyut. Bu örnekteki en yakın eşleşme P15 (256 GB, 1100 IOPS) olabilir. Şirket içi diskin gerektirdiği boyut 32 GB olsa da, şirket içi diskin yüksek ıOPS gereksinimi nedeniyle sunucu değerlendirmesi daha büyük bir disk önerir.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Çözüm: kullanılan çekirdek veya belleğin yüzdesi eksik

Sunucu değerlendirmesi, "PercentageOfCoresUtilizedMissing" veya "PercentageOfMemoryUtilizedMissing" raporlarını Azure geçiş gereci ilgili şirket içi VM 'Ler için performans verilerini toplayamayacak şekilde bildirir.

- Bu durum, değerlendirme süresi boyunca VM 'Ler kapatılmışsa ortaya çıkabilir. Gereç, devre dışı bırakıldığında bir VM için performans verilerini toplayamıyor.
- Yalnızca bellek sayaçları eksikse ve Hyper-V VM 'lerini değerlendirmeye çalışıyorsanız, bu VM 'lerde etkin bir dinamik bellek olup olmadığını kontrol edin. Yalnızca Hyper-V VM 'Leri için bir Azure geçişi gerecinin, dinamik belleği etkin olmayan VM 'Ler için bellek kullanım verilerini toplayamediği bilinen bir sorun vardır.
- Performans sayaçlarından herhangi biri eksikse, Azure geçişi sunucu değerlendirmesi ayrılmış çekirdeğe ve belleğe geri döner ve buna karşılık gelen VM boyutunu önerir.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>VM işletim sistemi lisans maliyeti maliyet değerlendirmesi 'ne dahildir mi?

Azure geçişi sunucu değerlendirmesi Şu anda yalnızca Windows makineler için işletim sistemi lisans maliyetini dikkate alır. Linux makineleri için lisans maliyetleri Şu anda dikkate alınmamaktadır.

## <a name="performance-history-and-percentile-use"></a>Performans geçmişi ve yüzdebirlik kullanımı

Bu özellikler yalnızca Azure geçişi sunucu değerlendirmesinde performans tabanlı boyutlandırma için geçerlidir.

Sunucu Değerlendirmesi şirket içi makinelerin performans verilerini sürekli toplar ve bunları Azure’da VM SKU'su ile disk SKU'su önermek için kullanır. Bu performans verileri sunucu değerlendirmesi tarafından aşağıdaki şekilde toplanır:
- Azure geçişi gereci, VMware VM 'Leri için her 20 saniyede bir gerçek zamanlı kullanım verisi toplamak üzere şirket içi ortamı sürekli olarak profiller ve Hyper-V VM 'Leri için 30 saniye sürer.
- Gereç, her 10 dakikada tek bir veri noktası oluşturmak için 20 veya 30 saniyelik örnek toplar. Tek veri noktasını oluşturmak için, Gereç tüm 20 saniyelik ve 30 saniyelik örneklerden tepe değeri seçer ve ardından bunu Azure 'a gönderir.
- Sunucu Değerlendirmesi'nde bir değerlendirme oluşturduğunuzda, performans süresi ve performans geçmişi yüzdebirlik değerine bağlı olarak temsili kullanım değeri belirlenir. Örneğin, performans geçmişi bir hafta ise ve yüzdebirlik kullanımı 95 ise, Azure geçişi son bir hafta için 10 dakikalık tüm örnek noktalarını artan düzende sıralar ve ardından temsili değeri olarak 95. yüzdebirlik değerini seçer.
- 95. yüzdebirlik değeri, 99. yüzdebirlik değerini seçerseniz dahil edilen tüm aykırı değerleri yok saydığınızdan emin olmanızı sağlar.
- Dönemin en yoğun kullanımını seçmek ve tüm aykırı değerleri kaçırmak istemiyorsanız, yüzdebirlik kullanımı için 99. yüzdebirlik ' ü seçmeniz gerekir.



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Azure Kamu için bağımlılık görselleştirmesini bulamıyorum

Azure geçişi, bağımlılık görselleştirme işlevselliği için Hizmet Eşlemesi bağımlıdır. Hizmet Eşlemesi Şu anda Azure Kamu 'da kullanılamadığından, bu işlev Azure Kamu 'da kullanılamaz.

## <a name="dependencies-dont-show-after-installing-agents"></a>Aracılar yüklendikten sonra bağımlılıklar gösterilmez


Şirket içi VM 'Lere bağımlılık görselleştirme aracılarını yükledikten sonra, Azure geçişi genellikle portalda bağımlılıkları göstermek için 15-30 dakika sürer. 30 dakikadan uzun süre beklemişseniz, Microsoft Monitoring Agent (MMA) Log Analytics çalışma alanına bağlanabildiğinizden emin olun.

Windows VM 'Leri için:
1. Denetim Masası 'nda MMA 'yı başlatın.
2. **Microsoft Monitoring Agent özellikler** > **Azure Log Analytics (OMS)** Içinde, çalışma alanı **durumunun** yeşil olduğundan emin olun.
3. Durum yeşil değilse, çalışma alanını kaldırmayı ve onu yeniden eklemeyi deneyin.

      ![MMA özellikleri iletişim kutusu](./media/troubleshooting-general/mma-status.png)

Linux sanal makineleri için, MMA ve bağımlılık aracısının yükleme komutlarının başarılı olduğundan emin olun.

## <a name="supported-mma-os"></a>Desteklenen MMA OS

 Desteklenen [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)ve [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) işletim sistemlerini gözden geçirin.

## <a name="supported-dependency-agent-os"></a>Desteklenen bağımlılık Aracısı işletim sistemi

Desteklenen [Windows ve Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) işletim sistemlerini gözden geçirin.

## <a name="dependencies-for-more-than-an-hour"></a>Bir saatten uzun bir süre bağımlılıkları

Azure geçişi, son ayın belirli bir tarihine geri dönmenize izin verse de, bağımlılıkları görselleştirebileceğiniz en uzun süre bir saattir.

Örneğin, dün bağımlılıklarını görüntülemek için bağımlılık eşlemesindeki süre süresi işlevini kullanabilirsiniz, ancak bunları yalnızca bir saatlik dönem için görüntüleyebilirsiniz.

Ancak, Azure Izleyici günlüklerini daha uzun bir süre boyunca [bağımlılık verilerini sorgulamak](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) için kullanabilirsiniz.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>10 ' dan fazla VM 'ye sahip gruplar için bağımlılıkları görselleştirilemiyor

Azure geçişi sunucu değerlendirmesi ' nde, en fazla 10 VM içeren [gruplar için bağımlılıkları görselleştirebilirsiniz](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . Daha büyük gruplar için, bağımlılıkları görselleştirmek üzere VM 'Leri daha küçük gruplara bölmeniz önerilir.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Makineler "aracıları görüntüle" olarak "görünüm bağımlılıklarını" gösterir

Bağımlılık görselleştirmesi etkinleştirilmiş makineleri Azure 'a geçirdikten sonra makineler, aşağıdaki davranış nedeniyle "bağımlılıkları görüntüle" yerine "aracıyı yüklemek" eylemini gösterebilir:


- Azure 'a geçiş yaptıktan sonra şirket içi makineler kapalıdır ve eşdeğer VM 'Ler Azure 'da kapalıdır. Bu makineler farklı bir MAC adresi edintir.
- Makineler, şirket içi IP adresini elde etmeksizin, aynı zamanda farklı bir IP adresine sahip olabilir.
- MAC ve IP adreslerinin her ikisi de Şirket içinden farklıysa Azure geçişi, şirket içi makineleri Hizmet Eşlemesi bağımlılık verileriyle ilişkilendirmez. Bu durumda, bağımlılıkları görüntülemek yerine aracıyı yüklemek için seçeneği görüntülenir.
- Azure 'a bir test geçişten sonra şirket içi makineler beklendiği gibi açık kalır. Azure 'da bulunan eşdeğer makineler, farklı bir MAC adresi elde edebilir ve farklı IP adresleri alabilir. Bu makinelerden giden Azure Izleyici günlük trafiğini engeletmediğiniz takdirde Azure geçişi, şirket içi makineleri Hizmet Eşlemesi bağımlılık verileriyle ilişkilendirmez ve bu nedenle, bağımlılıkları görüntülemek yerine aracıları yüklemek için seçeneği gösterir.


## <a name="collect-network-traffic-logs-in-portal"></a>Portalda ağ trafiği günlüklerini toplama

Günlükleri aşağıdaki şekilde toplayın:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
2. Geliştirici Araçları başlamak için F12 tuşuna basın. Gerekirse, **Gezinti ayarında girişleri temizle** ' yi temizleyin.
3. **Ağ** sekmesini seçin ve ağ trafiğini yakalamaya başlayın:
   - Chrome 'da **günlüğü koru**' yı seçin. Kayıt otomatik olarak başlamalıdır. Kırmızı bir daire trafiğin yakalandığını gösterir. Kırmızı daire görünmezse başlamak için siyah daireyi seçin.
   - Microsoft Edge ve Internet Explorer 'da kayıt otomatik olarak başlamalıdır. Aksi takdirde, yeşil oynatma düğmesini seçin.
4. Hatayı yeniden oluşturmaya çalışın.
5. Kayıt sırasında hata ile karşılaşduktan sonra kaydı durdurun ve kayıtlı etkinliğin bir kopyasını kaydedin:
   - Chrome 'da, sağ tıklayın ve **IÇERIKLE har olarak kaydet**' i seçin. Bu eylem, günlükleri bir. har dosyası olarak sıkıştırır ve dışarı aktarır.
   - Microsoft Edge veya Internet Explorer 'da **yakalanan trafiği dışarı aktar** seçeneğini belirleyin. Bu eylem günlüğü sıkıştırır ve dışarı aktarır.
6. Herhangi bir uyarı veya hata olup olmadığını denetlemek için **konsol** sekmesini seçin. Konsol günlüğünü kaydetmek için:
   - Chrome 'da konsol günlüğünde herhangi bir yere sağ tıklayın. Günlüğe **Kaydet**, dışarı aktarmak için ve günlüğü posta ' yı seçin.
   - Microsoft Edge veya Internet Explorer 'da hatalara sağ tıklayıp **Tümünü Kopyala**' yı seçin.
7. Geliştirici Araçları kapatın.
