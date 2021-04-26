---
title: Azure Stack Edge Pro 2103 sürüm notları
description: Azure Stack Edge Pro çalıştıran 2103 sürümü için kritik açık sorunları ve çözümleri açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 4d2a345ed49fae2e1d77b3c5da44b305d069874e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729465"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Azure Stack Edge 2103 sürüm notları

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Aşağıdaki sürüm notları, Azure Stack Edge cihazlarınız için kritik açık sorunları ve 2103 sürümü için çözülmüş sorunları belirler. Bu sürüm notları Azure Stack Edge Pro GPU, Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazları için geçerlidir. Belirli bir modele karşılık gelen özellikler ve sorunlar, uygulanabilir her yerde çağırılır.

Sürüm notları sürekli olarak güncelleştirilir ve geçici bir çözüm gerektiren kritik sorunlar keşfedildiğinde eklenirler. Cihazınızı dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Bu makale, **2.2.1540.2890** yazılım sürüm numarası ile eşleşen **Azure Stack Edge 2103** sürümü için geçerlidir. Bu yazılım, en az Azure Stack Edge 2010 (2.1.1377.2170) yazılımı çalıştırıyorsanız cihazınıza uygulanabilir.

## <a name="whats-new"></a>Yenilikler

Azure Stack Edge 2103 sürümünde aşağıdaki yeni özellikler mevcuttur. 
 
- **Sanal makineler Için yeni özellikler** -Bu sürümden başlayarak, sanal makinelerde aşağıdaki yönetim işlemlerini Azure Portal aracılığıyla gerçekleştirebilirsiniz:
    - Mevcut bir VM 'ye birden çok ağ arabirimi ekleyin veya kaldırın.
    - Mevcut bir VM 'ye birden çok disk ekleyin veya kaldırın.
    - VM 'yi yeniden boyutlandırın.
    - Bir Windows veya Linux VM dağıtımı yaparken özel veriler ekleyin.

  Ayrıca, [CIHAZıNıZDAKI VM konsoluna BAĞLANıP](azure-stack-edge-gpu-connect-virtual-machine-console.md) VM sorunlarını giderebilirsiniz.

