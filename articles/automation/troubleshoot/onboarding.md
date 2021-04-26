---
title: Azure Otomasyonu özelliği dağıtım sorunlarını giderme
description: Bu makalede, Azure Otomasyonu özellikleri dağıtıldığında ortaya çıkan sorunların nasıl giderileceği ve çözüleceği açıklanır.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651128"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Özellik dağıtımı sorunlarını giderme

Azure Otomasyonu Güncelleştirme Yönetimi özelliğini veya Değişiklik İzleme ve envanter özelliğini sanal makinelerinize dağıtırken hata iletileri alabilirsiniz. Bu makalede oluşabilecek hatalar ve bunların nasıl çözümleneceği açıklanır.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Senaryo: kayıtlı bir düğümü yeniden adlandırmak için kayıt silme veya yeniden kaydetme gerekir

#### <a name="issue"></a>Sorun

Bir düğüm Azure Otomasyonu 'na kaydedilir ve işletim sistemi bilgisayar adı değiştirilir. Düğüm içindeki raporlar özgün adla görünmeye devam eder.

#### <a name="cause"></a>Nedeni

Kayıtlı düğümlerin yeniden adlandırılması, Azure Automation 'da düğüm adını güncelleştirmez.

#### <a name="resolution"></a>Çözüm

Azure Otomasyonu durum yapılandırması ' ndan düğümün kaydını kaldırın ve yeniden kaydedin. Bu süreden önce hizmette yayınlanan raporların artık kullanılamayacak.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Senaryo: HTTPS proxy 'si aracılığıyla sertifikaların yeniden imzalanması desteklenmez

#### <a name="issue"></a>Sorun

HTTPS trafiğini sonlandıran ve ardından yeni bir sertifika kullanarak trafiği yeniden şifreleyen bir proxy üzerinden bağlandığınızda, hizmet bağlantıya izin vermez.

#### <a name="cause"></a>Nedeni

Azure Otomasyonu, trafiği şifrelemek için kullanılan sertifikaların yeniden imzalanmasını desteklemez.

#### <a name="resolution"></a>Çözüm

Şu anda bu sorun için geçici çözüm yoktur.

## <a name="general-errors"></a>Genel hatalar

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Senaryo: Özellik dağıtımı "çözüm etkinleştirilemiyor" iletisiyle başarısız oluyor

#### <a name="issue"></a>Sorun

Bir sanal makinede bir özelliği etkinleştirmeye çalıştığınızda aşağıdaki iletilerden birini alırsınız:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Nedeni

Bu hata, VM veya çalışma alanında ya da Kullanıcı için hatalı veya eksik izinlere neden olur.

#### <a name="resolution"></a>Çözüm

