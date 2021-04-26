---
title: Azure Cloud Services rol örneği başlatma hatası (genişletilmiş destek)
description: Azure Cloud Services 'de rol örneği başlatma hatası giderme (genişletilmiş destek).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748962"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Başlayamayabilir Azure Cloud Services (genişletilmiş destek) rollerinin sorunlarını giderme

İşte başlayamamasının başarısız olduğu Azure Cloud Services (genişletilmiş destek) rolleriyle ilgili bazı yaygın sorunlar ve çözümleri aşağıda verilmiştir.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Bulut hizmeti işlemi Roleınstancestartuptimeouterror ile başarısız oluyor

Azure Cloud Services (genişletilmiş destek) içindeki rol örneklerinizin bir veya daha fazlası başlatılabilir veya geri dönüşüm olabilir ve rol örneği başarısız olabilir. Rol uygulama hatası `RoleInstanceStartupTimeoutError` görüntülenir.

Rol uygulaması, rol geri dönüşüme neden olabilecek iki bölüm içerir: *Başlangıç görevleri* ve *rol kodu (roleentrypoint uygulaması)*. 

Rol durdurulduğunda, hizmet olarak platform (PaaS) Aracısı rolü yeniden başlatır.

PowerShell veya Azure portal kullanarak hataları tanılamak üzere bir rol örneğinin geçerli durum ve ayrıntılarını alabilirsiniz:

* **PowerShell**: rol örneğinin çalışma zamanı durumu hakkında bilgi almak için [Get-Azcloudserviceroleınstanceview](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) cmdlet 'ini kullanın:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal**: portalda bulut hizmeti örneğine gidin. Durum ayrıntılarını görüntülemek için **Roller ve örnekler**' i seçin ve ardından rol örneğini seçin.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Azure portal rol başlatma hatası gösteren ekran görüntüsü.":::

## <a name="missing-dlls-or-dependencies"></a>Eksik DLL 'Ler veya bağımlılıklar

Durumlar arasında geçiş yapan yanıt vermeyen roller ve roller, eksik DLL 'Ler veya derlemeler nedeniyle olabilir.

Eksik DLL 'Ler veya derlemelerin bazı belirtileri şunlardır:

* Rol örneğiniz **başlatma**, **meşgul** ve **durdurma** durumlarında geçiş yapar.
* Rol örneğiniz, **Ready** durumuna taşındı, ancak Web uygulamanıza giderseniz, sayfa görünmez.


Bir Web rolünde dağıtılan ve DLL dosyası eksik olan bir Web sitesi bu sunucu çalışma zamanı hatasını görüntüleyebilir:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Rol başlatma hatasından sonra bir çalışma zamanı hatası gösteren ekran görüntüsü.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Eksik DLL 'Leri ve derlemeleri çözümle

Eksik DLL 'Lerin ve derlemelerin hatalarını çözmek için:

1. Visual Studio 'da çözümü açın.
2. Çözüm Gezgini, *Başvurular* klasörünü açın.
3. Hatada tanımlanan derlemeyi seçin.
4. **Özellikler**' de yereli **Kopyala** özelliğini **true** olarak ayarlayın.
5. Bulut hizmetini yeniden dağıtın.

Hataların artık görünmediğini doğruladıktan sonra, hizmeti yeniden dağıtın. Dağıtımı ayarlarken, **.NET 4 rolleri Için IntelliTrace 'ı etkinleştir** onay kutusunu seçmeyin.

## <a name="diagnose-role-instance-errors"></a>Rol örneği hatalarını tanılama

Rol örnekleriyle ilgili sorunları tanılamak için aşağıdaki seçenekler arasından seçim yapın.

### <a name="turn-off-custom-errors"></a>Özel hataları kapat

Tüm hata bilgilerini görüntülemek için, Web rolünün *web.config* dosyasında, özel hata modunu olarak ayarlayın `off` ve ardından hizmeti yeniden dağıtın:

1. Visual Studio 'da çözümü açın.
2. Çözüm Gezgini, *web.config* dosyasını açın.
3. `system.web`Bölümünde aşağıdaki kodu ekleyin:

   ```xml
   <customErrors mode="Off" />
   ```

4. Dosyayı kaydedin.
5. Hizmeti yeniden paketleyin ve dağıtın.

Hizmet yeniden dağıtıldığında, bir hata iletisi eksik derlemelerin veya dll 'Lerin adını içerir.