- **Https aracılığıyla PowerShell arabirimine bağlanma** -Bu sürümden itibaren, bir uzak PowerShell oturumunu artık *http* üzerinden bir cihaza açamazsınız. Varsayılan olarak, *https* tüm oturumlarda kullanılacaktır. Daha fazla bilgi için bkz. Cihazınızın [PowerShell arabirimine bağlanma](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .

- **Işlem geliştirmeleri** -aşağıdakiler dahil olmak üzere çeşitli geliştirmeler ve geliştirmeler yapılmıştır:

    - **Genel işlem platformu kalitesi**. Bu sürümde, genel işlem platformu kalitesini geliştirmek için hata düzeltmeleri vardır. [2103 sürümünde düzeltilen sorunlar](#issues-fixed-in-2103-release)bölümüne bakın. 
    - **İşlem platformu bileşenleri**. Işlem VM görüntüsüne güvenlik güncelleştirmeleri uygulandı. IoT Edge ve Kubernetes sürümleri için Azure Arc de güncelleştirildi.
    - **Tanılama**. Kaynak ve ağ koşullarını denetlemek için yeni bir API yayımlanmıştır. Cihazın PowerShell arabirimine bağlanabilir ve bu `Test-HcsKubernetesStatus` komutu kullanarak cihazın ağ hazırlığını doğrulayabilirsiniz.
    - Geliştirilmiş hata ayıklama için yol açacak **günlük koleksiyonu** . 
    - İşlem IP adresleri için IP adresi çakışmalarını saptamanıza olanak tanıyan, **uyarma altyapısı** . 
    - Kubernetes ve yerel Azure Resource Manager **iş yükünü karıştırın** . 

- **Varsayılan olarak öngörülü günlük kaydı** -bu sürüm başlatıldığında, öngörülü günlük toplama, varsayılan olarak cihazınızda etkindir. Bu özellik, Microsoft 'un cihaz sorunlarını etkin bir şekilde gidermeye yardımcı olmak için sistem durumu göstergelerine dayalı olarak, Microsoft 'un günlükleri daha önce Daha fazla bilgi için, bkz. [cihazınızda öngörülü günlük toplama](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>2103 sürümünde düzeltilen sorunlar

Aşağıdaki tabloda, önceki sürümlerde not edilen ve geçerli sürümde düzeltilen sorunlar listelenmektedir.

| Hayır. | Özellik | Sorun | 
| --- | --- | --- |
|**1.**|Kubernetes |Web proxy etkinleştirildiğinde kenar kapsayıcısı kayıt defteri çalışmıyor.|
|**2.**|Kubernetes |Kenar kapsayıcısı kayıt defteri IoT Edge modüllerle çalışmaz.| 

## <a name="known-issues-in-2103-release"></a>2103 sürümündeki bilinen sorunlar

Aşağıdaki tabloda, 2103 sürümündeki bilinen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun | Geçici çözüm/açıklamalar |
| --- | --- | --- | --- |
|**1.**|Önizleme özellikleri |Bu sürümde, şu özellikler: yerel Azure Resource Manager, VM 'Ler, VM 'lerin bulut yönetimi, Kubernetes bulut yönetimi, Azure Arc etkin Kubernetes, Azure Stack Edge Pro R ve Azure Stack Edge Mini R için VPN, Azure Stack Edge Pro GPU için çok işlem hizmeti (MPS), önizleme aşamasında kullanıma sunuldu.  |Bu özellikler daha sonraki sürümlerde genel kullanıma sunulacaktır. |
|**2.**|GPU VM 'Leri |Bu sürümden önce, GPU VM yaşam döngüsü güncelleştirme akışında yönetilmedi. Bu nedenle, 2103 sürümüne güncelleştirirken güncelleştirme sırasında GPU VM 'Leri otomatik olarak durdurulmaz. Cihazınızı güncelleştirmeden önce bir bayrak kullanarak GPU VM 'Leri el ile durdurmanız gerekir `stop-stayProvisioned` . Daha fazla bilgi için bkz. [VM 'Yi askıya alma veya kapatma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Güncelleştirmeden önce çalışmakta olan tüm GPU VM 'Leri güncelleştirmeden sonra başlatılır. Bu örneklerde, VM 'lerde çalışan iş yükleri düzgün şekilde sonlandıraktarılmaz. Ve VM 'Ler, güncelleştirmeden sonra istenmeyen bir durumda olabilir. <br>Güncelleştirmeden önce ile durdurulan tüm GPU VM 'Leri, `stop-stayProvisioned` güncelleştirmeden sonra otomatik olarak başlatılır. <br>GPU sanal makinelerini Azure portal aracılığıyla durdurursanız, cihaz güncelleştirmesinden sonra VM 'yi el ile başlatmanız gerekir.| Kubernetes ile GPU VM 'Leri çalıştırıyorsanız, güncelleştirmeden önce GPU VM 'lerini hemen durdurun. <br>GPU VM 'Leri durdurulduğunda Kubernetes, başlangıçta VM 'Ler tarafından kullanılan GPU 'ları alır. <br>GPU VM 'Lerinin daha uzun olması, Kubernetes 'ın GPU 'Ların üzerinde sürme ihtimalini daha yüksektir. |
|**3.**|Özel Betik VM Uzantısı |Windows VM 'lerinde daha önceki bir sürümde oluşturulan ve cihaz 2103 olarak güncelleştirildiği bilinen bir sorun vardır. <br> Bu VM 'Lere özel bir betik uzantısı eklerseniz, Windows VM Konuk Aracısı (yalnızca sürüm 2.7.41491.901), uzantı dağıtımının zaman aşımına neden olan güncelleştirmede yer alır. | Bu soruna geçici bir çözüm olarak: <ul><li> Uzak Masaüstü Protokolü (RDP) kullanarak Windows VM 'ye bağlanın. </li><li> `waappagent.exe`Makinenin makinede çalıştığından emin olun: `Get-Process WaAppAgent` . </li><li> `waappagent.exe`Çalışmıyorsa, hizmeti yeniden başlatın `rdagent` : `Get-Service RdAgent` \| `Restart-Service` . 5 dakika bekleyin.</li><li> Çalışırken `waappagent.exe` `WindowsAzureGuest.exe` işlemi sonlandırın. </li><li>İşlemi sonlandırdıktan sonra işlem, daha yeni sürümle yeniden çalışmaya başlar.</li><li>Windows VM Konuk Aracısı sürümünün bu komutu kullanarak 2.7.41491.971 olduğunu doğrulayın: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[WINDOWS VM 'de özel Betik uzantısı ayarlayın](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Çoklu Işlem hizmeti (MPS) |Cihaz yazılımı ve Kubernetes kümesi güncelleştirilirken, iş yükleri için MPS ayarı korunmaz.   |[MPS 'Yi yeniden etkinleştirin](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) ve MPS 'yi kullanan Iş yüklerini yeniden dağıtın. |


## <a name="known-issues-from-previous-releases"></a>Önceki sürümlerden oluşan bilinen sorunlar

Aşağıdaki tabloda, önceki sürümlerden gerçekleştirilen bilinen sorunların bir özeti verilmiştir.

| Hayır. | Özellik | Sorun | Geçici çözüm/açıklamalar |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL veritabanı oluşturma, yönetici erişimi gerektirir.   |İçindeki adımlar 1-2 yerine aşağıdaki adımları uygulayın [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Cihazınızın yerel kullanıcı arabiriminde, işlem arabirimini etkinleştirin. İşlem **> Için işlem > bağlantı noktası # > etkinleştir** ' i seçin.</li><li>`sqlcmd`İstemci makinenize şuradan indirinhttps://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Adresin sonuna bir ", 1401" ekleyerek işlem arabirimi IP adresine (etkin olan bağlantı noktası) bağlanın.</li><li>Son komut şöyle görünür: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Bundan sonra geçerli belgelerden 3-4 arasındaki adımlar aynı olmalıdır. </li></ul> |
| **2.** |Yenile| **Yenileme** aracılığıyla geri yüklenen bloblara artımlı değişiklikler desteklenmez |Blob uç noktaları için, bir yenilemeden sonra Blobların kısmi güncelleştirmeleri, güncelleştirmelerin buluta yüklenmeme oluşmasına neden olabilir. Örneğin, gibi eylemler dizisi:<ul><li>Bulutta blob oluşturun. Ya da daha önce karşıya yüklenen bir blobu cihazdan silebilirsiniz.</li><li>Yenileme işlevini kullanarak buluttan blob 'u buluta yenileyin.</li><li>Azure SDK REST API 'Lerini kullanarak yalnızca blob 'un bir bölümünü güncelleştirin.</li></ul>Bu eylemler, blob 'un güncelleştirilmiş bölümlerinin bulutta güncelleştirilmesine neden olabilir. <br>**Geçici çözüm**: tüm Blobları değiştirmek için Robocopy veya gezgin veya komut satırı aracılığıyla normal dosya kopyalama gibi araçları kullanın.|
|**3.**|Azaltma|Daraltma sırasında, cihaza yeni yazmaya izin verilmiyorsa, NFS istemcisinin yazmaları "Izin reddedildi" hatasıyla başarısız olur.| Hata şu şekilde görünür:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ' test ' dizini oluşturulamıyor: Izin reddedildi|
|**4.**|BLOB depolama alımı|BLOB depolama alımı için AzCopy sürüm 10 ' u kullanırken, AzCopy öğesini aşağıdaki bağımsız değişkenle çalıştırın: `Azcopy <other arguments> --cap-mbps 2000`| AzCopy için bu sınırlar sağlanmazsa, cihaza çok sayıda istek gönderebilir ve hizmetle ilgili sorunlar ortaya çıkarır.|
|**e.**|Katmanlı depolama hesapları|Katmanlı depolama hesapları kullanılırken aşağıdakiler geçerlidir:<ul><li> Yalnızca blok Blobları desteklenir. Sayfa Blobları desteklenmez.</li><li>Anlık görüntü veya kopyalama API 'SI desteği yoktur.</li><li> `distcp`Kopyalama işlemini yoğun bir şekilde kullandığından Hadoop iş yükü alımı desteklenmez.</li></ul>||
|**inç.**|NFS bağlantı paylaşma|Birden çok işlem aynı paylaşıma kopyalandıysa ve `nolock` özniteliği kullanılmazsa, kopyalama sırasında hatalar görebilirsiniz.|`nolock`Dosyaları NFS paylaşımında kopyalamak için bağlama komutuna geçirilmesi gerekir. Örneğin: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes kümesi|Bir Kubernetes kümesi çalıştıran cihazınıza bir güncelleştirme uygulanırken, Kubernetes sanal makineleri yeniden başlatılır ve yeniden başlatılır. Bu örnekte, bir güncelleştirmeden sonra yalnızca belirtilen yinelemelerle dağıtılan bir dizin otomatik olarak geri yüklenir.  |Bir çoğaltma kümesi belirtmeden çoğaltma denetleyicisi dışında ayrı ayrı bir tane oluşturduysanız, bu yer işaretleri cihaz güncelleştirmesinden sonra otomatik olarak geri yüklenmez. Bu pods 'yi geri yüklemeniz gerekir.<br>Bir çoğaltma kümesi, düğüm arızası veya kesintiye uğramayan düğüm yükseltmesi gibi herhangi bir nedenle silinen veya sonlandırılmış olan Pod 'yi değiştirir. Bu nedenle, uygulamanız yalnızca tek bir pod gerektirdiğinden bile bir çoğaltma kümesi kullanmanızı öneririz.|
|**240.**|Kubernetes kümesi|Azure Stack Edge Pro 'da Kubernetes yalnızca helk v3 veya üzeri sürümlerle desteklenir. Daha fazla bilgi için [sık sorulan sorular: Tiller ' un kaldırılması](https://v3.helm.sh/docs/faq/)konusuna gidin.|
|**tuşlarına.**|Azure Arc özellikli Kubernetes |GA sürümünde, Azure Arc, Kubernetes, 0.1.18 sürümünden 0.2.9 sürümüne güncelleştirilir. Azure Arc etkinleştirilmiş Kubernetes güncelleştirmesi Azure Stack Edge cihazında desteklenmediğinden, Azure Arc etkinleştirilmiş Kubernetes 'i yeniden dağıtmanız gerekir.|Şu adımları izleyin:<ol><li>[Cihaz yazılımını ve Kubernetes güncelleştirmelerini uygulayın](azure-stack-edge-gpu-install-update.md).</li><li>[Cihazın PowerShell arabirimine](azure-stack-edge-gpu-connect-powershell-interface.md)bağlanın.</li><li>Mevcut Azure yay aracısını kaldırın. Şunu yazın: `Remove-HcsKubernetesAzureArcAgent` .</li><li>[Azure yayı 'yi yeni bir kaynağa](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)dağıtın. Mevcut bir Azure yay kaynağını kullanmayın.</li></ol>|
|**(.**|Azure Arc özellikli Kubernetes|Azure Stack Edge Pro cihazınızda Web proxy yapılandırıldıysa, Azure Arc dağıtımları desteklenmez.||
|**üst.**|Kubernetes |31000 numaralı bağlantı noktası Kubernetes panosu için ayrılmıştır. 31001 numaralı bağlantı noktası, sınır kapsayıcısı kayıt defteri için ayrılmıştır. Benzer şekilde, varsayılan yapılandırmada, 172.28.0.1 ve 172.28.0.10 IP adresleri sırasıyla Kubernetes hizmeti ve çekirdek DNS hizmeti için ayrılmıştır.|Ayrılmış IP 'Leri kullanmayın.|
|**+.**|Kubernetes |Kubernetes, şu anda çok protokollü yük dengeleyici hizmetlerine izin vermiyor. Örneğin, TCP ve UDP üzerinde dinleme yapmak zorunda olacak bir DNS hizmeti. |Kubernetes 'in bu şekilde, MetalLB ile bu sınırlamaya geçici bir çözüm bulmak için, iki hizmet (TCP için bir adet, UDP için bir adet) aynı Pod seçicisi üzerinde oluşturulabilir. Bu hizmetler aynı IP adresini paylaşmak için aynı paylaşım anahtarını ve spec. Loadbalancerıp 'yi kullanır. Kullanılabilir IP adreslerinden daha fazla hizmete sahipseniz, IP 'Ler de paylaşılabilir. <br> Daha fazla bilgi için bkz. [IP adresi paylaşımı](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**hatası.**|Kubernetes kümesi|Mevcut Azure IoT Edge Market modülleri, Azure Stack Edge cihazında IoT Edge çalıştırmak için değişiklikler gerektirebilir.|Daha fazla bilgi için bkz. Market 'ten Azure Stack Edge cihazında çalışacak Azure IoT Edge modülleri değiştirme.<!-- insert link-->|
|**May.**|Kubernetes |Dosya tabanlı bağlama takar, Azure Stack Edge cihazında Kubernetes üzerinde Azure IoT Edge desteklenmez.|IoT Edge, `ContainerCreate` seçenekleri Kubernetes yapılarına çevirmek için bir çeviri katmanı kullanır. `Binds`Dizine haritalar oluşturuluyor `hostpath` ve bu nedenle dosya tabanlı bağlama, IoT Edge kapsayıcılarındaki yollara bağlanamaz. Mümkünse, üst dizini eşleyin.|
|**aşamaz.**|Kubernetes |IoT Edge için kendi sertifikalarınızı getirir ve işlem cihazda yapılandırıldıktan sonra Azure Stack Edge cihazınıza bu sertifikaları eklerseniz, yeni sertifikalar alınmaz.|Bu sorunu geçici olarak çözmek için, cihazda işlem yapılandırmadan önce sertifikaları karşıya yüklemeniz gerekir. İşlem zaten yapılandırıldıysa, [cihazın PowerShell arabirimine bağlanın ve IoT Edge komutlarını çalıştırın](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Yeniden başlatma `iotedged` ve `edgehub` pods.|
|**k.**|Sertifikalar |Belirli örneklerde, yerel kullanıcı arabirimindeki sertifika durumunun güncelleştirilmesi birkaç saniye sürebilir. |Yerel Kullanıcı arabirimindeki aşağıdaki senaryolar etkilenebilir.<ul><li>**Sertifikalar** sayfasındaki **durum** sütunu.</li><li>**Başlarken** sayfasında **güvenlik** kutucuğu.</li><li>**Genel bakış** sayfasında **yapılandırma** kutucuğu.</li></ul>  |
|**17.**|IoT Edge |IoT Edge aracılığıyla dağıtılan modüller konak ağını kullanamaz. | |
|**18.**|İşlem + Kubernetes |İşlem/Kubernetes, NTLM Web proxy 'sini desteklemez. ||
|**renkli.**|Kubernetes + güncelleştirme |2008 yayınları gibi eski yazılım sürümlerinin, bu güncelleştirme ClusterConnectionException ile başarısız olmasına neden olan bir yarış durumu güncelleştirme sorunu vardır. |Yeni derlemelerin kullanılması bu sorundan kaçınmak için yardımcı olmalıdır. Bu sorunu hala görüyorsanız, geçici çözüm yükseltmeyi yeniden denemeli ve çalışır.|
|**2.0**|Internet Explorer|Gelişmiş güvenlik özellikleri etkinse, yerel Web UI sayfalarına erişemeyebilirsiniz. | Gelişmiş güvenliği devre dışı bırakın ve tarayıcınızı yeniden başlatın.|
|**21.**|Kubernetes panosu | SSL sertifikası ile Kubernetes panosu için *https* uç noktası desteklenmiyor. | |
|**#c16.**|Kubernetes |Kubernetes, .NET uygulamaları tarafından kullanılan ortam değişkeni adlarında ":" öğesini desteklemez. Bu ayrıca Event Grid IoT Edge modülünün Azure Stack Edge cihazında ve diğer uygulamalarda çalışması için de gereklidir. Daha fazla bilgi için bkz. [ASP.NET Core documentation](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables).|":" Öğesini çift alt çizgi ile değiştirin. Daha fazla bilgi için bkz. [Kubernetes sorunu](https://github.com/kubernetes/kubernetes/issues/53201)|
|**2008.** |Azure Arc + Kubernetes kümesi |Varsayılan olarak, `yamls` Git deposundan kaynak silindiğinde, ilgili kaynaklar Kubernetes kümesinden silinmez.  |Git deposundan silindiklerinde kaynakların silinmesine izin vermek için `--sync-garbage-collection` yay OperatorParams ' de ayarlayın. Daha fazla bilgi için bkz. [yapılandırmayı silme](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |NFS kullanan uygulamalar, verileri yazmak için cihazınızda paylaşılan takar ve özel yazma kullanmalıdır. Bu sayede yazma işlemleri diske yazılır.| |
|**250.**|İşlem yapılandırması |İşlem yapılandırması, ağ geçitlerinde ağ geçitleri veya anahtarların veya yönlendiricilerin ağda bulunmayan sistemler için Adres Çözümleme Protokolü (ARP) isteklerine yanıt verdiği ağ yapılandırmalarında başarısız olur.| |
|**:.**|İşlem ve Kubernetes |İlk olarak cihazınızda Kubernetes ayarlandıysa, tüm kullanılabilir GPU 'ları ister. Bu nedenle, Kubernetes 'i ayarladıktan sonra GPU 'ları kullanarak Azure Resource Manager VM 'Ler oluşturmak mümkün değildir. |Cihazınızda 2 GPU varsa, GPU 'YU kullanan 1 VM oluşturabilir ve sonra Kubernetes 'i yapılandırabilirsiniz. Bu durumda, Kubernetes, kalan kullanılabilir 1 GPU 'YU kullanacaktır. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Sonraki adımlar

- [Cihazınızı güncelleştirme](azure-stack-edge-gpu-install-update.md)
