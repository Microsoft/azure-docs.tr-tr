---
title: AzCopy ile v10 arasındaki yapılandırma ayarı (Azure depolama) | Microsoft Docs
description: Bu makale, AzCopy Ile v10 arasındaki yapılandırma ayarları için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509164"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy ile v10 arasındaki yapılandırma ayarları (Azure depolama)

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale, AzCopy ile v10 arasındaki 'yi yapılandırmak için kullanabileceğiniz ortam değişkenlerinin bir listesini içerir.

> [!NOTE]
> AzCopy kullanmaya başlamanıza yardımcı olacak içerik arıyorsanız, bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md).

## <a name="azcopy-v10-environment-variables"></a>AzCopy ile v10 arasındaki ortam değişkenleri

Aşağıdaki tabloda her bir ortam değişkeni açıklanmakta ve değişkeni kullanmanıza yardımcı olabilecek içeriklere yönelik bağlantılar sağlanmaktadır.

| Ortam değişkeni | Açıklama |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services erişim anahtarı. Amazon Web Services ile yetkilendirmek için bir anahtar sağlar. [AzCopy kullanarak Amazon S3 'Ten Azure depolama 'ya veri kopyalama](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services gizli erişim anahtarı, Amazon Web Services yetkilendirmek için bir gizli anahtar sağlar. [AzCopy kullanarak Amazon S3 'ten Azure depolama 'ya veri kopyalama](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Kullanılacak Azure Active Directory uç noktası. Bu değişken yalnızca otomatik oturum açma için kullanılır; Login komutunu çağırırken lütfen bunun yerine komut satırı bayrağını kullanın. |
| AZCOPY_AUTO_LOGIN_TYPE | Bu değişkeni,, `DEVICE` `MSI` veya olarak ayarlayın `SPN` . Bu değişken, komutunu kullanmadan yetkilendirme yeteneği sağlar `azcopy login` . Bu mekanizma, işletim sisteminizin Linux *kimlik anahtarlığı* gibi gizli bir mağazaya sahip olmadığı durumlarda faydalıdır. Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_BUFFER_GB | Dosya indirme ve karşıya yükleme sırasında AzCopy 'in kullanmasını istediğiniz en fazla sistem belleği miktarını belirtin. Bu değeri gigabayt (GB) cinsinden ifade edin. Bkz. [bellek kullanımını iyileştirme](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Varsayılan olarak Windows üzerinde AzCopy, ana bilgisayar düzeyinde ara sunucu aramalarını önbelleğe alır (URL yolu hesaba katmaz). Önbelleği devre dışı bırakmak için ' true ' değerinden başka bir değere ayarlayın. |
| AZCOPY_CONCURRENCY_VALUE | Gerçekleşebileceğini eşzamanlı isteklerin sayısını belirtir. Bu değişkeni, aktarım hızını artırmak için kullanabilirsiniz. Bilgisayarınızda 5 ' ten az CPU varsa, bu değişkenin değeri olarak ayarlanır `32` . CPU dana fazlaysa, varsayılan değer CPU sayısının 16 katına eşittir. Bu değişkenin en büyük varsayılan değeri `3000` , ancak bu değeri el ile veya daha düşük bir şekilde ayarlayabilirsiniz. Bkz. [eşzamanlılık artırma](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Eşzamanlı olarak başladığımız dosya sayısını denetleyerek herhangi bir zamanda devam eden dosya sayısını (yaklaşık) geçersiz kılar. |
| AZCOPY_CONCURRENT_SCAN | Tarama sırasında kullanılan paralellik derecesini (max) denetler. Yalnızca Azure dosyalarını/Bloblarını ve yerel dosya sistemlerini içeren paralelleştirilmiş numaralandırıcıları etkiler. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | AzCopy, Azure Stack gibi özel ortamlara uyum sağlaması için hizmet API sürümünü geçersiz kılar. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Yalnızca Azure Blob 'Ları kaynak olduğunda geçerlidir. Eşzamanlı tarama daha hızlıdır, ancak hiyerarşik listeleme API 'sini kullanır ve bu da daha fazla IOs/maliyete yol açabilir. Performansın feda olması için ' true ' değerini belirtin, ancak maliyetten tasarruf edin. |
| AZCOPY_JOB_PLAN_LOCATION | Bir disk doldurmaktan kaçınmak için iş planı dosyalarının (ilerleme izleme ve sürdürme için kullanılan) depolandığı geçersiz kılar. |
| AZCOPY_LOG_LOCATION | Bir diski doldurmaktan kaçınmak için günlük dosyalarının depolanacağı konumu geçersiz kılar. |
| AZCOPY_MSI_CLIENT_ID | Kullanıcı tarafından atanan yönetilen kimliğin istemci KIMLIĞI. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `MSI` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | Kullanıcı tarafından atanan yönetilen kimliğin nesne KIMLIĞI. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `MSI` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | Kullanıcı tarafından atanan yönetilen kimliğin kaynak KIMLIĞI. Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | Sayfa Blobları için üretilen iş, hizmet limitleriyle eşleşecek şekilde otomatik olarak ayarlanmalıdır mi? Varsayılan değer doğru değeridir. Devre dışı bırakmak için ' false ' olarak ayarla |
| AZCOPY_PARALLEL_STAT_FILES | Yerel dosya sistemi taranırken AzCopy 'in paralel ' iş parçacıkları ' üzerinde dosya özelliklerini arama yapmasına neden olur.  İş parçacıkları AZCOPY_CONCURRENT_SCAN tarafından tanımlanan havuzdan çizilir.  Bunu true olarak ayarlamak, Linux 'ta tarama performansını iyileştirebilir.  Windows 'da gerekli değildir veya önerilmez. |
| AZCOPY_SHOW_PERF_STATES | Ayarlanırsa, her şeye, ekran çıktılarına göre parça sayısı dahil edilir |
| AZCOPY_SPA_APPLICATION_ID | Hizmet sorumlusunun uygulama kaydının uygulama KIMLIĞI. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `SPN` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Bir sertifikanın parolası. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `SPN` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Bir sertifika dosyasının göreli veya tam yolu. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `SPN` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | İstemci parolası. Ne zaman `AZCOPY_AUTO_LOGIN_TYPE` ayarlandığında kullanın `SPN` . Bkz. [gizli depo olmadan yetkilendirme](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | OAuth cihaz etkileşimli oturum açma için kullanılacak Azure Active Directory kiracı KIMLIĞI. Bu değişken yalnızca otomatik oturum açma için kullanılır; Login komutunu çağırırken lütfen bunun yerine komut satırı bayrağını kullanın. |
| AZCOPY_TUNE_TO_CPU | AzCopy 'in eşzamanlılık düzeyini (örneğin, kıyaslama komutunda) tekrar ayarlama sırasında CPU kullanımını hesaba katmasını engellemek için false olarak ayarlayın. |
| AZCOPY_USER_AGENT_PREFIX | Telemetri amaçları için kullanılan varsayılan AzCopy kullanıcı aracısına bir ön ek ekleyin. Otomatik olarak bir boşluk eklenir. |
| GOOGLE_APPLICATION_CREDENTIALS | Hizmet hesabı anahtar dosyasının mutlak yolu, Google Cloud Storage ile yetki vermek için bir anahtar sağlar. [AzCopy (Önizleme) kullanarak Google bulut depolamadaki verileri Azure depolama 'ya kopyalama](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | AzCopy için proxy ayarlarını yapılandırır. Bu değişkeni, proxy IP adresi ve proxy bağlantı noktası numarası olarak ayarlayın. Örneğin, `xx.xxx.xx.xxx:xx`. AZCopy’yi Windows’da çalıştırıyorsanız AzCopy ara sunucu ayarlarını otomatik olarak algılar, bu nedenle bu ayarı Windows’da kullanmanız gerekmez. Bu ayarı Windows’da kullanmayı seçerseniz otomatik algılama geçersiz kılınır. Bkz. [proxy ayarlarını yapılandırma](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

AzCopy ara sunucu ayarlarını yapılandırmak için `HTTPS_PROXY` ortam değişkenini ayarlayın. AZCopy’yi Windows’da çalıştırıyorsanız AzCopy ara sunucu ayarlarını otomatik olarak algılar, bu nedenle bu ayarı Windows’da kullanmanız gerekmez. Bu ayarı Windows’da kullanmayı seçerseniz otomatik algılama geçersiz kılınır.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | Komut isteminde şunu kullanın: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> PowerShell kullanımı: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Şu anda AzCopy, NTLM veya Kerberos ile kimlik doğrulaması gerektiren proxy 'leri desteklemiyor.

### <a name="bypassing-a-proxy"></a>Proxy atlama

Windows üzerinde AzCopy çalıştırıyorsanız ve bu sunucuya _hiçbir_ proxy (ayarları otomatik olarak algılama yerine) kullanmayı söylemek istiyorsanız bu komutları kullanın. Bu ayarlarla AzCopy, herhangi bir proxy 'yi aramaz veya kullanmayı denemez.

| İşletim sistemi | Ortam | Komutlar  |
|--------|-----------|----------|
| **Windows** | Komut istemi (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Diğer işletim sistemlerinde, ara sunucu kullanmak istiyorsanız HTTPS_PROXY değişkenini yok etmeniz yeterlidir.

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Azure depolama ile AzCopy ile v10 arasındaki performansını iyileştirin](storage-use-azcopy-optimize.md)
- [Günlük dosyalarını kullanarak Azure Storage 'da AzCopy Ile v10 arasındaki sorunlarını giderme](storage-use-azcopy-configure.md)
