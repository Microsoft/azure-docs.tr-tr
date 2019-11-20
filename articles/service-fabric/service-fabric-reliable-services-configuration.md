---
title: Azure Service Fabric Reliable Services yapılandırma | Microsoft Docs
description: Azure Service Fabric 'da durum bilgisi olan Reliable Services yapılandırma hakkında bilgi edinin.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 60a4669e20aa8aaf80ae174c88631f3dc572656d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242896"
---
# <a name="configure-stateful-reliable-services"></a>Durum bilgisi olan güvenilir Hizmetleri yapılandırma
Güvenilir hizmetler için iki yapılandırma ayarı kümesi vardır. Bir küme, diğer küme belirli bir güvenilir hizmete özel olduğu sürece kümedeki tüm güvenilir hizmetler için geneldir.

## <a name="global-configuration"></a>Genel yapılandırma
Küresel olarak güvenilir hizmet yapılandırması, Ktlgünlükçü bölümünün altındaki kümenin küme bildiriminde belirtilir. Paylaşılan günlük konumunun ve boyutunun ve günlük kaydı tarafından kullanılan genel bellek sınırlarının yapılandırılmasına izin verir. Küme bildirimi, kümedeki tüm düğümlere ve hizmetlere uygulanan ayarları ve konfigürasyonları tutan tek bir XML dosyasıdır. Dosya genellikle ClusterManifest. xml olarak adlandırılır. Get-ServiceFabricClusterManifest PowerShell komutunu kullanarak kümenizin küme bildirimini görebilirsiniz.

### <a name="configuration-names"></a>Yapılandırma adları
| Adı | Birim | Varsayılan değer | Açıklamalar |
| --- | --- | --- | --- |
| Writebuffermemorypoolminimumınkb |KB |8388608 |Günlükçü yazma arabelleği bellek havuzu için çekirdek modunda ayrılacak en az KB sayısı. Bu bellek havuzu, diske yazmadan önce durum bilgilerini önbelleğe almak için kullanılır. |
| WriteBufferMemoryPoolMaximumInKB |KB |Sınırsız |Günlükçü yazma arabelleği bellek havuzunun büyüyebileceği maksimum boyut. |
| Sharedlogıd |'INI |"" |Kümedeki tüm güvenilir hizmetler tarafından, hizmet özel yapılandırmasında Sharedlogıd belirtmeyen tüm tüm düğümlerde kullanılan varsayılan paylaşılan günlük dosyasını tanımlamak için kullanılacak benzersiz bir GUID belirtir. Sharedlogıd belirtilmişse, SharedLogPath de belirtilmelidir. |
| SharedLogPath |Tam nitelikli yol adı |"" |Tüm güvenilir hizmetler tarafından, hizmet özel yapılandırmasında SharedLogPath belirtmeyen tüm düğümlerde, paylaşılan günlük dosyasının kullandığı tam yolu belirtir. Ancak SharedLogPath belirtilmişse Sharedlogıd de belirtilmelidir. |
| Sharedlogsizeınmb |TI |8192 |Paylaşılan günlük için statik olarak ayrılacak MB disk alanı sayısını belirtir. Değer 2048 veya daha büyük olmalıdır. |

Azure ARM veya şirket içi JSON şablonunda aşağıdaki örnekte, durum bilgisi olan hizmetler için güvenilir koleksiyonları geri yüklemek üzere oluşturulan paylaşılan işlem günlüğünün nasıl değiştirileceği gösterilmektedir.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Örnek yerel geliştirici kümesi bildirim bölümü
Bunu yerel geliştirme ortamınızda değiştirmek istiyorsanız, yerel clustermanifest. xml dosyasını düzenlemeniz gerekir.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Açıklamalar
Günlükçü, güvenilir hizmet çoğaltmasıyla ilişkili ayrılmış günlüğe yazılmadan önce, durum verilerinin önbelleğe alınması için bir düğümdeki tüm güvenilir hizmetler tarafından kullanılabilen, disk belleğine alınmayan çekirdek bellekten ayrılan küresel bir bellek havuzuna sahiptir. Havuz boyutu Writebuffermemorypoolminimumınkb ve WriteBufferMemoryPoolMaximumInKB Settings tarafından denetlenir. Writebuffermemorypoolminimumınkb, bu bellek havuzunun başlangıç boyutunu ve bellek havuzunun küçülebileceği en düşük boyutu belirtir. WriteBufferMemoryPoolMaximumInKB, bellek havuzunun büyüyebileceği en yüksek boyutudur. Açık olan her güvenilir hizmet çoğaltması, bellek havuzunun boyutunu WriteBufferMemoryPoolMaximumInKB olan bir sistem tarafından belirlenen miktarda artırabilir. Bellek havuzundan kullanılabilir olandan daha fazla bellek talebi varsa bellek kullanılabilir olana kadar bellek istekleri gecikecek. Bu nedenle, yazma arabelleği bellek havuzu belirli bir yapılandırma için çok küçükse performans düşebilir.

