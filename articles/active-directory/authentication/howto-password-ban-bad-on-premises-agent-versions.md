---
title: Parola koruma Aracısı yayın geçmişi-Azure Active Directory
description: Belgeler sürüm sürümü ve davranış değişikliği geçmişi
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ad7199360ca0acc8674f7a4e34bd206f8b335f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648789"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD parola koruma Aracısı sürümü geçmişi

## <a name="121720"></a>1.2.172.0

Yayın tarihi: 22 2021

Şirket içi Azure AD parola koruma aracılarının GA sürümlerinin yayımlanmasından bu yana neredeyse iki yıl sonra. Yeni bir güncelleştirme kullanıma sunuldu-aşağıdaki açıklamaları değiştirin bölümüne bakın. Ürün hakkında bize geri bildirim vermiş herkes için teşekkür ederiz. 

* DC Aracısı ve proxy aracısı yazılımı artık .NET 4.7.2 'in yüklenmesini gerektirir.
  * .NET 4.7.2 henüz yüklenmemişse, [Windows için .NET Framework 4.7.2 çevrimdışı yükleyicisi](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)' nde bulunan yükleyiciyi indirip çalıştırın.
* AzureADPasswordProtection PowerShell modülü artık DC Aracısı yazılımı tarafından da yüklenir.
* İki yeni sistem durumu ile ilgili PowerShell cmdlet 'leri eklendi: Test-AzureADPasswordProtectionDCAgent ve test-AzureADPasswordProtectionProxy.
* AzureADPasswordProtection DC aracısı parola filtresi dll 'si artık lsass.exe PPL modunda çalışacak şekilde yapılandırıldığı makinelerde yüklenir ve çalıştırılır.
* Parola algoritmasına, en fazla beş karakterden kısa bir süre sonra yanlış kabul edilmesine izin veren hata onarımı.
  * Bu hata yalnızca şirket içi AD en az parola uzunluğu ilkeniz ilk yerde beşten az karakter parolaya izin verecek şekilde yapılandırıldıysa geçerlidir.
* Diğer küçük hata düzeltmeleri.

Yeni yükleyiciler yazılımın eski sürümlerini otomatik olarak yükseltir. Hem DC aracısını hem de ara sunucu yazılımını tek bir makineye yüklediyseniz (yalnızca test ortamları için önerilir), her ikisini de aynı anda yükseltmeniz gerekir.

Bir etki alanı veya orman içinde DC aracısının ve proxy yazılımının eski ve daha yeni sürümlerini çalıştırmak, ancak tüm aracıların en iyi yöntem olarak en son sürüme yükseltilmesini öneririz. Aracı yükseltmelerinden herhangi bir sıralama desteklenir-yeni DC aracıları eski proxy aracılarıyla iletişim kurabilir ve eski DC aracıları daha yeni proxy aracılarıyla iletişim kurabilir.

## <a name="121250"></a>1.2.125.0

Yayın tarihi: Mart 22 2019

* Olay günlüğü iletilerinde küçük yazım hatası hatalarını çözme
* EULA anlaşmasını son genel kullanılabilirlik sürümüne Güncelleştir

> [!NOTE]
> Build 1.2.125.0, genel kullanılabilirlik yapıdır. Herkesin ürünle ilgili geri bildirimde bulunmak için tekrar teşekkür ederiz!

## <a name="121160"></a>1.2.116.0

Yayın tarihi: 3/13/2019

* Get-AzureADPasswordProtectionProxy ve Get-AzureADPasswordProtectionDCAgent cmdlet 'leri artık yazılım sürümünü ve geçerli Azure kiracısını aşağıdaki sınırlamalara bildirir:
  * Yazılım sürümü ve Azure kiracı verileri yalnızca, sürüm 1.2.116.0 veya üstünü çalıştıran DC aracıları ve proxy 'lerde kullanılabilir.
  * Proxy veya ormanın bir yeniden kaydı (veya yenilemesi) gerçekleşene kadar Azure kiracı verileri raporlanmayabilir.
* Proxy hizmeti artık .NET 4,7 'in yüklü olmasını gerektirir.
  * .NET 4,7 zaten yüklü değilse, [Windows için .NET Framework 4,7 çevrimdışı yükleyicisinde](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)bulunan yükleyiciyi indirip çalıştırın.
  * Sunucu Çekirdeği sistemlerinde, başarılı olması için/q bayrağını .NET 4,7 yükleyicisine geçirmek gerekebilir.