### <a name="use-remote-desktop"></a>Uzak Masaüstü kullanma

Role erişmek ve tamamlanan hata bilgilerini görüntülemek için Uzak Masaüstü 'Nü kullanın:

1. [Azure Cloud Services Için Uzak Masaüstü uzantısı 'nı (genişletilmiş destek) ekleyin](enable-rdp.md).
2. Azure portal, bulut hizmeti örneği bir **hazırlık** durumu gösterdiğinde, bulut hizmetinde oturum açmak Için uzak masaüstü 'nü kullanın. Daha fazla bilgi için bkz. [Uzak Masaüstü kullanarak rol örneklerine bağlanma](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Uzak Masaüstü kurmak için kullandığınız kimlik bilgilerini kullanarak sanal makinede oturum açın.
4. Bir komut istemi penceresi açın.
5. Komut isteminde **ipconfig** yazın. IPv4 adresi için döndürülen değeri aklınızda edin.
6. Microsoft Internet Explorer 'ı açın.
7. Adres çubuğunda IPv4 adresini, ardından bir eğik çizgi ve Web uygulaması varsayılan dosyası adını girin. Örneğin, `http://<IPv4 address>/default.aspx`.

Şimdi Web sitesine giderseniz, daha fazla bilgi içeren hata iletileri görürsünüz. Aşağıda bir örnek verilmiştir:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Bir hata iletisine bir örnek gösteren ekran görüntüsü.":::
  
### <a name="use-the-compute-emulator"></a>İşlem öykünücüsünü kullanma

Eksik bağımlılıkların ve *web.config* hatalarının sorunlarını tanılamak ve gidermek için Azure işlem öykünücüsünü kullanabilirsiniz. Sorunları tanılamak için bu yöntemi kullandığınızda, en iyi sonuçlar için, temiz bir Windows yüklemesi olan bir bilgisayar veya sanal makine kullanın.

Azure işlem öykünücüsünü kullanarak sorunları tanılamak için:

1. [Azure SDK 'sını](https://azure.microsoft.com/downloads/)yükler.
2. Geliştirme bilgisayarında, bulut hizmeti projesini derleyin.
3. Dosya Gezgini 'nde, bulut hizmeti projesinde *bin\Debug* klasörüne gidin.
4. *. CSX* klasörünü ve *. cscfg* dosyasını, sorunlarını ayıklamak için kullanmakta olduğunuz bilgisayara kopyalayın.
5. Temiz makinede, bir Azure SDK komut Istemi penceresi açın.
6. Komut isteminde **csrun.exe/Devstore: Start** yazın.
7. Ardından, **csrun \<path to .csx folder\> \<path to .cscfg file\> /launchbrowser** komutunu girin.
8. Rol başlatıldığında, Internet Explorer ayrıntılı hata bilgilerini görüntüler.

Daha fazla tanılama gerekliyse standart Windows sorun giderme araçları ' nı kullanabilirsiniz.

### <a name="use-intellitrace"></a>IntelliTrace kullanma

.NET Framework 4 kullanan çalışan ve Web rolleri için [IntelliTrace](/visualstudio/debugger/intellitrace)kullanabilirsiniz. IntelliTrace Visual Studio Enterprise bulunabilir.

Bulut hizmetinizi IntelliTrace 'e açık bir şekilde dağıtmak için:

1. Azure SDK 1,3 veya sonraki bir sürümünün yüklü olduğunu doğrulayın.
2. Visual Studio 'da çözümü dağıtın. Dağıtımı ayarlarken, **.NET 4 rolleri Için IntelliTrace 'ı etkinleştir** onay kutusunu işaretleyin.
3. Rol örneği başladıktan sonra Sunucu Gezgini açın.
4. **Azure\Cloud Services** düğümünü genişletin.
5. Rol örneklerini listelemek için dağıtımı genişletin. Rol örneğine sağ tıklayın.
6. **IntelliTrace günlüklerini görüntüle**' yi seçin.
7. **IntelliTrace Özeti**' nde **özel durum verileri**' ne gidin.
8. **Özel durum verilerini** genişletin ve bir hata olup olmadığına bakın `System.IO.FileNotFoundException` :

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Rol başlatma hatası için özel durum verisinin ekran görüntüsü." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PaaS bilgisayar tanılama verilerini kullanarak bulut hizmeti rolü sorunlarını giderme](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)hakkında bilgi edinin.
