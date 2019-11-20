---
title: StorSimple FileShare DR Azure Site Recovery ile otomatikleştirin | Microsoft Docs
description: Microsoft Azure StorSimple depolamada barındırılan dosya paylaşımları için bir olağanüstü durum kurtarma çözümü oluşturmaya yönelik adımları ve en iyi yöntemleri açıklar.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875387"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>StorSimple üzerinde barındırılan dosya paylaşımları için Azure Site Recovery kullanan otomatik olağanüstü durum kurtarma çözümü

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
Microsoft Azure StorSimple, dosya paylaşımları ile yaygın olarak ilişkilendirilen yapılandırılmamış verilerin karmaşıklıklarını ele alan karma bulut depolama çözümüdür. StorSimple, şirket içi çözümün bir uzantısı olarak bulut depolamayı kullanır ve verileri şirket içi depolama ve bulut depolama alanı genelinde otomatik olarak katmanlar. Yerel ve bulut anlık görüntüleriyle tümleşik veri koruması, bir, bir depolama altyapısı gereksinimini ortadan kaldırır.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) , sanal makinelerin çoğaltma, yük devretme ve kurtarmasını düzenleyerek olağanüstü durum kurtarma (Dr) olanakları sağlayan Azure tabanlı bir hizmettir. Azure Site Recovery, sanal makineleri ve uygulamaları özel/genel veya barındırılan bulutlara sürekli olarak çoğaltmak, korumak ve sorunsuz bir şekilde devretmek için çeşitli Çoğaltma teknolojilerini destekler.

Azure Site Recovery, sanal makine çoğaltma ve StorSimple bulut anlık görüntü yeteneklerini kullanarak, dosya sunucusu ortamının tamamını koruyabilirsiniz. Bir kesinti olması durumunda, Azure 'da dosya paylaşımlarınızı yalnızca birkaç dakika içinde çevrimiçi hale getirmek için tek bir tıklama kullanabilirsiniz.

Bu belgede, StorSimple depolamada barındırılan dosya paylaşımlarınız için bir olağanüstü durum kurtarma çözümü oluşturma ve tek tıklamayla kurtarma planı kullanarak planlanmış, planlanmamış ve test yük devretme işlemleri gerçekleştirme hakkında ayrıntılı bilgiler verilmektedir. Temelde, olağanüstü durum senaryolarında StorSimple yük devretme işlemleri için Azure Site Recovery kasasındaki kurtarma planını nasıl değiştirebileceğiniz gösterilmektedir. Ayrıca, desteklenen yapılandırma ve önkoşulları açıklar. Bu belgede Azure Site Recovery ve StorSimple mimarilerinin temelleri hakkında bilgi sahibi olduğunuz varsayılır.

## <a name="supported-azure-site-recovery-deployment-options"></a>Desteklenen Azure Site Recovery dağıtım seçenekleri
Müşteriler, Hyper-V veya VMware üzerinde çalışan fiziksel sunucu veya sanal makine (VM) olarak dosya sunucuları dağıtabilir ve ardından StorSimple depolama alanından alınan birimlerden dosya paylaşımları oluşturabilir. Azure Site Recovery, hem fiziksel hem de sanal dağıtımları ikincil bir siteye veya Azure 'a koruyabilir. Bu belge, Hyper-V ' d a ve StorSimple depolamada dosya paylaşımları ile barındırılan bir dosya sunucusu VM 'si için kurtarma sitesi olarak Azure ile bir DR çözümünün ayrıntılarını içermektedir. Dosya sunucusu VM 'sinin bir VMware VM 'de olduğu diğer senaryolar veya fiziksel makine benzer şekilde uygulanabilir.

## <a name="prerequisites"></a>Önkoşullar
StorSimple depolamada barındırılan dosya paylaşımları için Azure Site Recovery kullanan tek tıklamayla oluşan bir olağanüstü durum kurtarma çözümünün uygulanması aşağıdaki önkoşullara sahiptir:

   - Hyper-V veya VMware üzerinde barındırılan şirket içi Windows Server 2012 R2 dosya sunucusu VM 'si veya fiziksel makine
   - Azure StorSimple Manager 'a kayıtlı, şirket içi StorSimple depolama cihazı
   - StorSimple Cloud Appliance Azure StorSimple Manager 'da oluşturuldu. Gereç bir kapatma durumunda tutulabilir.
   - StorSimple depolama cihazında yapılandırılan birimlerde barındırılan dosya paylaşımları
   - Microsoft Azure abonelikte oluşturulan [Azure Site Recovery Services Kasası](../site-recovery/site-recovery-vmm-to-vmm.md)