Sharedlogıd ve SharedLogPath ayarları her zaman, kümedeki tüm düğümler için varsayılan paylaşılan günlük için GUID ve konum tanımlamak üzere birlikte kullanılır. Varsayılan paylaşılan günlük, belirli bir hizmet için Settings. xml dosyasında ayarları belirtmeyen tüm güvenilir hizmetler için kullanılır. En iyi performansı elde etmek için, paylaşılan günlük dosyaları yalnızca paylaşılan günlük dosyası için kullanılan disklere yerleştirilmelidir ve bu da çekişmeyi azaltır.

Sharedlogsizeınmb, tüm düğümlerde varsayılan paylaşılan günlük için önceden ayrılacak disk alanı miktarını belirtir.  Sharedlogsizeınmb 'nin belirtime sırası için Sharedlogıd ve SharedLogPath 'in belirtilmesi gerekmez.

## <a name="service-specific-configuration"></a>Hizmete özel yapılandırma
Durum bilgisi olan Reliable Services ' varsayılan yapılandırmalarını yapılandırma paketini (config) veya hizmet uygulamasını (kod) kullanarak değiştirebilirsiniz.

* **Yapılandırma paketi** aracılığıyla yapılandırma yapılandırması, uygulamadaki her bir hizmetin yapılandırma klasörü altındaki Microsoft Visual Studio paket kökünde oluşturulan Settings. xml dosyası değiştirilerek gerçekleştirilir.
* Kod aracılığıyla **kod** yapılandırması, uygun seçenekler ayarlanmış bir ReliableStateManagerConfiguration nesnesi kullanılarak bir ReliableStateManager oluşturularak gerçekleştirilir.

Varsayılan olarak, Azure Service Fabric Runtime, Settings. xml dosyasında önceden tanımlanmış bölüm adlarını arar ve temel alınan çalışma zamanı bileşenlerini oluştururken yapılandırma değerlerini kullanır.

> [!NOTE]
> Hizmetinizi kod aracılığıyla yapılandırmayı planlamıyorsanız, Visual Studio çözümünde oluşturulan Settings. xml dosyasında aşağıdaki yapılandırmaların bölüm **adlarını silmeyin.**
> Yapılandırma paketinin veya bölüm adlarının yeniden adlandırılması, ReliableStateManager yapılandırılırken bir kod değişikliği gerektirir.
> 
> 

### <a name="replicator-security-configuration"></a>Çoğaltıcı güvenlik yapılandırması
Çoğaltıcı güvenlik yapılandırması, çoğaltma sırasında kullanılan iletişim kanalının güvenliğini sağlamak için kullanılır. Bu, yüksek oranda kullanılabilir hale getirilen verilerin da güvenli olduğundan emin olmak için hizmetlerin birbirlerinin çoğaltma trafiğini göremeyeceği anlamına gelir. Varsayılan olarak, boş bir güvenlik yapılandırması bölümü, çoğaltma güvenliğini engeller.

> [!IMPORTANT]
> Linux düğümlerinde, sertifikaların ped biçimli olması gerekir. Linux için sertifikaları bulma ve yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Linux 'ta sertifikaları yapılandırma](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Varsayılan bölüm adı
ReplicatorSecurityConfig

> [!NOTE]
> Bu bölüm adını değiştirmek için, bu hizmet için ReliableStateManager 'ı oluştururken, replicatorSecuritySectionName parametresini ReliableStateManagerConfiguration oluşturucusuna geçersiz kılın.
> 
> 

### <a name="replicator-configuration"></a>Çoğaltıcı yapılandırması
Çoğaltıcı yapılandırması, durumu yerel olarak çoğaltırken ve kalıcı hale getirerek durum bilgisi olan güvenilir hizmetin durumunu yüksek oranda güvenilir hale getirmekten sorumlu olan çoğaltıcısını yapılandırır.
Varsayılan yapılandırma, Visual Studio şablonu tarafından oluşturulur ve yeterli olacaktır. Bu bölüm, çoğaltıcının ayarlanmasından kullanılabilecek ek yapılandırmaların ele geçirilmesini sağlar.

### <a name="default-section-name"></a>Varsayılan bölüm adı
ReplicatorConfig

