---
title: Service Fabric kümenizdeki Windows işletim sistemini düzeltme eki uygulama | Microsoft Docs
description: Bu makalede, düzeltme eki düzenleme uygulaması kullanılarak bir Service Fabric kümesinde işletim sistemi düzeltme eki uygulama işlemini nasıl otomatikleştirebileceğiniz açıklanır.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: a02228593a9d8efc9fb363232da1cede3c80a8b3
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592525"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric kümenizdeki Windows işletim sistemini düzeltme eki uygulama

> 
> [!IMPORTANT]
> 30 Nisan 2019 itibariyle, düzeltme eki Orchestration uygulama sürümü 1,2. * artık desteklenmemektedir. En son sürüme yükseltdiğinizden emin olun.

> [!NOTE]
> [Sanal makine ölçek kümeniz üzerinde OTOMATIK işletim sistemi görüntüsü yükseltmelerini](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) alma, Işletim sisteminizi Azure 'da düzeltme eki uygulama konusunda en iyi uygulamadır. Sanal makine ölçek kümesi tabanlı otomatik işletim sistemi görüntüsü yükseltmeleri, ölçek kümesi üzerinde gümüş veya daha fazla dayanıklılık gerektirir.
>

 Düzeltme Eki düzenleme uygulaması (POA), Azure 'da barındırılan kümeler için yapılandırma tabanlı işletim sistemi düzeltme eki zamanlamaya izin veren Azure Service Fabric Onarım Yöneticisi hizmeti etrafında bir sarmalayıcıdır. Azure dışı barındırılan kümeler için POA gerekli değildir, ancak kapalı kalma süresi olmadan Service Fabric küme konaklarına yama yapmak için güncelleştirme etki alanı tarafından yapılan düzeltme eki yüklemeyi zamanlama gerekir.

POA, kapalı kalma süresi olmadan bir Service Fabric kümesinde işletim sistemi düzeltme eki uygulamayı otomatikleştiren Service Fabric bir uygulamadır.

POA aşağıdaki özellikleri sağlar:

- **Otomatik işletim sistemi güncelleştirme yüklemesi**. İşletim sistemi güncelleştirmeleri otomatik olarak indirilir ve yüklenir. Küme düğümleri, küme kapalı kalma süresi olmadan gerektiği şekilde yeniden başlatılır.

- **Küme durumunu algılayan düzeltme eki uygulama ve sistem durumu tümleştirmesi**. POA güncelleştirmeleri uygularken, küme düğümlerinin sistem durumunu izler. Küme düğümleri tek seferde bir düğüm veya tek bir güncelleştirme etki alanı olarak güncelleştirilir. Düzeltme eki uygulama işlemi nedeniyle kümenin sistem durumu kapalıysa, sorunu gidermek için düzeltme eki uygulama durdurulur.

## <a name="internal-details-of-poa"></a>POA 'nın iç ayrıntıları

POA aşağıdaki alt bileşenleri oluşur:

- **Koordinatör hizmeti**: Bu durum bilgisi olan bu hizmetin sorumluluğundadır:
    - Tüm kümedeki Windows Update işi koordine edin.
    - Tamamlanan Windows Update işlemlerinin sonucunu depolama.

- **Düğüm Aracısı hizmeti**: Bu durum bilgisiz hizmeti tüm Service Fabric küme düğümlerinde çalışır. Hizmetin sorumluluğundadır:
    - Düğüm Aracısı NTService önyükleniyor.
    - Düğüm Aracısı NTService izleniyor.

- **Düğüm Aracısı NTService**: Bu Windows NT hizmeti, daha üst düzey bir ayrıcalıkta (sistem) çalışır. Buna karşılık, düğüm Aracısı hizmeti ve Düzenleyici hizmeti alt düzey bir ayrıcalıkta (ağ HIZMETI) çalışır. Hizmet, tüm küme düğümlerinde aşağıdaki Windows Update işleri gerçekleştirmekten sorumludur:
    - Düğümdeki otomatik Windows güncelleştirmelerini devre dışı bırakma.
    - Windows güncelleştirmelerini kullanıcı tarafından girilen ilkeye göre indirme ve yükleme.
    - Makine Windows güncelleştirme sonrası yüklemesi yeniden başlatılıyor.
    - Windows güncelleştirmelerinin sonuçları düzenleyici hizmetine yükleniyor.
    - Tüm yeniden denemeler tüketildikten sonra bir işlem başarısız olduysa, raporlama sistem durumu raporları.

> [!NOTE]
> POA, düğümü devre dışı bırakmak veya etkinleştirmek ve sistem durumu denetimleri gerçekleştirmek için Service Fabric Onarım Yöneticisi hizmetini kullanır. POA tarafından oluşturulan onarım görevi her düğüm için Windows Update ilerlemesini izler.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
> Gerekli en düşük .NET Framework sürümü 4,6 ' dir.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Onarım Yöneticisi hizmetini etkinleştirin (zaten çalışmıyorsa)

POA, küme üzerinde Onarım Yöneticisi hizmetinin etkinleştirilmesini gerektirir.

#### <a name="azure-clusters"></a>Azure kümeleri