Ayrıca, Azure kurtarma siteniz ise, Azure VM 'Leri ve Azure Site Recovery hizmetleriyle uyumlu olduklarından emin olmak için VM 'lerde [Azure sanal makine hazırlığı değerlendirmesi aracı](https://azure.microsoft.com/downloads/vm-readiness-assessment/) 'nı çalıştırın.

Gecikme sorunlarından kaçınmak için (daha yüksek maliyetlere neden olabilecek) StorSimple Cloud Appliance, Otomasyon hesabınızı ve depolama hesabınızı aynı bölgede oluşturduğunuzdan emin olun.

## <a name="enable-dr-for-storsimple-file-shares"></a>StorSimple dosya paylaşımları için DR 'yi etkinleştirme
Tam çoğaltma ve kurtarmayı etkinleştirmek için şirket içi ortamın her bileşeninin korunması gerekir. Bu bölümde nasıl yapılacağı açıklanmaktadır:
    
   - Active Directory ve DNS çoğaltmasını ayarlama (isteğe bağlı)
   - Dosya sunucusu VM korumasını etkinleştirmek için Azure Site Recovery kullanma
   - StorSimple birimlerinin korunmasını etkinleştir
   - Ağı yapılandırma

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Active Directory ve DNS çoğaltmasını ayarlama (isteğe bağlı)
Active Directory ve DNS çalıştıran makineleri DR sitesinde kullanılabilir olacak şekilde korumak istiyorsanız, bunları açıkça korumanız gerekir (böylece, kimlik doğrulamasıyla yük devretmeden sonra dosya sunucularına erişilebilir). Müşterinin Şirket içi ortamının karmaşıklığına göre önerilen iki seçenek vardır.

#### <a name="option-1"></a>Seçenek 1
Müşterinin, tüm şirket içi site için tek bir etki alanı denetleyicisi olan az sayıda uygulaması varsa ve tüm site üzerinden yük devrettikten sonra, etki alanı denetleyicisi makinesini ikincil olarak çoğaltmak için Azure Site Recovery çoğaltmanın kullanılması önerilir Site (Bu, hem siteden siteye, hem de siteden Azure için geçerlidir).

#### <a name="option-2"></a>Seçenek 2
Müşterinin çok sayıda uygulaması varsa, bir Active Directory ormanı çalıştırıyorsa ve aynı anda birkaç uygulama üzerinde başarısız olursa, DR sitesinde (ikincil site veya Azure 'da) ek bir etki alanı denetleyicisi ayarlamayı öneririz.

DR sitesinde kullanılabilir bir etki alanı denetleyicisi yaparken yönergeler için [Azure Site Recovery kullanarak Active Directory ve DNS Için OTOMATIK Dr çözümüne](../site-recovery/site-recovery-active-directory.md) bakın. Bu belgenin geri kalanında, DR sitesinde bir etki alanı denetleyicisinin bulunduğunu varsayalım.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Dosya sunucusu VM korumasını etkinleştirmek için Azure Site Recovery kullanma
Bu adım, şirket içi dosya sunucusu ortamını hazırlamanızı, bir Azure Site Recovery Kasası oluşturup hazırlamanızı ve VM 'nin dosya korumasını etkinleştirmenizi gerektirir.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Şirket içi dosya sunucusu ortamını hazırlamak için
1. **Kullanıcı hesabı denetimini** **hiçbir şekilde uyarma**olarak ayarlayın. Azure Site Recovery tarafından yük devretmeden sonra Iscsı hedeflerini bağlamak için Azure Otomasyonu betikleri kullanabilmeniz için bu gereklidir.
   
   1. Windows tuşu + Q tuşlarına basın ve **UAC**'yi arayın.  
   1. **Kullanıcı hesabı denetim ayarlarını değiştir**' i seçin.  
   1. Çubuğu, **hiçbir şekilde uyarma**öğesine doğru sürükleyin.  
   1. **Tamam** ' a tıklayın ve istendiğinde **Evet** ' i seçin.  
   
      ![Kullanıcı hesabı denetim ayarları](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. VM aracısını dosya sunucusu VM 'lerinin her birine yükler. Bu, yük devredilen VM 'lerde Azure Otomasyonu betikleri çalıştırabilmeniz için gereklidir.
   
   1. [Aracısını](https://aka.ms/vmagentwin) öğesine `C:\\Users\\<username>\\Downloads`indirin.
   1. Windows PowerShell 'i yönetici modunda açın (yönetici olarak çalıştır) ve ardından indirme konumuna gitmek için aşağıdaki komutu girin:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Sürüme bağlı olarak dosya adı değişebilir.
      
1.           **İleri**'ye tıklayın.
1. **Sözleşme koşullarını** kabul edin ve ardından **İleri**' ye tıklayın.
1.           **Son**'a tıklayın.
1. StorSimple Storage 'dan alınan birimleri kullanarak dosya paylaşımları oluşturun. Daha fazla bilgi için bkz. [birimleri yönetmek Için StorSimple Yöneticisi hizmetini kullanma](storsimple-manage-volumes.md).
   
   1. Şirket içi sanal makinelerinize Windows tuşu + Q tuşlarına basın ve **iSCSI**araması yapın.
   1. **İSCSI başlatıcısı**' nı seçin.
   1. **Yapılandırma** sekmesini seçin ve Başlatıcı adını kopyalayın.
   1. [Azure Portal](https://portal.azure.com/)’da oturum açın.
   1. **StorSimple** sekmesini seçin ve ardından fiziksel cihazı Içeren StorSimple Yöneticisi hizmetini seçin.
   1. Birim kapsayıcısı oluşturun ve sonra birimleri oluşturun. (Bu birimler dosya sunucusu VM 'lerinde dosya paylaşımlardır). Birimleri oluştururken Başlatıcı adını kopyalayın ve Access Control kayıtları için uygun bir ad verin.
   1. **Yapılandır** sekmesini seçin ve cihazın IP adresini göz önünde edin.
   1. Şirket içi sanal makinelerunuzda **iSCSI başlatıcısına** tekrar gidin ve Hızlı Bağlan bölümüne IP 'yi girin. **Hızlı Bağlan** ' a tıklayın (cihaz artık bağlı olmalıdır).
   1. Azure portal açın ve **birimler ve aygıtlar** sekmesini seçin. **Otomatik Yapılandır**öğesine tıklayın. Oluşturduğunuz birimin görünmesi gerekir.
   1. Portalda, **cihazlar** sekmesini seçin ve ardından **Yeni bir sanal cihaz oluştur** ' u seçin. (Bu sanal cihaz, bir yük devretme gerçekleşirse kullanılır). Bu yeni sanal cihaz, ek maliyetlerin önüne geçmek için çevrimdışı durumda tutulabilir. Sanal cihazı çevrimdışına almak için portaldaki **sanal makineler** bölümüne gidin ve kapatın.
   1. Şirket içi VM 'lere geri dönün ve disk yönetimi 'ni açın (Windows tuşu + X tuşlarına basın ve **disk yönetimi**'ni seçin).
   1. Bazı ek diskler (oluşturduğunuz birim sayısına bağlı olarak) fark edeceksiniz. İlk öğesine sağ tıklayın, **Diski Başlat**' ı seçin ve **Tamam**' ı seçin. **Ayrılmamış** bölümüne sağ tıklayın, **Yeni basit birim**' i seçin, bir sürücü harfi atayın ve Sihirbazı sona erdirin.
   1. Tüm diskler için l adımını tekrarlayın. Artık **Bu bilgisayardaki** tüm diskleri Windows Gezgini 'nde görebilirsiniz.
   1. Bu birimlerde dosya paylaşımları oluşturmak için dosya ve Depolama Hizmetleri rolünü kullanın.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Azure Site Recovery kasasını oluşturmak ve hazırlamak için
Dosya sunucusu VM 'sini korumadan önce Azure Site Recovery kullanmaya başlamak için [Azure Site Recovery belgelerine](../site-recovery/site-recovery-hyper-v-site-to-azure.md) bakın.

#### <a name="to-enable-protection"></a>Korumayı etkinleştirmek için
1. Azure Site Recovery üzerinden korumak istediğiniz şirket içi VM 'lerden Iscsı hedefinin bağlantısını kesin:
   
   1. Windows tuşu + Q tuşlarına basın ve **iSCSI**araması yapın.
   1. **İSCSI başlatıcısı ayarla**' yı seçin.
   1. Daha önce bağladığınız StorSimple cihazını kesin. Alternatif olarak, korumayı etkinleştirirken dosya sunucusunu birkaç dakika boyunca kapatabilirsiniz.
      
   > [!NOTE]
   > Bu, dosya paylaşımlarının geçici olarak kullanılamamasına neden olur.
   
1. Azure Site Recovery portalından dosya sunucusu VM 'sinin [sanal makine korumasını etkinleştirin](../site-recovery/site-recovery-hyper-v-site-to-azure.md) .
1. İlk eşitleme başladığında hedefi yeniden yeniden bağlayabilirsiniz. Iscsı başlatıcısına gidin, StorSimple cihazını seçin ve **Bağlan**' a tıklayın.
1. Eşitleme tamamlandığında ve VM 'nin durumu korunduğunda, VM 'yi seçin , **Yapılandır** sekmesini seçin ve VM 'nin ağını uygun şekilde güncelleştirin (Bu, yük devredilen VM (ler) ın bir parçası olacağı ağ olur). Ağ görünmüyorsa, eşitlemenin hala devam ettiği anlamına gelir.

### <a name="enable-protection-of-storsimple-volumes"></a>StorSimple birimlerinin korunmasını etkinleştir
StorSimple birimlerine yönelik **Bu birim için varsayılan yedeklemeyi etkinleştir** seçeneğini seçmediyseniz, StorSimple Yöneticisi hizmetindeki **yedekleme ilkeleri** ' ne gidin ve tüm birimler için uygun bir yedekleme ilkesi oluşturun. Uygulama için görmek istediğiniz kurtarma noktası hedefine (RPO) yedekleme sıklığını ayarlamanızı öneririz.

### <a name="configure-the-network"></a>Ağı yapılandırma
Dosya sunucusu VM 'si için, VM ağlarının yük devretmeden sonra doğru DR ağına bağlanması için Azure Site Recovery ağ ayarlarını yapılandırın.

Aşağıdaki çizimde gösterildiği gibi, ağ ayarlarını yapılandırmak için **çoğaltılan öğeler** sekmesinden VM 'yi seçebilirsiniz.

![İşlem ve ağ](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma
Dosya paylaşımlarının yük devretme işlemini otomatik hale getirmek için ASR 'de bir kurtarma planı oluşturabilirsiniz. Bir kesinti oluşursa, yalnızca tek bir tıklama ile dosya paylaşımlarını birkaç dakika içinde taşıyabilirsiniz. Bu Otomasyonu etkinleştirmek için bir Azure Otomasyonu hesabına ihtiyacınız olacaktır.

#### <a name="to-create-an-automation-account"></a>Otomasyon hesabı oluşturmak için
1. Azure Portal &gt; **Otomasyonu** bölümüne gidin.
1. **+ Ekle** düğmesine tıklayın, dikey pencerenin altında açılır.
   
   ![Automation Hesabı ekleme](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Ad-yeni bir Otomasyon hesabı girin
   - Abonelik-abonelik seçin
   - Kaynak grubu-yeni oluştur/mevcut kaynak grubunu Seç
   - Konum-konum seçin, StorSimple Cloud Appliance ve depolama hesaplarının oluşturulduğu coğrafi/bölgede saklayın.
   - Azure farklı çalıştır hesabı oluştur- **Evet** seçeneğini belirleyin.
   
1. Otomasyon hesabına gidin ve gerekli tüm runbook 'ları Otomasyon hesabına aktarmak için **runbook 'lar** &gt; **tarayıcı galerisine** tıklayın.
1. Galerideki **olağanüstü durum kurtarma** etiketini bularak aşağıdaki runbook 'ları ekleyin:
   
   - Yük devretme sınamasını (TFO) tamamladıktan sonra StorSimple birimlerini Temizleme
   - Yük devretme StorSimple birim kapsayıcıları
   - Yük devretmeden sonra StorSimple cihazında birimleri bağlama
   - Azure VM 'de özel Betik uzantısı 'nı kaldırma
   - StorSimple Sanal Gereç Başlat
   
      ![Galeriye gözatamıyorum](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Otomasyon hesabındaki runbook 'u seçip **Yayımla** ' **ya tıklayın ve** &gt; doğrulama iletisine **Evet** ' e tıklayarak tüm betikleri yayımlayın. Bu adımdan sonra, **runbook 'lar** sekmesi şu şekilde görünür:
   
   ![Runbook'lar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Otomasyon hesabında, **değişkenler** &gt; **bir değişken Ekle** ' ye tıklayın ve aşağıdaki değişkenleri ekleyin. Bu varlıkları şifrelemeyi seçebilirsiniz. Bu değişkenler, kurtarma planına özgüdür. Bir sonraki adımda oluşturacağınız kurtarma planınız, Name TestPlan olduğunda, değişkenleriniz TestPlan-Storsıadı, TestPlan-Azuyeniden gönderme Scriptionname vb. olmalıdır.

   - **BaseUrl**: Azure bulutunun URL 'si Kaynak Yöneticisi. **Get-AzEnvironment kullanarak Get | Select-Object name, ResourceManagerUrl** cmdlet 'i.
   - _Recoveryplanname_ **-Resourcegroupname**: StorSimple kaynağına sahip Kaynak Yöneticisi grubu.
   - _Recoveryplanname_ **-ManagerName**: StorSimple cihazına sahip StorSimple kaynağı.
   - _Recoveryplanname_ **-Aygıtadı**: Yük devredilecek olan StorSimple cihazı.
   - _Recoveryplanname_ **-Deviceıpaddress**: Cihazın IP adresi (Bu, StorSimple aygıt yöneticisi bölüm &gt; **ayarları** &gt; **ağ** &gt; **DNS ayarları** grubu altında **aygıtlar** sekmesinde bulunabilir.
   - _Recoveryplanname_ **-Volumecontainers**: Yük devredilme yapması gereken cihazda bulunan, virgülle ayrılmış birim kapsayıcılarının bir dizesi. Örneğin: volcon1, volcon2, volcon3.
   - _Recoveryplanname_ **-Targetaygıtadı**: Kapsayıcının yük devretme StorSimple Cloud Appliance.
   - _Recoveryplanname_ **-Targetdeviceıpaddress**: Hedef cihazın IP adresi (Bu, **sanal makine** bölüm &gt; **ayarları** grup &gt; **ağı** sekmesinde bulunabilir).
   - _Recoveryplanname_ **-StorageAccountName**: Betiğin (yük devredilen VM üzerinde çalışması gereken) depolanacağı depolama hesabı adı. Bu, betiği geçici olarak depolamak için bir boşluk olan herhangi bir depolama hesabı olabilir.
   - _Recoveryplanname_ **-Storageaccountkey**: Yukarıdaki depolama hesabı için erişim anahtarı.
   - _Recoveryplanname_ **-Vmguid 'ler**: Bir VM 'yi koruduğunuzda Azure Site Recovery her VM 'ye, yük devredilen VM 'nin ayrıntılarını veren benzersiz bir KIMLIK atar. VMGUID 'yi almak için **Kurtarma Hizmetleri** sekmesini seçin ve **korunan öğe** &gt; **koruma grupları** &gt; **makine** &gt; **özellikleri**' ne tıklayın. Birden çok sanal makine varsa, GUID 'Leri virgülle ayrılmış bir dize olarak ekleyin.

     Örneğin, kurtarma planının adı fileServerpredayRP ise, tüm varlıkları ekledikten sonra değişkenleriniz,  **Bağlantılar** ve **Sertifikalar** sekmesinin aşağıdaki gibi görünmesi gerekir.

      ![Varlıklar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Otomasyon hesabınızda StorSimple 8000 serisi runbook modülünü karşıya yükleyin. Modül eklemek için aşağıdaki adımları kullanın:
   
   1. PowerShell 'i açın, klasörü klasöre değiştirmek & yeni bir klasör oluşturun.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. NuGet CLı 'yi adım içindeki aynı klasöre indirin.
      NuGet [İndirmeleri](https://www.nuget.org/downloads)üzerinde çeşitli NuGet. exe sürümleri mevcuttur. Her bir indirme bağlantı noktası doğrudan bir. exe dosyasına, bu nedenle sağ tıklayıp dosyayı tarayıcıdan çalıştırmak yerine bilgisayarınıza kaydettiğinizden emin olun.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Bağımlı SDK 'Yı indirme
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. StorSimple 8000 serisi cihaz yönetimi için bir Azure Otomasyonu runbook modülü oluşturun. Otomasyon modülü ZIP dosyası oluşturmak için aşağıdaki komutları kullanın.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Yukarıdaki adımda oluşturulan Azure Automation modül ZIP dosyasını (Microsoft. Azure. Management. StorSimple8000Series. zip) içeri aktarın. Bu işlem Otomasyon hesabı seçilerek, PAYLAŞıLAN kaynaklar altındaki **modüller** ' e ve ardından **Modül Ekle**' ye tıklayarak yapılabilir.
   
   StorSimple 8000 serisi modülünü içeri aktardıktan sonra, **modüller** sekmesi aşağıdaki gibi görünmelidir:
   
      ![Modüller](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. **Kurtarma Hizmetleri** bölümüne gidin ve daha önce oluşturduğunuz Azure Site Recovery kasasını seçin.
1. **Yönet** grubundan **kurtarma planları (Site Recovery)** seçeneğini belirleyin ve yeni bir kurtarma planı oluşturun:
   
   - **+ Kurtar plan** düğmesine tıklayın, aşağıdaki dikey pencereyi açar.
      
      ![Kurtarma planı oluşturma](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Bir kurtarma planı adı girin, kaynak, hedef & dağıtım modeli değerlerini seçin.
   
   - Kurtarma planına dahil etmek istediğiniz koruma grubundan VM 'Leri seçin ve **Tamam** düğmesine tıklayın.
   
   - Daha önce oluşturduğunuz kurtarma planını seçin, kurtarma planı özelleştirme görünümünü açmak için **Özelleştir** düğmesine tıklayın.
   
   - **Tüm gruplar kapalı** öğesine sağ tıklayın ve **ön eylem Ekle**' ye tıklayın.
   
   - Eylem Ekle dikey penceresini açar, bir ad girin, çalıştırılacağı konum seçeneğinde **birincil taraf** seçeneğini belirleyin, Otomasyon hesabı ' nı seçin (runbook 'ları eklediğiniz) ve ardından **Yük devretme-StorSimple-Volume-containers** runbook 'unu seçin.
   
   - **Grup 1 ' e sağ tıklayın: Başlat** ' ı seçin ve **korumalı öğeler Ekle** ' ye tıklayın, ardından kurtarma planında korunacak VM 'leri seçin ve **Tamam** düğmesine tıklayın. Zaten seçili VM 'Ler varsa isteğe bağlı.
   
   - **Grup 1 ' e sağ tıklayın: Başlat** ' a tıklayın ve ardından **eylem sonrası** seçeneğine tıkladıktan sonra aşağıdaki komut dosyalarını ekleyin:  
      
      - Start-StorSimple-Sanal Gereç runbook 'u  
      - Yük devretme-StorSimple-Volume-containers runbook 'u  
      - Bağlama-birimler-sonrasında yük devretme runbook 'u  
      - Uninstall-özel Betik uzantısı runbook 'u  
        
   - Aynı **Grup 1 ' deki yukarıdaki 4 komut dosyalarından sonra el ile eylem ekleyin: Adımlar** sonrası bölüm. Bu eylem, her şeyin doğru şekilde çalıştığını doğrulayabilmeniz gereken bir noktasıdır. Bu eylemin yalnızca test yük devretmesinin bir parçası olarak eklenmesi gerekir (Bu nedenle yalnızca **Test yük devretmesi** onay kutusunu seçin).
    
   - El ile gerçekleştirilen eylem sonrasında, diğer runbook 'lar için kullandığınız yordamın aynısını kullanarak **Temizleme** betiğini ekleyin. Kurtarma planını **kaydedin** .
    
   > [!NOTE]
   > Yük devretme testi çalıştırırken, el ile gerçekleştirilen eylem adımında her şeyi doğrulamanız gerekir, çünkü hedef cihaza kopyalanmış olan StorSimple birimleri el ile gerçekleştirilen eylem tamamlandıktan sonra Temizleme işleminin bir parçası olarak silinir.
       
      ![Kurtarma planı](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Yük devretme testi gerçekleştirme
Sınama yük devretmesi sırasında Active Directory özgü hususlar için [ACTIVE DIRECTORY Dr çözüm](../site-recovery/site-recovery-active-directory.md) Yardımcısı Kılavuzu ' na bakın. Yük devretme testi gerçekleştiğinde şirket içi kurulum hiç olumsuz bir şekilde çalışmaz. Şirket içi VM 'ye eklenmiş olan StorSimple birimleri, Azure 'daki StorSimple Cloud Appliance klonlanır. Test amaçlı bir VM, Azure 'da sağlanır ve kopyalanan birimler sanal makineye eklenir.

#### <a name="to-perform-the-test-failover"></a>Yük devretme testini gerçekleştirmek için
1. Azure portal, Site Recovery kasanızı seçin.
1. Dosya sunucusu VM 'si için oluşturulan kurtarma planına tıklayın.
1. **Yük devretme testi**' ne tıklayın.
1. Yük devretme gerçekleştikten sonra Azure VM 'lerinin bağlanacağı Azure sanal ağını seçin.
   
   ![Yük devretmeyi Başlat](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Yük devretmeyi başlatmak için **Tamam**'a tıklayın. Özelliklerini açmak için VM 'ye tıklayarak veya kasa adı &gt; **işlerinde** &gt; **Test yük devretmesi işi** **Site Recovery işleri**' ne tıklayarak ilerlemeyi izleyebilirsiniz.
1. Yük devretme tamamlandıktan sonra, çoğaltma Azure makinesi 'nin Azure Portal &gt; **sanal makinelerde**göründüğünü de görmeniz gerekir. Doğrulamalarınızı gerçekleştirebilirsiniz.
1. Doğrulamalar yapıldıktan sonra, **doğrulamalar tamamlandı**' ya tıklayın. Bu, StorSimple birimlerini kaldıracak ve StorSimple Cloud Appliance kapatacaktır.
1. İşiniz bittiğinde kurtarma planında **Yük devretme testini temizle** ' ye tıklayın. Notlar ' da, test yük devretmesi ile ilişkili tüm gözlemlerinizi kaydeder ve kaydeder. Bu işlem, yük devretme testi sırasında oluşturulan sanal makineyi silecektir.

## <a name="perform-a-planned-failover"></a>Planlı Yük devretme gerçekleştirme
   Planlı Yük devretme sırasında, şirket içi dosya sunucusu VM 'si düzgün kapatılır ve StorSimple cihazındaki birimlerin bulut yedekleme anlık görüntüsü alınır. StorSimple birimlerinin yük devretilmesi sanal cihaza, Azure 'da bir çoğaltma sanal makinesi alınır ve birimler sanal makineye eklenir.

#### <a name="to-perform-a-planned-failover"></a>Planlı Yük devretme gerçekleştirmek için
1. Azure Portal, dosya sunucusu VM 'si için **Kurtarma Hizmetleri** Kasası &gt; **kurtarma planları (Site Recovery)** &gt; **recoveryplan_name** oluşturuldu ' u seçin.
1. Kurtarma planı dikey penceresinde, **daha** &gt; **Planlı Yük devretme**' ya tıklayın.

   ![Kurtarma planı](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. **Planlı Yük devretmeyi Onayla** dikey penceresinde, kaynak ve hedef konumları seçin ve hedef ağ ' ı seçin ve ✓ onay simgesine tıklayarak yük devretme sürecini başlatın.
1. Çoğaltma sanal makineleri oluşturulduktan sonra, bir tamamlama bekleme durumunda yer alırlar. Yük devretmeyi yürütmek için **Yürüt** ' e tıklayın.
1. Çoğaltma tamamlandıktan sonra, sanal makineler ikincil konumda başlatılır.

## <a name="perform-a-failover"></a>Yük devretme gerçekleştirme
Planlanmamış bir yük devretme sırasında, StorSimple birimlerinin yükü sanal cihaza devredilmekte, Azure 'da bir çoğaltma VM 'si alınacaktır ve birimler VM 'ye eklenir.

#### <a name="to-perform-a-failover"></a>Yük devretme gerçekleştirmek için
1. Azure Portal, dosya sunucusu VM 'si için **Kurtarma Hizmetleri** Kasası &gt; **kurtarma planları (Site Recovery)** &gt; **recoveryplan_name** oluşturuldu ' u seçin.
1. Kurtarma planı dikey penceresinde, **daha fazla** &gt; **Yük devretme**' ye tıklayın.
1. **Yük devretmeyi Onayla** dikey penceresinde kaynak ve hedef konumları seçin.
1. **Sanal makineleri Kapat ' ı seçin ve en son verileri eşitleyerek** Site Recovery korunan sanal makineyi kapatmaya ve verilerin en son sürümünün yük devretmeleri için verileri eşitlemeye çalışır.
1. Yük devretmeden sonra, sanal makineler bir tamamlama bekleme durumunda. Yük devretmeyi yürütmek için **Yürüt** ' e tıklayın.


## <a name="perform-a-failback"></a>Yeniden çalışma gerçekleştirme
Yeniden çalışma sırasında StorSimple birim kapsayıcıları, bir yedekleme gerçekleştirildikten sonra fiziksel cihaza yük devretmesini sağlar.

#### <a name="to-perform-a-failback"></a>Yeniden çalışma gerçekleştirmek için
1. Azure Portal, dosya sunucusu VM 'si için **Kurtarma Hizmetleri** Kasası &gt; **kurtarma planları (Site Recovery)** &gt; **recoveryplan_name** oluşturuldu ' u seçin.
1. Kurtarma planı dikey penceresinde, **daha** &gt; **Planlı Yük devretme**' ya tıklayın.
1. Kaynak ve hedef konumları seçin, uygun veri eşitleme ve VM oluşturma seçeneklerini belirleyin.
1. Yeniden çalışma işlemini başlatmak için **Tamam** düğmesine tıklayın.
   
   ![Yeniden çalışmayı Başlat](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>En İyi Uygulamalar
### <a name="capacity-planning-and-readiness-assessment"></a>Kapasite planlama ve hazırlık değerlendirmesi
#### <a name="hyper-v-site"></a>Hyper-V sitesi
Hyper-V çoğaltma ortamınız için sunucu, depolama ve ağ altyapısını tasarlamak üzere [Kullanıcı kapasitesi planlayıcısı aracını](https://www.microsoft.com/download/details.aspx?id=39057) kullanın.

#### <a name="azure"></a>Azure
Azure VM 'Leri ve Azure Site Recovery hizmetleriyle uyumlu olduklarından emin olmak için VM 'lerde [Azure sanal makine hazırlığı değerlendirmesi aracını](https://azure.microsoft.com/downloads/vm-readiness-assessment/) çalıştırabilirsiniz. Hazır olma durumu değerlendirmesi Aracı, sanal makine yapılandırmasını denetler ve Konfigürasyonlar Azure ile uyumsuz olduğunda uyarır. Örneğin, C: sürücüsü 127 GB 'den büyükse bir uyarı verir.

Kapasite planlaması, en az iki önemli süreçden oluşur:

   - Şirket içi Hyper-V VM 'lerini Azure VM boyutlarına (A6, A7, A8 ve A9 gibi) eşleme.
   - Gerekli Internet bant genişliği belirleniyor.

## <a name="limitations"></a>Sınırlamalar
- Şu anda yalnızca 1 StorSimple cihazının yük devretme (tek bir StorSimple Cloud Appliance) olabilir. Çeşitli StorSimple cihazlarına yayılan bir dosya sunucusunun senaryosu henüz desteklenmiyor.
- Bir VM için korumayı etkinleştirirken hata alırsanız, Iscsı hedeflerinin bağlantısını kesdiğinizden emin olun.
- Birim kapsayıcılarına yayılan yedekleme ilkeleri nedeniyle birlikte gruplanmış tüm birim kapsayıcıları birlikte devredilecek.
- Seçtiğiniz birim kapsayıcılarındaki tüm birimlerin yük devretme işlemi yapılır.
- Tek bir StorSimple Cloud Appliance maksimum kapasitesi 64 TB olduğundan, 64 TB 'den fazla ekleyen birimler yük devredilemedi.
- Planlı/planlanmamış yük devretme başarısız olursa ve VM 'Ler Azure 'da oluşturulduysa, VM 'Leri temizlemeyin. Bunun yerine, yeniden çalışma yapın. VM 'Leri silerseniz, şirket içi VM 'Ler yeniden açılamaz.
- Yük devretme işleminden sonra birimleri göremiyorsanız, VM 'lere gidin, disk yönetimi 'ni açın, diskleri yeniden tarayın ve sonra çevrimiçi duruma getirin.
- Bazı örneklerde, DR sitesindeki sürücü harfleri, şirket içi mektuplardan farklı olabilir. Bu durumda, yük devretme bittikten sonra sorunu el ile düzeltmeniz gerekir.
- Yük devretme işi zaman aşımı: Birim kapsayıcılarının yük devretmesi, betik başına Azure Site Recovery sınırından daha fazla zaman alıyorsa StorSimple betiği zaman aşımına uğrar (Şu anda 120 dakika).
- Yedekleme işi zaman aşımı: Birimlerin yedeklenmesi betik başına Azure Site Recovery sınırından daha fazla zaman alıyorsa StorSimple betiği zaman aşımına uğrar (Şu anda 120 dakika).
   
  > [!IMPORTANT]
  > Yedeklemeyi Azure portal el ile çalıştırın ve kurtarma planını yeniden çalıştırın.
   
- Kopyalama işi zaman aşımı: Birimlerin kopyalanması betik başına Azure Site Recovery sınırından daha fazla zaman alıyorsa StorSimple betiği zaman aşımına uğrar (Şu anda 120 dakika).
- Zaman eşitleme hatası: StorSimple betikleri, yedekleme portalda başarılı olsa bile yedeklemelerin başarısız olduğunu bildiren hatalar oluştu. Bunun olası nedeni, StorSimple gerecinin saati saat dilimindeki geçerli zamandan eşitlenmemiş olabilir.
   
  > [!IMPORTANT]
  > Gereç süresini saat dilimindeki geçerli zaman ile eşitleyin.
   
- Gereç yük devretme hatası: Kurtarma planı çalışırken bir gereç yük devretmesi varsa StorSimple betiği başarısız olabilir.
   
  > [!IMPORTANT]
  > Gereç yük devretmesi tamamlandıktan sonra kurtarma planını yeniden çalıştırın.


## <a name="summary"></a>Özet
Azure Site Recovery kullanarak, StorSimple depolamada barındırılan dosya paylaşımlarına sahip bir dosya sunucusu VM 'si için tamamen bir otomatik olağanüstü durum kurtarma planı oluşturabilirsiniz. Yük devretmeyi saniye içinde bir kesinti durumunda başlatabilir ve birkaç dakika içinde uygulamayı çalışır duruma getirebilirsiniz.
