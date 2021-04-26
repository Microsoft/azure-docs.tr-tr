---
title: WSFC ve Azure Paylaşılan disk ile SAP ASCS/SCS çoklu SID HA | Microsoft Docs
description: WSFC ve Azure Paylaşılan disk ile SAP ASCS/SCS örneği için çoklu SID yüksek kullanılabilirlik
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cef0630cfc7ec7d080073085e577153ae7a47ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504465"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Windows Server Yük Devretme Kümelemesi ve Azure Paylaşılan disk ile SAP ASCS/SCS örneği çoklu SID yüksek kullanılabilirliği

> ![Windows İşletim Sistemi][Logo_Windows] Windows
>

Bu makalede, Azure Paylaşılan disk ile var olan bir Windows Server Yük Devretme Kümelemesi (WSFC) kümesine ek SAP ASCS/SCS kümelenmiş örnekleri yükleyerek tek bir ASCS/SCS yüklemesinden bir SAP çoklu SID yapılandırmasına nasıl geçiş yapılacağı ele alınmaktadır. Bu işlem tamamlandığında, SAP çoklu SID kümesi yapılandırdınız.

## <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

Şu anda Azure Premium SSD disklerini SAP ASCS/SCS örneği için Azure Paylaşılan diski olarak kullanabilirsiniz. Aşağıdaki sınırlamalar geçerlidir:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) , SAP iş yükleri Için Azure Paylaşılan diski olarak desteklenmez. Şu anda Azure sanal makinelerini, kullanılabilirlik kümesi 'nde Azure Ultra disk kullanarak yerleştirmek mümkün değildir
-  Premium SSD disklere sahip [Azure Paylaşılan disk](../../disks-shared.md) yalnızca kullanılabilirlik kümesindeki VM 'ler ile desteklenir. Kullanılabilirlik Alanları dağıtımında desteklenmez. 
-  Azure Paylaşılan disk değeri [MAXSHARES](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) , kaç küme düğümünün paylaşılan diski kullanabileceğinizi belirler. Genellikle SAP ASCS/SCS örneği için Windows Yük devretme kümesinde iki düğüm yapılandıracaksınız, bu nedenle değerinin `maxShares` iki olarak ayarlanması gerekir.
-  Tüm SAP yoks/SCS kümesi VM 'lerinin aynı [Azure yakınlık yerleşimi grubuna](../../windows/proximity-placement-groups.md)dağıtılması gerekir.   
   PPG olmadan Azure Paylaşılan disk ile kullanılabilirlik kümesinde Windows küme VM 'Leri dağıtabilseniz de PPG, Azure Paylaşılan disklerinin ve küme VM 'lerinin fiziksel yakınlığını, bu nedenle VM 'Ler ve depolama katmanı arasında daha düşük gecikme süresine neden olur.    

