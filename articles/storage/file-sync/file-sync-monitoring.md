---
title: İzleyici Azure Dosya Eşitleme | Microsoft Docs
description: Azure Izleyici, depolama eşitleme hizmeti ve Windows Server kullanarak Azure Dosya Eşitleme dağıtımınızı nasıl izleyeceğinizi inceleyin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797311"
---
# <a name="monitor-azure-file-sync"></a>Azure Dosya Eşitleme’yi izleme

Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server’ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makalede, Azure Izleyici, depolama eşitleme hizmeti ve Windows Server kullanılarak Azure Dosya Eşitleme dağıtımınızın nasıl izleneceği açıklanır.

Bu kılavuzda aşağıdaki senaryolar ele alınmıştır: 
- Azure Izleyici 'de Azure Dosya Eşitleme ölçümlerini görüntüleyin.
- Azure Izleyici 'de, kritik koşullar hakkında önceden bildirim almak için uyarılar oluşturun.
- Azure portal kullanarak Azure Dosya Eşitleme dağıtımınızın durumunu görüntüleyin.
- Azure Dosya Eşitleme dağıtımınızın durumunu izlemek için Windows sunucularınızda olay günlükleri ve performans sayaçlarını kullanma. 

## <a name="azure-monitor"></a>Azure İzleyici

Ölçümleri görüntülemek ve eşitleme, bulut katmanlama ve sunucu bağlantısı için uyarıları yapılandırmak üzere [Azure izleyici](../../azure-monitor/overview.md) 'yi kullanın.  

### <a name="metrics"></a>Ölçümler

Azure Dosya Eşitleme ölçümleri varsayılan olarak etkindir ve 15 dakikada bir Azure Izleyicisine gönderilir.

**Azure Izleyici 'de Azure Dosya Eşitleme ölçümlerini görüntüleme**
1. **Azure Portal** **depolama eşitleme hizmetinize** gidin ve **ölçümler**' e tıklayın.
2. **Ölçüm** açılan düğmesine tıklayın ve görüntülemek istediğiniz ölçümü seçin.