Doğru [özellik dağıtım izinlerine](../automation-role-based-access-control.md#feature-setup-permissions)sahip olduğunuzdan emin olun ve sonra özelliği yeniden dağıtmayı deneyin. Hata iletisini alırsanız `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , aşağıdaki [sorun giderme bilgilerine](update-management.md#failed-to-enable-error)bakın.

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Senaryo: Özellik dağıtımı "tanılama günlüğü için Otomasyon hesabı yapılandırılamadı" iletisiyle başarısız oluyor

#### <a name="issue"></a>Sorun

Bir sanal makinede bir özelliği etkinleştirmeye çalıştığınızda şu iletiyi alırsınız:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Nedeni

Fiyatlandırma Katmanı aboneliğin faturalama modeliyle eşleşmezse bu hataya neden olmuş olabilir. Daha fazla bilgi için bkz. [Azure izleyici 'de kullanımı ve tahmini maliyetleri izleme](../../azure-monitor//usage-estimated-costs.md).

#### <a name="resolution"></a>Çözüm

Log Analytics çalışma alanınızı el ile oluşturun ve oluşturulan çalışma alanını seçmek için özellik dağıtım sürecini tekrarlayın.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Senaryo: ComputerGroupQueryFormatError

#### <a name="issue"></a>Sorun

Bu hata kodu, özelliği hedeflemek için kullanılan kayıtlı arama bilgisayar grubu sorgusunun doğru biçimlendirilmediği anlamına gelir. 

#### <a name="cause"></a>Nedeni

Sorguyu değiştirmiş olabilirsiniz veya sistem onu değiştirmiş olabilir.

#### <a name="resolution"></a>Çözüm

Özelliğin sorgusunu silebilir ve sonra yeniden etkinleştirerek özelliği yeniden oluşturabilirsiniz. Sorgu, **kayıtlı aramalar** altında çalışma alanınızda bulunabilir. Sorgunun adı **Microsoftdefaultcomputergroup**, sorgunun kategorisi ise ilişkili özelliğin adıdır. Birden çok özellik etkinleştirilirse, **Microsoftdefaultcomputergroup** sorgusu **kayıtlı aramalarda** birden çok kez görünür.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Senaryo: policyihla

#### <a name="issue"></a>Sorun

Bu hata kodu, bir veya daha fazla ilkenin ihlalinden dolayı dağıtımın başarısız olduğunu gösterir.

#### <a name="cause"></a>Nedeni 

Bir ilke işlemin tamamlanmasını engelliyor.

#### <a name="resolution"></a>Çözüm

Özelliği başarıyla dağıtmak için, belirtilen ilkeyi değiştirmeyi göz önünde bulundurmanız gerekir. Tanımlanabileceğinden çok sayıda farklı ilke türü olduğundan, gerekli değişiklikler ihlal edilen ilkeye göre değişir. Örneğin, bir ilke, içerilen bazı kaynakların içeriğini değiştirme iznini reddeden bir kaynak grubunda tanımlanmışsa, bu düzeltmelerden birini seçebilirsiniz:

* İlkeyi tamamen kaldırın.
* Farklı bir kaynak grubu için özelliği etkinleştirmeyi deneyin.
* İlkeyi belirli bir kaynak için yeniden hedefleyin, örneğin bir Otomasyon hesabı.
* İlkenin reddedecek şekilde yapılandırıldığı kaynak kümesini gözden geçirin.

Azure portal, sağ üst köşedeki bildirimleri denetleyin veya Otomasyon hesabınızı içeren kaynak grubuna gidin ve başarısız dağıtımı görüntülemek için **Ayarlar** altında **dağıtımlar** ' ı seçin. Azure Ilkesi hakkında daha fazla bilgi edinmek için bkz. [Azure Ilkesine genel bakış](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Senaryo: bir çalışma alanının bağlantısını kaldırılmaya çalışırken hata oluştu

#### <a name="issue"></a>Sorun

Bir çalışma alanının bağlantısını kaldırmayı denediğinizde aşağıdaki hata iletisini alırsınız:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Nedeni

Bu hata, Otomasyon hesabınıza ve Log Analytics çalışma alanına bağlı Log Analytics çalışma alanınızda etkin olan özelliklerden hala sahip olduğunuzda oluşur.

### <a name="resolution"></a>Çözüm

Aşağıdaki özelliklerle ilgili kaynakları, kullanıyorsanız, çalışma alanınızdan kaldırın:

* Güncelleştirme Yönetimi
* Değişiklik İzleme ve Stok
* Hizmetin kapalı olduğu saatlerde Sanal Makineleri Başlatma/Durdurma

Özellik kaynaklarını kaldırdıktan sonra, çalışma alanınızın bağlantısını kaldırabilirsiniz. Çalışma alanınızda ve otomasyon hesabınızda bu özelliklerden var olan yapıtları temizlemek önemlidir:

* Güncelleştirme Yönetimi için, Otomasyon hesabınızdan **güncelleştirme dağıtımlarını (zamanlamalar)** kaldırın.
* VM'leri çalışma saatleri dışında Başlat/Durdur için, Otomasyon hesabınızdaki Özellik bileşenlerinde bulunan kilitleri **Ayarlar**  >  **kilitleri** altında kaldırın. Daha fazla bilgi için bkz. [özelliği kaldırma](../automation-solution-vm-management-remove.md).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows uzantısı hatalarında Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Windows uzantısı için Log Analytics aracısının yüklemesi çeşitli nedenlerden dolayı başarısız olabilir. Aşağıdaki bölümde, Windows uzantısı için Log Analytics aracısının dağıtımı sırasında hatalara neden olabilecek Özellik dağıtım sorunları açıklanmaktadır.

>[!NOTE]
>Windows için Log Analytics Aracısı, şu anda Azure Otomasyonu 'nda Microsoft Monitoring Agent (MMA) için kullanılan addır.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Senaryo: bir WebClient isteği sırasında özel durum oluştu

VM 'deki Windows uzantısı Log Analytics dış kaynaklarla iletişim kuramıyor ve dağıtım başarısız olur.

#### <a name="issue"></a>Sorun

Aşağıda döndürülen hata iletilerinin örnekleri verilmiştir:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Nedeni

Bu hatanın olası nedenleri şunlardır:

* VM 'de yapılandırılmış bir proxy yalnızca belirli bağlantı noktalarına izin verir.
* Bir güvenlik duvarı ayarı, gerekli bağlantı noktalarına ve adreslere erişimi engelledi.

#### <a name="resolution"></a>Çözüm

İletişim için uygun bağlantı noktalarına ve adreslere sahip olduğunuzdan emin olun. Bağlantı noktalarının ve adreslerin listesi için bkz. [ağınızı planlama](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Senaryo: geçici ortam sorunları nedeniyle yüklenemedi

Windows uzantısı için Log Analytics yüklemesi, başka bir yükleme veya yüklemeyi engelleyen bir eylem nedeniyle dağıtım sırasında başarısız oldu.

#### <a name="issue"></a>Sorun

Aşağıda, döndürülen hata iletilerinin örnekleri verilmiştir:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Nedeni

Bu hatanın olası nedenleri şunlardır:

* Başka bir yükleme devam ediyor.
* Sistem, şablon dağıtımı sırasında yeniden başlatılacak şekilde tetiklenir.

#### <a name="resolution"></a>Çözüm

Bu hata, doğası gereği geçicidir. Uzantıyı yüklemek için dağıtımı yeniden deneyin.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Senaryo: yükleme zaman aşımı

Windows uzantısı için Log Analytics yüklemesi bir zaman aşımı nedeniyle tamamlanmadı.

#### <a name="issue"></a>Sorun

Aşağıda, döndürülebilecek bir hata iletisine örnek verilmiştir:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Nedeni

Bu tür bir hata, VM 'nin yükleme sırasında ağır bir yük altında olması nedeniyle oluşur.

### <a name="resolution"></a>Çözüm

VM daha düşük bir yük altında olduğunda Windows uzantısı için Log Analytics aracısını yüklemeyi deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.