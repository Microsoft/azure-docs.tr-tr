---
title: Azure VMware çözümünde vSAN 'da Windows Server yük devretme kümesini yapılandırma
description: Yerel paylaşılan disklerle Azure VMware çözümü vSAN üzerinde Windows Server yük devretme kümesi (WSFC) ayarlayın.
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: f1bc8199eb0d3317e4b6e07a6a297b4ebfe95cc8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308698"
---
# <a name="configure-windows-server-failover-cluster-on-azure-vmware-solution-vsan"></a>Azure VMware çözümünde vSAN 'da Windows Server yük devretme kümesini yapılandırma

Bu makalede, yerel paylaşılan disklerle Azure VMware çözümü vSAN üzerinde Windows Server yük devretme kümesi ayarlamayı öğreneceksiniz. 

>[!IMPORTANT]
>Bu makaledeki uygulama kavram kanıtı ve pilot amaçlar için tasarlanmıştır. Yerleştirme ilkeleri kullanılabilir olana kadar bir küme içi (CıB) yapılandırma kullanmanızı öneririz.

Daha önce Microsoft hizmet kümesi hizmeti (MSCS) olarak bilinen Windows Server yük devretme kümesi (WSFC), Windows Server Işletim sisteminin (OS) bir özelliğidir. WSFC, iş açısından kritik bir özelliktir ve birçok uygulama için gereklidir. Örneğin, aşağıdaki yapılandırmalarda WSFC gereklidir:

- SQL Server şu şekilde yapılandırılır:
  - Her zaman yük devretme kümesi örneği (FCı), örnek düzeyi yüksek kullanılabilirlik için.
  - Veritabanı düzeyinde yüksek kullanılabilirlik için Always on kullanılabilirlik grubu (AG).
- Windows Dosya Hizmetleri:
  - Etkin küme düğümünde çalışan genel dosya paylaşma.
  - Dosyaları Küme Paylaşılan birimlerinde (CSV) depolayan Scale-Out dosya sunucusu (SOFS).
  - Depolama Alanları Doğrudan (S2D); farklı küme düğümlerinde depolama havuzları oluşturmak için kullanılan yerel diskler.

WSFC kümesini küme genelindeki (CAB) olarak bilinen farklı Azure VMware Çözüm örneklerinde barındırabilirsiniz. Ayrıca, WSFC kümesini tek bir Azure VMware çözüm düğümüne yerleştirebilirsiniz. Bu yapılandırma, bir kutu içinde küme (CıB) olarak bilinir. Üretim uygulamaları için bir CıB çözümü kullanmanızı önermiyoruz. Tek bir Azure VMware Çözüm düğümünün başarısız olması, tüm WSFC küme düğümlerinin kapalı olması ve uygulamanın kapalı kalma süresiyle karşılaşması gerekiyordu. Azure VMware çözümü, özel bir bulut kümesinde en az üç düğüm gerektirir.

