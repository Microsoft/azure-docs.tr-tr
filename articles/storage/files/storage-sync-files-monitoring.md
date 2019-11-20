---
title: İzleyici Azure Dosya Eşitleme | Microsoft Docs
description: Azure Dosya Eşitleme nasıl izlenir.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699284"
---
# <a name="monitor-azure-file-sync"></a>Azure Dosya Eşitleme’yi izleme

Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server 'ı Azure dosya paylaşımınızın hızlı önbelleğine dönüştürür. SMB, NFS ve FTPS dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makalede, Azure Izleyici, depolama eşitleme hizmeti ve Windows Server kullanılarak Azure Dosya Eşitleme dağıtımınızın nasıl izleneceği açıklanır.

Aşağıdaki izleme seçenekleri şu anda kullanılabilir.

## <a name="azure-monitor"></a>Azure İzleyici

Ölçümleri görüntülemek ve eşitleme, bulut katmanlama ve sunucu bağlantısı için uyarıları yapılandırmak üzere [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview) 'yi kullanın.  

### <a name="metrics"></a>Ölçümler

Azure Dosya Eşitleme ölçümleri varsayılan olarak etkindir ve 15 dakikada bir Azure Izleyicisine gönderilir.

Azure Izleyici 'de Azure Dosya Eşitleme ölçümleri görüntülemek için **depolama Eşitleme Hizmetleri** kaynak türünü seçin.

Azure Dosya Eşitleme için aşağıdaki ölçümler Azure Izleyici 'de bulunabilir:

| Ölçüm adı | Açıklama |
|-|-|
| Eşitlenen baytlar | Aktarılan verilerin boyutu (karşıya yükleme ve indirme).<br><br>Birim Bayt<br>Toplama türü: Toplam<br>Geçerli Boyutlar: Sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Bulut katmanlama geri çağırma | Geri çekilen verilerin boyutu.<br><br>**Not**: Bu ölçüm gelecekte kaldırılacak. Geri çekilen verilerin boyutunu izlemek için bulut katmanlama geri çekme boyutu ölçümünü kullanın.<br><br>Birim Bayt<br>Toplama türü: Toplam<br>Geçerli boyut: Sunucu adı |
| Bulut katmanlama geri çağırma boyutu | Geri çekilen verilerin boyutu.<br><br>Birim Bayt<br>Toplama türü: Toplam<br>Geçerli boyut: Sunucu adı, eşitleme grubu adı |
| Uygulamaya göre bulut katmanlama geri çağırma boyutu | Uygulama tarafından geri çekilen verilerin boyutu.<br><br>Birim Bayt<br>Toplama türü: Toplam<br>Geçerli boyut: Uygulama adı, sunucu adı, eşitleme grubu adı |
| Bulut katmanlama geri çekme performansı | Veri geri çağırma üretilen iş boyutu.<br><br>Birim Bayt<br>Toplama türü: Toplam<br>Geçerli boyut: Sunucu adı, eşitleme grubu adı |
| Dosyalar eşitlenmiyor | Eşitleme başarısız olan dosyaların sayısı.<br><br>Birim Count<br>Toplama türü: Toplam<br>Geçerli Boyutlar: Sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Eşitlenen dosyalar | Aktarılan dosya sayısı (karşıya yükleme ve indirme).<br><br>Birim Count<br>Toplama türü: Toplam<br>Geçerli Boyutlar: Sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |
| Sunucu çevrimiçi durumu | Sunucudan alınan sinyal sayısı.<br><br>Birim Count<br>Toplama türü: Maksimum<br>Geçerli boyut: Sunucu adı |
| Eşitleme oturumu sonucu | Eşitleme oturumu sonucu (1 = başarılı eşitleme oturumu; 0 = başarısız eşitleme oturumu)<br><br>Birim Count<br>Toplama türleri: Maksimum<br>Geçerli Boyutlar: Sunucu uç noktası adı, eşitleme yönü, eşitleme grubu adı |

### <a name="alerts"></a>Uyarılar

Azure Izleyici 'de uyarıları yapılandırmak için depolama eşitleme hizmeti ' ni seçin ve ardından uyarı için kullanılacak [Azure dosya eşitleme ölçümünü](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) seçin.  

Aşağıdaki tabloda, izlemek için bazı örnek senaryolar ve uyarı için kullanılacak uygun ölçüm listelenmektedir:

| Senaryo | Uyarı için kullanılacak ölçüm |
|-|-|
| Portalda sunucu uç noktası sistem durumu = hata | Eşitleme oturumu sonucu |
| Dosyalar bir sunucu veya bulut uç noktasına eşitlenemiyor | Dosyalar eşitlenmiyor |
| Kayıtlı sunucu, depolama eşitleme hizmeti ile iletişim kuramıyor | Sunucu çevrimiçi durumu |
| Bulut katmanlama geri çağırma boyutu günde 500 gib 'yi aştı  | Bulut katmanlama geri çağırma boyutu |

Azure Izleyici 'de uyarıları yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Microsoft Azure uyarılara genel bakış]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Depolama Eşitleme Hizmeti