* Proxy hizmeti artık otomatik yükseltmeyi destekliyor. Otomatik yükseltme, proxy hizmeti ile yan yana yüklenen Microsoft Azure AD Connect Agent Güncelleştirici hizmetini kullanır. Otomatik yükseltme varsayılan olarak açık.
* Set-AzureADPasswordProtectionProxyConfiguration cmdlet 'i kullanılarak otomatik yükseltme etkinleştirilebilir veya devre dışı bırakılabilir. Geçerli ayar Get-AzureADPasswordProtectionProxyConfiguration cmdlet 'i kullanılarak sorgulanabilir.
* DC Aracısı hizmeti için hizmet ikilisi AzureADPasswordProtectionDCAgent.exe olarak yeniden adlandırıldı.
* Proxy hizmeti için hizmet ikilisi AzureADPasswordProtectionProxy.exe olarak yeniden adlandırıldı. Bir üçüncü taraf güvenlik duvarı kullanımda olduğunda Güvenlik Duvarı kurallarının de değiştirilmesi gerekebilir.
  * NOTE: önceki bir proxy yüklemede bir http proxy yapılandırma dosyası kullanılıyorsa, bu yükseltmeden sonra yeniden adlandırılması gerekir ( *proxyservice.exe.config* *AzureADPasswordProtectionProxy.exe.config*).
* Tüm zaman sınırlı işlevsellik denetimleri DC aracısından kaldırılmıştır.
* Önemsiz hata düzeltmeleri ve günlüğe kaydetme geliştirmeleri.

## <a name="12650"></a>1.2.65.0

Yayın tarihi: 1 Şubat 2019

Değişikliklerine

* DC Aracısı ve proxy hizmeti artık sunucu çekirdeğinde destekleniyor. En düşük işletim sistemi gereksinimleri, daha önce değiştirilmeden önce: DC aracıları için Windows Server 2012 ve proxy 'ler için Windows Server 2012 R2.
* Register-AzureADPasswordProtectionProxy ve Register-AzureADPasswordProtectionForest cmdlet 'leri artık cihaz kodu tabanlı Azure kimlik doğrulama modlarını desteklemektedir.
* Get-AzureADPasswordProtectionDCAgent cmdlet 'i karıştırılmış ve/veya geçersiz hizmet bağlantı noktalarını yoksayacak. Bu değişiklik, etki alanı denetleyicilerinin çıktıda bazen birden çok kez göstereceği hatayı düzeltir.
* Get-AzureADPasswordProtectionSummaryReport cmdlet 'i karıştırılmış ve/veya geçersiz hizmet bağlantı noktalarını yoksayacak. Bu değişiklik, etki alanı denetleyicilerinin çıktıda bazen birden çok kez göstereceği hatayı düzeltir.
* Proxy PowerShell modülü artık%ProgramFiles%\WindowsPowerShell\Modules. adresinden kaydedilir Makinenin PSModulePath ortam değişkeni artık değiştirilmez.
* Bir ormanda veya etki alanında kayıtlı proxy 'leri bulmaya yardımcı olmak için yeni bir Get-AzureADPasswordProtectionProxy cmdlet 'i eklenmiştir.
* DC Aracısı, parola ilkelerini ve diğer dosyaları çoğaltmak için SYSVOL paylaşımında yeni bir klasör kullanır.

   Eski klasör konumu:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Yeni klasör konumu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Yanlış pozitif "yalnız bırakılmış GPO" uyarılarını önlemek için bu değişiklik yapılmıştır.)

   > [!NOTE]
   > Eski klasör ve yeni klasör arasında veri geçişi veya paylaşımı yapılmaz. Eski DC Aracısı sürümleri, bu sürüme veya daha yenisine yükseltilene kadar eski konumu kullanmaya devam edecektir. Tüm DC aracıları sürüm 1.2.65.0 veya üzerini çalıştırıyorsa, eski SYSVOL klasörü el ile silinebilir.

* DC Aracısı ve proxy hizmeti artık ilgili hizmet bağlantı noktalarının karıştırılmış kopyalarını algılayacak ve silecek.
* Her DC Aracısı, etki alanındaki hem DC Aracısı hem de proxy hizmeti bağlantı noktaları için karışık ve eski hizmet bağlantı noktalarını düzenli aralıklarla siler. Hem DC Aracısı hem de proxy hizmeti bağlantı noktaları, sinyal zaman damgası yedi günden daha eskiyse eski olarak değerlendirilir.
* DC Aracısı artık gerektiğinde orman sertifikasını yeniler.
* Proxy hizmeti artık gerektiği şekilde proxy sertifikasını yenileyecek.
* Parola Doğrulama algoritmasına yönelik güncelleştirmeler: genel yasaklanmış parola listesi ve müşteriye özgü yasaklanmış parola listesi (yapılandırıldıysa), parola doğrulamaları öncesinde birleştirilir. Yalnızca genel ve müşteriye özgü listeden belirteçler içeriyorsa, belirli bir parola artık reddedilebilir (başarısız veya yalnızca denetim). Olay günlüğü belgeleri bunu yansıtacak şekilde güncelleştirilmiştir; bkz. [Azure AD parola korumasını izleme](howto-password-ban-bad-on-premises-monitor.md).
* Performans ve sağlamlık düzeltmeleri
* İyileştirilmiş günlük kaydı

