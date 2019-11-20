---
title: Şirket içi Azure AD parola koruması Aracı sürüm yayımlama geçmişi - Azure Active Directory
description: Belgeler sürüm ve davranışı değişiklik geçmişi
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c024954053588537ac3363703876f716a38f41d9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702942"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD parola koruması Aracı sürüm geçmişi

## <a name="121250"></a>1.2.125.0

Yayın Tarihi: 3/22/2019

* Olay günlüğü iletilerinde Önemsiz yazım hatalarını Düzelt
* EULA anlaşmasını genel kullanılabilirlik, son sürüme güncelleştirme

> [!NOTE]
> Derleme 1.2.125.0 Genel kullanılabilirlik yapıdır. Ürün geri bildirim sağlanan herkes için tekrar teşekkür ederiz!

## <a name="121160"></a>1.2.116.0

Yayın Tarihi: 3/13/2019

* Aşağıdaki kısıtlamalarla rapor yazılım sürümü ve geçerli Azure Kiracı artık Get-AzureADPasswordProtectionProxy ve Get-AzureADPasswordProtectionDCAgent cmdlet'leri:
  * Yazılım sürümü ve Azure kiracısına ilişkin veriler yalnızca DC aracılar ve proxy'ler 1.2.116.0 sürümünü çalıştıran veya sonraki kullanılabilir.
  * Azure Kiracı verilerini bir yeniden kayıt (veya yenileme) proxy kadar bildirilmeyebilir veya orman oluştu.
* Proxy Hizmeti, artık .NET 4.7 yüklü olmasını gerektirir.
  * .NET 4.7 zaten tamamen güncelleştirilmiş bir Windows sunucusuna yüklenmesi gerekir. Durum bu değilse, indirmek ve bulunan yükleyiciyi çalıştırın [Windows için .NET Framework 4.7 çevrimdışı yükleyici](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Sunucu Çekirdeği sistemlerine başarılı şekilde almak için .NET 4.7 yükleyici için /q bayrak geçirmek için gerekli olabilir.
* Proxy Hizmeti, artık otomatik yükseltmeyi destekler. Otomatik yükseltme yüklü yan yana Proxy hizmeti olan Microsoft Azure AD Connect aracı Updater hizmeti kullanır. Otomatik yükseltme varsayılan olarak açıktır.
* Otomatik yükseltme etkinleştirilebilir veya Set-AzureADPasswordProtectionProxyConfiguration cmdlet'i kullanılarak devre dışı. Geçerli ayarı, Get-AzureADPasswordProtectionProxyConfiguration cmdlet'ini kullanarak sorgulanabilir.
* DC Aracı hizmeti için hizmet ikili AzureADPasswordProtectionDCAgent.exe için yeniden adlandırıldı.
* Proxy Hizmeti için hizmet ikili AzureADPasswordProtectionProxy.exe için yeniden adlandırıldı. Güvenlik duvarı kuralları, bir üçüncü taraf güvenlik duvarı kullanımda olup olmadığını buna uygun olarak değiştirilmesi gerekebilir.
  * Not: bir http proxy yapılandırma dosyası önceki Proxy kullanıldıysa yüklemek, yeniden adlandırılması gerekir (gelen *proxyservice.exe.config* için *AzureADPasswordProtectionProxy.exe.config*) bundan sonra yükseltin.
* Tüm süre sınırlı işlevsellik denetimleri DC Aracısı'ndan kaldırıldı.
* Küçük hata düzeltmeleri ve günlüğe kaydetme geliştirmeleri.

## <a name="12650"></a>1.2.65.0

Yayın Tarihi: 2/1/2019

Değişiklikler:

* DC aracı ve proxy hizmeti sunucu Çekirdeğinde artık desteklenmektedir. Önce değiştirilmemiştir Mininimum işletim sistemi gereksinimleri şunlardır: Windows Server 2012 DC aracılar ve proxy'leri için Windows Server 2012 R2.
* Register-AzureADPasswordProtectionProxy ve kayıt AzureADPasswordProtectionForest cmdlet'leri artık cihaz kod tabanlı Azure kimlik doğrulama modlarını destekler.
* Get-AzureADPasswordProtectionDCAgent cmdlet karıştırılmış ve/veya geçersiz hizmet bağlantı noktaları göz ardı eder. Bu, burada etki alanı denetleyicileri bazen birden çok kez çıkışında gösterilmesini hatayı düzeltir.
* Get-AzureADPasswordProtectionSummaryReport cmdlet karıştırılmış ve/veya geçersiz hizmet bağlantı noktaları göz ardı eder. Bu, burada etki alanı denetleyicileri bazen birden çok kez çıkışında gösterilmesini hatayı düzeltir.
* Proxy powershell modülünü % ProgramFiles%\WindowsPowerShell\Modules artık kaydedilmiştir. Makinenin PSModulePath ortam değişkeni artık değiştirildi.
* Bir orman veya etki alanında kayıtlı proxy'ler bulunuyor yardımcı olmak için yeni cmdlet Get-AzureADPasswordProtectionProxy eklendi.
* DC Aracısı'nı, parola ilkeleri ve diğer dosyaları çoğaltmak için sysvol paylaşımı içinde yeni bir klasör kullanır.

   Eski klasör konumu:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Yeni klasör konumu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Bu değişiklik "hatalı pozitif sonuç GPO artık" uyarılarından kaçınmak için yapılmıştır.)

   > [!NOTE]
   > Eski klasör ve yeni klasör arasında herhangi bir geçiş veya verilerin paylaşımını gerçekleştirilir. Eski DC Aracı sürümleri, bu sürümü veya sonraki bir sürüme yükseltildiğinde kadar eski konumu kullanmak üzere devam eder. Daha sonra eski sysvol klasörünü el ile silinebilir veya sürüm 1.2.65.0 tüm DC aracılar çalıştırıldıktan sonra.