![Azure Dosya Eşitleme ölçümlerinin ekran görüntüsü](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Azure Dosya Eşitleme için aşağıdaki ölçümler Azure Izleyici 'de bulunabilir:

| Ölçüm adı | Description |
|-|-|
| Eşitlenen baytlar | Aktarılan verilerin boyutu (karşıya yükleme ve indirme).<br><br>Birim: bayt<br>Toplama türü: Toplam<br>Geçerli Boyutlar: sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Bulut katmanlama geri çağırma | Geri çekilen verilerin boyutu.<br><br>**Note**: Bu ölçüm gelecekte kaldırılacak. Geri çekilen verilerin boyutunu izlemek için bulut katmanlama geri çekme boyutu ölçümünü kullanın.<br><br>Birim: bayt<br>Toplama türü: Toplam<br>Geçerli boyut: sunucu adı |
| Bulut katmanlama geri çağırma boyutu | Geri çekilen verilerin boyutu.<br><br>Birim: bayt<br>Toplama türü: Toplam<br>Geçerli Boyutlar: sunucu adı, eşitleme grubu adı |
| Uygulamaya göre bulut katmanlama geri çağırma boyutu | Uygulama tarafından geri çekilen verilerin boyutu.<br><br>Birim: bayt<br>Toplama türü: Toplam<br>Geçerli Boyutlar: uygulama adı, sunucu adı, eşitleme grubu adı |
| Bulut katmanlama geri çekme performansı | Veri geri çağırma üretilen iş boyutu.<br><br>Birim: bayt<br>Toplama türü: Toplam<br>Geçerli Boyutlar: sunucu adı, eşitleme grubu adı |
| Dosyalar eşitlenmiyor | Eşitleme başarısız olan dosyaların sayısı.<br><br>Birim: sayım<br>Toplama türleri: ortalama, toplam<br>Geçerli Boyutlar: sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Eşitlenen dosyalar | Aktarılan dosya sayısı (karşıya yükleme ve indirme).<br><br>Birim: sayım<br>Toplama türü: Toplam<br>Geçerli Boyutlar: sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Sunucu çevrimiçi durumu | Sunucudan alınan sinyal sayısı.<br><br>Birim: sayım<br>Toplama türü: en fazla<br>Geçerli boyut: sunucu adı |
| Eşitleme oturumu sonucu | Eşitleme oturumu sonucu (1 = başarılı eşitleme oturumu; 0 = başarısız eşitleme oturumu)<br><br>Birim: sayım<br>Toplama türleri: en fazla<br>Geçerli Boyutlar: sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |

### <a name="alerts"></a>Uyarılar

Uyarılar, izleme verilerinizde önemli koşullar bulunduğunda size bir bildirim gönderir. Azure Izleyici 'de uyarıları yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Microsoft Azure uyarılara genel bakış](../../azure-monitor/alerts/alerts-overview.md).

**Azure Dosya Eşitleme için uyarı oluşturma**

1. **Azure Portal** **depolama eşitleme hizmetinize** gidin. 
2. Izleme bölümünde **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.
3. **Koşul Seç** ' e tıklayın ve uyarı için aşağıdaki bilgileri sağlayın: 
    - **Ölçüm**
    - **Boyut adı**
    - **Uyarı mantığı**
4. **Eylem grubunu Seç** ' e tıklayın ve var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) ekleyin.
5. Uyarı **kuralı adı**, **Açıklama** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
6. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' a tıklayın.  

Aşağıdaki tabloda, izlemek için bazı örnek senaryolar ve uyarı için kullanılacak uygun ölçüm listelenmektedir:

| Senaryo | Uyarı için kullanılacak ölçüm |
|-|-|
| Sunucu uç noktası sistem durumu portalda bir hata gösteriyor | Eşitleme oturumu sonucu |
| Dosyalar bir sunucu veya bulut uç noktasına eşitlenemiyor | Dosyalar eşitlenmiyor |
| Kayıtlı sunucu, depolama eşitleme hizmeti ile iletişim kuramıyor | Sunucu çevrimiçi durumu |
| Bulut katmanlama geri çağırma boyutu günde 500 gib 'yi aştı  | Bulut katmanlama geri çağırma boyutu |

Bu senaryolar için uyarı oluşturma yönergeleri için [uyarı örnekleri](#alert-examples) bölümüne bakın.

## <a name="storage-sync-service"></a>Depolama Eşitleme Hizmeti

Azure Dosya Eşitleme dağıtımınızın sistem durumunu **Azure Portal** görüntülemek Için **depolama eşitleme hizmeti** ' ne gidin ve aşağıdaki bilgiler kullanılabilir:

- Kayıtlı sunucu durumu
- Sunucu uç noktası sistem durumu
    - Dosyalar eşitlenmiyor
    - Eşitleme etkinliği
    - Bulut katmanlama verimliliği
    - Katmanlama içermeyen dosyalar
    - Hataları geri çağır
- Ölçümler

### <a name="registered-server-health"></a>Kayıtlı sunucu durumu

Portalda **kayıtlı sunucu durumunu** görüntülemek Için, **depolama eşitleme hizmeti**'nin **kayıtlı sunucular** bölümüne gidin.

![Kayıtlı sunucu durumunun ekran görüntüsü](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- **Kayıtlı sunucu** durumu **çevrimiçi** ise, sunucu hizmetle başarılı bir şekilde iletişim kuruyor.
- **Kayıtlı sunucu** durumu **çevrimdışı görünüyorsa**, depolama eşitleme İzleyicisi işlemi (AzureStorageSyncMonitor.exe) çalışmıyor veya sunucu Azure dosya eşitleme hizmetine erişemiyor. Rehberlik için [sorun giderme belgelerine](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) bakın.

### <a name="server-endpoint-health"></a>Sunucu uç noktası sistem durumu

Portalda bir **sunucu uç noktasının** durumunu görüntülemek Için, **depolama eşitleme hizmeti** 'nin **eşitleme grupları** bölümüne gidin ve bir **eşitleme grubu** seçin.

![Sunucu uç noktası durumunun ekran görüntüsü](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- Portaldaki **sunucu uç noktası sistem durumu** ve **eşitleme etkinliği** , sunucudaki telemetri olay günlüğünde günlüğe kaydedilen eşitleme OLAYLARıNı temel alır (kimlik 9102 ve 9302). Hata iptal edildi gibi geçici bir hata nedeniyle eşitleme oturumu başarısız olursa, geçerli eşitleme oturumu ilerleme yaptığından (dosyalar uygulanır), sunucu uç noktası portalda hala **sağlıklı** olarak görünür. Olay KIMLIĞI 9302, eşitleme işlemi tamamlandıktan sonra eşitleme ilerleme olayı ve olay KIMLIĞI 9102 günlüğe kaydedilir.  Daha fazla bilgi için bkz. [eşitleme durumu](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) ve [Eşitleme ilerleme durumu](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Sunucu uç noktası sistem durumu bir **hata** gösteriyorsa veya **etkinlik yoksa**, rehberlik için [sorun giderme belgelerine](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) bakın.
- Portalda **Eşitleme olmayan dosyalar** , sunucusundaki telemetri olay günlüğüne kaydedilen 9121 olay kimliğini temel alır. Bu olay, eşitleme oturumu tamamlandığında her öğe için ayrı hata için günlüğe kaydedilir. Öğe başına hataları çözmek için bkz. [nasıl yaparım?, eşitlenmekte olan belirli dosya veya klasörler olup](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)olmadığını görün.
- Portalda **bulut katmanlama verimliliğini** görüntülemek Için **sunucu uç noktası özelliklerine** gidin ve **bulut katmanlaması** bölümüne gidin. Bulut katmanlama verimliliği için girilen veriler, sunucusundaki telemetri olay günlüğünde günlüğe kaydedilen olay KIMLIĞI 9071 ' i temel alır. Daha fazla bilgi için bkz. [bulut katmanlamayı izleme](file-sync-monitor-cloud-tiering.md).
- Portalda **hata giderme ve geri çağırma** **dosyalarını** görüntülemek Için **sunucu uç noktası özelliklerine** gidin ve **bulut katmanlaması** bölümüne gidin. **Katmanlama olmayan dosyalar** , sunucuda telemetri olay günlüğünde günlüğe KAYDEDILEN olay kimliği 9003 ' i temel alır ve **hataları GERI çağırır** olay kimliği 9006 ' i temel alır. Katman veya geri çağırma işlemleri başarısız olan dosyaları araştırmak için bkz. [katmanın başarısız olduğu dosyaların nasıl giderileceği](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) ve geri [çekilemiyor dosyaları nasıl giderebileceğiniz](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Ölçüm grafikleri

- Aşağıdaki ölçüm grafikleri, depolama eşitleme hizmeti portalında görüntülenebilir:

  | Ölçüm adı | Description | Dikey pencere adı |
  |-|-|-|
  | Eşitlenen baytlar | Aktarılan verilerin boyutu (karşıya yükleme ve indirme) | Eşitleme grubu, sunucu uç noktası |
  | Bulut katmanlama geri çağırma | Geri çekilen verilerin boyutu | Kayıtlı sunucular |
  | Dosyalar eşitlenmiyor | Eşitleme başarısız olan dosyaların sayısı | Sunucu uç noktası |
  | Eşitlenen dosyalar | Aktarılan dosya sayısı (karşıya yükleme ve indirme) | Eşitleme grubu, sunucu uç noktası |
  | Sunucu çevrimiçi durumu | Sunucudan alınan sinyal sayısı | Kayıtlı sunucular |

- Daha fazla bilgi için bkz. [Azure izleyici](#azure-monitor).

  > [!Note]  
  > Depolama eşitleme hizmeti portalındaki grafiklerde 24 saatlik bir zaman aralığı vardır. Farklı zaman aralıklarını veya boyutları görüntülemek için Azure Izleyici 'yi kullanın.

## <a name="windows-server"></a>Windows Server

Azure Dosya Eşitleme aracısının yüklü olduğu **Windows Server** 'da, **olay günlükleri** ve **performans sayaçlarını** kullanarak sunucu uç noktalarının sistem durumunu görüntüleyebilirsiniz.

### <a name="event-logs"></a>Olay günlükleri

Kayıtlı sunucu, eşitleme ve bulut katmanlama durumunu izlemek için sunucuda telemetri olay günlüğünü kullanın. Telemetri olay günlüğü, *uygulamalar ve Services\Microsoft\FileSync\Agent* altında Olay Görüntüleyicisi bulunur.

Eşitleme durumu

- Olay KIMLIĞI 9102, eşitleme oturumu tamamlandıktan sonra günlüğe kaydedilir. Eşitleme oturumlarının başarılı olup olmadığını (**HRESULT = 0**) ve öğe başına Eşitleme hataları olup olmadığını (**Peritemerrorcount**) öğrenmek için bu olayı kullanın. Daha fazla bilgi için bkz. [eşitleme durumu](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) ve  [öğe başına hatalar](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) belgeleri.

  > [!Note]  
  > Bazen eşitleme oturumları genel başarısız olur ya da sıfır olmayan bir PerItemErrorCount sayısına sahiptir. Ancak, yine de ilerleme durumu ve bazı dosyalar başarıyla eşitlenir. Bunu, AppliedFileCount, AppliedDirCount, AppliedTombstoneCount ve AppliedSizeBytes gibi uygulanan alanlarda görebilirsiniz. Bu alanlar, oturumun ne kadarının başarılı olduğunu bildirir. Bir satırda birden çok eşitleme oturumu başarısız görürseniz ve artan uygulanmış sayımla karşılaşırsanız, bir destek bileti açmadan önce yeniden denemek için eşitleme süresi verin.

- Olay KIMLIĞI 9121, eşitleme oturumu tamamlandıktan sonra her öğe başına hata için günlüğe kaydedilir. Bu hatayla (**Persistentcount** ve **geçişli entcount**) eşitlemeyen dosya sayısını öğrenmek için bu olayı kullanın. Kalıcı öğe başına hataların araştırılması gerekir. Nasıl yaparım?, [eşitlenmeyen belirli dosya veya klasörler olup](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)olmadığını görmek için bkz..

- Etkin bir eşitleme oturumu varsa olay KIMLIĞI 9302, her 5 dakikada bir günlüğe kaydedilir. Kaç öğenin eşitleneceğini (**Totalıtemcount**), şimdiye kadar eşitlenmiş olan öğelerin sayısını (**AppliedItemCount**) ve öğe başına hata (**peritemerrorcount**) nedeniyle eşitleyemediği öğe sayısını öğrenmek için bu olayı kullanın. Eşitleme ilerlemeye devam etmez (**AppliedItemCount = 0**), eşitleme oturumu sonunda başarısız olur ve hata Ile 9102 olay kimliği günlüğe kaydedilir. Daha fazla bilgi için bkz. [eşitleme ilerlemesi belgeleri](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Kayıtlı sunucu durumu

- Olay KIMLIĞI 9301, bir sunucu işler için hizmeti sorguladığında her 30 saniyede bir günlüğe kaydedilir. GetNextJob **durum = 0** ile sonlandıysanız, sunucu hizmetle iletişim kurabilir. GetNextJob bir hata ile sonlandıysanız, kılavuzluk için [sorun giderme belgelerini](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) denetleyin.

Bulut katmanlama durumu

- Bir sunucudaki katmanlama etkinliğini izlemek için, *uygulamalar ve Services\Microsoft\FileSync\Agent* altında Olay Görüntüleyicisi bulunan telemetri olay GÜNLÜĞÜNDE olay kimliği 9003, 9016 ve 9029 kullanın.

  - Olay KIMLIĞI 9003, bir sunucu uç noktası için hata dağıtımı sağlar. Örneğin: Toplam hata sayısı ve hata kodu. Hata kodu başına bir olay günlüğe kaydedilir.
  - Olay kimliği 9016, bir birim için hayalet kopya oluşturuluyor sonuçları sağlar. Örneğin: boş alan yüzdesi, oturumdaki hayalet dosya sayısı ve hayalet başarısız olan dosya sayısı.
  - Olay kimliği 9029, bir sunucu uç noktası için hayalet kopya oluşturuluyor oturum bilgileri sağlar. Örneğin: oturumda denenen dosya sayısı, oturumda katmanlı dosya sayısı ve zaten katmanlı dosya sayısı.

- Bir sunucudaki geri çekme etkinliğini izlemek için, *uygulamalar ve Services\Microsoft\FileSync\Agent* altında Olay Görüntüleyicisi bulunan telemetri olay GÜNLÜĞÜNDE olay kimliği 9005, 9006, 9009, 9059 ve 9071 kullanın.

  - Olay KIMLIĞI 9005, bir sunucu uç noktası için geri çağırma güvenilirliği sağlar. Örneğin: erişilen toplam benzersiz dosya ve başarısız erişimi olan toplam benzersiz dosya sayısı.
  - Olay KIMLIĞI 9006, bir sunucu uç noktası için geri çağırma hatası dağıtımı sağlar. Örneğin: Toplam başarısız Istek ve hata kodu. Hata kodu başına bir olay günlüğe kaydedilir.
  - Olay KIMLIĞI 9009, bir sunucu uç noktası için geri çağırma oturumu bilgilerini sağlar. Örneğin: DurationSeconds, CountFilesRecallSucceeded ve CountFilesRecallFailed.
  - Olay KIMLIĞI 9059, bir sunucu uç noktası için uygulama geri çağırma dağıtımı sağlar. Örneğin: Shareıd, Application Name ve TotalEgressNetworkBytes.
  - Olay KIMLIĞI 9071, bir sunucu uç noktası için bulut katmanlama verimliliği sağlar. Örneğin: Totaldıstıntfilecountcachehit, Totaldıstıntfilecountcacheisabetsiz, TotalCacheHitBytes ve TotalCacheMissBytes.

### <a name="performance-counters"></a>Performans sayaçları

Eşitleme etkinliğini izlemek için sunucuda Azure Dosya Eşitleme performans sayaçlarını kullanın.

Sunucuda Azure Dosya Eşitleme performans sayaçlarını görüntülemek için performans Izleyicisi 'ni (Perfmon.exe) açın. Sayaçları, **aktarılan AFS bayt** ve **AFS eşitleme işlemleri** nesneleri altında bulabilirsiniz.

Azure Dosya Eşitleme için aşağıdaki performans sayaçları performans Izleyicisinde kullanılabilir:

| Performans Object\sayaç adı | Description |
|-|-|
| AFS bayt Transferred\indirilen bayt/sn | Saniye başına indirilen bayt sayısı. |
| AFS bayt Transferred\karşıya yüklenen bayt/sn | Saniye başına karşıya yüklenen bayt sayısı. |
| AFS bayt Transferred\toplam bayt/sn | Toplam bayt/saniye (karşıya yükleme ve indirme). |
| AFS Sync Operations\indirilen eşitleme dosyaları/sn | Saniye başına indirilen dosya sayısı. |
| AFS Sync Operations\karşıya yüklenen eşitleme dosyaları/sn | Saniye başına karşıya yüklenen dosya sayısı. |
| AFS Sync Operations\toplam eşitleme dosyası Işlemi/sn | Eşitlenen toplam dosya sayısı (karşıya yükleme ve indirme). |

## <a name="alert-examples"></a>Uyarı örnekleri
Bu bölüm Azure Dosya Eşitleme için bazı örnek uyarılar sağlar.

  > [!Note]  
  > Bir uyarı oluşturursanız ve çok gürültülü bir uyarı oluşturursanız eşik değerini ve uyarı mantığını ayarlayın.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Sunucu uç noktası durumu portalda bir hata gösteriyorsa uyarı oluşturma

1. **Azure Portal** Ilgili **depolama eşitleme hizmeti**' ne gidin. 
2. **İzleme** bölümüne gidin ve **Uyarılar**' a tıklayın. 
3. Yeni bir uyarı kuralı oluşturmak için **+ Yeni uyarı kuralına** tıklayın. 
4. **Koşul Seç**' i tıklatarak koşulu yapılandırın.
5. **Sinyal mantığını Yapılandır** dikey penceresinde, sinyal adı altında **eşitleme oturumu sonucu** ' na tıklayın.  
6. Aşağıdaki boyut yapılandırmasını seçin: 
     - Boyut adı: **sunucu uç noktası adı**  
     - İşlecinde **=** 
     - Boyut değerleri: **tüm geçerli ve gelecekteki değerler**  
7. **Uyarı mantığına** gidin ve aşağıdakileri doldurun: 
     - Eşik **statik** olarak ayarlandı 
     - İşleç: **küçüktür** 
     - Toplama türü: **en fazla**  
     - Eşik değeri: **1** 
     - Temel alınarak değerlendirilen: toplama ayrıntı düzeyi = **24 saat** | Değerlendirme sıklığı = **her saat** 
     - Bitti ' ye tıklayın **.** 
8. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) eklemek için **Eylem grubunu Seç** ' e tıklayın.
9. Uyarı **kuralı adı**, **Açıklama** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
10. **Uyarı kuralı oluştur**’a tıklayın. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Dosyalar bir sunucu veya bulut uç noktası ile eşitlenememesi durumunda uyarı oluşturma

1. **Azure Portal** Ilgili **depolama eşitleme hizmeti**' ne gidin. 
2. **İzleme** bölümüne gidin ve **Uyarılar**' a tıklayın. 
3. Yeni bir uyarı kuralı oluşturmak için **+ Yeni uyarı kuralına** tıklayın. 
4. **Koşul Seç**' i tıklatarak koşulu yapılandırın.
5. **Sinyal mantığını Yapılandır** dikey penceresinde, sinyal adı altında **eşitleme değil dosyalar** ' a tıklayın.  
6. Aşağıdaki boyut yapılandırmasını seçin: 
     - Boyut adı: **sunucu uç noktası adı**  
     - İşlecinde **=** 
     - Boyut değerleri: **tüm geçerli ve gelecekteki değerler**  
7. **Uyarı mantığına** gidin ve aşağıdakileri doldurun: 
     - Eşik **statik** olarak ayarlandı 
     - İşleç: **büyüktür** 
     - Toplama türü: **Ortalama**  
     - Eşik değeri: **100** 
     - Temel alınarak değerlendirilen: toplama ayrıntı düzeyi = **5 dakika** | Değerlendirme sıklığı = **her 5 dakikada** bir 
     - Bitti ' ye tıklayın **.** 
8. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) eklemek için **Eylem grubunu Seç** ' e tıklayın.
9. Uyarı **kuralı adı**, **Açıklama** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
10. **Uyarı kuralı oluştur**’a tıklayın. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Kayıtlı bir sunucu depolama eşitleme hizmeti ile iletişim kuramıyorsa uyarı oluşturma

1. **Azure Portal** Ilgili **depolama eşitleme hizmeti**' ne gidin. 
2. **İzleme** bölümüne gidin ve **Uyarılar**' a tıklayın. 
3. Yeni bir uyarı kuralı oluşturmak için **+ Yeni uyarı kuralına** tıklayın. 
4. **Koşul Seç**' i tıklatarak koşulu yapılandırın.
5. **Sinyal mantığını Yapılandır** dikey penceresinde, sinyal adı altında **sunucu çevrimiçi durumu** ' na tıklayın.  
6. Aşağıdaki boyut yapılandırmasını seçin: 
     - Boyut adı: **sunucu adı**  
     - İşlecinde **=** 
     - Boyut değerleri: **tüm geçerli ve gelecekteki değerler**  
7. **Uyarı mantığına** gidin ve aşağıdakileri doldurun: 
     - Eşik **statik** olarak ayarlandı 
     - İşleç: **küçüktür** 
     - Toplama türü: **en fazla**  
     - Eşik değeri (bayt): **1** 
     - Temel alınarak değerlendirilen: toplama ayrıntı düzeyi = **1 saat** | Değerlendirme sıklığı = **her 30 dakikada** bir 
         - Ölçümlerin her 15 ila 20 dakikada bir Azure Izleyici 'ye gönderileceğini unutmayın. **Değerlendirme sıklığını** 30 dakikadan kısa bir süre ayarlama (yanlış uyarılar üretir).
     - Bitti ' ye tıklayın **.** 
8. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) eklemek için **Eylem grubunu Seç** ' e tıklayın.
9. Uyarı **kuralı adı**, **Açıklama** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
10. **Uyarı kuralı oluştur**’a tıklayın. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Bulut katmanlama geri çağırma boyutu günde 500 gib 'yi aşarsa uyarı oluşturma

1. **Azure Portal** Ilgili **depolama eşitleme hizmeti**' ne gidin. 
2. **İzleme** bölümüne gidin ve **Uyarılar**' a tıklayın. 
3. Yeni bir uyarı kuralı oluşturmak için **+ Yeni uyarı kuralına** tıklayın. 
4. **Koşul Seç**' i tıklatarak koşulu yapılandırın.
5. **Sinyal mantığını Yapılandır** dikey penceresinde, sinyal adı altında **bulut katmanlama geri çağırma boyutu** ' na tıklayın.  
6. Aşağıdaki boyut yapılandırmasını seçin: 
     - Boyut adı: **sunucu adı**  
     - İşlecinde **=** 
     - Boyut değerleri: **tüm geçerli ve gelecekteki değerler**  
7. **Uyarı mantığına** gidin ve aşağıdakileri doldurun: 
     - Eşik **statik** olarak ayarlandı 
     - İşleç: **büyüktür** 
     - Toplama türü: **Toplam**  
     - Eşik değeri (bayt): **67108864000** 
     - Temel alınarak değerlendirilen: toplama ayrıntı düzeyi = **24 saat** | Değerlendirme sıklığı = **her saat** 
     - Bitti ' ye tıklayın **.** 
8. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) eklemek için **Eylem grubunu Seç** ' e tıklayın.
9. Uyarı **kuralı adı**, **Açıklama** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
10. **Uyarı kuralı oluştur**’a tıklayın. 

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)
- [Güvenlik duvarını ve proxy ayarlarını değerlendirin](file-sync-firewall-and-proxy.md)
- [Azure Dosya Eşitleme’yi dağıtma](file-sync-deployment-guide.md)
- [Azure Dosya Eşitleme'de sorun giderme](file-sync-troubleshoot.md)