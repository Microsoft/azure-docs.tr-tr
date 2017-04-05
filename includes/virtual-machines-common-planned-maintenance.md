

## <a name="memory-preserving-updates"></a>Bellek koruma güncelleştirmeleri
Microsoft Azure’daki bir güncelleştirmeler grubu için, müşteriler çalışan sanal makinelerine herhangi bir etki görmez. Bu güncelleştirmelerin çoğu, çalışan örneği engellemeden güncelleştirilebilecek bileşenlere veya hizmetler içindir. Bu güncelleştirmelerin bazıları, sanal makinelerin tam olarak yeniden başlatılması gerekmeden ana bilgisayar işletim sisteminde uygulanabilecek platform altyapısı güncelleştirmeleridir.

Bu güncelleştirmeler, "bellek koruma" güncelleştirmesi olarak da adlandırılan, yerinde dinamik geçiş olanağı sağlayan teknolojiyle gerçekleştirilir. Güncelleştirirken, RAM belleği korunarak sanal makine “duraklatılmış” duruma getirilir, bu arada alttaki konak işletim sistemi gerekli güncelleştirmeleri ve düzeltme eklerini alır. Sanal makine duraklatıldıktan sonra 30 saniye içinde devam ettirilir. Devam ettirildikten sonra, sanal makinenin saati otomatik olarak eşitlenir.

Tüm güncelleştirmeler bu mekanizma kullanılarak dağıtılamaz, ancak kısa duraklatma süresi düşünüldüğünde, güncelleştirmelerin bu şekilde dağıtılmasıyla sanal makinelere etki büyük ölçüde azaltılır.

Çok örnekli güncelleştirmeler (bir kullanılabilirlik kümesindeki sanal makineler için), bir seferde bir güncelleme etki alanı şeklinde uygulanır.  

## <a name="virtual-machine-configurations"></a>Sanal makine yapılandırmaları
İki tür sanal makine yapılandırması vardır: çok örnekli ve tek örnekli. Çok örnekli yapılandırmada, benzer sanal makineler bir kullanılabilirlik kümesine yerleştirilir.

Çok örnekli yapılandırma, fiziksel makineler arasında yedeklilik, güç ile ağ sağlar ve uygulamanızın kullanılabilirliğini sağlamak için önerilir. Kullanılabilirlik kümesindeki tüm sanal makineler, uygulamanız için aynı amaca hizmet etmelidir.

