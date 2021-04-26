---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarma için destek matrisi
description: Azure Site Recovery olan ikincil bir bölgeye Azure VM olağanüstü durum kurtarma desteğini özetler.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: daba23ec1382f1b5fb18bffba48d0571fe998a2f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792462"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure bölgeleri arasında Azure sanal makinesi olağanüstü durum kurtarma için destek matrisi

Bu makalede, Azure VM 'lerinin bir Azure bölgesinden diğerine olağanüstü durum kurtarmaya yönelik destek ve Önkoşullar [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak özetlenmektedir.


## <a name="deployment-method-support"></a>Dağıtım yöntemi desteği

**Dağıtım** |  **Destek**
--- | ---
**Azure portalı** | Destekleniyor.
**PowerShell** | Destekleniyor. [Daha fazla bilgi edinin](azure-to-azure-powershell.md)
**REST API** | Destekleniyor.
**CLI** | Şu anda desteklenmiyor


## <a name="resource-support"></a>Kaynak desteği

**Kaynak eylemi** | **Ayrıntılar**
--- | ---
**Kaynakları kaynak grupları arasında taşıma** | Desteklenmez
**İşlem/depolama/ağ kaynaklarını kaynak grupları arasında taşıma** | Desteklenmez.<br/><br/> VM çoğaltıldıktan sonra bir VM 'yi veya depolama/ağ gibi ilişkili bileşenleri taşırsanız VM için çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.
**Olağanüstü durum kurtarma için Azure VM 'lerini bir abonelikten diğerine çoğaltma** | Aynı Azure Active Directory kiracısı içinde desteklenir.
**Desteklenen coğrafi kümeler içindeki bölgeler arasında VM 'Leri geçirme (ve abonelikler arasında)** | Aynı Azure Active Directory kiracısı içinde desteklenir.
**Aynı bölgedeki VM 'Leri geçirme** | Desteklenmez.
**Azure ayrılmış Konakları** | Desteklenmez.

## <a name="region-support"></a>Bölge desteği

Aynı coğrafi küme içindeki iki bölge arasında VM 'Leri çoğaltıp kurtarabilirsiniz. Coğrafi kümeler, veri gecikmesi ve egemenlik göz önünde tutulması için tanımlanır.


**Coğrafi küme** | **Azure bölgeleri**
-- | --
Amerika | Kanada Doğu, Kanada Orta, Orta Güney ABD, Orta Batı ABD, Doğu ABD, Doğu ABD 2, Batı ABD, Batı ABD 2, Orta ABD, Orta Kuzey ABD
Avrupa | UK Batı, UK Güney, Kuzey Avrupa, Batı Avrupa, Güney Afrika Batı, Güney Afrika Kuzey, Norveç Doğu, Fransa Orta, İsviçre Kuzey Almanya Orta Batı
Asya | Güney Hindistan, Orta Hindistan, Batı Hindistan, Güneydoğu Asya, Doğu Asya, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney
JIO | JıO Hindistan Batı
Avustralya    | Avustralya Doğu, Avustralya Güneydoğu, Avustralya Orta, Avustralya Orta 2
Azure Kamu    | ABD devleti Virginia, US GOV IWA, US GOV Arizona, US GOV Texas, US DOD Doğu, US DOD orta
Almanya    | Almanya Orta, Almanya Kuzeydoğu
Çin | Çin Doğu, Çin Kuzey, Çin North2, Çin Doğu2
Brezilya | Güney Brezilya
Ülke içi olağanüstü durum kurtarma için ayrılan kısıtlı bölgeler |İsviçre Batı İsviçre Kuzey için ayrılan Fransa Güney Fransa Orta için ayrılan BAE Orta BAE Kuzey müşterileri için Norveç Batı Doğu müşterileri, JIO Hindistan orta müşterileri için JıO Hindistan Batı, Brezilya Güneydoğu

Farklı kıtaların iki bölgesi arasında VM 'lerin çoğaltılması ve kurtarılması aşağıdaki bölge çiftleri ile sınırlıdır:

* Güneydoğu Asya ve Avustralya Doğu
* Güneydoğu Asya ve Avustralya Güneydoğu
* Batı Avrupa ve Orta Güney ABD.

>[!NOTE]
>
> - **Brezilya Güney** için şu bölgelere çoğaltabilir ve yük devredebilmeniz gerekir: Brezilya güneydoğu, Orta Güney ABD, Orta Batı ABD, Doğu ABD, Doğu ABD 2, Batı ABD, Batı ABD 2 ve Orta Kuzey ABD.
> - Brezilya Güney yalnızca, Site Recovery kullanılarak sanal makinelerin çoğaltılacağı kaynak bölge olarak kullanılabilir. Hedef bölge işlevi görebilir. Bu, coğrafi uzaklıkları nedeniyle gecikme sorunlarından kaynaklanır. Bir hedefe kaynak bölge olarak Brezilya Güney yük devretmek, hedef bölgeden Brezilya Güney için yeniden çalışma desteklendiğini unutmayın. Brezilya Güneydoğu, yalnızca hedef bölge olarak kullanılabilir.
> - Uygun erişiminiz olan bölgeler içinde çalışabilirsiniz.
> - Bir kasa oluşturmak istediğiniz bölge görünmüyorsa, aboneliğinizin bu bölgedeki kaynakları oluşturmak için erişime sahip olduğundan emin olun.
> - Çoğaltmayı etkinleştirdiğinizde coğrafi bir küme içinde bölge göremiyorsanız, aboneliğinizin bu bölgede VM oluşturma izinlerine sahip olduğundan emin olun.