* DC aracı ve proxy hizmeti, artık algılayın ve bunların ilgili hizmet bağlantı noktaları karıştırılmış bir kopyasını silebilirsiniz.
* Her bir DC aracı, düzenli aralıklarla karıştırılmış ve eski hizmet bağlantı noktaları hem DC aracı ve proxy hizmet bağlantı noktaları için kendi etki alanındaki siler. Her iki DC aracı ve proxy hizmet bağlantı noktaları, sinyal zaman damgası yedi günden daha eskiyse eski olarak kabul edilir.
* DC aracı şimdi, gerektiğinde orman sertifika yenilenir.
* Proxy Hizmeti proxy sertifikası artık gerektiği şekilde yenilenir.
* Parola doğrulama algoritması güncelleştirmeleri: Genel yasaklı parola listesi ve (yapılandırılmışsa) müşteriye özgü yasaklı parola listesi önce parola doğrulamaları birleştirilir. Verilen parola artık reddedildi (başarısız veya yalnızca denetim) belirteçleri genel ve müşteriye özgü listesinden içeriyorsa. Olay günlüğü belgeler, bu yansıtacak şekilde güncelleştirildi; Lütfen [İzleyici Azure AD parola koruması](howto-password-ban-bad-on-premises-monitor.md).
* Performans ve sağlamlık düzeltmeleri
* Gelişmiş günlük kaydı

> [!WARNING]
> Süre sınırlı işlevsellik: 1 Eylül 2019 tarihinde parola doğrulama isteği işleme (1.2.65.0) bu sürümde DC aracı hizmetini durdurur.  DC Aracısı hizmetlerinin önceki sürümlerinde (aşağıdaki listeye bakın), 1 Temmuz 2019'den itibaren işlemi durdurur. DC Aracı hizmeti tüm sürümlerde 10021 olayları, bu son tarihleri önde gelen iki ay içinde yönetici olay günlüğüne kayıt. Tüm zaman sınırı kısıtlamalar kaldırılır GA sürümünde gelecek. Proxy Aracısı hizmeti herhangi bir sürümle süre sınırlı değildir, ancak sonraki tüm hata düzeltmeleri ve diğer iyileştirmeleri yararlanmak için en son sürüme hala yükseltilmelidir.