Sanal makinelerinizi yüksek kullanılabilirlik için yapılandırma hakkında daha fazla bilgi için, bkz: [Windows sanal makinelerinizin kullanılabilirliğini yönetme](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) veya [Linux sanal makinelerinizin kullanılabilirliğini yönetme](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Bunun aksine, tek örnekli yapılandırma, bir kullanılabilirlik kümesine yerleştirilmemiş bağımsız sanal makineler için kullanılır. Bu sanal makineler, iki veya daha fazla sanal makinenin aynı kullanılabilirlik kümesine dağıtılmasını gerektiren hizmet düzeyi sözleşmesi (SLA) için uygun değildir.

SLA’lar hakkında daha fazla bilgi için, [Hizmet Düzeyi Sözleşmeleri](https://azure.microsoft.com/support/legal/sla/)’ndeki "Bulut Hizmetleri ve Sanal Makineler" bölümlerine bakın.

## <a name="multi-instance-configuration-updates"></a>Çok örnekli yapılandırma güncelleştirmeleri
Planlı bakım esnasında, Azure platformu önce bir çok örnekli yapılandırmada barındırılan sanal makineler kümesini güncelleştirir. Güncelleştirme bu sanal makinelerin yeniden başlatılmasına neden olur ve yaklaşık 15 dakika kapalı kalma süresi meydana gelir.

Bir çok örnekli yapılandırma güncelleştirmesi, her bir sanal makinenin kullanılabilirlik kümesindeki diğerleriyle benzer bir işlev gördüğünü varsayar. Bu ayarda, sanal makineler işlem boyunca kullanılabilirliği koruyan bir şekilde güncelleştirilir.

Bir kullanılabilirlik kümesindeki her sanal makineye, temel alınan Azure platformu tarafından bir güncelleme etki alanı ve bir hata etki alanı atanır. Her güncelleme etki alanı, aynı süre içinde yeniden başlatılacak olan bir sanal makineler grubudur. Her hata etki alanı, ortak bir güç kaynağı ve ağ anahtarını paylaşan bir sanal makine grubudur.


Güncelleştirme etki alanları ve hata etki alanları hakkında daha fazla bilgi için, bkz: [Bir kullanılabilirlik kümesindeki birden çok sanal makineyi yedeklilik için yapılandırma](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Azure, bir güncelleştirme boyunca kullanılabilirliğini sağlamak için, bir seferde bir etki alanını güncelleştirerek, bakımı güncelleme etki alanına göre gerçekleştirir. Bir güncelleme etki alanında bakım, etki alanındaki her bir sanal makinenin kapatılmasından, güncelleştirmenin ana makinelere uygulanmasından ve ardından sanal makinelerin yeniden başlatılmasından oluşur. Etki alanındaki bakım tamamlandığında, Azure işlemi sonraki güncelleme etki alanıyla tekrarlar ve tümü güncelleştirilene kadar her bir etki alanı ile devam eder.

Yeniden başlatılmakta olan güncelleme etki alanlarının sırası, planlanan bakım esnasında sıralı olarak ilerlemeyebilir, ancak aynı anda yalnızca bir güncelleme etki alanı yeniden başlatılır. Şu anda Azure, çok örnekli yapılandırmadaki sanal makinelerin planlanan bakımı için 1 hafta önceden bildirim sunmaktadır.

Bir sanal makine geri yüklendikten sonra Windows Olay Görüntüleyicisi'nde görüntülenebileceklere dair bir örnek aşağıda verilmiştir:

<!--Image reference-->
![][image2]


Azure portalı, Azure PowerShell veya Azure CLI kullanılarak çok örnekli bir yapılandırmada yapılandırılmış sanal makineleri bildirmek için görüntüleyiciyi kullanın. Örneğin, Azure portalını kullanarak, _Kullanılabilirlik Kümesi_’ni **sanal makineler (klasik)** gezinme iletişimine ekleyebilirsiniz. Aynı kullanılabilirlik kümesini bildiren sanal makineler, çok örnekli bir yapılandırmanın parçasıdır. Aşağıdaki örnekte, çok örnekli yapılandırma SQLContoso01 ve SQLContoso02 sanal makinelerinden oluşur.

<!--Image reference-->
  ![Azure portalından sanal makineler (klasik) görünümü][image4]

## <a name="single-instance-configuration-updates"></a>Tek örnekli yapılandırma güncelleştirmeleri
Çok örnekli yapılandırma güncelleştirmeleri tamamlandıktan sonra Azure tek örnek yapılandırma güncelleştirmelerini gerçekleştirir. Bu güncelleştirmeler, kullanılabilirlik kümelerinde çalışmayan sanal makineleriniz için de yeniden başlatmalara neden olur.

> [!NOTE]
> Bir kullanılabilirlik kümesinde çalışan yalnızca bir sanal makine örneği varsa, Azure platformu bunu çok örnekli bir yapılandırmasını güncelleştirmesi olarak işler.
>

Tek örnekli yapılandırmada bakım, bir ana makinede çalışan her bir sanal makinenin kapatılmasından, ana makinenin güncelleştirilmesinden ve ardından sanal makinelerin yeniden başlatılmasından oluşur. Bakım için yaklaşık 15 dakika kapalı kalma süresi gerekir. Planlanan bakım olayı, bir bölgedeki tüm sanal makinelerde tek bir bakım penceresinde çalışır.


Planlı bakım olayları, tek örnekli yapılandırmalar için uygulamanızın kullanılabilirliğini etkiler. Şu anda Azure, tek örnekli yapılandırmalardaki sanal makinelerin planlanan bakımı için bir hafta önceden bildirim sunmaktadır.

## <a name="email-notification"></a>E-posta ile bildirim
Yalnızca tek örnekli ve çok örnekli sanal makine yapılandırmaları için, Azure yaklaşan planlı bakımın e-posta uyarısını gönderir (bir hafta önceden). Bu e-posta, abonelik yöneticisine ve ortak yönetici e-posta hesaplarına gönderilir. Bu türden bir e-postanın bir örneği aşağıda verilmiştir:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Bölge çiftleri

Azure bakım yürütürken yalnızca, çiftinin tek bir bölgesindeki Sanal Makine örneklerini güncelleştirir. Örneğin, Orta Kuzey ABD’de Sanal Makineler güncelleştirilirken Azure aynı anda Orta Güney ABD’deki bir Sanal Makineyi güncelleştirmez. Bu, ayrı bir şekilde zamanlanır, böylelikle bölgeler arasında yük devretme veya yük dengeleme sağlanır. Ancak, Kuzey Avrupa gibi diğer bölgelerde, Doğu ABD ile aynı zamanda bakım yapılabilir.

Geçerli bölge çiftleri için aşağıdaki tabloya bakın:

| Bölge 1 | Bölge 2 |
|:--- | ---:|
| Doğu ABD |Batı ABD |
| Doğu ABD 2 |Orta ABD |
| Orta Kuzey ABD |Orta Güney ABD |
| Batı Orta ABD |Batı ABD 2 |
| Doğu Kanada |Orta Kanada |
| Güney Brezilya |Orta Güney ABD |
| ABD Devleti Iowa |ABD Devleti Virginia |
| US DoD Doğu |US DoD Orta |
| Kuzey Avrupa |Batı Avrupa |
| Birleşik Krallık Batı |Birleşik Krallık Güney |
| Almanya Orta |Almanya Kuzeydoğu |
| Güneydoğu Asya |Doğu Asya |
| Avustralya Güneydoğu |Avustralya Doğu |
| Hindistan Orta |Hindistan Güney |
| Hindistan Batı |Hindistan Güney |
| Japonya Doğu |Japonya Batı |
| Kore Orta |Kore Güney |
| Doğu Çin |Kuzey Çin |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