Azure Paylaşılan disk sınırlamaları hakkında daha fazla bilgi için, Azure Paylaşılan disk belgelerinin [sınırlamalar](../../disks-shared.md#limitations) bölümünü dikkatle inceleyin.  

> [!IMPORTANT]
> SAP ASCS/SCS Windows Yük devretme kümesini Azure Paylaşılan disk ile dağıttığınızda, dağıtımınızın tek bir depolama kümesinde tek bir paylaşılan diskle birlikte çalışır durumda olacağını unutmayın. SAP ASCS/SCS Örneğinizde, Azure Paylaşılan diskinin dağıtıldığı depolama kümesiyle ilgili sorunlar söz konusu olduğunda bu örnek etkilenecektir.  

> [!IMPORTANT]
> Kurulumun aşağıdaki koşullara uyması gerekir:
> * Her veritabanı yönetim sistemi (DBMS) SID 'sinin kendi adanmış WSFC kümesi olmalıdır.  
> * Bir SAP sistem SID 'sine ait SAP uygulama sunucularının kendi ayrılmış VM 'lerine sahip olması gerekir.  
> * Aynı kümedeki sıraya alma çoğaltması sunucusu 1 ve sıraya alma çoğaltma sunucusu 2 ' nin karışımı desteklenmez.  


## <a name="supported-os-versions"></a>Desteklenen işletim sistemi sürümleri

Hem Windows Server 2016 hem de Windows Server 2019 desteklenir (en son veri merkezi görüntülerini kullanın).

**Windows Server 2019 Datacenter** kullanmanızı kesinlikle öneririz; örneğin:
- Windows 2019 yük devretme kümesi hizmeti Azure ile uyumlu
- Azure Schedule olaylarını izleyerek Azure ana bilgisayar bakımı ve geliştirilmiş deneyimle ilgili tümleştirme ve tanıma özellikleri eklendi.
- Dağıtılmış ağ adı (varsayılan seçenektir) kullanılabilir. Bu nedenle, küme ağ adı için ayrılmış bir IP adresine sahip olmanız gerekmez. Ayrıca, Azure Iç Load Balancer bu IP adresini yapılandırmaya gerek yoktur. 

## <a name="architecture"></a>Mimari

Birden çok SID yapılandırmasında hem sıraya alma çoğaltma sunucusu 1 (ERS1) hem de sıraya alma çoğaltma sunucusu 2 (ERS2) desteklenir.  Aynı kümede bir ERS1 ve ERS2 karışımı desteklenmez. 

1. İlk örnek, her ikisi de ERS1 mimarisi ile iki SAP SID gösterir:

   - SAP SID1, ERS1 ile paylaşılan diske dağıtılır. ERS örneği yerel konakta ve yerel sürücüde yüklüdür.
     SAP SID1, Azure Iç yük dengeleyicide yapılandırılan kendi (sanal) IP adresine (SID1 (A) SCS ıP1) sahiptir.

   - SAP SID2, ERS1 ile paylaşılan diske dağıtılır. ERS örneği yerel konakta ve yerel sürücüde yüklüdür.
     SAP SID2, Azure Iç yük dengeleyicide da yapılandırılan kendi (sanal) IP adresine (SID2 (A) SCS ıP2) sahiptir.

![Yüksek kullanılabilirliğe sahip SAP ASCS/SCS örneği-ERS1 yapılandırması ile iki örnek][sap-ha-guide-figure-6007]

2. İkinci örnek, her ikisi de ERS2 mimarisi ile iki SAP SID gösterir: 

   - ERS2 ile SAP SID1, aynı zamanda kümelenmiş ve yerel sürücüde dağıtılır.  
     SAP SID1, Azure Iç yük dengeleyicide yapılandırılan kendi (sanal) IP adresine (SID1 (A) SCS ıP1) sahiptir.
     SAP SID1 System tarafından kullanılan SAP ERS2, Azure Iç yük dengeleyicide yapılandırılan kendi (sanal) IP adresine (SID1 ERS2 ıP2) sahiptir.

   - ERS2 ile SAP SID2, aynı zamanda kümelenmiş ve yerel sürücüde dağıtılır.  
     SAP SID2, Azure Iç yük dengeleyicide yapılandırılan kendi (sanal) IP adresine (SID2 (A) SCS IP3) sahiptir.
     SAP SID2 System tarafından kullanılan SAP ERS2, Azure Iç yük dengeleyicide yapılandırılan kendi (sanal) IP adresine (SID2 ERS2 ıP4) sahiptir.

   Burada toplam dört sanal IP adresi vardır:  
   - SID1 (A) SCS ıP1
   - SID2 ERS2 ıP2
   - SID2 (A) SCS IP3
   - SID2 ERS2 ıP4

![Yüksek kullanılabilirliğe sahip SAP ASCS/SCS örneği-ERS1 ve ERS2 yapılandırması ile iki örnek][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Altyapı hazırlığı

**Mevcut kümelenmiş** SAP **PR1** ascs/SCS örneğine ek olarak yeni bir SAP SID **PR2** yükleyeceğiz.  

### <a name="host-names-and-ip-addresses"></a>Ana bilgisayar adları ve IP adresleri

| Ana bilgisayar adı rolü | Konak adı | Statik IP adresi | Kullanılabilirlik kümesi | Yakınlık yerleşimi grubu |
| --- | --- | --- |---| ---|
| 1. küme düğümü yoks/SCS kümesi |PR1-ascs-10 |10.0.0.4 |PR1-ascs-avset |PR1PPG |
| 2. küme düğümü yoks/SCS kümesi |PR1-ascs-11 |10.0.0.5 |PR1-ascs-avset |PR1PPG |
| Küme ağ adı | pr1clust |10.0.0.42 (**yalnızca** Win 2016 kümesi için) | yok | yok |
| **SID1** YOKS küme ağı adı | PR1-ascscl |10.0.0.43 | yok | yok |
| **SID1** ERS küme ağı adı (**yalnızca** ERS2 için) | PR1-erscl |10.0.0.44 | yok | yok |
| **SID2** YOKS küme ağı adı | PR2-ascscl |10.0.0.45 | yok | yok |
| **SID2** ERS küme ağı adı (**yalnızca** ERS2 için) | PR1-erscl |10.0.0.46 | yok | yok |

### <a name="create-azure-internal-load-balancer"></a>Azure iç yük dengeleyici oluşturma

SAP ASCS, SAP SCS ve yeni SAP ERS2, sanal konak adı ve sanal IP adresleri kullanın. Azure 'da bir sanal IP adresi kullanmak için bir [yük dengeleyici](../../../load-balancer/load-balancer-overview.md) gereklidir. [Standart yük dengeleyiciyi](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)kullanmanızı kesinlikle öneririz. 

İkinci SAP SID yoks/SCS/ERS örneği **PR2** için mevcut yük dengeleyiciye yapılandırma eklemeniz gerekir. İlk SAP SID **PR1** yapılandırması zaten yerinde olmalıdır.  

**A SCS PR2 [örnek numarası 02]**
- Ön uç yapılandırması
    - Statik yoks/SCS IP adresi **10.0.0.45**
- Arka uç yapılandırması  
    Zaten mevcut-VM 'Ler zaten arka uç havuzuna eklenmiş, SAP SID **PR1** için Yapılandırılıyor
- Araştırma bağlantı noktası
    - Bağlantı noktası 620 **NR** [**62002**] protokol (TCP), Aralık (5) için varsayılan seçeneği bırakın, sağlıksız eşik (2)
- Yük Dengeleme kuralları
    - Standart Load Balancer kullanıyorsanız HA bağlantı noktaları ' nı seçin.
    - Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
        - 32 **NR** TCP [**3202**]
        - 36 **NR** TCP [**3602**]
        - 39 **NR** TCP [**3902**]
        - 81 **NR** TCP [**8102**]
        - 5 **NR** 13 TCP [**50213**]
        - 5 **NR** 14 TCP [**50214**]
        - 5 **NR** 16 TCP [**50216**]
        - **PR2** ascs ön uç IP 'si, sistem durumu araştırması ve mevcut arka uç havuzuyla ilişkilendirin.  

    - Boşta zaman aşımı (dakika) değerinin en fazla 30 değerine ayarlandığından ve kayan IP (doğrudan sunucu dönüşü) etkinleştirildiğinden emin olun.

**ERS2 PR2 [örnek numarası 12]** 

Sıraya alma çoğaltma sunucusu 2 (ERS2) da kümelenmiş olduğundan, ERS2 sanal IP adresinin, yukarıdaki SAP ASCS/SCS IP 'ye ek olarak Azure ıLB üzerinde de yapılandırılması gerekir. Bu bölüm yalnızca **PR2** için sıraya alma çoğaltma sunucusu 2 mimarisi kullanılıyorsa geçerlidir.  
- Yeni ön uç yapılandırması
    - Statik SAP ERS2 IP adresi **10.0.0.46**

- Arka uç yapılandırması  
  VM 'Ler zaten ıLB arka uç havuzuna eklenmiş.  

- Yeni yoklama bağlantı noktası
    - Bağlantı noktası 621 **NR**  [**62112**] protokol (TCP), Aralık (5) için varsayılan seçeneği bırakın, sağlıksız eşik (2)

- Yeni Yük Dengeleme kuralları
    - Standart Load Balancer kullanıyorsanız HA bağlantı noktaları ' nı seçin.
    - Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
        - 32 **NR** TCP [**3212**]
        - 33 **NR** TCP [**3312**]
        - 5 **NR** 13 TCP [**51212**]
        - 5 **NR** 14 TCP [**51212**]
        - 5 **NR** 16 TCP [**51212**]
        - **PR2** ERS2 ön uç IP 'si, sistem durumu araştırması ve mevcut arka uç havuzuyla ilişkilendirin.  

    - Boşta kalma zaman aşımı (dakika) değerinin en fazla değere (30) ayarlandığından ve kayan IP (doğrudan sunucu dönüşü) etkinleştirildiğinden emin olun.


### <a name="create-and-attach-second-azure-shared-disk"></a>İkinci Azure Paylaşılan diski oluşturun ve ekleyin

Küme düğümlerinden birinde bu komutu çalıştırın. Kaynak grubunuz, Azure bölgesi, SAPSıD, vb. değerlerini ayarlamanız gerekir.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Paylaşılan diski PowerShell ile biçimlendirme
1. Disk numarasını alın. Küme düğümlerinden birinde PowerShell komutlarını çalıştırın:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Diski biçimlendirin. Bu örnekte disk numarası 3 ' dir. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Diskin artık bir küme diski olarak görünür olduğunu doğrulayın.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Diski kümeye kaydedin.  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Kümelenmiş SAP Ass/SCS örneği için sanal ana bilgisayar adı oluşturma

1. Windows DNS Yöneticisi 'nde yeni SAP ASCS/SCS örneği için sanal konak adı için bir DNS girişi oluşturun.  
   DNS 'de sanal ana bilgisayar adına atadığınız IP adresinin, Azure Load Balancer atadığınız IP adresiyle aynı olması gerekir.  

   ![SAP ASCS/SCS kümesi sanal adı ve IP adresi için DNS girişini _Define][sap-ha-guide-figure-6009]
 
   _SAP ASCS/SCS kümesi sanal adı ve IP adresi için DNS girişi tanımlayın_

2. Ayrıca kümelenmiş örnek olan SAP sıraya alma çoğaltma sunucusu 2 ' yi kullanıyorsanız, DNS 'de ERS2 için bir sanal ana bilgisayar adı da ayırmanız gerekir. 
   DNS 'de ERS2 için sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız IP adresiyle aynı olmalıdır.  

   ![SAP ERS2 kümesi sanal adı ve IP adresi için DNS girişini _Define][sap-ha-guide-figure-6010]
 
   _SAP ERS2 kümesi sanal adı ve IP adresi için DNS girişi tanımlama_

3. Sanal ana bilgisayar adına atanan IP adresini tanımlamak için **DNS Yöneticisi**  >  **etki alanı**' nı seçin.

   ![SAP ASCS/SCS ve ERS2 küme yapılandırması için yeni sanal ad ve IP adresi][sap-ha-guide-figure-6011]

   _SAP ASCS/SCS ve ERS2 küme yapılandırması için yeni sanal ad ve TCP/IP adresi_

## <a name="sap-installation"></a>SAP Yüklemesi 

### <a name="install-the-sap-first-cluster-node"></a>SAP ilk küme düğümünü yükler

Tanımlanan SAP yükleme yordamını izleyin. "Ilk küme düğümü" yükleme başlatma seçeneğinde ve "küme paylaşılan disk" öğesini yapılandırma seçeneği olarak seçtiğinizden emin olun.  
Yeni paylaşılan disk oluştur ' a tıklayın.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>ASCS/SCS örneğinin SAP profilini değiştirme

Sıraya alma çoğaltma sunucusu 1 çalıştırıyorsanız, aşağıda açıklandığı gibi SAP profili parametresi ekleyin `enque/encni/set_so_keepalive` . Profil parametresi, SAP iş işlemleriyle sıraya alma sunucusu arasındaki bağlantıları çok uzun süre boşta kaldığında kapatmadan önler. SAP parametresi ERS2 için gerekli değildir. 

1. ERS1 kullanılıyorsa, bu profile parametresini SAP ASCS/SCS örnek profiline ekleyin.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Hem ERS1 hem de ERS2 için, `keepalive` işletim sistemi PARAMETRELERININ SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)' de açıklandığı gibi ayarlandığından emin olun.   

2. SAP profili parametre değişikliklerini uygulamak için SAP ASCS/SCS örneğini yeniden başlatın.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Küme kaynağında araştırma bağlantı noktasını yapılandırma

Tüm küme yapılandırmasının Azure Load Balancer ile çalışmasını sağlamak için iç yük dengeleyicinin araştırma işlevini kullanın. Azure iç yük dengeleyici, genellikle katılan sanal makineler arasında eşit olarak gelen iş yükünü dağıtır.

Ancak, yalnızca bir örnek etkin olduğundan, bazı küme yapılandırmalarında bu çalışmaz. Diğer örnek pasif ve iş yükünün hiçbirini kabul edemiyor. Bir araştırma işlevselliği, Azure iç yük dengeleyicinin hangi Örneğin etkin olduğunu algılayarak ve yalnızca etkin örneği hedeflemeye yardımcı olur.  

> [!IMPORTANT]
> Bu örnek yapılandırmada, **Probeport** 620 **NR** olarak ayarlanır. SAP ASCS örneği için **02** sayı ile 620 **02** olur.
> Yapılandırmayı SAP örnek numaralarınız ve SAP SID 'niz ile eşleşecek şekilde ayarlamanız gerekecektir.

Bir araştırma bağlantı noktası eklemek için, bu PowerShell modülünü küme VM 'lerinden birinde çalıştırın:

- Örnek numarası **02** olan SAP ASC/SCS örneği söz konusu olduğunda 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- ERS2 kullanıyorsanız, kümelenmiş örnek numarası **12** olan. Kümelenmemiş olduğundan, ERS1 için araştırma bağlantı noktasını yapılandırmaya gerek yoktur.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 İşlevin kodu `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` şöyle görünür:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>SAP yüklemesine devam edin

1. SAP yükleme kılavuzunda açıklanan süreci izleyerek veritabanı örneğini yükleme.  
2. SAP yükleme kılavuzunda açıklanan adımları izleyerek, ikinci küme düğümüne SAP 'yi yükleme.  
3. SAP birincil uygulama sunucusu (PAS) örneğini, PAS 'yi barındırmak için belirlediğiniz sanal makineye yükler.   
   SAP yükleme kılavuzunda açıklanan süreci izleyin. Azure üzerinde hiçbir bağımlılık yoktur.
4. SAP uygulama sunucusu örneklerini barındırmak için tasarlanan sanal makinelere ek SAP uygulama sunucuları yükler.  
   SAP yükleme kılavuzunda açıklanan süreci izleyin. Azure üzerinde hiçbir bağımlılık yoktur. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS örneği yük devretmesini test etme
Seviyelendirilmiş yük devretme testleri için SAP Ass 'nin A düğümünde etkin olduğunu varsaytık.  

1. SAP sisteminin A düğümünden B düğümüne başarıyla yük devredebildiğini doğrulayın. Bu örnekte, test SAPSıD **PR2** için yapılır.  
   SAPSıD 'nin her birinin diğer küme düğümüne başarıyla taşınerişebildiğinizden emin olun.   
   A kümesi düğümünden B kümesine SAP küme grubunun yük devretmesini başlatmak için şu seçeneklerden birini seçin \<SID\> :
    - Yük Devretme Kümesi Yöneticisi  
    - Yük devretme kümesi PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Windows Konuk işletim sisteminin içindeki küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.  
3. Azure portal küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.  
4. Azure PowerShell kullanarak küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.

## <a name="next-steps"></a>Sonraki adımlar

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP NetWeaver HA 'yi bir Windows Yük devretme kümesine ve bir SAP ASCS/SCS örneği için paylaşılan diske yükler][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../availability.md