## <a name="12250"></a>1.2.25.0

Yayın Tarihi: 11/01/2018

Düzeltmeleri:

* DC aracı ve proxy hizmeti artık sertifika güven hataları nedeniyle başarısız olması.
* DC aracı ve proxy hizmeti FIPS uyumlu makineler için ek düzeltmelere sahip.
* Proxy hizmeti artık düzgün şekilde TLS 1.2 yalnızca ağ ortamında çalışır.
* Küçük bir performans ve sağlamlık düzeltmeleri
* Gelişmiş günlük kaydı

Değişiklikler:

* Gereken en düşük işletim sistemi düzeyinde Proxy Hizmeti için artık Windows Server 2012 R2. DC Aracı hizmeti için en düşük gerekli işletim sistemi düzeyi Windows Server 2012 kalır.
* Proxy Hizmeti artık .NET sürüm 4.6.2 gerektirir.
* Parola doğrulama algoritması bir Genişletilmiş karakter normalleştirme tablosunu kullanır. Bu, önceki sürümlerde kabul edildi reddediliyor parolalarda neden olabilir.

## <a name="12100"></a>1.2.10.0

Yayın Tarihi: 8/17/2018

Düzeltmeleri:

* Register-AzureADPasswordProtectionProxy ve kayıt AzureADPasswordProtectionForest artık çok faktörlü kimlik doğrulaması desteği
* WS2012 veya üzeri etki alanı denetleyicisi etki alanında şifreleme hataları önlemek için kayıt AzureADPasswordProtectionProxy gerektirir.
* DC Aracısı hizmetini yeni bir parola ilkesi Azure'dan başlangıçta isteme hakkında daha güvenilir oldu.
* DC Aracısı hizmetini yeni bir parola ilkesi Azure'dan saatte gerekirse ister, ancak bir rastgele seçilen başlangıç zamanında artık bunu yapar.
* DC Aracısı hizmeti artık yeni DC tanıtım bir kopya olarak, yükseltmeden önce bir sunucu üzerinde yüklü olduğunda, belirsiz bir gecikme neden olur.
* DC Aracısı hizmeti, "Windows Server Active Directory parola korumasını etkinleştir" yapılandırma ayarı artık dokunmaz
* Sonraki sürümlere yükseltme yapılırken, her iki DC aracı ve proxy yükleyici yerinde yükseltme artık destekleyecektir.

> [!WARNING]
> 1\.1.10.3 sürümünden yerinde yükseltme desteklenmez ve bir yükleme hataya neden olur. Çok 1.2.10 sürüme yükseltin veya sonraki sürümlerde, önce tamamen DC aracı ve proxy hizmeti yazılımı kaldırın, ardından gerekir sıfırdan yeni sürümünü yükleyin. Azure AD parola koruması Proxy Hizmeti yeniden kayıt gereklidir.  Orman yeniden kaydolmak için gerekli değildir.

> [!NOTE]
> Yerinde yükseltme DC Aracısı yazılım yeniden başlatma gerektirir.

* DC aracı ve proxy hizmet desteği yalnızca FIPS uyumlu algoritmalar kullanmak için yapılandırılmış bir sunucuda çalıştırmak.
* Küçük bir performans ve sağlamlık düzeltmeleri
* Gelişmiş günlük kaydı

## <a name="11103"></a>1.1.10.3

Yayın Tarihi: 6/15/2018

İlk genel Önizleme sürümü

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD parola koruması dağıtma](howto-password-ban-bad-on-premises-deploy.md)
