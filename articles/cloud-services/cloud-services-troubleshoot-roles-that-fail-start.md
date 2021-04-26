---
title: Başlayamayabilir olan rollerin sorunlarını giderme | Microsoft Docs
description: Bir bulut hizmeti rolünün başlayabileceği bazı yaygın nedenler aşağıda verilmiştir. Bu sorunlara yönelik çözümler de sağlanır.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743228"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>Başlayamayabilir Azure bulut hizmeti (klasik) rollerinin sorunlarını giderme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

İşte başlayamamasının başarısız olduğu Azure Cloud Services rolleriyle ilgili bazı yaygın sorunlar ve çözümler aşağıda verilmiştir.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Eksik DLL 'Ler veya bağımlılıklar
**Başlatma**, **meşgul** ve **durdurma** durumları arasında geçiş yapan yanıt vermeyen roller ve roller, eksik DLL 'ler veya derlemeler nedeniyle oluşabilir.

Eksik DLL 'Ler veya derlemelerin belirtileri şu olabilir:

* Rol örneğiniz **başlatma**, **meşgul** ve **durdurma** durumlarıyla geçiş yapıyor.
* Rol örneğiniz **başlamaya** taşındı, ancak Web uygulamanıza gittiğinizde sayfa görünmez.

Bu sorunları araştırmak için önerilen birkaç yöntem vardır.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Eksik DLL sorunlarını bir Web rolünde tanılayın
Bir Web rolünde dağıtılan bir Web sitesine gittiğinizde ve tarayıcı aşağıdakine benzer bir sunucu hatası görüntülerse, bir DLL 'nin eksik olduğunu gösterebilir.