Gümüş dayanıklılık katmanındaki Azure kümelerinin varsayılan olarak Onarım Yöneticisi hizmeti etkinleştirilmiştir. Altın dayanıklılık katmanındaki Azure kümeleri, bu kümelerin ne zaman oluşturulduğuna bağlı olarak Onarım Yöneticisi hizmeti etkin olabilir veya olmayabilir. Varsayılan olarak, bronz dayanıklılık katmanındaki Azure kümelerinin Onarım Yöneticisi hizmeti etkin değildir. Hizmet zaten etkinleştirilmişse, Service Fabric Explorer ' deki Sistem Hizmetleri bölümünde çalıştığını görebilirsiniz.

##### <a name="the-azure-portal"></a>Azure portal
Kümeyi ayarlarken Azure portal Onarım Yöneticisi etkinleştirebilirsiniz. Kümeyi yapılandırırken, **eklenti özellikleri**altında **içerme onarım Yöneticisi** seçeneğini belirleyin.

![Azure portal Onarım Yöneticisi etkinleştirme görüntüsü](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager dağıtım modeli
Alternatif olarak, yeni ve mevcut Service Fabric kümelerinde Onarım Yöneticisi hizmetini etkinleştirmek için [Azure Resource Manager dağıtım modelini](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) kullanabilirsiniz. Dağıtmak istediğiniz kümenin şablonunu alın. Örnek şablonları kullanabilir ya da özel bir Azure Resource Manager dağıtım modeli şablonu oluşturabilirsiniz. 

Onarım Yöneticisi hizmetini [Azure Resource Manager dağıtım modeli şablonunu](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)kullanarak etkinleştirmek için aşağıdakileri yapın:

1. *Microsoft. ServiceFabric/kümeler* kaynağı için `apiVersion` *2017-07-01-Preview* olarak ayarlandığından emin olun. Farklıysa, `apiVersion` *2017-07-01-Preview* veya sonraki bir sürüme güncelleştirmeniz gerekir:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. @No__t_1 bölümünden sonra aşağıdaki `addonFeatures` bölümünü ekleyerek Onarım Yöneticisi hizmetini etkinleştirin:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Küme şablonunuzu bu değişikliklerle güncelleştirdikten sonra, bu değişiklikleri uygulayın ve güncelleştirme işleminin bitmesini sağlayın. Artık kümenizde çalışan Onarım Yöneticisi hizmeti görebilirsiniz. *Yapı:/System/RepairManagerService* , Service Fabric Explorer içindeki Sistem Hizmetleri bölümünde yer olarak adlandırılır. 

### <a name="standalone-on-premises-clusters"></a>Tek başına şirket içi kümeler

Yeni veya mevcut bir Service Fabric kümesinde Onarım Yöneticisi hizmeti etkinleştirmek için, [tek başına Windows kümesi yapılandırma ayarlarını](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)kullanabilirsiniz.

Onarım Yöneticisi hizmetini etkinleştirmek için:

1. [Genel küme yapılandırmalarında](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) `apiVersion`, burada gösterildiği gibi, *04-2017* veya üzeri bir sürüme ayarlandığından emin olun:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Aşağıda gösterildiği gibi `fabricSettings` bölümünden sonra aşağıdaki `addonFeatures` bölümünü ekleyerek Onarım Yöneticisi hizmetini etkinleştirin:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Güncelleştirilmiş küme bildirimini kullanarak bu değişikliklerle küme bildiriminizi güncelleştirin [Yeni bir küme oluşturun](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) veya [küme yapılandırmasını yükseltin](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Küme, güncelleştirilmiş bir küme bildirimiyle çalıştıktan sonra, kümenizde çalışan Onarım Yöneticisi hizmetini görebilirsiniz. *Fabric:/System/RepairManagerService*olarak adlandırılır ve bu, Service Fabric Explorer sistem hizmetleri bölümünde yer alabilir.

### <a name="configure-windows-updates-for-all-nodes"></a>Tüm düğümler için Windows güncelleştirmelerini yapılandırma

Aynı anda birden çok küme düğümü yeniden başlatabildiğinden otomatik Windows güncelleştirmeleri kullanılabilirlik kaybına neden olabilir. POA, varsayılan olarak her küme düğümündeki otomatik Windows güncelleştirmelerini devre dışı bırakmayı dener. Ancak, ayarlar bir yönetici veya bir grup ilkesi tarafından yönetiliyorsa, Windows Update ilkesini "Indirmadan önce bildir" olarak ayarlamayı öneririz.

## <a name="download-the-application-package"></a>Uygulama paketini indirin

Uygulama paketini indirmek için GitHub 'daki [Düzeltme Eki düzenleme uygulaması sürüm sayfasına](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) gidin.

## <a name="configure-poa-behavior"></a>POA davranışını yapılandırma

Gereksinimlerinizi karşılamak için POA davranışını yapılandırabilirsiniz. Uygulamayı oluştururken veya güncelleştirirken uygulama parametresini geçirerek varsayılan değerleri geçersiz kılın. @No__t_1 veya `New-ServiceFabricApplication` cmdlet 'lerine `ApplicationParameter` belirterek uygulama parametreleri sağlayabilirsiniz.

| Parametre        | Tür                          | Ayrıntılar |
|:-|-|-|
|MaxResultsToCache    |Uzun                              | Önbelleğe alınması gereken Windows Update sonuçlarının en fazla sayısı. <br><br>Varsayılan değer 3000 ' dir, ancak şunları kabul edilir: <br> &nbsp; &nbsp;-düğüm sayısı 20 ' dir. <br> &nbsp; &nbsp;-ayda bir düğümdeki güncelleştirme sayısı 5 ' tir. <br> &nbsp; &nbsp;-işlem başına sonuç sayısı 10 olabilir. <br> &nbsp; &nbsp;-son üç aya ait sonuçlar depolanmalıdır. |
|TaskApprovalPolicy   |Sabit Listesi <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy, Windows güncelleştirmelerini Service Fabric kümesi düğümlerine yüklemek için Düzenleyici hizmeti tarafından kullanılacak ilkeyi gösterir.<br><br>İzin verilen değerler şunlardır: <br>*Nodewise*: Windows güncelleştirmeleri tek seferde bir düğüm yüklenir. <br> *Upgradedomainwise*: Windows güncelleştirmeleri tek seferde bir güncelleştirme etki alanı yüklenir. (En çok, bir güncelleştirme etki alanına ait tüm düğümler Windows Update 'e gidebilir.)<br><br> Kümeniz için en uygun ilke hakkında karar vermeye yardımcı olmak için [SSS](#frequently-asked-questions) bölümüne bakın.
|Logsdiskquocontainer MB   |Uzun  <br> (Varsayılan: *1024*)               | , Düğümlerde yerel olarak kalıcı hale getirebilen, yama düzenleme uygulamasının en büyük boyutu MB olarak kaydedilir.
| WUQuery               | string<br>(Varsayılan: *IsInstalled = 0*)                | Windows güncelleştirmelerini almak için sorgu. Daha fazla bilgi için bkz [. Wuquery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| Installwindowsosonlyupdates | *Boolean* <br> (varsayılan: false)                 | Hangi güncelleştirmelerin indirilip yükleneceğini denetlemek için bu bayrağı kullanın. Aşağıdaki değerlere izin verilir <br>true-yalnızca Windows işletim sistemi güncelleştirmelerini kurar.<br>false-makinede bulunan tüm güncelleştirmeleri yükleme.          |
| Wuoperationtimeoutınminutes | 'Tir <br>(Varsayılan: *90*)                   | Herhangi bir Windows Update işlemi için (arama veya indirme veya yükleme) zaman aşımını belirtir. İşlem belirtilen zaman aşımı süresi içinde tamamlanmazsa, durdurulur.       |
| WURescheduleCount     | 'Tir <br> (Varsayılan: *5*)                  | Bir işlemin kalıcı olarak başarısız olması halinde hizmetin Windows Update müşteri sizinle randevusunu en fazla kaç kez başarısız olduğunu.          |
| WURescheduleTimeInMinutes | 'Tir <br>(Varsayılan: *30*) | Hata devam ederse hizmetin Windows güncelleştirmelerini müşteri sizinle randevusunu zaman aralığı. |
| WUFrequency           | Virgülle ayrılmış dize (varsayılan: *haftalık, Çarşamba, 7:00:00*)     | Windows güncelleştirmelerini yükleme sıklığı. Biçim ve olası değerler şunlardır: <br>&nbsp; &nbsp;-aylık: gg, ss: DD: SS (örneğin, *aylık, 5, 12:22:32*)<br>Alan gg (gün) için izin verilen değerler 1 ile 28 ve "son" sayılardır. <br> &nbsp; &nbsp;-haftalık, gün, ss: DD: SS (örneğin, *haftalık, Salı, 12:22:32*)  <br> &nbsp; &nbsp;-günlük, ss: DD: SS (örneğin, *günlük, 12:22:32*)  <br> &nbsp; &nbsp; -  *yok* , Windows güncelleştirmelerinin yapılmayacağını gösterir.  <br><br> Süreler UTC olarak.|
| AcceptWindowsUpdateEula | Boole <br>(Varsayılan: *true*) | Bu bayrak ayarlanarak uygulama, makinenin sahibi adına Windows Update için Son Kullanıcı Lisans sözleşmesini kabul eder.              |

> [!TIP]
> Windows güncelleştirmelerinin hemen gerçekleşmesini istiyorsanız uygulama dağıtım zamanına göre `WUFrequency` ayarlayın. Örneğin, beş düğümlü bir test kümeniz olduğunu ve uygulamayı 5:00 PM UTC 'de dağıtmayı planladığınızı varsayalım. Uygulama yükseltme veya dağıtımın en fazla 30 dakika sürdüğünü varsaydıysanız, WUFrequency *'Yi günlük, 17:30:00*olarak ayarlayın.

## <a name="deploy-poa"></a>POA dağıtma

1. Kümeyi hazırlamak için tüm önkoşul adımlarını sona erdirin.
1. POA 'yı diğer Service Fabric uygulamaları gibi dağıtın. PowerShell kullanarak dağıtmak için bkz. [PowerShell kullanarak uygulama dağıtma ve kaldırma](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Uygulamayı dağıtım sırasında yapılandırmak için `ApplicationParameter` `New-ServiceFabricApplication` cmdlet 'ine geçirin. Size kolaylık olması için, uygulama ile birlikte Deploy. ps1 betiğini sağladık. Betiği kullanmak için:

    - @No__t_0 kullanarak Service Fabric kümesine bağlanın.
    - Dağıtım. ps1 PowerShell betiğini uygun `ApplicationParameter` değeriyle yürütün.

> [!NOTE]
> Betiği ve uygulama klasörü *Patchorchestrationapplication* klasörünü aynı dizinde saklayın.

## <a name="upgrade-poa"></a>Yükseltme POA

POA sürümünüzü PowerShell kullanarak yükseltmek için, [PowerShell kullanarak uygulama yükseltme Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)içindeki yönergeleri izleyin.

## <a name="remove-poa"></a>POA 'yı kaldır

Uygulamayı kaldırmak için [PowerShell kullanarak uygulamaları dağıtma ve kaldırma](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)bölümündeki yönergeleri izleyin.

Kolaylık olması için uygulama ile birlikte undeploy. ps1 betiğini sağladık. Betiği kullanmak için:

  - @No__t_0 kullanarak Service Fabric kümesine bağlanın.
  - PowerShell betiğini undeploy. ps1 ' i yürütün.

> [!NOTE]
> Betiği ve uygulama klasörü *Patchorchestrationapplication* klasörünü aynı dizinde saklayın.

## <a name="view-the-windows-update-results"></a>Windows Update sonuçlarını görüntüleme

POA, geçmiş sonuçları kullanıcılara göstermek için REST API 'Leri sunar. İşte JSON sonucu örneği:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON alanları aşağıdaki tabloda açıklanmıştır:

Alan | Değerler | Ayrıntılar
-- | -- | --
OperationResult | 0-başarılı<br> 1-hatalarla başarılı oldu<br> 2-başarısız oldu<br> 3-iptal edildi<br> 4-zaman aşımı Ile iptal edildi | Genellikle bir veya daha fazla güncelleştirmenin yüklenmesini içeren genel işlemin sonucunu gösterir.
ResultCode | OperationResult ile aynı | Bu alan, tek bir güncelleştirme için yükleme işleminin sonucunu gösterir.
OperationType | 1-yükleme<br> 0-arama ve Indirme| Varsayılan olarak, yükleme sonuçlarında gösterilen tek OperationType ' dır.
WindowsUpdateQuery | Varsayılan değer "IsInstalled = 0" | Güncelleştirme aramak için kullanılan sorgu Windows Update. Daha fazla bilgi için bkz. [Wuquery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | doğru-yeniden başlatma gerekiyordu<br> false-yeniden başlatma gerekli değil | Güncelleştirmelerin yüklenmesinin tamamlanabilmesi için yeniden başlatma gerekip gerekmediğini gösterir.
OperationStartTime | Tarih Saat | İşlemin (Indirme/yükleme) başladığı süreyi belirtir.
OperationTime | Tarih Saat | İşlemin (Indirme/yükleme) tamamlandığı süreyi belirtir.
HResult | 0-başarılı<br> diğer hata| UpdateID ile Windows Update hatasının nedeni "7392acaf-6a85-427c-8a8d-058c25beb0d6" olduğunu gösterir.

Henüz bir güncelleştirme zamanlanmamışsa, JSON sonucu boştur.

Windows Update sonuçları sorgulamak için kümede oturum açın. Koordinatör hizmetinin birincil adresinin çoğaltma IP adresini bulun ve şu URL 'YI tarayıcıdan açın: http://&lt;REPLICA-IP &gt;: &lt;ApplicationPort &gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

Koordinatör hizmeti için REST uç noktası dinamik bir bağlantı noktasına sahiptir. Tam URL 'yi denetlemek için Service Fabric Explorer başvurun. Örneğin, sonuçlar *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* kullanılabilir.

![REST uç noktasının görüntüsü](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Kümede ters proxy etkinse, URL 'ye küme dışından da erişebilirsiniz.

İsabet etmeniz gereken uç nokta, *http://&lt;SERVERURL &gt;: &lt;REVERSEPROXYPORT &gt;/Patchorchestrationapplication/koordinattorservice/v1/getwindowsupdateresults*.

Kümede ters proxy 'yi etkinleştirmek için [Azure Service Fabric 'de ters proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)'deki yönergeleri izleyin. 

> 
> [!WARNING]
> Ters proxy yapılandırıldıktan sonra, kümedeki bir HTTP uç noktasını kullanıma sunan tüm mikro hizmetler küme dışından adreslenebilir.

## <a name="diagnostics-and-health-events"></a>Tanılama ve sistem durumu olayları

Bu bölümde, Service Fabric kümelerinde POA üzerinden düzeltme eki güncelleştirmeleriyle ilgili sorunları ayıklama veya tanılama konularında açıklanmaktadır.

> [!NOTE]
> Aşağıdaki, kendinden fazla tanılama geliştirmelerinden çoğunu almak için, POA sürüm 1.4.0 veya daha yeni BIR sürümü yüklemiş olmanız gerekir.

NTService düğüm Aracısı, düğümlere güncelleştirme yüklemek için [onarım görevleri](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) oluşturuyor. Her görev daha sonra, görev onay ilkesine göre Düzenleyici hizmeti tarafından hazırlanır. Son olarak, hazırlanan görevler Onarım Yöneticisi tarafından onaylanır ve bu, küme sağlıksız durumdaysa hiçbir görevi onaylamaz. 

Güncelleştirmelerin düğüm üzerinde nasıl ilerleyerek anlamanıza yardımcı olmak için adım adım:

1. Her düğümde çalışan NodeAgentNTService, zamanlanan zamanda kullanılabilir Windows güncelleştirmelerini arar. Güncelleştirmeler varsa, bunları düğümüne indirir.

1. Güncelleştirmeler indirildikten sonra, NTService düğüm Aracısı, *POS___ \<unique_id >* adlı düğüm için karşılık gelen bir onarım görevi oluşturur. Bu onarım görevlerini [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) cmdlet 'ini kullanarak veya düğüm ayrıntıları bölümünde SFX ' i kullanarak görüntüleyebilirsiniz. Onarım görevi oluşturulduktan sonra hızlı bir şekilde, [ *talep* edilen duruma](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)gider.

1. Koordinatör hizmeti, belirli *bir durumdaki onarım* görevlerini düzenli aralıklarla arar ve ardından TaskApprovalPolicy 'e göre durumu *hazırlamaya* yönelik olarak güncelleştirir. TaskApprovalPolicy NodeWise olacak şekilde yapılandırıldıysa, bir düğüme karşılık gelen bir onarım görevi yalnızca *hazırlama*, *onaylanan*, *yürütme*veya *geri yükleme* durumunda başka bir onarım görevi yoksa hazırlanır. 

   Benzer şekilde, UpgradeWise TaskApprovalPolicy söz konusu olduğunda, önceki durumlarda yalnızca aynı güncelleştirme etki alanına ait düğümler için görevler vardır. Bir onarım görevi *hazırlama* durumuna taşındıktan sonra, karşılık gelen Service Fabric düğümü, amaç *yeniden başlatılacak*şekilde ayarlanmış olarak [devre dışıdır](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) .

   POA sürümleri 1.4.0 ve üzeri, düzeltme eki eklenen düğümleri göstermek için Koordinatör Torservice üzerindeki ClusterPatchingStatus özelliğiyle olayları nakleder. Güncelleştirmeler, aşağıdaki görüntüde gösterildiği gibi _poanode_0 üzerine yüklenir:

    [küme düzeltme eki uygulama durumu ![Image](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Düğüm devre dışı bırakıldıktan sonra onarma görevi *yürütülüyor* durumuna taşınır. 
   
   > [!NOTE]
   > *Devre dışı bırakılmış* durumda kalmış bir düğüm yeni bir onarım görevini engelleyebilir ve bu da kümedeki düzeltme eki uygulama işlemini durdurur.

1. Onarım görevi *yürütme* durumundaysa, bu düğümdeki düzeltme eki yüklemesi başlar. Düzeltme eki yüklendikten sonra, düzeltme ekine bağlı olarak düğüm yeniden başlatılmamış olabilir veya başlatılamayabilir. Ardından, onarım görevi, düğümü yeniden sağlayan *geri yükleme* durumuna taşınır. Onarım görevi daha sonra tamamlandı olarak işaretlenir.

   POA sürümlerinde 1.4.0 ve üzeri sürümlerde, WUOperationStatus-\<NodeName > özelliği ile NodeAgentService üzerindeki sistem durumu olaylarını görüntüleyerek güncelleştirme durumunu bulabilirsiniz. Aşağıdaki resimlerde vurgulanan bölümler, *poanode_0* ve *poanode_2*düğümlerinde Windows güncelleştirmelerinin durumunu gösterir:

   [Windows Update işlem durumunun ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [Windows Update işlem durumunun ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Ayrıca, PowerShell 'i kullanarak da Ayrıntılar alabilirsiniz. Bunu yapmak için kümeye bağlanır ve [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)kullanarak onarım görevinin durumunu getirebilirsiniz. 
   
   Aşağıdaki örnekte, "POS__poanode_2_125f2969-933c-4774-85D1-ebdf85e79f15" görevi *Downloadtamamlanma* durumunda. Bu, güncelleştirmelerin *poanode_2* düğümüne indirildiğini ve görev *yürütme* durumuna geçerse yüklemenin denenmesini sağlar.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Daha fazla sorun bulunmaya devam ederseniz, sanal makinenizde (VM) veya VM 'Lerde oturum açın ve Windows olay günlüklerini kullanarak bunlarla ilgili bilgi edinin. Daha önce bahsedilen onarım görevi yalnızca aşağıdaki yürütücü alt durumlarında bulunabilir:

      Yürütişalt durumu | Açıklama
    -- | -- 
      Hiçbiri = 1 |  Düğümde devam eden bir işlem olmadığını gösterir. Durum geçiş durumunda olabilir.
      DownloadCompleted = 2 | İndirme işleminin başarılı, kısmi hata veya hata ile tamamlandığını gösterir.
      Instalonaylanan onaylandı = 3 | İndirme işleminin daha önce tamamlandığını ve Onarım Yöneticisi yüklemeyi onayladığını gösterir.
      Instalınstalprogress = 4 | Onarım görevinin yürütme durumuna karşılık gelir.
      Yüklemetamamlandı = 5 | Yüklemenin başarılı, kısmen başarılı veya başarısız ile tamamlandığını gösterir.
      RestartRequested = 6 | Düzeltme Eki yüklemesinin tamamlandığını ve düğümde bekleyen bir yeniden başlatma eylemi olduğunu gösterir.
      RestartNotNeeded = 7 |  Düzeltme eki yüklemesi tamamlandıktan sonra yeniden başlatmanın gerekli olmadığı anlamına gelir.
      RestartCompleted = 8 | Yeniden başlatmanın başarıyla tamamlandığını gösterir.
      OperationCompleted = 9 | Windows Update işlemi başarıyla tamamlandı.
      Operationdurdurulan = 10 | Windows Update işleminin durdurulduğunu belirtir.

1. POA sürümlerinde 1.4.0 ve sonraki sürümlerde, bir düğüm güncelleştirme denemesi tamamlandığında, Windows güncelleştirmelerini indirme ve yükleme denemesinin başlaması için NodeAgentService üzerinde "WUOperationStatus-[DüğümAdı]" özelliği ile bir olay gönderilir. Bu, aşağıdaki görüntüde görüntülenir:

     [Windows Update işlem durumunun ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Tanılama günlükleri

Düzeltme Eki düzenleme uygulama günlükleri Service Fabric çalışma zamanı günlüklerinin bir parçası olarak toplanır.

Tanılama aracını veya tercih ettiğiniz işlem hattını kullanarak günlükleri yakalayabilirsiniz. POA olayları [olay kaynağı](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)aracılığıyla günlüğe kaydetmek için aşağıdaki sabit sağlayıcı kimliklerini kullanır:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Sistem durumu raporları

POA Ayrıca, aşağıdaki senaryolarda düğüm Aracısı hizmetine veya Koordinatör hizmetine karşı sistem durumu raporlarını yayınlar:

* NTService düğüm Aracısı çalışmıyor

   Düğüm Aracısı NTService düğüm üzerinde kapalıysa, düğüm Aracısı hizmetine karşı bir uyarı düzeyi sistem durumu raporu oluşturulur.

* Onarım Yöneticisi hizmeti etkin değil

   Onarım Yöneticisi hizmeti kümede bulunamazsa, Düzenleyici hizmeti için bir uyarı düzeyi sistem durumu raporu oluşturulur.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: POA çalışırken Kümemin neden hata durumunda olduğunu görüyorum?**

Y: yükleme işlemi sırasında, POA, düğümleri devre dışı bırakır veya yeniden başlatır, bu durum geçici olarak sağlıksız bir kümeyle sonuçlanabilir.

Uygulamanın ilkesine bağlı olarak, düzeltme eki uygulama sırasında tek bir düğüm aşağı gidebilir *veya* tüm güncelleştirme etki alanı bir kez daha devam edebilir.

Windows güncelleştirmeleri yüklemesinin sonuna kadar, düğümler yeniden başlatma sonrası yeniden etkinleştirilir.

Aşağıdaki örnekte, iki düğüm çalışmadığından ve MaxPercentageUnhealthyNodes İlkesi ihlal edildiğinden, küme geçici olarak bir hata durumuna geçti. Düzeltme eki uygulama başlayana kadar hata geçicidir.

![Sağlıksız kümenin görüntüsü](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Sorun devam ederse, sorun giderme bölümüne bakın.

**S: POA bir uyarı durumundaysa ne yapabilirim?**

A: uygulamaya karşı gönderilen bir sistem durumu raporunun kök nedenin olup olmadığını denetleyin. Genellikle uyarı, sorunun ayrıntılarını içerir. Sorun geçici ise, uygulamanın otomatik olarak kurtarılması beklenir.

**S: Kümem sağlıksız ve acil bir işletim sistemi güncelleştirmesi yapmam gerekiyorsa ne yapabilirim?**

Y: POA, küme sağlıksız olduğu sürece güncelleştirmeleri yüklemez. POA iş akışının engellemesini kaldırmak için kümenizi sağlıklı bir duruma getirmeyi deneyin.

**S: Kümem için TaskApprovalPolicy "NodeWise" veya "UpgradeDomainWise" olarak ayarlanmalıdır mi?**

Y: "UpgradeDomainWise" ayarı, bir güncelleştirme etki alanına ait tüm düğümlerde paralel olarak düzeltme eki uygulayarak tüm küme onarımını hızlandırır. İşlem sırasında, bir güncelleştirme etki alanının tamamına ait olan düğümler kullanılamaz ( [ *devre dışı* durumda](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Buna karşılık, "NodeWise" ayarı, tek seferde yalnızca bir düğümün düzeltme eklerini ekler ve bu da genel küme düzeltme eki uygulama işleminin daha uzun sürebileceğini göstermez. Ancak, düzeltme eki uygulama sırasında en fazla yalnızca bir düğüm kullanılamaz ( *devre dışı* durumda).

Kümeniz, düzeltme eki uygulama sırasında N-1 güncelleştirme etki alanı üzerinde çalışmaya tolerans sağlayabilir (burada N, kümenizdeki toplam güncelleştirme etki alanı sayısıdır) ilkeyi "UpgradeDomainWise" olarak ayarlayabilirsiniz. Aksi takdirde, bunu "NodeWise" olarak ayarlayın.

**S: bir düğümün yama ne kadar zaman alır?**

Y: bir düğümün düzeltme eki uygulama (örneğin, Windows [Defender tanım güncelleştirmeleri](https://www.microsoft.com/en-us/wdsi/definitions)) ile saatlere kadar sürebilir (örneğin, [Windows toplu güncelleştirmeleri](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Bir düğüme düzeltme için gereken süre, genellikle şunlara bağlıdır: 
 - Güncelleştirmelerin boyutu.
 - Düzeltme eki uygulama penceresinde uygulanması gereken güncelleştirme sayısı.
 - Güncelleştirmelerin yüklenmesi için geçen süre (gerekliyse) ve yeniden başlatma sonrası yükleme adımlarını tamamlaymanız gerekir.
 - VM veya makinenin performansı ve ağ koşulları.

**S: bir kümenin tamamına yama ne kadar sürer?**

Y: bir kümenin tamamına yama yapmak için gereken süre şunlara bağlıdır:

- Bir düğümün yaması için gereken süre.

- Düzenleyici hizmetinin ilkesi. "NodeWise" adlı varsayılan ilke, "UpgradeDomainWise" kullanmaktan daha yavaş bir yaklaşım olan tek seferde yalnızca bir düğüm düzeltme eki uygulama ile sonuçlanır. 

   Örneğin: bir düğümün düzeltme eki uygulamak için yaklaşık 1 saat sürüyorsa, 5 güncelleştirme etki alanı bulunan her biri 4 düğüm içeren bir 20 düğüm (aynı düğüm türü) kümesi düzeltme eki uygulama şunları gerektirir:
    - "NodeWise" için: ~ 20 saat.
    - "UpgradeDomainWise" için: ~ 5 saat.

- Küme yükü. Düzeltme eki uygulama işlemi, müşteri iş yükünün kümedeki diğer kullanılabilir düğümlere yeniden konumlandırması gerektirir. Düzeltme Eki eklenen bir düğüm, bu süre içinde [durumu *devre dışı bırakmak* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) için kullanılabilir. Küme yoğun yük yakın çalışıyorsa, devre dışı bırakma işlemi daha uzun sürer. Bu nedenle, düzeltme eki uygulama işlemi, bu tür bir işlem koşullarında yavaş görünüyor olabilir.

- Düzeltme eki sırasında küme sistem durumu sorunları. [Kümenin sistem](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) durumunda herhangi bir [azalma](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) , düzeltme eki uygulama işlemini kesintiye uğratır. Bu sorun, tüm kümeyi düzeltme için gereken toplam zamanı ekler.

**S: neden Windows Update sonuçlarında REST API, ancak makinede Windows Update geçmişi altında bulunmayan bazı güncelleştirmeler görüyorum?**

A: bazı ürün güncelleştirmeleri yalnızca kendi güncelleştirme veya düzeltme eki geçmişinde görünür. Örneğin, Windows Defender güncelleştirmeleri Windows Server 2016 ' de Windows Update geçmişi 'nde görüntülenmeyebilir veya görünmeyebilir.

**S: dev kümemi (tek düğümlü küme) yaması için BIR POA eklenebilir mi?**

Y: Hayır, tek düğümlü bir küme için düzeltme eki uygulamak üzere POA kullanılamaz. [Service Fabric sistem hizmetleri](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) veya diğer müşteri uygulamalarının kapalı kalma süresi olacağı için bu sınırlama tasarıma göre yapılır. Bu nedenle, onarma işlerinin düzeltme eki uygulama Onarım Yöneticisi tarafından hiçbir şekilde onaylanmamış.

**S: Linux üzerinde Nasıl yaparım? Patch kümesi düğümleri mi?**

Y: Linux üzerinde güncelleştirmeleri düzenleme hakkında bilgi edinmek Için bkz. [Azure sanal makine ölçek kümesi otomatik işletim sistemi görüntüsü yükseltmeleri](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**S: güncelleştirme döngüsünün neden bu kadar uzun sürüyor?**

Y: sonuç JSON için sorgu yapın, tüm düğümlerin güncelleştirme döngüsünü girin ve ardından OperationStartTime ve OperationTime (OperationCompletionTime) kullanarak her düğümde güncelleştirme yüklemesi tarafından gerçekleştirilen süreyi bulmayı deneyebilirsiniz. 

Hiçbir güncelleştirme gerçekleşmeyecek büyük bir zaman penceresi varsa, küme bir hata durumunda olabilir ve sonuç olarak Onarım Yöneticisi hiçbir bir onarım görevini onaylayamaz. Güncelleştirme yüklemesi herhangi bir düğümde uzun sürüyorsa bu düğüm bir süredir güncelleştirilmemiş olabilir. Çok sayıda güncelleştirme bekleyen yükleme olabilir, bu da gecikmelere neden olabilir. 

Ayrıca, *devre dışı bırakma* durumunda kalmış olduğundan, düğüm düzeltme eki uygulama engellenemez. Bunun nedeni genellikle düğümün devre dışı bırakılması çekirdek veya veri kaybı durumlarına neden olabilir.

**S: BIR POA yama yaparken düğümün neden devre dışı bırakılması gerekir?**

Y: POA, düğüm üzerinde çalışan tüm Service Fabric hizmetlerini durduran veya *tekrar bulan yeniden başlatma* amacına sahip düğümü devre dışı bırakır. POA, uygulamaların yeni ve eski dll 'Lerin bir karışımını kullanarak bitmemesini sağlamak için bunu, devre dışı bırakmadan bir düğüme düzeltme eki uygulamamasını öneririz.

## <a name="disclaimers"></a>Bildirimler

- POA, Kullanıcı adına Windows Update için son kullanıcı lisans anlaşmasını kabul eder. İsteğe bağlı olarak, ayar uygulamanın yapılandırmasında kapatılabilir.

- POA, kullanımı ve performansı izlemek için telemetri toplar. Uygulamanın telemetrisi, Service Fabric çalışma zamanının telemetri ayarı ayarını izler (varsayılan olarak açık).

## <a name="troubleshooting"></a>Sorun giderme

Bu bölümde, düzeltme eki uygulama ile ilgili sorunlara yönelik olası sorun giderme çözümleri sağlanmaktadır.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Düğüm, duruma geri gelmedi

* Düğüm şu nedenle *devre dışı bırakılıyor* durumunda kalmış olabilir:

  - Bir güvenlik denetimi bekliyor. Bu durumu gidermek için sağlıklı durumda yeterli sayıda düğüm bulunduğundan emin olun.

* Düğüm *devre dışı bırakılmış* durumda kalmış olabilir, çünkü:

  - El ile devre dışı bırakıldı.
  - Devam eden bir Azure altyapı işi nedeniyle devre dışı bırakıldı.
  - Düğüm yama için BIR POA tarafından geçici olarak devre dışı bırakıldı.

* Düğüm aşağı durumunda kalmış olabilir, çünkü:

  - Bu, el ile bir duruma yerleştirildi.
  - Yeniden başlatma işlemi yaşıyor (POA tarafından tetiklenebilir).
  - Hatalı bir VM veya makineye sahip ya da ağ bağlantısı sorunları yaşıyor.

### <a name="updates-were-skipped-on-some-nodes"></a>Bazı düğümlerde güncelleştirmeler atlandı

POA, yeniden zamanlama ilkesine göre bir Windows güncelleştirmesi yüklemeye çalışır. Hizmet, düğümü kurtarmaya çalışır ve uygulama ilkesine göre güncelleştirmeyi atlar.

Böyle bir durumda, düğüm Aracısı hizmetine karşı uyarı düzeyi bir sistem durumu raporu oluşturulur. Windows Update sonucu hatanın olası nedenini da içerir.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Güncelleştirme yüklenirken kümenin sistem durumu hataya gidiyor

Hatalı bir Windows Update, belirli bir düğümdeki veya güncelleştirme etki alanındaki bir uygulamanın veya kümenin sistem durumunu getirebilir. POA, küme sağlıklı olana kadar sonraki Windows Update işlemine devam eder.

Yönetici, Windows Update nedeniyle uygulamanın veya kümenin neden sağlıksız olacağını belirlememelidir.

## <a name="poa-release-notes"></a>POA sürüm notları

>[!NOTE]
> 1\.4.0 ve üzeri sürümler için sürüm notlarını ve sürümlerini GitHub 'daki [Düzeltme Eki düzenleme uygulaması sürüm sayfasında](https://github.com/microsoft/Service-Fabric-POA/releases/) bulabilirsiniz.

### <a name="version-110"></a>Sürüm 1.1.0
- Genel Yayın

### <a name="version-111"></a>Sürüm 1.1.1
- Nodeagentservice yüklemesinin önlendiği NodeAgentService 'in SetupEntryPoint içindeki bir hata düzeltildi.

### <a name="version-120"></a>Sürüm 1.2.0

- Sistem yeniden başlatma iş akışı etrafında hata düzeltmeleri.
- Onarım görevlerinin hazırlanması sırasında durum denetimi beklendiği gibi gerçekleşmediği için RM görevlerinin oluşturulmasında hata düzeltildi.
- Windows hizmeti POANodeSvc 'nin başlangıç modunu Auto ' dan Gecikmeli-otomatik ' e değiştirdi.

### <a name="version-121"></a>Sürüm 1.2.1

- Küme ölçeği azaltma iş akışında hata düzeltildi. Varolmayan düğümlere ait olmayan BIR onarım görevi için çöp toplama mantığı sunuldu.

### <a name="version-122"></a>Sürüm 1.2.2

- Çeşitli hata düzeltmeleri.
- İkili dosyalar artık imzalandı.
- Uygulama için sfpkg bağlantısı eklendi.

### <a name="version-130"></a>Sürüm 1.3.0

- Installwindowsosonlyupdates false olarak ayarlandığında, artık tüm kullanılabilir güncelleştirmeler yüklenir.
- Otomatik güncelleştirmeleri devre dışı bırakma mantığını değiştirdi. Bu, otomatik güncelleştirmelerin sunucu 2016 ve üzeri sürümlerde devre dışı bırakılmamasına neden olan bir hatayı düzeltir.
- Gelişmiş kullanım durumları için hem mikro hizmet mikro hizmetleri için parametreli yerleştirme kısıtlaması.

### <a name="version-131"></a>Sürüm 1.3.1
- POA 1.3.0 'in otomatik güncelleştirmeleri devre dışı bırakma hatası nedeniyle Windows Server 2012 R2 veya daha önceki bir sürümü üzerinde çalışmadığı gerileme düzeltiliyor. 
- Installwindowsosonlyupdates yapılandırmasının her zaman doğru olarak çekildiği hata düzeltiliyor.
- Installwindowsosonlyupdates varsayılan değeri false olarak değiştiriliyor.

### <a name="version-132"></a>Sürüm 1.3.2
- Geçerli düğüm adının bir alt kümesi olan bir ada sahip düğümler varsa, bir düğümdeki düzeltme eki uygulama yaşam döngüsünü etkileyen bir sorunu düzeltme. Bu tür düğümler için düzeltme eki kaçırılmış veya bir yeniden başlatma beklemede olabilir.