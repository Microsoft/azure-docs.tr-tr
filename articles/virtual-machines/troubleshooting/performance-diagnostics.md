---
title: Azure sanal makineleri için Performans Tanılama | Microsoft Docs
description: Windows için Azure performans tanılamayı tanıtır.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080611"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure sanal makineleri için performans tanılamaları

Performans Tanılama Aracı, bir Windows veya Linux sanal makinesini (VM) etkileyebilecek performans sorunlarını gidermenize yardımcı olur. Desteklenen sorun giderme senaryoları, bilinen sorunlar ve en iyi uygulamalar üzerinde hızlı denetimler ve yavaş VM performansı ya da CPU, disk alanı veya bellek kullanımının yüksek kullanımı ile ilgili karmaşık sorunlar içerir.

Performans tanılamayı doğrudan Azure portal, çeşitli günlüklerde, zengin yapılandırma ve tanılama verilerinde de öngörüleri ve bir raporu gözden geçirebileceğiniz bir şekilde çalıştırabilirsiniz. Microsoft Desteği iletişime geçmeden önce performans tanılamayı çalıştırmanızı ve öngörüleri ve tanılama verilerini incelemenizi öneririz.

> [!NOTE]
> Windows için Performans Tanılama Şu anda .NET SDK sürüm 4,5 veya sonraki bir sürümü yüklü olan VM 'lerde desteklenmektedir. Klasik VM 'lerde performans Tanılamayı Çalıştırma adımları için bkz. [Azure Performans Tanılama VM Uzantısı](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standart, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-Database-Ee 13,8 Market Image), CentOS 6,5 [`*`], 7,6, RHEL 7,2, 7,5, 8,0 [`*`], Ubuntu 14,04, 16,04, 18,04, de, 8, 9, 10 [`*`], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Lütfen [bilinen sorunlara](how-to-use-perfinsights-linux.md#known-issues) bakın

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>VM 'nize performans tanılamayı yükleyip çalıştırın

Performans Tanılama, Perfinsıghts adlı bir tanılama aracı çalıştıran bir VM Uzantısı yüklüyor. Perfinsıghts, hem [Windows](https://aka.ms/perfinsights) hem de [Linux](https://aka.ms/perfinsightslinux)için kullanılabilir. Performans tanılamayı yüklemek ve çalıştırmak için aşağıdaki adımları izleyin:

1. Komutların sol sütununda **sanal makineler**' i seçin.
1. VM adları listesinden, tanılamayı çalıştırmak istediğiniz VM 'yi seçin.
1. Komutların sağ sütununda **Performans Tanılama**' yı seçin.

    ![Azure portal ekran görüntüsü, Install Performance Diagnostic Button vurgulanmış](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Bu ekran görüntüsünde, VM adlarının dikey penceresi gizlenir.
1. Bir depolama hesabı seçin (isteğe bağlı)

    Birden çok VM için Performans Tanılama sonuçlarını depolamak üzere tek bir depolama hesabı kullanmak istiyorsanız, araç çubuğundaki **Ayarlar** düğmesine tıklayarak bir depolama hesabı seçebilirsiniz. Depolama hesabını seçtiğinizde **Tamam** düğmesine tıklayın.

    Bir depolama hesabı belirtmezseniz, varsayılan olarak yeni bir depolama hesabı oluşturulur.

    ![Ayarlar araç çubuğu düğmesi vurgulanmış şekilde performans tanılama dikey penceresinin ekran görüntüsü](media/performance-diagnostics/settings-button.png)

    ![Performans Tanılama ayarları dikey penceresinden depolama hesabı seçiminin ekran görüntüsü](media/performance-diagnostics/select-storage-account.png)

1. **Performans tanılamayı yüklensin** düğmesini seçin.
1. Yükleme tamamlandıktan sonra bir tanı çalıştırmak istiyorsanız **Tanılamayı Çalıştır** onay kutusunu seçin. Bu seçimi yaparsanız, performans analizi senaryosunu ve ilgili seçenekleri seçebileceksiniz.

    ![Performans Tanılama yüklemesi düğmesinin ekran görüntüsü](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Çalıştırılacak analiz senaryosunu seçin

Aşağıdaki analiz senaryoları Azure portal kullanılabilir. Sahip olduğunuz performans sorununa bağlı olarak bir analiz seçin. Analiz için gereken süre ve izleme seçeneklerini belirleyin.

* **Hızlı performans analizi**  
    Bilinen sorunları denetler, en iyi yöntemleri analiz eder ve tanılama verilerini toplar. Bu çözümlemenin çalıştırılması birkaç dakika sürer. Daha fazla [Windows](https://aka.ms/perfinsights/quick) veya [Linux](https://aka.ms/perfinsightslinux/quick) öğrenin

* **Performans Analizi**  
    Hızlı performans analizinde tüm denetimleri içerir ve yüksek kaynak tüketimini izler. Yüksek CPU, bellek ve disk kullanımı gibi genel performans sorunlarını gidermek için bu sürümü kullanın. Bu analiz, seçilen süreye bağlı olarak 30 saniye ila 15 dakika sürer. Daha fazla [Windows](https://aka.ms/perfinsights/vmslow) veya [Linux](https://aka.ms/perfinsightslinux/vmslow) öğrenin

* **Gelişmiş performans analizi**`*`  
    , Performans analizinde tüm denetimleri içerir ve aşağıdaki bölümlerde listelendiği gibi izlemeleri bir veya daha fazla toplar. Ek izlemeler gerektiren karmaşık sorunları gidermek için bu senaryoyu kullanın. Bu senaryonun uzun süreler için çalıştırılması, sanal makinenin boyutuna ve seçilen izleme seçeneklerine bağlı olarak tanılama çıktısının genel boyutunu artıracaktır. Bu çözümlemenin çalıştırılması, seçilen süreye bağlı olarak 30 saniye ila 15 dakika sürer. [Daha fazla bilgi edinin](https://aka.ms/perfinsights/advanced)

* **Azure dosyaları Analizi**`*`  
    Performans analizinde tüm denetimleri içerir ve bir ağ izleme ve SMB sayaçlarını yakalar. Azure dosyalarının performansı sorunlarını gidermek için bu senaryoyu kullanın. Bu çözümlemenin çalıştırılması, seçilen süreye bağlı olarak 30 saniye ila 15 dakika sürer. [Daha fazla bilgi edinin](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Bu analiz senaryoları yalnızca Windows 'da desteklenir.

![Performans Tanılama dikey penceresinde tanılama bölmesini Çalıştır 'ın ekran görüntüsü](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Belirtileri sağlama (isteğe bağlı)

Listeden önceden seçilmiş belirtileri seçin veya yeni belirtiler ekleyin. Bu, gelecekte analizine geliştirmemize yardımcı olur.

### <a name="provide-support-request-number-if-available-optional"></a>Varsa destek istek numarasını sağlayın (isteğe bağlı)

Mevcut bir destek bileti üzerinde bir Microsoft Destek Mühendisi ile çalışıyorsanız, destek bileti numarasını sağlayın.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Gizlilik ilkesini ve yasal koşulları gözden geçirin ve onaylamak için onay kutusunu seçin (gerekli)

Tanılamayı çalıştırmak için, yasal koşulları kabul etmeniz ve gizlilik ilkesini kabul etmeniz gerekir.

### <a name="select-ok-to-run-the-diagnostics"></a>Tanılamayı çalıştırmak için Tamam ' ı seçin

Performans Tanılama yüklenmeye başladığı için bir bildirim görüntülenir. Yükleme tamamlandıktan sonra, yüklemenin başarılı olduğunu belirten bir bildirim görürsünüz. Seçili analiz daha sonra belirtilen süre için çalıştırılır. Bu, tanılama verilerinin doğru zamanda yakalanabilmesi için performans sorununun yeniden oluşturulması iyi bir zaman olabilir.

Analiz tamamlandıktan sonra, aşağıdaki öğeler Azure tablolarına ve belirtilen depolama hesabındaki bir ikili büyük nesne (BLOB) kapsayıcısına yüklenir:

* Çalışma hakkındaki tüm Öngörüler ve ilgili bilgiler
* Windows üzerinde bir çıkış sıkıştırılmış (. zip) dosya ( **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-ss-fff. zip** ) ve günlük dosyaları içeren Linux üzerinde bir tar Ball dosyası ( **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-ss-fff. tar. gz** olarak adlandırılır)
* HTML raporu

Karşıya yüklemeden sonra, Azure portal yeni bir tanılama raporu listelenir.

![Performans Tanılama dikey penceresinde tanılama raporu listesinin ekran görüntüsü](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Performans tanılama ayarlarını değiştirme

Tanılama öngörülerinin ve çıktısının depolanabileceği depolama hesabını değiştirmek için **Ayarlar** araç çubuğu düğmesini kullanın. Performans tanılamayı kullanan birden çok VM için aynı depolama hesabını kullanabilirsiniz. Depolama hesabını değiştirdiğinizde, eski raporlar ve Öngörüler silinmez. Ancak, bunlar artık tanılama raporları listesinde gösterilmeyecektir.

## <a name="review-insights-and-performance-diagnostics-report"></a>Öngörüleri ve Performans Tanılama raporunu gözden geçirin

Her bir tanılama çalıştırması, Öngörüler ve önerilerin bir listesini, etkilenen kaynakları, günlük dosyalarını ve toplanan diğer zengin tanılama bilgilerini, ayrıca çevrimdışı görüntüleme için bir rapor içerir. Tüm toplanan tanılama verilerinin listesi için bkz. [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) veya [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights)'Ta **Perfinsıghts? tarafından ne tür bilgiler toplandı?** .

### <a name="select-a-performance-diagnostics-report"></a>Bir performans tanılama raporu seçin

Çalıştırılan tüm tanılama raporlarını bulmak için tanılama raporu listesini kullanabilirsiniz. Bu liste, kullanılan analizler, bulunan Öngörüler ve bunların etki düzeyleri hakkında ayrıntılar içerir. Daha fazla ayrıntı görüntülemek için bir satır seçin.

![Performans Tanılama dikey penceresinden tanılama raporu seçme ekran görüntüsü](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Performans Tanılama raporunu gözden geçirme

Her performans tanılama raporu, çeşitli Öngörüler içerebilir ve yüksek, orta veya düşük bir etki düzeyini belirtebilir. Her öngörü, sorunu gidermek için öneriler de içerir. Öngörüler kolay filtreleme için gruplandırılır.

Etki düzeyleri, yanlış yapılandırma, bilinen sorunlar veya diğer kullanıcılar tarafından bildirilen sorunlar gibi etkenlere bağlı olarak performans sorunlarının potansiyelini temsil eder. Listelenen sorunlardan birini veya daha fazlasını henüz yaşamaya meyebilirsiniz. Örneğin, aynı veri diskinde SQL günlük dosyalarınız ve veritabanı dosyalarınız olabilir. Bu durum, performans sorunları ve veritabanı kullanımı yüksekse diğer performans sorunları için yüksek bir potansiyeli sahiptir, ancak kullanım düşükse bir sorun fark edemeyebilirsiniz.

![Performans Tanılama raporuna genel bakış dikey penceresinin ekran görüntüsü](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Performans Tanılama öngörülerini ve önerilerini gözden geçirme

Etkilenen kaynaklar, önerilen azaltmaları ve başvuru bağlantıları hakkında daha fazla ayrıntı görüntülemek için bir öngörü seçebilirsiniz.

![Performans Tanılama Insight ayrıntısı ekran görüntüsü](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Tam performans Tanılama raporunu indirin ve gözden geçirin

Depolama ve ağ yapılandırması, performans sayaçları, izlemeler, işlem listesi ve Günlükler gibi ek zengin tanılama bilgileri içeren bir HTML raporu indirmek için **raporu indir** düğmesini kullanabilirsiniz. İçerik seçili Analize bağlıdır. Gelişmiş sorun giderme için rapor, yüksek CPU kullanımı, yüksek disk kullanımı ve aşırı bellek kullanan işlemlerle ilgili ek bilgiler ve etkileşimli grafikler içerebilir. Performans Tanılama raporu hakkında daha fazla bilgi için bkz. [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) veya [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Performans tanılama raporlarını yönetme

**Raporu Sil** düğmesini kullanarak bir veya daha fazla performans Tanılama raporunu silebilirsiniz.

## <a name="how-to-uninstall-performance-diagnostics"></a>Performans tanılamayı kaldırma

Performans tanılamayı bir VM 'den kaldırabilirsiniz. Bu eylem, VM uzantısını kaldırır ancak depolama hesabında bulunan tanılama verilerini etkilemez.

![Kaldır düğmesi vurgulanmış şekilde performans tanılama dikey penceresinin ekran görüntüsü](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>VM 'lerimin depolandığı Tanılama verileri nerede?

Tüm performans tanılama öngörüleri ve raporları kendi depolama hesabınızda depolanır. Öngörüler Azure tabloları içinde depolanır. Rapor sıkıştırılan dosyası azdiagextnresults adlı bir ikili büyük nesne (BLOB) kapsayıcısında depolanır.

Araç çubuğundaki ayarlar düğmesini kullanarak depolama hesabı bilgilerini görüntüleyebilirsiniz.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Nasıl yaparım? bu verileri Microsoft müşteri desteğiyle paylaşma

Tanılama raporunu Microsoft ile paylaşmanın birden çok yolu vardır.

**Seçenek 1:** En son raporu otomatik olarak paylaşma  
Microsoft ile bir destek bileti açtığınızda, performans tanılama raporu 'nun paylaşılması önemlidir. Tanılamayı çalıştırırken bu bilgileri Microsoft ile paylaşmayı tercih ettiyseniz ("**Tanılama bilgilerini Microsoft ile paylaşmayı kabul**ediyorum" onay kutusunu seçerek), Microsoft, bir SAS bağlantısı kullanarak depolama hesabınızdan rapora erişebilir çıkış ZIP dosyasına, çalışma tarihinden itibaren en fazla 30 gün boyunca. Destek mühendisine yalnızca en son rapor kullanılabilir.

**Seçenek 2:** Tanılama raporu sıkıştırılan dosya için paylaşılan erişim Imzası oluşturma  
Paylaşılan erişim Imzalarını kullanarak, rapor sıkıştırılan dosya için bir bağlantı paylaşabilirsiniz. Bunu yapmak için şu adımları uygulayın:

1. Azure portal, tanılama verilerinin depolandığı depolama hesabına gidin.
1. **BLOB hizmeti** bölümünde **BLOB 'ları** seçin.
1. **Azdiagextnresults** kapsayıcısını seçin.
1. Paylaşmak istediğiniz performans tanılama çıkışı sıkıştırılmış dosyasını seçin.
1. **SAS oluştur** sekmesinde, paylaşma ölçütlerini seçin.
1. **BLOB SAS belirteci oluştur ve URL**'yi tıklatın.
1. **BLOB SAS URL 'sini**kopyalayın ve destek mühendisiyle paylaşabilirsiniz.

**Seçenek 3:** Raporu depolama hesabından indirin

Ayrıca, adım 2 ' de 1 – 4 ' teki adımları kullanarak performans tanılama raporu sıkıştırılmış dosyasını da bulabilirsiniz. Dosyayı indirmek için öğesini seçin ve ardından e-posta ile paylaşabilirsiniz veya destek mühendisinden dosyayı karşıya yükleme yönergelerini isteyin.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Nasıl yaparım? tanılama verilerini doğru zamanda yakala

Her performans tanılama çalışmasının iki aşaması vardır:

1. Performans Tanılama VM uzantısını yükler veya güncelleştirir.
1. Tanılamayı belirtilen süre için çalıştırın.

Şu anda VM Uzantısı yüklemesi tamamlandığında tam olarak bilmeniz kolay bir yol yoktur. Genellikle VM uzantısının yüklenmesi yaklaşık 45 saniye ile 1 dakika sürer. VM Uzantısı yüklendikten sonra, performans tanılamayı sorun gidermeye yönelik doğru veri kümesini yakalayacak şekilde yeniden üretme adımlarınızı çalıştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Performans Tanılama öngörülerini ve raporunu gözden geçirdikten sonra, sorunun nedenini tespit edemez ve daha fazla yardıma ihtiyaç duyuyorsanız, Microsoft müşteri desteği ile bir destek bileti açabilirsiniz.

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