!['/' Uygulamasında sunucu hatası.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Özel hataları kapatarak sorunları tanılayın
Web rolü web.config, hizmetin kapalı ve yeniden dağıtılması için özel hata modunu ayarlamak üzere yapılandırılarak daha fazla hata bilgisi görüntülenebilir.

Uzak Masaüstü kullanmadan daha fazla Tamam hata görüntülemek için:

1. Çözümü Microsoft Visual Studio açın.
2. **Çözüm Gezgini**, web.config dosyasını bulun ve açın.
3. web.config dosyasında, System. Web bölümünü bulun ve aşağıdaki satırı ekleyin:

    ```xml
    <customErrors mode="Off" />
    ```
4. Dosyayı kaydedin.
5. Hizmeti yeniden paketleyin ve dağıtın.

Hizmet yeniden dağıtıldıktan sonra, eksik derleme veya DLL adına sahip bir hata iletisi görürsünüz.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Hatayı uzaktan görüntüleyerek sorunları tanılayın
Role erişmek ve daha fazla hata bilgisini uzaktan görüntülemek için Uzak Masaüstü 'Nü kullanabilirsiniz. Uzak Masaüstü kullanarak hataları görüntülemek için aşağıdaki adımları kullanın:

1. Azure SDK 1,3 veya sonraki bir sürümünün yüklü olduğundan emin olun.
2. Visual Studio 'Yu kullanarak çözümün dağıtımı sırasında uzak masaüstü 'Nü etkinleştirin. Daha fazla bilgi için bkz. [Visual Studio kullanarak Azure Cloud Services bir rol için Uzak Masaüstü bağlantısı etkinleştirme](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. Microsoft Azure portal, **örnek, örneğine** uzak bir durumu gösterir. Cloud Services ile uzak masaüstü kullanma hakkında daha fazla bilgi için bkz. [Uzak rol örneklerine uzaktan](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Uzak Masaüstü yapılandırması sırasında belirtilen kimlik bilgilerini kullanarak sanal makinede oturum açın.
6. Komut penceresi açın.
7. `IPconfig` yazın.
8. IPV4 adresi değerini aklınızda edin.
9. Internet Explorer'ı açın.
10. Web uygulamasının adresini ve adını yazın. Örneğin, `http://<IPV4 Address>/default.aspx`.

Web sitesine gidildiğinde artık daha açık hata iletileri geri alınacaktır:

* '/' Uygulamasında sunucu hatası.
* Açıklama: geçerli Web isteğinin yürütülmesi sırasında işlenmeyen bir özel durum oluştu. Hata ve kodun kaynaklandığı yer hakkında daha fazla bilgi için lütfen yığın izlemesini gözden geçirin.
* Özel durum ayrıntıları: System. ıO. FIleNotFoundException: dosya veya derleme ' Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 ' veya bağımlılıklarından biri yüklenemedi. Sistem belirtilen dosyayı bulamıyor.

Örnek:

!['/' Uygulamasında açık sunucu hatası](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>İşlem öykünücüsünü kullanarak sorunları tanılama
Eksik bağımlılıkların ve web.config hataların sorunlarını tanılamak ve gidermek için Microsoft Azure Işlem öykünücüsünü kullanabilirsiniz.

Bu tanılama yöntemini kullanmanın en iyi sonucu için, temiz bir Windows yüklemesi olan bir bilgisayar veya sanal makine kullanmanız gerekir. Azure ortamının en iyi benzetimini yapmak için Windows Server 2008 R2 x64 kullanın.

1. [Azure SDK 'sının](https://azure.microsoft.com/downloads/)tek başına sürümünü yükleme.
2. Geliştirme makinesinde, bulut hizmeti projesini derleyin.
3. Windows Gezgini 'nde, bulut hizmeti projesinin bin\debug klasörüne gidin.
4. . CSX klasörünü ve. cscfg dosyasını, sorunları ayıklamak için kullandığınız bilgisayara kopyalayın.
5. Temiz makinede, bir Azure SDK komut Istemi penceresi açın ve yazın `csrun.exe /devstore:start` .
6. Komut istemine yazın `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` .
7. Rol başlatıldığında, Internet Explorer 'da ayrıntılı hata bilgileri görürsünüz. Ayrıca, sorunu tanılamak için standart Windows sorun giderme araçları 'nı kullanabilirsiniz.

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace kullanarak sorunları tanılama
.NET Framework 4 kullanan çalışan ve Web rolleri için, Microsoft Visual Studio Enterprise kullanılabilen [IntelliTrace](/visualstudio/debugger/intellitrace)kullanabilirsiniz.

Hizmeti IntelliTrace etkin olarak dağıtmak için şu adımları izleyin:

1. Azure SDK 1,3 veya sonraki bir sürümünün yüklü olduğunu doğrulayın.
2. Visual Studio 'Yu kullanarak çözümü dağıtın. Dağıtım sırasında, **.NET 4 rolleri Için IntelliTrace 'ı etkinleştir** onay kutusunu işaretleyin.
3. Örnek başladıktan sonra **Sunucu Gezgini** açın.
4. **Azure \\ Cloud Services** düğümünü genişletin ve dağıtımı bulun.
5. Rol örneklerini görene kadar dağıtımı genişletin. Örneklerden birine sağ tıklayın.
6. **IntelliTrace günlüklerini görüntüle**' yi seçin. **IntelliTrace Özeti** açılır.
7. Özetin özel durumlar bölümünü bulun. Özel durumlar varsa, bölüm **özel durum verileri** olarak etiketlenir.
8. **Özel durum verilerini** genişletin ve aşağıdakilere benzer şekilde **System. IO. FileNotFoundException** hatalarını arayın:

![Özel durum verileri, eksik dosya veya derleme](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Eksik DLL 'Ler ve derlemeler için adres
Eksik DLL ve derleme hatalarını gidermek için aşağıdaki adımları izleyin:

1. Visual Studio 'da çözümü açın.
2. **Çözüm Gezgini**, **Başvurular** klasörünü açın.
3. Hatada tanımlanan derlemeye tıklayın.
4. **Özellikler** bölmesinde, **yerel özelliği Kopyala** ' yı bulun ve değeri **true** olarak ayarlayın.
5. Bulut hizmetini yeniden dağıtın.

Tüm hataların düzeltilmediğini doğruladıktan sonra, **.NET 4 rolleri Için IntelliTrace 'ı etkinleştir** onay kutusunu işaretleyerek Hizmeti dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Cloud Services için daha fazla [sorun giderme makalesini](../index.yml?product=cloud-services&tag=top-support-issue) görüntüleyin.

Azure PaaS bilgisayar tanılama verilerini kullanarak bulut hizmeti rolü sorunlarını giderme hakkında bilgi edinmek için bkz. [Kevin Williamson 'ın blog serisi](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