> [!NOTE]
> Bu bölüm adını değiştirmek için, bu hizmet için ReliableStateManager 'ı oluştururken, replicatorSettingsSectionName parametresini ReliableStateManagerConfiguration oluşturucusuna geçersiz kılın.
> 
> 

### <a name="configuration-names"></a>Yapılandırma adları
| Adı | Birim | Varsayılan değer | Açıklamalar |
| --- | --- | --- | --- |
| Batchval Gementınterval |Saniye |0,015 |Birincili çoğaltıcının, birincil öğesine bir onay göndermeden önce bir işlem aldıktan sonra beklediği zaman aralığı. Bu Aralık dahilinde işlenen işlemler için gönderilecek diğer tüm onaylar bir yanıt olarak gönderilir. |
| ReplicatorEndpoint |Yok |Varsayılan değer yok--gerekli parametre |Birincil/ikincil çoğaltıcıların, çoğaltma kümesindeki diğer çoğaltıcılar ile iletişim kurmak için kullanacağı IP adresi ve bağlantı noktası. Bu, hizmet bildiriminde bir TCP kaynak uç noktasına başvurmalıdır. Hizmet bildiriminde uç nokta kaynakları tanımlama hakkında daha fazla bilgi edinmek için [hizmet bildirimi kaynaklarına](service-fabric-service-manifest-resources.md) bakın. |
| MaxPrimaryReplicationQueueSize |İşlem sayısı |8192 |Birincil kuyruktaki en fazla işlem sayısı. Birincil çoğaltıcı tüm ikincil replica'lerden onay aldıktan sonra bir işlem serbest bırakılır. Bu değer 64 ' den büyük ve 2 ' nin üssü olmalıdır. |
| MaxSecondaryReplicationQueueSize |İşlem sayısı |16384 |İkincil kuyruktaki en fazla işlem sayısı. Bir işlem, durumu Kalıcılık aracılığıyla yüksek oranda kullanılabilir olduktan sonra serbest bırakılır. Bu değer 64 ' den büyük ve 2 ' nin üssü olmalıdır. |
| CheckpointThresholdInMB |MB |50 |Durum denetim noktası olan günlük dosyası alanı miktarı. |
| Maxrecordsizeınkb |KB |1024 |Çoğaltıcının günlüğe yazabileceği en büyük kayıt boyutu. Bu değer 4 ' ün katı ve 16 ' dan büyük olmalıdır. |
| Minlogsizeınmb |MB |0 (sistem tarafından belirlenen) |İşlem günlüğünün en küçük boyutu. Bu ayarın altındaki bir boyuta kadar günlüğe Bu günlüğün kesilmesini izin verilmez. 0, çoğaltıcının en düşük günlük boyutunu belirleyeceğini belirtir. Bu değerin artırılması, kısmen kopya ve artımlı yedeklemeler yapma olasılığını artırarak, ilgili günlük kayıtlarının kesilme olasılığı düşürüldü. |
| TruncationThresholdFactor |Çarpan |2 |Günlüğün ne boyutta olduğunu belirler, kesilme tetiklenecek. Kesme eşiği, TruncationThresholdFactor tarafından çarpılan Minlogsizeınmb tarafından belirlenir. TruncationThresholdFactor 1 ' den büyük olmalıdır. Minlogsizeınmb * TruncationThresholdFactor, Maxstreamsizeınmb 'den küçük olmalıdır. |
| Kısıtlar Lingthresholdfactor |Çarpan |4 |Günlüğün ne boyutta olduğunu belirler, çoğaltmanın ne kadar kısıtlanacağını belirler. Azaltma eşiği (MB cinsinden), en fazla ((Minlogsizeınmb * kısıtlar Lingthresholdfactor), (Checkpointthresholdınmb * kısıtlar Lingthresholdfactor)) tarafından belirlenir. Azaltma eşiği (MB), kesme eşiğinden büyük olmalıdır (MB cinsinden). Kesme eşiği (MB), Maxstreamsizeınmb 'den küçük olmalıdır. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Belirli bir yedekleme günlüğü zincirindeki yedekleme günlüklerinin en büyük birikmiş boyutu (MB cinsinden). Artımlı yedekleme, ilgili tam yedeklemenin bu boyuttan daha büyük olması nedeniyle birikmiş yedekleme günlüklerinin oluşmasına neden olacak bir yedekleme günlüğü üretilebileceğinden, artımlı yedekleme istekleri başarısız olur. Bu gibi durumlarda, kullanıcının tam yedekleme yapması gerekir. |
| Sharedlogıd |'INI |"" |Bu çoğaltmayla kullanılan paylaşılan günlük dosyasını tanımlamak için kullanılacak benzersiz bir GUID belirtir. Genellikle, hizmetler bu ayarı kullanmamalıdır. Ancak, Sharedlogıd belirtilmişse, SharedLogPath de belirtilmelidir. |
| SharedLogPath |Tam nitelikli yol adı |"" |Bu çoğaltmanın paylaşılan günlük dosyasının oluşturulacağı tam yolu belirtir. Genellikle, hizmetler bu ayarı kullanmamalıdır. Ancak SharedLogPath belirtilmişse Sharedlogıd de belirtilmelidir. |
| Yavaş Apimonitoringduration |Saniye |300 |Yönetilen API çağrıları için izleme aralığını ayarlar. Örnek: Kullanıcı, yedekleme geri çağırma işlevi sağladı. Aralık geçtikten sonra, sistem durumu yöneticisine bir uyarı sistem durumu raporu gönderilir. |
| LogTruncationIntervalSeconds |Saniye |0 |Her yinelemede günlük kesilmesi başlatılacak yapılandırılabilir Aralık. Günlük boyutunun yalnızca günlük boyutu yerine zamana göre kesildiğinden emin olmak için kullanılır. Bu ayar ayrıca, güvenilir sözlükte silinen girdilerin temizlenmeye zorlar. Bu nedenle, silinen öğelerin zamanında temizlendiğinden emin olmak için kullanılabilir. |
| EnableStableReads |Boole |Yanlış |Kararlı okuma özelliğinin etkinleştirilmesi, ikincil çoğaltmaların çekirdek erişimi olan değerleri döndürmesini kısıtlar. |

