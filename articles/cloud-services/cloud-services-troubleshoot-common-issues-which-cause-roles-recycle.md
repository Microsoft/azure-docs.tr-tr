---
title: Bulut hizmeti rollerinin geri dönüşümlerine ilişkin yaygın nedenler | Microsoft Docs
description: Aniden geri dönüşümlü bir bulut hizmeti rolü önemli bir kesinti oluşmasına neden olabilir. Rollerin geri dönüştürülmesine neden olan bazı yaygın sorunlar aşağıda verilmiştir. Bu, kapalı kalma süresini azaltmanıza yardımcı olabilir.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154971"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Rollerin geri dönüştürülmesine neden olan yaygın sorunlar
Bu makalede, dağıtım sorunlarının bazı yaygın nedenleri ele alınmaktadır ve bu sorunları çözmenize yardımcı olacak sorun giderme ipuçları sunulmaktadır. Bir uygulamada sorun olduğunu belirten bir ifade, rol örneğinin başlayamadığını veya başlatma, meşgul ve durdurma durumları arasında geçiş yapar.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Çalışma zamanı bağımlılıkları eksik
Uygulamanızdaki bir rol, .NET Framework veya Azure yönetilen Kitaplığı 'nın parçası olmayan herhangi bir derlemeyi kullanıyorsa, bu derlemeyi uygulama paketine açıkça eklemeniz gerekir. Diğer Microsoft çerçevelerinin Azure 'da varsayılan olarak mevcut olmadığından emin olmak için aklınızda bulundurun. Rolünüz böyle bir çerçeveye dayanıyorsa, bu derlemeleri uygulama paketine eklemeniz gerekir.

Uygulamanızı oluşturmadan ve paketlemenize başlamadan önce aşağıdakileri doğrulayın:

* Visual Studio kullanıyorsanız, projenizde Azure SDK 'sının veya .NET Framework bir parçası olmayan her Başvurulmuş derleme için yereli **Kopyala** özelliğinin **true** olarak ayarlandığından emin olun.
* Web. config dosyasının, derleme öğesinde kullanılmayan derlemelere başvurmadığından emin olun.
* Her. cshtml dosyasının **derleme eylemi** **içerik**olarak ayarlanır. Bu, dosyaların pakette doğru görünmesini ve başvurulan diğer dosyaların pakette görünmesini sağlar.

## <a name="assembly-targets-wrong-platform"></a>Derleme yanlış platformu hedefliyor
Azure, 64 bitlik bir ortamdır. Bu nedenle, 32 bitlik bir hedef için derlenen .NET derlemeleri Azure 'da çalışmaz.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rol, başlatılırken veya durdurulurken işlenmemiş özel durumlar oluşturuyor
[Başlangıçta], [OnStop]ve [Çalıştırma] yöntemlerini içeren [roleentrypoint] sınıfının yöntemleri tarafından oluşturulan tüm özel durumlar işlenmemiş özel durumlardır. Bu yöntemlerin birinde işlenmeyen bir özel durum oluşursa, rol geri dönüşüme eklenir. Rol tekrar tekrar geri dönüştürüldükten sonra, her başlatma denemesinde işlenmeyen bir özel durum oluşturulabilir.

## <a name="role-returns-from-run-method"></a>Rol Run yönteminden geri döner
[Çalıştırma] yöntemi süresiz olarak çalışacak şekilde tasarlanmıştır. Kodunuz [Çalıştırma] yöntemini geçersiz kılıyorsa, süresiz olarak uyku moduna alınmalıdır. [Çalıştırma] yöntemi döndürülürse, rol geri dönüştürülür.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Yanlış DiagnosticsConnectionString ayarı
Uygulama Azure tanılama kullanıyorsa, hizmet yapılandırma dosyanızda `DiagnosticsConnectionString` yapılandırma ayarı belirtilmelidir. Bu ayar, Azure 'daki depolama hesabınıza bir HTTPS bağlantısı belirtmelidir.

Uygulama paketinizi Azure 'a `DiagnosticsConnectionString` dağıtmadan önce ayarınızı doğru olduğundan emin olmak için aşağıdakileri doğrulayın:  

* Bu `DiagnosticsConnectionString` ayar, Azure 'da geçerli bir depolama hesabına işaret eder.  
  Varsayılan olarak, bu ayar öykünülmüş depolama hesabına işaret eder, bu nedenle uygulama paketinizi dağıtmadan önce bu ayarı açıkça değiştirmeniz gerekir. Bu ayarı değiştirmeyin, rol örneği tanılama izleyicisini başlatmaya çalıştığında bir özel durum oluşturulur. Bu, rol örneğinin sonsuza kadar geri dönüştürülmesine neden olabilir.
* Bağlantı dizesi aşağıdaki [biçimde](../storage/common/storage-configure-connection-string.md)belirtilir. (Protokol HTTPS olarak belirtilmelidir.) *MyAccountName* değerini depolama hesabınızın adıyla ve *myaccountkey* değerini erişim anahtarınızla değiştirin:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Uygulamanızı Microsoft Visual Studio için Azure Araçları 'nı kullanarak geliştiriyorsanız, bu değeri ayarlamak için özellik sayfalarını kullanabilirsiniz.

## <a name="exported-certificate-does-not-include-private-key"></a>İçe aktarılmış sertifika özel anahtar içermiyor
SSL altında bir Web rolü çalıştırmak için, verdiğiniz yönetim sertifikanızın özel anahtarı içerdiğinden emin olmanız gerekir. Sertifikayı dışarı aktarmak için *Windows Sertifika Yöneticisi* 'ni kullanırsanız, **özel anahtarı dışarı aktar** seçeneğini için **Evet** ' i seçtiğinizden emin olun. Sertifika, şu anda desteklenen tek Biçim olan PFX biçiminde aktarılmalıdır.

## <a name="next-steps"></a>Sonraki adımlar
Cloud Services için daha fazla [sorun giderme makalesini](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) görüntüleyin.

[Kevin Williamson 'ın blog serisinde](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)daha fazla rol geri dönüşüm senaryosu görüntüleyin.

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Başlangıçta]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Çalıştırma]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