## <a name="cache-storage"></a>Önbellek depolama

Bu tablo, çoğaltma sırasında Site Recovery tarafından kullanılan önbellek depolama hesabı desteğini özetler.

**Ayar** | **Destek** | **Ayrıntılar**
--- | --- | ---
Genel amaçlı v2 depolama hesapları (sık erişimli ve Seyrek Erişimli Katman) | Desteklenir | V2 için işlem maliyetleri, v1 depolama hesaplarından önemli ölçüde yüksek olduğundan, GPv2 kullanımı önerilmez.
Premium depolama | Desteklenmez | Standart depolama hesapları, maliyetleri iyileştirmenize yardımcı olmak için önbellek depolaması için kullanılır.
Sanal ağlar için Azure Storage güvenlik duvarları  | Desteklenir | Güvenlik Duvarı etkin önbellek depolama hesabı veya hedef depolama hesabı kullanıyorsanız, [' güvenilen Microsoft hizmetlerine Izin ver ' '](../storage/common/storage-network-security.md#exceptions)i doğrulayın.<br></br>Ayrıca, kaynak VNET 'in en az bir alt ağına erişime izin verdiğinizden emin olun.

Aşağıdaki tabloda, tek bir depolama hesabına çoğaltabilmek için disk sayısı açısından sınırlar listelenmektedir.

**Depolama hesabı türü**    |    **Dalgalanma = disk başına 4 MBps**    |    **Dalgalanma = disk başına 8 MBps**
---    |    ---    |    ---
V1 depolama hesabı    |    300 disk    |    150 disk
V2 depolama hesabı    |    750 disk    |    375 disk

Diskler üzerinde ortalama dalgalanma arttıkça, bir depolama hesabının destekleyebileceği disk sayısı azalır. Yukarıdaki tablo, sağlanması gereken depolama hesabı sayısıyla ilgili kararlar almak için bir kılavuz olarak kullanılabilir.

Yukarıdaki limitlerin Azure 'dan Azure 'a ve bölge DR senaryolarıyla bölgeye özgü olduğunu lütfen unutmayın. 

## <a name="replicated-machine-operating-systems"></a>Çoğaltılan makine işletim sistemleri

Site Recovery, bu bölümde listelenen işletim sistemlerini çalıştıran Azure VM 'lerinin çoğaltılmasını destekler. Zaten çoğaltılan bir makine farklı bir ana çekirdeğe yükseltildiyse (veya indirgenirse), çoğaltmayı devre dışı bırakmanız ve yükseltmeden sonra çoğaltmayı yeniden etkinleştirmeniz gerektiğini unutmayın.

### <a name="windows"></a>Windows


**İşletim sistemi** | **Ayrıntılar**
--- | ---
Windows Server 2019 | Sunucu çekirdeği için desteklenir, masaüstü deneyimi olan sunucu.
Windows Server 2016  | Desteklenen sunucu çekirdeği, masaüstü deneyimi ile sunucu.
Windows Server 2012 R2 | Destekleniyor.
Windows Server 2012 | Destekleniyor.
SP1/SP2 ile Windows Server 2008 R2 | Destekleniyor.<br/><br/> Azure VM 'Leri için Mobility hizmeti uzantısının [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) sürümünden windows Server 2008 R2 SP1/SP2 çalıştıran makinelere bir Windows [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) yüklemeniz gerekir.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://aka.ms/SHA-2KB)hakkında daha fazla bilgi edinin.
Windows 10 (x64) | Destekleniyor.
Windows 8.1 (x64) | Destekleniyor.
Windows 8 (x64) | Destekleniyor.
SP1 ile Windows 7 (x64) sürümleri | Azure VM 'Leri için Mobility hizmeti uzantısının [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) sürümünden WINDOWS 7 SP1 çalıştıran makinelerde bir Windows [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) yüklemeniz gerekir.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://aka.ms/SHA-2KB)hakkında daha fazla bilgi edinin.



#### <a name="linux"></a>Linux

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/), [8,3](https://support.microsoft.com/help/4597409/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 pre-GA sürüm](https://support.microsoft.com/help/4578241/), 7,9 </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Ubuntu 14,04 LTS sunucusu | Tüm 14,04 için destek içerir. *x* sürümleri; [Desteklenen çekirdek sürümleri](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16,04 LTS sunucusu | Tüm 16,04 için destek içerir. *x* sürümleri; [Desteklenen çekirdek sürümü](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Parola tabanlı kimlik doğrulaması ve oturum açma kullanan Ubuntu sunucuları ve bulut VM 'Leri yapılandırmak için Cloud-init paketi, yük devretmede (cloudinit yapılandırmasına bağlı olarak) parola tabanlı oturum açma devre dışı kalabilir. Parola tabanlı oturum açma, Azure portal > sorun giderme > ayarları menüsündeki (yük devredilen VM 'nin) parolasını sıfırlayarak sanal makinede yeniden etkinleştirilebilir.
Ubuntu 18,04 LTS sunucusu | Tüm 18,04 için destek içerir. *x* sürümleri; [Desteklenen çekirdek sürümü](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Parola tabanlı kimlik doğrulaması ve oturum açma kullanan Ubuntu sunucuları ve bulut VM 'Leri yapılandırmak için Cloud-init paketi, yük devretmede (cloudinit yapılandırmasına bağlı olarak) parola tabanlı oturum açma devre dışı kalabilir. Parola tabanlı oturum açma, Azure portal > sorun giderme > ayarları menüsündeki (yük devredilen VM 'nin) parolasını sıfırlayarak sanal makinede yeniden etkinleştirilebilir.
Ubuntu 20,04 LTS sunucusu | Tüm 20,04 için destek içerir. *x* sürümleri; [Desteklenen çekirdek sürümü](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Deyi 7 | Tüm 7 desteğini içerir. *x* sürümleri [desteklenen çekirdek sürümleri](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Tüm 8 desteğini içerir. *x* sürümleri [desteklenen çekirdek sürümleri](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | 9,13 için 9,1 desteğini içerir. De, 9,0 desteklenmez. [Desteklenen çekirdek sürümleri](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Desteklenen çekirdek sürümleri](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(desteklenen çekirdek sürümleri)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(desteklenen çekirdek sürümleri)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Makine, SP3 ile SP4'E çoğaltma için yükseltme desteklenmez. Çoğaltılan bir makine yükseltildiyse, çoğaltmayı devre dışı bırakmanız ve yükseltmeden sonra çoğaltmayı yeniden etkinleştirmeniz gerekir.
SUSE Linux Enterprise Server 11 | MADAN
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409), [8,0, 8,1](https://support.microsoft.com/help/4573888/) [(Red](https://support.microsoft.com/help/4573888/) hat uyumlu çekirdek veya AYıRıCı kurumsal çekırdek sürüm 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8,1 (tüm uıek çekirdekler ve RedHat çekirdekte çalışan <= 3.10.0-1062. *, [9,35](https://support.microsoft.com/help/4573888/)' de desteklenir. RedHat çekirdekler 'nin geri kalanı için destek [9,36](https://support.microsoft.com/help/4578241/)' de mevcuttur)

> [!NOTE]
> Linux sürümleri için Azure Site Recovery özelleştirilmiş işletim sistemi görüntülerini desteklemez. Yalnızca dağıtım alt sürüm sürümünün/güncelleştirmesinin parçası olan hisse senedi çekirdekleri desteklenir.

* * Note: 15 gün içinde en son Linux keritelerini desteklemek Için, Azure Site Recovery en son Mobility Aracısı sürümünün en üstünde etkin düzeltme eki uygulama. Bu düzeltilme iki ana sürüm sürümü arasında kullanıma sunulacaktır. Mobility aracısının en son sürümüne (sık düzeltme eki ekleme) güncelleştirmek için [Bu makalede](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)bahsedilen adımları izleyin. Bu düzeltme eki Şu anda Azure 'da Azure DR senaryosunda kullanılan Mobility aracıları için kullanıma hazır.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure sanal makineleri için desteklenen Ubuntu çekirdek sürümleri

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure |
|||
16,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-Generic-4.4.0-201-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-133-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1106-Azure <br/> 4.4.0-203-Generic, 4.4.0-204-Generic, 4.4.0-206-Generic, 4.15.0-136-Generic, 4.15.0-137-Generic, 4.15.0-139-Generic, 4.15.0-140-Generic, 4.15.0-1108-Azure, 4.15.0-1109-Azure, 4.15.0-1110-Azure, 4.15.0-1111-Azure ile 9,41 sık düzeltme eki uygulama|
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-Generic-4.4.0-197-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Generic ila 4.15.0-128-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1102-Azure </br> 4.15.0-132-Generic, 4.4.0-200-Generic, 4.15.0-1106-Azure, 4.15.0-133-Generic, 4.4.0-201-Generic ila 9,40 Hot düzeltme eki uygulama * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-Generic-4.4.0-194-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-123-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1098-Azure </br> 4.4.0-197-Generic, 4.15.0-126-Generic, 4.15.0-128-Generic, 4.15.0-1100-Azure, 4.15.0-1102-Azure ile 9,39 sık düzeltme eki düzeltme eki * *|
16,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-Generic-4.4.0-190-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-118-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1096-Azure </br> 4.4.0-193-Generic, 4.15.0-120-Generic, 4.15.0-122.368-Generic, 4.15.0-1098-Azure-9,38 sık Düzeltme Eki|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-Generic-4.4.0--Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-115-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1093-Azure </br> 4.4.0-190-Generic, 4.15.0-117-Generic, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure ile 9,37 sık düzeltme eki düzeltme eki * *|
|||
18,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-Genel-4.15.0-135-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-65-Generic </br> 5.3.0-19-Generic-5.3.0-70-Generic </br> 5.4.0-37-genel-5.4.0-59-Generic</br> 5.4.0-60-Generic ila 5.4.0-65-Generic </br> 4.15.0-1009-Azure-4.15.0-1106-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1039-Azure </br> 4.15.0-136-Generic, 4.15.0-137-Generic, 4.15.0-139-Generic, 4.15.0-140-Generic, 5.3.0-72-Generic, 5.4.0-66-Generic, 5.4.0-67-Generic, 5.4.0-70-Generic, 4.15.0-1108-Azure, 4.15.0-1111-Azure, 5.4.0-1040-Azure, 5.4.0-1041-Azure, 5.4.0-1043-Azure, 4.15.0-1109-Azure, 4.15.0-1110-Azure ile 9,41 sık düzeltme eki uygulama|
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-Genel-4.15.0-129-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-63-Generic </br> 5.3.0-19-genel-5.3.0-69-Generic </br> 5.4.0-37-genel-5.4.0-59-Generic</br> 4.15.0-1009-Azure-4.15.0-1103-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1035-Azure </br> 4.15.0-1104-Azure, 4.15.0-130-Generic, 4.15.0-132-Generic, 5.4.0-1036-Azure, 5.4.0-60-Genel, 5.4.0-62-Generic, 4.15.0-1106-Azure, 4.15.0-134-Generic, 4.15.0-135-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic ile 9,40 sık düzeltme eki uygulama|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-Generic ila 4.15.0-123-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-63-Generic </br> 5.3.0-19-genel-5.3.0-69-Generic </br> 5.4.0-37-Generic-5.4.0-53-Generic</br> 4.15.0-1009-Azure-4.15.0-1099-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1031-Azure </br> 4.15.0-124-Generic, 5.4.0-54-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 4.15.0-1100-Azure, 4.15.0-126-Generic, 4.15.0-128-Generic, 5.4.0-58-Generic, 4.15.0-1102-Azure, 5.4.0-1034-Azure ile 9,39 Hot düzeltmesini Düzeltme Eki * *|
18,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-Genel-4.15.0-118-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-61-Generic </br> 5.3.0-19-Generic-5.3.0-67-Generic </br> 5.4.0-37-Generic to 5.4.0-48-Generic</br> 4.15.0-1009-Azure-4.15.0-1096-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1026-Azure </br> 4.15.0-121-Generic, 4.15.0-122.368-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure-9,38 sık Düzeltme Eki|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-Generic ila 4.15.0-115-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-60-Generic </br> 5.3.0-19-Generic-5.3.0-66-Generic </br> 5.4.0-37-Generic to 5.4.0-45-Generic</br> 4.15.0-1009-Azure-4.15.0-1093-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure ile 5.4.0-1023-Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-Generic, 5.3.0-67-Generic, 5.4.0-47-Generic, 5.4.0-48-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure ile 9,37 Hot düzeltmesini Düzeltme Eki|
|||
20,04 LTS |[9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-Generic-5.4.0-65 </br> -Generic 5.4.0-1010-Azure-5.4.0-1039-Azure </br> 5.8.0-29-Generic to 5.8.0-43-Generic </br> 5.4.0-66-Generic, 5.4.0-67-Generic, 5.4.0-70-Generic, 5.8.0-44-Generic, 5.8.0-45-Generic, 5.8.0-48-Generic, 5.4.0-1040-Azure, 5.4.0-1041-Azure, 5.4.0-1043-Azure ile 9,41 sık düzeltme eki uygulama|
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-Generic-5.4.0-59 </br> -Generic 5.4.0-1010-Azure-5.4.0-1035-Azure </br> 5.8.0-29-genel-5.8.0-34-Generic </br> 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 5.8.0-36-Generic, 5.8.0-38-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic, 5.8.0-40-Generic, 5.8.0-41-Generic ila 9,40 Hot düzeltmesini Düzeltme Eki * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-Generic-5.4.0-53 </br> -Generic 5.4.0-1010-Azure-5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure-9,39 sık Düzeltme Eki
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-Generic-5.4.0-53 </br> -Generic 5.4.0-1010-Azure-5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure-9,39 sık Düzeltme Eki
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-Generic to 5.4.0-48 </br> -Generic 5.4.0-1010-Azure-5.4.0-1026-Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-genel, 5.8.0-25-Generic, 5.4.0-1031-Azure ile 9,38 sık düzeltme eki uygulama
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-Generic-5.4.0-45 </br> -Generic 5.4.0-1010-Azure ile 5.4.0-1023-Azure </br> 5.4.0-47-Generic, 5.4.0-48-Generic, 5.4.0-1025-Azure, 5.4.0-1026-Azure ile 9,37 sık düzeltme eki düzeltme eki * *

* * Note: 15 gün içinde en son Linux keritelerini desteklemek Için, Azure Site Recovery en son Mobility Aracısı sürümünün en üstünde etkin düzeltme eki uygulama. Bu düzeltilme iki ana sürüm sürümü arasında kullanıma sunulacaktır. Mobility aracısının en son sürümüne (sık düzeltme eki ekleme) güncelleştirmek için [Bu makalede](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)bahsedilen adımları izleyin. Bu düzeltme eki Şu anda Azure 'da Azure DR senaryosunda kullanılan Mobility aracıları için kullanıma hazır.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure sanal makineleri için desteklenen de, çekirdek sürümleri

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
Deyi 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-AMD64-3.2.0-6-AMD64, 3.16.0 -0. BPO. 4-AMD64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-AMD64 ila 3.16.0-11-AMD64, 4.9.0 -0. BPO. 4-AMD64 ila 4.9.0 -0. BPO. 11-AMD64 |
|||
De, 9,1 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 14-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 14-Cloud-AMD64 </br> 4.9.0-15-AMD64, 4.19.0 -0. BPO. 16-AMD64, 4.19.0 -0. BPO. 16-Cloud-AMD64 ila 9,41 sık düzeltme eki uygulama * *
De, 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 13-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 13-Cloud-AMD64 
De, 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 12-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 12-Cloud-AMD64 </br> 4.19.0 -0. BPO. 13-AMD64, 4.19.0 -0. BPO. 13-Cloud-AMD64 ila 9,39 sık Düzeltme Eki</br> 
De, 9,1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-AMD64-4.9.0-13-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 ila 4.19.0 -0. BPO. 11-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 ila 4.19.0 -0. BPO. 11-Cloud-AMD64 </br> 4.9.0-14-AMD64, 4.19.0 -0. BPO. 12-AMD64, 4.19.0 -0. BPO. 12-Cloud-AMD64 ila 9,38 sık düzeltme eki uygulama * *
De, 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-AMD64-4.9.0-13-AMD64, 4.19.0 -0. BPO. 6-AMD64 ila 4.19.0 -0. BPO. 10-AMD64, 4.19.0 -0. BPO. 6-Cloud-AMD64 ila 4.19.0 -0. BPO. 10-Cloud-AMD64
|||
Debian 10 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-AMD64-4.19.0-14-AMD64 </br> 4.19.0-6-Cloud-AMD64, 4.19.0-14-Cloud-AMD64 </br> 5.8.0 -0. BPO. 2-AMD64 </br> 5.8.0 -0. BPO. 2-Cloud-AMD64 </br> 4.19.0-10-Cloud-AMD64, 4.19.0-16-AMD64, 4.19.0-16-Cloud-AMD64 ila 9,41 sık Düzeltme Eki
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-AMD64-4.19.0-13-AMD64 </br> 4.19.0-6-Cloud-AMD64, 4.19.0-13-Cloud-AMD64 </br> 5.8.0 -0. BPO. 2-AMD64 </br> 5.8.0 -0. BPO. 2-Cloud-AMD64

* * Note: 15 gün içinde en son Linux keritelerini desteklemek Için, Azure Site Recovery en son Mobility Aracısı sürümünün en üstünde etkin düzeltme eki uygulama. Bu düzeltilme iki ana sürüm sürümü arasında kullanıma sunulacaktır. Mobility aracısının en son sürümüne (sık düzeltme eki ekleme) güncelleştirmek için [Bu makalede](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)bahsedilen adımları izleyin. Bu düzeltme eki Şu anda Azure 'da Azure DR senaryosunda kullanılan Mobility aracıları için kullanıma hazır.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure sanal makineleri için desteklenen SUSE Linux Enterprise Server 12 çekirdek sürümü

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.44-Azure </br> 4.12.14-16.47-Azure 9,41 sık düzeltme eki uygulama * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.38-Azure </br> 4.12.14-16.41-Azure 9,40 sık düzeltme eki uygulama * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.34-Azure </br> 4.12.14-16.38-Azure 9,39 sık düzeltme eki uygulama * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,37](https://support.microsoft.com/help/4582666/) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.22-Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-Azure ile 9,37 sık Düzeltme Eki|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure sanal makineleri için desteklenen SUSE Linux Enterprise Server 15 çekirdek sürümü

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.55-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.35-Azure </br> 5.3.18-18.38-Azure 9,41 sık düzeltme eki uygulama * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.58-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.29-Azure </br> 5.3.18-18.32-Azure, 4.12.14-8.58-Azure ile 9,40 sık Düzeltme Eki
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.47-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.21-Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure ile 9,39 sık Düzeltme Eki
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.44-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.18-Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-Azure ile 9,38 sık Düzeltme Eki
SUSE Linux Enterprise Server 15 ve 15 SP1 | [9,37](https://support.microsoft.com/help/4582666/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.38-Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-Azure ile 9,37 sık Düzeltme Eki

* * Note: 15 gün içinde en son Linux keritelerini desteklemek Için, Azure Site Recovery en son Mobility Aracısı sürümünün en üstünde etkin düzeltme eki uygulama. Bu düzeltilme iki ana sürüm sürümü arasında kullanıma sunulacaktır. Mobility aracısının en son sürümüne (sık düzeltme eki ekleme) güncelleştirmek için [Bu makalede](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)bahsedilen adımları izleyin. Bu düzeltme eki Şu anda Azure 'da Azure DR senaryosunda kullanılan Mobility aracıları için kullanıma hazır.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Çoğaltılan makineler-Linux dosya sistemi/Konuk depolama

* Dosya sistemleri: ext3, ext4, XFS, BTRFS
* Birim Yöneticisi: LVM2

> [!NOTE]
> Çok yollu yazılım desteklenmez.


## <a name="replicated-machines---compute-settings"></a>Çoğaltılan makineler-işlem ayarları

**Ayar** | **Destek** | **Ayrıntılar**
--- | --- | ---
Boyut | En az 2 CPU çekirdeğine ve 1 GB RAM 'e sahip Azure VM boyutu | [Azure sanal makine boyutlarını](../virtual-machines/sizes.md)doğrulayın.
Kullanılabilirlik kümeleri | Desteklenir | Varsayılan seçeneklerle bir Azure VM için çoğaltmayı etkinleştirirseniz, kaynak bölge ayarlarına bağlı olarak bir kullanılabilirlik kümesi otomatik olarak oluşturulur. Bu ayarları değiştirebilirsiniz.
Kullanılabilirlik alanları | Desteklenir |
Karma kullanım teklifi (HUB) | Desteklenir | Kaynak VM 'nin etkinleştirilmiş bir HUB lisansı varsa, yük devretme testi veya yük devretme VM 'si de HUB lisansı kullanır.
Sanal makine ölçek kümeleri | Desteklenmez |
Azure galeri görüntüleri-Microsoft tarafından yayımlandı | Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Azure galeri görüntüleri-üçüncü taraf yayımlandı | Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Özel görüntüler-üçüncü taraf yayımlandı | Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Site Recovery kullanılarak geçirilen VM 'Ler | Desteklenir | Site Recovery kullanılarak Azure 'a bir VMware VM veya fiziksel makine geçirilirse, makinede çalışan Mobility hizmetinin eski sürümünü kaldırmanız ve makineyi başka bir Azure bölgesine çoğaltmadan önce yeniden başlatmanız gerekir.
Azure RBAC ilkeleri | Desteklenmez | VM 'lerde Azure rol tabanlı erişim denetimi (Azure RBAC) ilkeleri hedef bölgedeki yük devretme VM 'sine çoğaltılmaz.
Uzantıları | Desteklenmez | Uzantılar, hedef bölgedeki yük devretme VM 'sine çoğaltılmaz. Yük devretmenin ardından el ile yüklenmesi gerekir.
Yakınlık yerleştirme grupları | Desteklenir | Bir yakınlık yerleşimi grubunda bulunan sanal makineler, Site Recovery kullanılarak korunabilir.
Etiketler  | Desteklenir | Kaynak sanal makinelere uygulanan kullanıcı tarafından oluşturulan Etiketler, test yük devretmesi veya yük devretme sonrası hedef sanal makinelerin üzerine taşınır. VM 'ler üzerindeki etiketler, sanal makine (ler) hedef bölgede bulunduğu sürece her 24 saatte bir çoğaltılır.


## <a name="replicated-machines---disk-actions"></a>Çoğaltılan makineler-disk eylemleri

**Eylem** | **Ayrıntılar**
-- | ---
Çoğaltılan VM 'de diski yeniden boyutlandır | Yük devretmeden önce kaynak VM 'de desteklenir. Çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeye gerek yoktur.<br/><br/> Yük devretmeden sonra kaynak VM 'yi değiştirirseniz, değişiklikler yakalanmaz.<br/><br/> Yük devretmeden sonra Azure VM 'de disk boyutunu değiştirirseniz, değişiklikler Site Recovery tarafından yakalanmaz ve yeniden çalışma özgün VM boyutuna göre yapılır.
Çoğaltılan bir VM 'ye disk ekleme | Desteklenir
Korumalı disklerde çevrimdışı değişiklikler | Disklerin bağlantısının kesilmesi ve üzerinde çevrimdışı değişiklikler yapılması, tam yeniden eşitleme tetiklemesini gerektirir.

## <a name="replicated-machines---storage"></a>Çoğaltılan makineler-depolama

Bu tabloda, Azure VM işletim sistemi diski, veri diski ve geçici disk için destek özetlenmektedir.

- Performans sorunlarından kaçınmak için, [yönetilen DISKLERIN](../virtual-machines/disks-scalability-targets.md) VM disk sınırlarını ve hedeflerini gözlemlemek önemlidir.
- Varsayılan ayarlarla dağıtırsanız, Site Recovery kaynak ayarlarına bağlı olarak diskleri ve depolama hesaplarını otomatik olarak oluşturur.
- ' Yi özelleştirirseniz, yönergeleri izlediğinizden emin olun.

**Bileşen** | **Destek** | **Ayrıntılar**
--- | --- | ---
İşletim sistemi diski en büyük boyutu | 2048 GB | VM diskleri hakkında [daha fazla bilgi edinin](../virtual-machines/managed-disks-overview.md) .
Geçici disk | Desteklenmez | Geçici disk her zaman çoğaltmadan çıkarılır.<br/><br/> Kalıcı verileri geçici diskte depolamamayın. [Daha fazla bilgi edinin](../virtual-machines/managed-disks-overview.md).
Veri diski en büyük boyutu | yönetilen diskler için 32 TB<br></br>yönetilmeyen diskler için 4 TB|
Veri diski en küçük boyutu | Yönetilmeyen diskler için kısıtlama yoktur. yönetilen diskler için 2 GB |
Veri diski en fazla sayı | Belirli bir Azure VM boyutu için desteğe uygun olarak 64 'e kadar | VM boyutları hakkında [daha fazla bilgi edinin](../virtual-machines/sizes.md) .
Veri diski değişim oranı | Premium Depolama için disk başına en fazla 20 MBps. Standart depolama için disk başına en fazla 2 MBps. | Disk üzerindeki ortalama veri değişim oranı, en yüksek değerden sürekli yüksek ise çoğaltma işlemi gerçekleştirilmez.<br/><br/>  Ancak, en yüksek değer, tek tek aşılırsa, çoğaltma yakalayabilir, ancak biraz gecikmeli kurtarma noktası görebilirsiniz.
Veri diski-standart depolama hesabı | Desteklenir |
Veri diski-Premium depolama hesabı | Desteklenir | Bir VM 'nin Premium ve standart depolama hesapları arasında yayıldığı diskler varsa, hedef bölgede aynı depolama yapılandırmasına sahip olduğunuzdan emin olmak için her bir disk için farklı bir hedef depolama hesabı seçebilirsiniz.
Yönetilen disk-standart | Azure Site Recovery desteklendiği Azure bölgelerinde desteklenir. |
Yönetilen disk-Premium | Azure Site Recovery desteklendiği Azure bölgelerinde desteklenir. |
Standart SSD | Desteklenir |
Yedeklilik | LRS ve GRS desteklenir.<br/><br/> ZRS desteklenmez.
Seyrek erişimli ve sık erişimli depolama | Desteklenmez | Seyrek erişimli ve sık erişimli depolamada VM diskleri desteklenmez
Depolama Alanları | Desteklenir |
NVMe depolama arabirimi | Desteklenmez
Bekleyen şifreleme (SSE) | Desteklenir | SSE, depolama hesaplarında varsayılan ayardır.
Bekleyen şifreleme (CMK) | Desteklenir | Yönetilen diskler için hem yazılım hem de HSM anahtarları desteklenir
Rest 'te Çift şifreleme | Desteklenir | [Windows](../virtual-machines/disk-encryption.md) ve [Linux](../virtual-machines/disk-encryption.md) için desteklenen bölgeler hakkında daha fazla bilgi edinin
Windows işletim sistemi için Azure disk şifrelemesi (ADE) | Yönetilen disklere sahip VM 'Ler için desteklenir. | Yönetilmeyen diskleri kullanan VM 'Ler desteklenmez. <br/><br/> HSM korumalı anahtarlar desteklenmez. <br/><br/> Tek bir diskte ayrı ayrı birimlerin şifrelenmesi desteklenmez. |
Linux işletim sistemi için Azure disk şifrelemesi (ADE) | Yönetilen disklere sahip VM 'Ler için desteklenir. | Yönetilmeyen diskleri kullanan VM 'Ler desteklenmez. <br/><br/> HSM korumalı anahtarlar desteklenmez. <br/><br/> Tek bir diskte ayrı ayrı birimlerin şifrelenmesi desteklenmez. <br><br> Çoğaltmayı etkinleştirme ile ilgili bilinen sorun. [Daha fazla bilgi edinin.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS anahtarı döndürme | Desteklenmiyor | Depolama hesapları için SAS anahtarı döndürülürse, müşterinin çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeleri gerekir. |
Konak önbelleğe alma | Desteklenir
Sık erişimli ekleme    | Desteklenir | Çoğaltılan bir Azure VM 'ye eklediğiniz bir veri diski için çoğaltmanın etkinleştirilmesi, yönetilen diskleri kullanan VM 'Ler için desteklenir. <br/><br/> Tek seferde bir Azure VM 'sine yalnızca bir disk eklenebilir. Birden çok diskin paralel eklenmesi desteklenmez. |
Diski sık erişimli kaldır    | Desteklenmez | SANAL makinede veri diski kaldırırsanız, çoğaltmayı devre dışı bırakmanız ve VM için çoğaltmayı yeniden etkinleştirmeniz gerekir.
Diski hariç tutma | Support. Yapılandırmak için [PowerShell](azure-to-azure-exclude-disks.md) kullanmanız gerekir. |    Geçici diskler varsayılan olarak dışlanır.
Doğrudan Depolama Alanları  | Kilitlenme ile tutarlı kurtarma noktaları için desteklenir. Uygulamayla tutarlı kurtarma noktaları desteklenmez. |
Genişleme dosya sunucusu  | Kilitlenme ile tutarlı kurtarma noktaları için desteklenir. Uygulamayla tutarlı kurtarma noktaları desteklenmez. |
DRBD | Bir DRBD kurulumunun parçası olan diskler desteklenmez. |
LRS | Desteklenir |
GRS | Desteklenir |
RA-GRS | Desteklenir |
ZRS | Desteklenmez |
Seyrek erişimli ve sık erişimli depolama | Desteklenmez | Seyrek erişimli ve sık erişimli depolamada sanal makine diskleri desteklenmez
Sanal ağlar için Azure Storage güvenlik duvarları  | Desteklenir | Depolama hesaplarına sanal ağ erişimini kısıtladıysanız, [Güvenilen Microsoft hizmetlerine Izin vermeyi](../storage/common/storage-network-security.md#exceptions)etkinleştirin.
Genel amaçlı v2 depolama hesapları (sık erişimli ve Seyrek Erişimli Katman) | Desteklenir | İşlem maliyetleri, genel amaçlı v1 depolama hesaplarıyla karşılaştırıldığında önemli ölçüde artar
2. nesil (UEFı önyüklemesi) | Desteklenir
NVMe diskleri | Desteklenmez
Azure paylaşılan diskleri | Desteklenmez
Güvenli aktarım seçeneği | Desteklenir
Hızlandırıcı etkinleştirilmiş diskler yazma | Desteklenmez
Etiketler  | Desteklenir | Kullanıcı tarafından oluşturulan Etiketler her 24 saatte bir çoğaltılır.

>[!IMPORTANT]
> Performans sorunlarından kaçınmak için, [yönetilen diskler](../virtual-machines/disks-scalability-targets.md)için VM disk ölçeklenebilirliğini ve performans hedeflerini izlediğinizden emin olun. Varsayılan ayarları kullanıyorsanız, Site Recovery, kaynak yapılandırmasına göre gerekli diskleri ve depolama hesaplarını oluşturur. Kendi ayarlarınızı özelleştirip seçerseniz, kaynak sanal makinelerinize yönelik disk ölçeklenebilirlik ve performans hedeflerini izleyin.

## <a name="limits-and-data-change-rates"></a>Sınırlar ve veri değişim oranları

Aşağıdaki tablo Site Recovery sınırlarını özetler.

- Bu sınırlar, testlerimize dayalıdır, ancak olası tüm uygulama g/ç birleşimlerini kapsamamaktadır.
- Gerçek sonuçlar, uygulama g/ç karışımına göre farklılık gösterebilir.
- Disk başına veri dalgalanması ve sanal makine veri dalgalanması için göz önünde bulundurulması gereken iki sınır vardır.
- Sanal makine veri karmaşası için geçerli sınır, boyut ne olursa olsun 54 MB/sn 'dir.

**Depolama hedefi** | **Ortalama kaynak disk g/ç** |**Ortalama kaynak disk veri değişim sıklığı** | **Gün başına toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |    Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük |20 MB/sn | disk başına 1684 GB

## <a name="replicated-machines---networking"></a>Çoğaltılan makineler-ağ
**Ayar** | **Destek** | **Ayrıntılar**
--- | --- | ---
NIC | Belirli bir Azure VM boyutu için desteklenen en yüksek sayı | NIC 'ler, yük devretme sırasında VM oluşturulduğunda oluşturulur.<br/><br/> Yük devretme sanal makinesinde bulunan NIC 'lerin sayısı, çoğaltma etkinleştirildiğinde kaynak VM üzerindeki NIC 'lerin sayısına bağlıdır. Çoğaltmayı etkinleştirdikten sonra bir NIC ekler veya kaldırırsanız, yük devretmeden sonra çoğaltılan VM 'deki NIC 'lerin sayısını etkilemez. <br/><br/> Yük devretmeden sonra NIC 'lerin sırası orijinal siparişle aynı olmalıdır. <br/><br/> Hedef bölgede NIC 'Leri, kuruluşunuzun adlandırma kurallarına göre yeniden adlandırabilirsiniz. NIC yeniden adlandırma, PowerShell kullanılarak desteklenir.
İnternet Yük Dengeleyici | Desteklenmez | Birincil bölgede ortak/internet yük dengeleyiciler ayarlayabilirsiniz. Ancak, genel/İnternet yük dengeleyiciler DR bölgesindeki Azure Site Recovery tarafından desteklenmez.
İç yük dengeleyici | Desteklenir | Önceden yapılandırılmış yük dengeleyiciyi bir kurtarma planında bir Azure Otomasyonu betiği kullanarak ilişkilendirin.
Genel IP adresi | Desteklenir | Mevcut bir genel IP adresini NIC ile ilişkilendirin. Ya da bir genel IP adresi oluşturun ve bir kurtarma planında Azure Otomasyonu betiği kullanarak NIC ile ilişkilendirin.
NIC üzerinde NSG | Desteklenir | Bir kurtarma planında Azure Otomasyonu betiği kullanarak NSG 'yi NIC ile ilişkilendirin.
Alt ağda NSG | Desteklenir | Bir kurtarma planında Azure Otomasyonu betiği kullanarak NSG 'yi alt ağ ile ilişkilendirin.
Ayrılmış (statik) IP adresi | Desteklenir | Kaynak VM üzerindeki NIC 'nin statik bir IP adresi varsa ve hedef alt ağda aynı IP adresi varsa, yük devredilen VM 'ye atanır.<br/><br/> Hedef alt ağa aynı IP adresi yoksa, alt ağdaki kullanılabilir IP adreslerinden biri VM için ayrılmıştır.<br/><br/> Ayrıca, **çoğaltılan öğeler**  >  **ayarları**  >  **işlem ve ağ**  >  **ağı arabirimleri**' nde sabit bir IP adresi ve alt ağ belirtebilirsiniz.
Dinamik IP adresi | Desteklenir | Kaynaktaki NIC dinamik IP adreslemesini içeriyorsa, yük devredilen VM üzerindeki NIC de varsayılan olarak dinamik olur.<br/><br/> Gerekirse bunu sabit bir IP adresi ile değiştirebilirsiniz.
Birden çok IP adresi | Desteklenmez | Birden çok IP adresli NIC 'ye sahip bir VM 'nin yükünü devretmek için yalnızca kaynak bölgedeki NIC 'in birincil IP adresi tutulur. Birden çok IP adresi atamak için, bir [kurtarma planına](recovery-plan-overview.md) VM ekleyebilir ve plana ek IP adresleri atamak için bir komut dosyası ekleyebilir ya da değişikliği yük devretmeden sonra el ile veya bir komut dosyasıyla yapabilirsiniz.
Traffic Manager     | Desteklenir | Traffic Manager, trafiğin kaynak bölgedeki uç noktaya ve yük devretme durumunda hedef bölgedeki uç noktaya yönlendirilmesi için önceden yapılandırabilirsiniz.
Azure DNS | Desteklenir |
Özel DNS    | Desteklenir |
Kimliği doğrulanmamış proxy | Desteklenir | [Daha fazla bilgi edinin](./azure-to-azure-about-networking.md)
Kimliği doğrulanmış ara sunucu | Desteklenmez | VM, giden bağlantı için kimliği doğrulanmış bir proxy kullanıyorsa, Azure Site Recovery kullanılarak çoğaltılamaz.
Şirket içinde VPN siteden siteye bağlantısı<br/><br/>(ExpressRoute ile veya olmayan)| Desteklenir | UDRs ve NSG 'Lerin Site Recovery trafiğinin şirket içine yönlendirilmediğinden emin olmak için yapılandırıldığından emin olun. [Daha fazla bilgi edinin](./azure-to-azure-about-networking.md)
VNET 'ten VNET 'e bağlantı    | Desteklenir | [Daha fazla bilgi edinin](./azure-to-azure-about-networking.md)
Sanal Ağ Hizmet Uç Noktaları | Desteklenir | Depolama hesaplarına sanal ağ erişimini kısıtladığınız takdirde, güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin verildiğinden emin olun.
Hızlandırılmış ağ iletişimi | Desteklenir | Kaynak VM 'de hızlandırılmış ağ etkin olmalıdır. [Daha fazla bilgi edinin](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto ağ gereci | Desteklenmez | Üçüncü taraf gereçlerle, genellikle sağlayıcı tarafından sanal makine içinde uygulanan kısıtlamalar vardır. Azure Site Recovery aracı, Uzantılar ve giden bağlantıların kullanılabilir olmasını gerektirir. Ancak gereç, sanal makine içinde herhangi bir giden etkinliğin yapılandırılmasına izin vermez.
IPv6  | Desteklenmez | Hem IPv4 hem de IPv6 içeren karışık yapılandırma de desteklenmez. Lütfen herhangi bir Site Recovery işleminden önce IPv6 aralığının alt ağını boşaltın.
Site Recovery hizmetine özel bağlantı erişimi | Desteklenir | [Daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Etiketler  | Desteklenir | NIC 'lerde Kullanıcı tarafından oluşturulan Etiketler her 24 saatte bir çoğaltılır.



## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'lerini çoğaltmak için [ağ kılavuzunu](./azure-to-azure-about-networking.md)  okuyun.
- [Azure VM 'lerini çoğaltarak](./azure-to-azure-quickstart.md)olağanüstü durum kurtarma dağıtın.