### <a name="sample-configuration-via-code"></a>Kod aracılığıyla örnek yapılandırma
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Örnek yapılandırma dosyası
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Açıklamalar
Batchbildirimlerin Gementınterval çoğaltma gecikmesini denetler. ' 0 ' değeri, aktarım hızına (daha az sayıda bildirim içeren daha fazla onay iletisi gönderilmesi ve işlenmek üzere) karşı olası en düşük gecikme süresine neden olur.
Batchval Gementınterval değeri arttıkça, daha yüksek işlem gecikmesi maliyetinde genel çoğaltma üretilen işi artar. Bu, doğrudan işlem yürütmelerinin gecikmesini dönüştürür.

Checkpointthresholınvomb değeri, Replicator 'ın, durum bilgilerini çoğaltmanın ayrılmış günlük dosyasında depolamak için kullanabileceği disk alanı miktarını denetler. Bunu varsayılandan daha yüksek bir değere yükseltmek, kümesine yeni bir çoğaltma eklendiğinde daha hızlı yeniden yapılandırma süreleriyle sonuçlanabilir. Bu, günlükteki daha fazla işlem geçmişinin kullanılabilirliği nedeniyle gerçekleşen kısmi durum aktarımından kaynaklanır. Bu, kilitlenme sonrasında bir çoğaltmanın kurtarma süresini artırabilir.

Maxrecordsizeınkb ayarı, çoğaltıcının günlük dosyasına yazılabileceği en büyük bir kayıt boyutunu tanımlar. Çoğu durumda, varsayılan 1024 KB kayıt boyutu idealdir. Ancak, hizmet daha büyük veri öğelerinin durum bilgilerinin bir parçası olmasına neden oluyorsa, bu değerin artırılması gerekebilir. Daha küçük kayıtlar yalnızca daha küçük bir kayıt için gereken alanı kullanırken, 1024 'den daha küçük olan Maxrecordsizeınkb 'yi yaparken çok daha fazla avantaj vardır. Bu değerin yalnızca nadir durumlarda değiştirilmesi gerektiğini umuz.

Bir hizmetin, düğümün varsayılan paylaşılan günlüğünden ayrı bir paylaşılan günlük kullanmasını sağlamak için Sharedlogıd ve SharedLogPath ayarları her zaman birlikte kullanılır. En iyi verimlilik için, olabildiğince fazla hizmetin aynı paylaşılan günlüğü belirtmesi gerekir. Paylaşılan günlük dosyaları, baş hareket çekişmesini azaltmak için yalnızca paylaşılan günlük dosyası için kullanılan disklere yerleştirilmelidir. Bu değerin yalnızca nadir durumlarda değiştirilmesi gerektiğini umuz.

## <a name="next-steps"></a>Sonraki adımlar
* [Visual Studio 'da Service Fabric uygulamanızda hata ayıklama](service-fabric-debugging-your-application.md)
* [Reliable Services için geliştirici başvurusu](https://msdn.microsoft.com/library/azure/dn706529.aspx)