Kayıtlı sunucu sistem durumu, sunucu uç noktası sistem durumu ve ölçümleri görüntülemek için Azure portal depolama Eşitleme hizmetine gidin. Kayıtlı sunucu sistem durumunu **kayıtlı sunucular** dikey penceresinde ve sunucu uç noktası sistem durumu ' nu **eşitleme grupları** dikey penceresinde görüntüleyebilirsiniz.

### <a name="registered-server-health"></a>Kayıtlı sunucu durumu

- **Kayıtlı sunucu** durumu **çevrimiçi**ise, sunucu hizmetle başarılı bir şekilde iletişim kuruyor.
- **Kayıtlı sunucu** durumu **çevrimdışı görünüyorsa**, sunucuda depolama eşitleme Izleyicisi (AzureStorageSyncMonitor. exe) işleminin çalıştığını doğrulayın. Sunucu bir güvenlik duvarı veya proxy 'nin arkasındaysa, güvenlik duvarını ve proxy 'yi yapılandırmak için [Bu makaleye](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) bakın.

### <a name="server-endpoint-health"></a>Sunucu uç noktası sistem durumu

- Portaldaki sunucu uç noktası sistem durumu, sunucudaki telemetri olay günlüğüne kaydedilen eşitleme olaylarını temel alır (KIMLIK 9102 ve 9302). Hata iptal edildiğinde, eşitleme oturumu geçici bir hata nedeniyle başarısız olursa, eşitleme, geçerli eşitleme oturumu devam ettiği sürece portalda hala sağlıklı olarak görünebilir. Olay KIMLIĞI 9302, dosyaların uygulanmakta olup olmadığını belirlemekte kullanılır. Daha fazla bilgi için bkz. [eşitleme durumu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) ve [Eşitleme ilerleme durumu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Eşitleme ilerleme yaptığından Portal bir eşitleme hatası gösteriyorsa, rehberlik için [sorun giderme belgelerine](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) bakın.

### <a name="metric-charts"></a>Ölçüm grafikleri

- Aşağıdaki ölçüm grafikleri, depolama eşitleme hizmeti portalında görüntülenebilir:

  | Ölçüm adı | Açıklama | Dikey pencere adı |
  |-|-|-|
  | Eşitlenen baytlar | Aktarılan verilerin boyutu (karşıya yükleme ve indirme) | Eşitleme grubu, sunucu uç noktası |
  | Bulut katmanlama geri çağırma | Geri çekilen verilerin boyutu | Kayıtlı sunucular |
  | Dosyalar eşitlenmiyor | Eşitleme başarısız olan dosyaların sayısı | Sunucu uç noktası |
  | Eşitlenen dosyalar | Aktarılan dosya sayısı (karşıya yükleme ve indirme) | Eşitleme grubu, sunucu uç noktası |
  | Sunucu çevrimiçi durumu | Sunucudan alınan sinyal sayısı | Kayıtlı sunucular |

- Daha fazla bilgi için bkz. [Azure izleyici](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Depolama eşitleme hizmeti portalındaki grafiklerde 24 saatlik bir zaman aralığı vardır. Farklı zaman aralıklarını veya boyutları görüntülemek için Azure Izleyici 'yi kullanın.

## <a name="windows-server"></a>Windows Server

Windows Server 'da bulut katmanlaması, kayıtlı sunucu ve eşitleme sistem durumunu görüntüleyebilirsiniz.

### <a name="event-logs"></a>Olay günlükleri

Kayıtlı sunucu, eşitleme ve bulut katmanlama durumunu izlemek için sunucuda telemetri olay günlüğünü kullanın. Telemetri olay günlüğü, *uygulamalar ve Services\Microsoft\FileSync\Agent*altında Olay Görüntüleyicisi bulunur.

Eşitleme durumu:

- Olay KIMLIĞI 9102, bir eşitleme oturumu bittikten sonra günlüğe kaydedilir. Eşitleme oturumlarının başarılı olup olmadığını (**HRESULT = 0**) ve öğe başına Eşitleme hataları olup olmadığını öğrenmek için bu olayı kullanın. Daha fazla bilgi için bkz. [eşitleme durumu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) ve [öğe başına hatalar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) belgeleri.

  > [!Note]  
  > Bazen eşitleme oturumları genel başarısız olur ya da sıfır olmayan bir PerItemErrorCount sayısına sahiptir. Ancak, yine de ilerleme durumu ve bazı dosyalar başarıyla eşitlenir. Bunu, AppliedFileCount, AppliedDirCount, AppliedTombstoneCount ve AppliedSizeBytes gibi uygulanan alanlarda görebilirsiniz. Bu alanlar, oturumun ne kadarının başarılı olduğunu bildirir. Bir satırda birden çok eşitleme oturumu başarısız görürseniz ve artan uygulanmış sayımla karşılaşırsanız, bir destek bileti açmadan önce yeniden denemek için eşitleme süresi verin.

- Etkin bir eşitleme oturumu varsa olay KIMLIĞI 9302, her 5 dakikada bir günlüğe kaydedilir. Geçerli eşitleme oturumunun ilerleme yapıp yapmadığını (**AppliedItemCount > 0**) öğrenmek için bu olayı kullanın. Eşitleme ilerlemeye devam etmez, eşitleme oturumunun sonunda başarısız olması gerekir ve hata ile 9102 olay KIMLIĞI günlüğe kaydedilir. Daha fazla bilgi için bkz. [eşitleme ilerlemesi belgeleri](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Kayıtlı sunucu durumu:

- Olay KIMLIĞI 9301, bir sunucu işler için hizmeti sorguladığında her 30 saniyede bir günlüğe kaydedilir. GetNextJob **durum = 0**ile sonlandıysanız, sunucu hizmetle iletişim kurabilir. GetNextJob bir hata ile sonlandıysanız, kılavuzluk için [sorun giderme belgelerini](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) denetleyin.

Bulut katmanlama durumu:

- Bir sunucudaki katmanlama etkinliğini izlemek için, *uygulamalar ve Services\Microsoft\FileSync\Agent*altında Olay Görüntüleyicisi bulunan telemetri olay GÜNLÜĞÜNDE olay kimliği 9003, 9016 ve 9029 kullanın.

  - Olay KIMLIĞI 9003, bir sunucu uç noktası için hata dağıtımı sağlar. Örneğin: Toplam hata sayısı ve hata kodu. Hata kodu başına bir olay günlüğe kaydedilir.
  - Olay kimliği 9016, bir birim için hayalet kopya oluşturuluyor sonuçları sağlar. Örneğin: Boş alan yüzdesi, oturumdaki hayalet dosya sayısı ve hayalet dosya sayısı ile başarısız oldu.
  - Olay kimliği 9029, bir sunucu uç noktası için hayalet kopya oluşturuluyor oturum bilgileri sağlar. Örneğin: Oturumda denenen dosya sayısı, oturumda katmanlı dosya sayısı ve zaten katmanlı dosya sayısı.
  
- Bir sunucudaki geri çekme etkinliğini izlemek için, *uygulamalar ve Services\Microsoft\FileSync\Agent*altında Olay Görüntüleyicisi bulunan telemetri olay GÜNLÜĞÜNDE olay kimliği 9005, 9006, 9009 ve 9059 kullanın.

  - Olay KIMLIĞI 9005, bir sunucu uç noktası için geri çağırma güvenilirliği sağlar. Örneğin: Erişilen toplam benzersiz dosya ve hatalı erişimi olan toplam benzersiz dosya sayısı.
  - Olay KIMLIĞI 9006, bir sunucu uç noktası için geri çağırma hatası dağıtımı sağlar. Örneğin: Toplam başarısız Istek ve hata kodu. Hata kodu başına bir olay günlüğe kaydedilir.
  - Olay KIMLIĞI 9009, bir sunucu uç noktası için geri çağırma oturumu bilgilerini sağlar. Örneğin: DurationSeconds, CountFilesRecallSucceeded ve CountFilesRecallFailed.
  - Olay KIMLIĞI 9059, bir sunucu uç noktası için uygulama geri çağırma dağıtımı sağlar. Örneğin: Shareıd, Application Name ve TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Performans sayaçları

Eşitleme etkinliğini izlemek için sunucuda Azure Dosya Eşitleme performans sayaçlarını kullanın.

Sunucuda Azure Dosya Eşitleme performans sayaçlarını görüntülemek için, performans Izleyicisi 'ni (Perfmon. exe) açın. Sayaçları, **aktarılan AFS bayt** ve **AFS eşitleme işlemleri** nesneleri altında bulabilirsiniz.

Azure Dosya Eşitleme için aşağıdaki performans sayaçları performans Izleyicisinde kullanılabilir:

| Performans Object\sayaç adı | Açıklama |
|-|-|
| AFS bayt Transferred\indirilen bayt/sn | Saniye başına indirilen bayt sayısı. |
| AFS bayt Transferred\karşıya yüklenen bayt/sn | Saniye başına karşıya yüklenen bayt sayısı. |
| AFS bayt Transferred\toplam bayt/sn | Toplam bayt/saniye (karşıya yükleme ve indirme). |
| AFS Sync Operations\indirilen eşitleme dosyaları/sn | Saniye başına indirilen dosya sayısı. |
| AFS Sync Operations\karşıya yüklenen eşitleme dosyaları/sn | Saniye başına karşıya yüklenen dosya sayısı. |
| AFS Sync Operations\toplam eşitleme dosyası Işlemi/sn | Eşitlenen toplam dosya sayısı (karşıya yükleme ve indirme). |

## <a name="next-steps"></a>Sonraki adımlar
- [Bir Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md)
- [Güvenlik duvarını ve proxy ayarlarını değerlendirin](storage-sync-files-firewall-and-proxy.md)
- [Azure Dosya Eşitleme’yi dağıtma](storage-sync-files-deployment-guide.md)
- [Azure Dosya Eşitleme ile ilgili sorunları giderme](storage-sync-files-troubleshoot.md)
- [Azure dosyaları hakkında sık sorulan sorular](storage-files-faq.md)