> [!WARNING]
> Zamana sınırlı işlevsellik: Bu sürümdeki DC Aracısı hizmeti (1.2.65.0), 1 Eylül 2019 itibariyle parola doğrulama isteklerini işlemeyi durdurur.  Önceki sürümlerde DC Aracısı Hizmetleri (aşağıdaki listeye bakın) 1 Temmuz 2019 itibariyle işlemeyi durdurur. Tüm sürümlerdeki DC Aracısı hizmeti, 10021 olaylarını iki ay içindeki yönetici olay günlüğüne günlüğe kaydeder ve bu son tarihleri önde kalır. Tüm zaman sınırı kısıtlamaları yakında çıkacak GA sürümünde kaldırılacak. Proxy aracısı hizmeti herhangi bir sürümde zaman sınırlı değildir, ancak sonraki tüm hata düzeltmelerinden ve diğer geliştirmelerden faydalanmak için hala en son sürüme yükseltilmelidir.

## <a name="12250"></a>1.2.25.0

Yayın tarihi: 1 Kasım 2018

Düzeltilen

* DC Aracısı ve proxy hizmeti, sertifika güven hatalarından dolayı artık başarısız olmamalıdır.
* DC Aracısı ve proxy hizmeti, FIPS uyumlu makineler için düzeltmeler de vardır.
* Proxy hizmeti artık yalnızca TLS 1,2-ağ ortamında düzgün çalışacaktır.
* Düşük performans ve sağlamlık düzeltmeleri
* İyileştirilmiş günlük kaydı

Değişikliklerine

* Proxy hizmeti için gereken en düşük işletim sistemi düzeyi artık Windows Server 2012 R2 'dir. DC Aracısı hizmeti için gerekli en düşük işletim sistemi düzeyi Windows Server 2012 ' de kalır.
* Proxy hizmeti artık .NET sürüm 4.6.2 gerektirir.
* Parola doğrulama algoritması genişletilmiş bir karakter normalleştirme tablosu kullanır. Bu değişiklik, önceki sürümlerde kabul edilen parolaların reddedilme oluşmasına neden olabilir.

## <a name="12100"></a>1.2.10.0

Yayın tarihi: 17 Ağustos 2018

Düzeltilen

* Register-AzureADPasswordProtectionProxy ve Register-AzureADPasswordProtectionForest artık Multi-Factor Authentication 'ı destekliyor
* Register-AzureADPasswordProtectionProxy, şifreleme hatalarını önlemek için etki alanında WS2012 veya üzeri bir etki alanı denetleyicisi gerektirir.
* DC Aracısı hizmeti, başlangıçta Azure 'dan yeni bir parola ilkesi istemek üzere daha güvenilirdir.
* DC Aracısı hizmeti, gerektiğinde Azure 'dan her saat yeni bir parola ilkesi isteyecek, ancak şimdi rastgele seçilen bir başlangıç zamanında bunu yapacaktır.
* DC Aracısı hizmeti, çoğaltma olarak yükseltilmesinden önce bir sunucuya yüklendiğinde yeni DC tanıtımına yönelik sınırsız gecikmeye neden olmaz.
* DC Aracısı hizmeti artık "Windows Server Active Directory 'da parola korumasını etkinleştir" yapılandırma ayarında yer alacak
* Hem DC Aracısı hem de proxy yükleyicileri, gelecekteki sürümlere yükseltirken yerinde yükseltmeyi de destekleyecektir.

> [!WARNING]
> 1.1.10.3 sürümünden yerinde yükseltme desteklenmez ve yükleme hatasına neden olur. Sürüm 1.2.10 veya sonraki bir sürüme yükseltmek için önce DC Aracısı ve proxy hizmeti yazılımını tamamen kaldırmanız ve sonra yeni sürümü sıfırdan yüklemeniz gerekir. Azure AD parola koruma proxy hizmeti 'nin yeniden kaydı gereklidir.  Ormanın yeniden kaydedilmesi gerekli değildir.

> [!NOTE]
> DC Aracısı yazılımının yerinde yükseltmeleri için yeniden başlatma gerekir.

* DC Aracısı ve proxy hizmeti artık yalnızca FIPS uyumlu algoritmalar kullanmak üzere yapılandırılmış bir sunucuda çalışmayı destekliyor.
* Düşük performans ve sağlamlık düzeltmeleri
* İyileştirilmiş günlük kaydı

## <a name="11103"></a>1.1.10.3

Yayın tarihi: 15 Haziran 2018

İlk genel önizleme sürümü

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD parola korumasını dağıtma](howto-password-ban-bad-on-premises-deploy.md)