Desteklenen bir WSFC yapılandırması dağıtmak önemlidir. Çözümünüzü vSphere ve Azure VMware çözümü ile desteklenecek şekilde isteyeceksiniz. VMware, vSphere 6,7 üzerinde WSFC hakkında ayrıntılı bir belge sağlar, [Yük Devretme Kümelemesi ve Microsoft Cluster hizmeti Için kurulum](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Bu makalede Windows Server 2016 ve Windows Server 2019 ' de WSFC 'ye odaklanılır. Eski Windows Server sürümleri, [temel destekten](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) oluşur ve bu nedenle bunları burada kabul etmezsiniz.

Önce [BIR wsfc oluşturmanız](/windows-server/failover-clustering/create-failover-cluster)gerekir. WSFC hakkında daha fazla bilgi için bkz. [Windows Server 'Da Yük Devretme Kümelemesi](/windows-server/failover-clustering/failover-clustering-overview). Azure VMware çözümünde bir WSFC dağıtımının özellikleri için bu makalede sağladığımız bilgileri kullanın.

## <a name="prerequisites"></a>Önkoşullar

- Azure VMware Çözüm ortamı
- Microsoft Windows Server işletim sistemi yükleme medyası

## <a name="reference-architecture"></a>Başvuru mimarisi

Azure VMware çözümü, sanallaştırılmış WSFC için yerel destek sağlar. Bir sanal disk düzeyinde SCSI-3 kalıcı ayırmaları (SCSI3PR) destekler. Bu destek, WSFC tarafından düğümler arasında paylaşılan bir diske erişim sağlamak için gereklidir. SCSI3PRs desteği, vSAN veri depolarında yerel olarak VM 'Ler arasında paylaşılan bir disk kaynağıyla WSFC yapılandırmasına izin vermez.

Aşağıdaki diyagramda, bir Azure VMware çözümü özel bulutundaki WSFC sanal düğümlerinin mimarisi gösterilmektedir. Azure VMware çözümünün, daha geniş Azure platformuna bağlı olarak, WSFC sanal sunucuları (kırmızı kutu) dahil olmak üzere bulunduğu konumu gösterir. Bu diyagramda tipik bir hub-ışınsal mimarisi gösterilmektedir, ancak Azure sanal WAN kullanımı ile benzer bir kurulum mümkündür. Her ikisi de diğer Azure hizmetlerinin tüm değerlerini sunmaktadır.

:::image type="content" source="media/windows-server-failover-cluster/windows-server-failover-architecture.svg" alt-text="Azure VMware Çözüm özel bulutu 'ndaki Windows Server yük devretme kümesi sanal düğümlerinin mimari diyagramı." border="false" lightbox="media/windows-server-failover-cluster/windows-server-failover-architecture.svg":::

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Şu anda aşağıdaki yapılandırma desteklenir:

- Microsoft Windows Server 2012 veya üzeri
- Küme başına en fazla beş yük devretme kümesi düğümü
- VM başına en fazla dört PVSCSı bağdaştırıcısı
- PVSCSı bağdaştırıcısı başına en fazla 64 disk

## <a name="virtual-machine-configuration-requirements"></a>Sanal makine yapılandırma gereksinimleri

### <a name="wsfc-node-configuration-parameters"></a>WSFC düğümü yapılandırma parametreleri

- Her WSFC düğümüne en son VMware araçlarını yükler.
- Tek bir sanal SCSI bağdaştırıcısında paylaşılmayan ve paylaşılan disklerin karıştırılması desteklenmez. Örneğin, sistem diski (sürücü C:) SCSI0:0 ' a iliştirilmiş, ilk paylaşılan disk SCSI1 'e iliştirilebilecek: 0. Bir WSFC 'nin VM düğümü, normal bir VM ile en fazla dört (4) sanal SCSI denetleyicisi ile aynı sanal SCSI denetleyici 'ye sahiptir.
- Sanal diskler SCSI kimlikleri aynı WSFC 'nin düğümleri barındıran tüm VM 'Ler arasında tutarlı olmalıdır.

| **Bileşen** | **Gereksinimler** |
| --- | --- |
| VM donanım sürümü | Canlı vMotion 'ı desteklemek için 11 veya üzeri. |
| Sanal NIC | VMXNET3 parasanallaştırılmış ağ arabirimi kartı (NIC); sanal NIC 'de Konuk Windows Alma Tarafı Ölçeklendirmesi (RSS) özelliğini etkinleştirin. |
| Bellek | WSFC kümesindeki düğümler için tam VM ayırma belleğini kullanın. |
| Her WSFC düğümünün g/ç zaman aşımını artırın. | HKEY \_ Local \_ Machıne\system\currentcontrolset\services\disk\timeoutvalueset değerini 60 saniye veya daha fazla olacak şekilde değiştirin. (Kümeyi yeniden oluşturursanız, bu değer varsayılana sıfırlanabilir, bu nedenle yeniden değiştirmelisiniz.) |
| Windows Cluster sistem durumu izleme | Windows küme durumu izlemenin SameSubnetThreshold parametresinin değeri, en az 10 eksik sinyalle izin verecek şekilde değiştirilmelidir. Bu [, Windows Server 2016 ' de varsayılandır](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Bu öneri, paylaşılan ve paylaşılmayan diskler dahil olmak üzere WSFC kullanan tüm uygulamalar için geçerlidir. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC düğümü-önyükleme diskleri yapılandırma parametreleri


| **Bileşen** | **Gereksinimler** |
| --- | --- |
| SCSI denetleyici türü | LSI Logic SAS |
| Disk modu | Sanal |
| SCSI veri yolu paylaşımı | Yok |
| Önyükleme cihazını barındıran bir sanal SCSI denetleyicisinin gelişmiş ayarlarını değiştirin. | Aşağıdaki gelişmiş ayarları her bir WSFC düğümüne ekleyin:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Burada X önyükleme cihazının SCSI veri yolu denetleyicisi KIMLIK numarasıdır. Varsayılan olarak, X 0 olarak ayarlanır. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC düğümü-paylaşılan diskler yapılandırma parametreleri


| **Bileşen** | **Gereksinimler** |
| --- | --- |
| SCSI denetleyici türü | VMware Paravirtualınitialize (PVSCSı) |
| Disk modu | Bağımsız-kalıcı (aşağıdaki çizimde 2. adım). Bu ayarı kullanarak tüm disklerin anlık görüntülerden dışlandığından emin olursunuz. Anlık görüntüler, WSFC tabanlı VM 'Ler için desteklenmez. |
| SCSI veri yolu paylaşımı | Fiziksel (aşağıdaki çizimde 1. adım) |
| Çoklu yazıcı bayrağı | Kullanılmıyor |
| Disk biçimi | Kalın sağlandı. (Eager sıfırlandı kalın (EZT), vSAN ile birlikte gerekli değildir.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Sanal donanımın düzenleme ayarları sayfasını gösteren ekran görüntüsü.":::

## <a name="non-supported-scenarios"></a>Desteklenmeyen senaryolar

Azure VMware çözümünde WSFC için aşağıdaki işlevler desteklenmez:

- NFS veri depoları
- Depolama Alanları
- Iscsı hizmetini kullanan vSAN
- vSAN uzatılmış kümesi
- Gelişmiş vMotion uyumluluğu (EVC)
- vSphere hata toleransı (FT)
- Anlık Görüntüler
- Canlı (çevrimiçi) Depolama vMotion
- N-bağlantı noktası KIMLIĞI Sanallaştırması (NPıV)

Sanal makine donanımında sık yapılan değişiklikler, WSFC düğümleri arasındaki sinyali kesintiye uğratabilir.

Aşağıdaki etkinlikler desteklenmez ve WSFC düğümü yük devretmesine neden olabilir:

- Dinamik bellek ekleme
- Dinamik ekleme CPU 'SU
- Anlık görüntü kullanma
- Paylaşılan disk boyutunu artırma
- Sanal makine durumunu duraklatma ve sürdürme
- İsteğe bağlı bellek-ESXi değiştirme veya VM belleği Ballooning
- SCSI veri yolu paylaşım denetleyicisi ile ilişkilendirilmemiş olsa bile yerel VMDK dosyasını sık Genişlet

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Azure VMware çözümünde vSAN 'da paylaşılan disklerle WSFC yapılandırma

1. Active Directory ortamının kullanılabilir olduğundan emin olun.
2. VSAN veri deposunda sanal makineler (VM) oluşturun.
3. Tüm VM 'lerde güç, ana bilgisayar adını, IP adreslerini yapılandırın, tüm VM 'Leri bir Active Directory etki alanına ekleyin ve en son kullanılabilir işletim sistemi güncelleştirmelerini yükler.
4. En son VMware araçlarını yükler.
5. Her VM 'de Windows Server yük devretme kümesi özelliğini etkinleştirin ve yapılandırın.
6. Çekirdek için bir küme tanığı yapılandırın (bir dosya paylaşma tanığı düzgün çalışıyor).
7. WSFC kümesinin tüm düğümlerini kapatın.
8. WSFC 'nin her bir VM parçasına bir veya daha fazla Paravirtual SCSI denetleyicisi (en fazla dört) ekleyin. Önceki paragraflara göre ayarları kullanın.
9. İlk küme düğümünde, **yeni cihaz**  >  **sabit diski** Ekle ' yi kullanarak gereken tüm paylaşılan diskleri ekleyin. Disk paylaşımı, **belirtilmemiş** (varsayılan) ve disk modu **bağımsız** olarak bırakılmalıdır. Önceki adımlarda oluşturulan denetleyiciye ekleyin.
10. Kalan WSFC düğümlerine devam edin. Önceki adımda oluşturulan diskleri **yeni cihaz ekle**  >  **mevcut sabit disk**' i seçerek ekleyin. Tüm WSFC düğümlerinde aynı disk SCSI kimliklerini koruduğunuzdan emin olun.
11. İlk WSFC düğümünde güç; oturum açın ve Disk Yönetimi konsolu 'nu (MMC) açın. Eklenen paylaşılan disklerin işletim sistemi tarafından yönetilebilmesi ve başlatıldığından emin olun. Diskleri biçimlendirin ve bir sürücü harfi atayın.
12. Diğer WSFC düğümlerinde güç.
13. Disk **Ekle sihirbazını** kullanarak diski wsfc kümesine ekleyin ve bir küme paylaşılan birimi ekleyin.
14. **Disk Taşıma Sihirbazı 'nı** kullanarak yük devretmeyi test edin ve paylaşılan DISKLERIN bulunduğu wsfc kümesinin düzgün şekilde çalıştığından emin olun.
15. Kümenin ve düğümlerin düzgün çalışıp çalışmadığını doğrulamak için **doğrulama kümesi Sihirbazı 'nı** çalıştırın.

    Küme doğrulama testinizden aşağıdaki belirli öğeleri göz önünde bulundurmanız önemlidir:

       - **Depolama alanları kalıcı ayırmasını doğrulayın**. Kümeniz ile depolama alanları kullanmıyorsanız (örneğin, Azure VMware Çözüm vSAN), bu test geçerli değildir. Bu uyarı dahil olmak üzere depolama alanlarını doğrula kalıcı ayırma testinin sonuçlarını yoksayabilirsiniz. Uyarıları önlemek için bu testi hariç bırakabilirsiniz.
        
      - **Ağ Iletişimini doğrulayın**. Küme doğrulama testi, her küme düğümü için yalnızca bir ağ arabiriminin kullanılabilir olduğunu belirten bir uyarı oluşturur. Bu uyarıyı yoksayabilirsiniz. Düğümler NSX-T segmentlerinden birine bağlı olduğundan, Azure VMware çözümü gerekli kullanılabilirliği ve performansı sağlar. Ancak, ağ iletişiminin diğer yönlerini doğrulayacaktır, bu öğeyi küme doğrulama testinin bir parçası olarak saklayın.

16. WSFC VM 'Leri aynı Azure VMware Çözüm düğümlerine yerleştirmek için bir DRS kuralı oluşturun. Bunu yapmak için, konaktan VM benzeşim kuralına ihtiyacınız vardır. Bu şekilde, küme düğümleri aynı Azure VMware Çözüm ana bilgisayarında çalışır. Bu, yerleştirme ilkeleri kullanılabilir olana kadar pilot amaçlar için de geçerlidir.

    >[!NOTE]
    > Bunun için bir destek Istek bileti oluşturmanız gerekir. Azure destek kuruluşumuz, bu konuda size yardımcı olabilir.

## <a name="related-information"></a>İlgili bilgiler

- [Windows Server'da Yük Devretme Kümelemesi](/windows-server/failover-clustering/failover-clustering-overview)
- [VSphere üzerinde Microsoft Kümelemesi için yönergeler (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Yük Devretme Kümelemesi ve Microsoft Cluster hizmeti (vmware.com) kurulumu hakkında](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6,7 U3-WSFC paylaşılan diskler &amp; SCSI-3 kalıcı ayırmalar (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware Çözüm sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware çözümünde bir WSFC ayarlamayı artık kapsadığınıza göre şunları öğrenmek isteyebilirsiniz:

- WSFC özelliği gerektiren daha fazla uygulama ekleyerek yeni WSFC 'nizi ayarlama. Örneğin, SQL Server ve SAP yoks.
- Bir yedekleme çözümü kurma.
  - [Azure VMware çözümü için Azure Backup Sunucusu ayarlama](./set-up-backup-server-for-azure-vmware-solution.md)
  - [Azure VMware çözümü sanal makineleri için yedekleme çözümleri](./ecosystem-back-up-vms.md)