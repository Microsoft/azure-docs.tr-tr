<a name="tellmevm"></a>

## <a name="tell-me-about-virtual-machines"></a>Bana sanal makineleri anlat
Azure Virtual Machines, bulutta sanal makineler oluşturmanızı ve kullanmanızı sağlar. *Hizmet olarak altyapı (IaaS)* olarak bilineni sağlayarak, sanal makine teknolojisi çeşitli şekillerde kullanılabilir. Bazı örnekler şunlardır:

* **Geliştirme ve test için sanal makineler (VM).** Bir uygulamayı kodlamak ve test etmek için gereken belirli yapılandırmalarla bir bilgisayarı oluşturmanın hızlı, kolay yolunu sunduklarından, geliştirme grupları yaygın olarak VM’leri kullanır. Azure Virtual Machines, bu VM’leri oluşturmanın, bunları kullanmanın ve artık gerekmediğinde de silmenin basit ve ekonomik bir yolunu sağlıyor.
* **Uygulamaları bulutta çalıştırma.** Bazı uygulamaları genel bulut ortamında çalıştırmanın ekonomik mantığı vardır. Bir örnek, isteğe bağlı büyük depoları olan bir uygulamadır. Zamanın çoğunda donanım verimli kullanılamadığından en yüksek talebi işlemek için kendi veri merkezinizi yeterli donanımla donatmış olabilirsiniz. Bu uygulamanın Azure’da çalıştırılması, yalnızca gerektiğinde VM’ler için fazladan ödeme yapmanızı, gerekmediğinde de bunları kapatmanızı sağlar. Bunun yerine, hızlı ve hiçbir taahhüdü olmadan isteği bağlı bilgi işlem kaynaklarına ihtiyaç duyan başlangıç düzeyinde biri olduğunuzu varsayalım. Bir kez daha, Azure doğru seçim olabilir.
* **Kendi veri merkezinizi genel buluta genişletme.** Azure Virtual Network kullandığınızda, kuruluşunuz kendi şirket içi ağınıza uzantısı olan sanal ağ (VNET) oluşturabilir ve VM’leri bu VNET'e ekleyebilir. Böylece, Azure VM’de [SharePoint](../articles/virtual-machines/windows/sharepoint-farm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), [SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) ve diğerlerinin çalıştırılması sağlanır. Bu yaklaşım, uygulamaları dağıtmanızı kolaylaştırabilir ve bunları kendi veri merkezinizdeki VM’lerde çalıştırmaya kıyasla daha ekonomik olabilir.   
* **Olağanüstü durum kurtarma.** Nadiren kullanılan yedek veri merkezi için sürekli ödeme yapmak yerine, IaaS temelli olağanüstü durum kurtarması, gereken bilgi işlem kaynakları için yalnızca gerçekten gerek duyduğunuzda ödeme yapmanızı sağlar.  Örneğin, birincil veri merkezinizde arıza olursa, temel uygulamaları çalıştırmak için Azure üzerinde çalışan VM’leri oluşturabilir, artık gerekmediklerinde de .unları kapatabilirsiniz.

Diğer sanal makineler gibi Azure’daki VM’de de bir işletim sistemi, depolama ve ağ özellikleri vardır, çok çeşitli uygulamaları çalıştırabilir. Azure veya ortaklarından biri tarafından sağlanan bir görüntüyü kullanabilir; isterseniz de kendinize ait olanlardan birini kullanın. Çeşitli sürümlerin, basımların ve yapılandırmaların bulunduğu örnekler:

* Suse, Ubuntu ve CentOS gibi Linux sunucuları
* Windows Server 
* SQL Server
* BizTalk Server 
* SharePoint Server

Sanal makineler, kendi işletim sistemlerini (OS) ve verilerini depolamak için sanal sabit diskleri (VHD) kullanır. VHD bir işletim sistemi yüklemek için seçebileceğiniz görüntüler için de kullanılır. Aşağıdaki şekilde, VM’lerin oluşturulması ve yönetilmesi için araçlardan ikisinin yanı sıra bu da gösterilmektedir.

<a name="fig_createvms"></a>
![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Şekil: Azure Sanal Makineler, Hizmet Olarak Altyapı sağlar.**

VM’ler tarayıcı tabanlı bir portal, betik oluşturma desteğine sahip komut satırı araçları kullanılarak veya doğrudan REST API aracılığıyla yönetilebilir. RightScale ve ScaleXtreme gibi Microsoft ortakları da REST API ile ilgili yönetim hizmetleri sağlar. 

İşletim sisteminin yanı sıra, VM’lerde bulunan diğer yapılandırma seçenekleri:

* Ekleyebileceğiniz disk sayısı veya işlem gücü gibi etkenleri saptayan boyut. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar. Ayrıntılar için bkz. [Virtual Machines boyutları](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  
* Yeni VM’nin barındırıldığı Azure bölgesi; ABD, Avrupa veya Asya gibi. 
* VM uzantıları, virüsten koruma çalıştırma ya da Windows PowerShell İstenen Durum Yapılandırması özelliğini kullanma gibi sanal makine ek özellikleri sağlar.

VM’ler için dikkate alınması gereken diğer avantajlar şunlardır:

**Kullandıkça öde** --Azure’un ücretlendirdiği, VM’nin boyutu ve işletim sistemi temelinde saatlik fiyat. Kısmi saatler için, Azure yalnızca kullanılan dakika sayısını ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir. Ayrıntılar için bkz. [Virtual Machines Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Dayanıklılık** --Çalışan her sanal makineyi barındıran fiziksel donanımı Azure izler. VM çalıştıran fiziksel sunucu arızalınırsa, Azure bunu fark eder, VM’yi yeni donanıma taşır ve VM’yi yeniden başlatır. Bu işleme bazen hizmet onarma denir. Azure, Blob Storage’da VHD'lerin yedek kopyasını tutarak sanal makinenin verilerini de korur. 

