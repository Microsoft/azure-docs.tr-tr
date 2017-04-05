

İşlem kaynaklarının ölçeğini artırmaya veya azaltmaya gerek duyan uygulamalar için ölçeklendirme işlemleri, arıza ve güncelleştirme etki alanlarında örtülü olarak dengelenir. VM ölçek kümelerine giriş için son [Azure blog duyurularına](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) bakın.

VM ölçek kümeleri hakkında daha fazla bilgi için şu videolara göz atın:

* [Mark Russinovich, Azure Ölçek Kümeleri hakkında konuşuyor](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Guy Bowerman ile Sanal Makine Ölçek Kümeleri](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>VM ölçek kümeleri oluşturma ve yönetme
Sanal makine ölçek kümeleri, tıpkı tek Azure Resource Manager VM’lerinde olduğu gibi JSON şablonları ve [REST API’leri](https://msdn.microsoft.com/library/mt589023.aspx) kullanılarak tanımlanıp dağıtılabilir. Bu nedenle, tüm standart Azure Resource Manager dağıtım yöntemleri kullanılabilir. Şablonlar hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../articles/resource-group-authoring-templates.md).

VM ölçek kümelerine ilişkin bir dizi örnek, buradaki Azure Hızlı Başlangıç şablonları GitHub deposunda bulunabilir:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - başlığında *vmss* olan şablonları arayın.

Bu şablonların ayrıntı sayfalarında, portal dağıtım özelliğine bağlanan bir düğme görürsünüz. VM ölçek kümesini dağıtmak için düğmeye tıklayın ve ardından portalda gerekli olan tüm parametreleri doldurun. Bir kaynağın büyük harfleri veya büyük/küçük harflerin karışımını desteklediğinden emin değilseniz, her zaman küçük harfli parametrelerin kullanılması daha güvenlidir. Burada ayrıca bir VM ölçek kümesi şablonunun kullanışlı bir video açıklaması verilmiştir:

[VM Ölçek Kümesi Şablon Açıklaması](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>VM ölçeğini artırma veya azaltma
Bir VM ölçek kümesindeki sanal makinelerin sayısını artırmak ya da azaltmak için, *capacity* özelliğini değiştirmeniz ve şablonu yeniden dağıtmanız yeterlidir. Bu kolaylık, Azure otomatik ölçeklendirme tarafından desteklenmeyen özel ölçek olayları tanımlamak isterseniz kendi özel ölçeklendirme katmanınızı yazmayı kolay hale getirir.

Kapasiteyi değiştirmek için bir şablonu yeniden dağıtıyorsanız, yalnızca SKU ve güncelleştirilmiş kapasiteyi içeren çok daha küçük bir şablon tanımlayabilirsiniz. Bunun bir örneği şurada gösterilmiştir: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Otomatik olarak ölçeklendirilen bir ölçek kümesi oluşturan adımları uygulamak için bkz. [Sanal Makine Ölçek Kümesinde Makineleri Otomatik Olarak Ölçeklendirme](../articles/virtual-machines/windows/vmss-powershell-creating.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="monitoring-your-vm-scale-set"></a>VM ölçek kümenizi izleme
Şu anda VM ölçek kümelerini görüntülemek için [Azure Resource Explorer](https://resources.azure.com)’ı kullanmanız önerilir. VM ölçek kümeleri Microsoft.Compute altındaki bir kaynaktır; bu nedenle, bu siteden aşağıdaki bağlantıları genişleterek bunları görebilirsiniz:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>VM ölçek kümesi senaryoları
Bu bölümde tipik VM ölçek kümesi senaryolarından bazıları listelenmektedir. Daha yüksek düzeydeki bazı Azure hizmetleri (Batch, Service Fabric, Azure Container Service gibi) bu senaryoları kullanır.

* **RDP / SSH’den VM ölçek kümesi örneklerine** - VM ölçek kümesi bir sanal ağ içinde oluşturulur ve içindeki VM’lere genel IP adresleri atanmaz. Genellikle işlem kılavuzunuzda durum bilgisi olmayan tüm kaynaklara genel IP adresleri ayırmanın harcama ve yönetim yüklerini istemediğinizden ve bu VM’lere, sanal ağınız içindeki yük dengeleyiciler veya tek başına sanal makineler gibi genel IP adreslerine sahip olan diğer kaynaklardan kolayca bağlanabileceğinizden, bunun yapılması yararlıdır.
* **NAT kurallarını kullanarak VM’lere bağlanma** - Bir genel IP adresi oluşturabilir, bunu bir yük dengeleyiciye bağlayabilir ve IP adresi üzerindeki bir bağlantı noktasını VM ölçek kümesindeki bir VM’nin bağlantı noktasına eşleyen gelen NAT kuralları tanımlayabilirsiniz. Örneğin
  
   Genel IP->Bağlantı Noktası 50000 -> vmss\_0->Bağlantı Noktası 22  Genel IP->Bağlantı Noktası 50001 -> vmss\_1->Bağlantı Noktası 22  Genel IP->Bağlantı Noktası 50002-> vmss\_2->Bağlantı Noktası 22
  
   Tek bir genel IP kullanarak bir ölçek kümesindeki her VM ile SSH bağlantısını etkinleştirmek üzere NAT kuralları kullanan bir VM ölçeği oluşturma örneği burada verilmiştir: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   RDP ve Windows ile aynı işlemi yapmanın örneği ise burada verilmiştir: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **"Sıçrama kutusu" kullanarak VM’lere bağlanma** - Aynı sanal ağ için bir VM ölçeği ve tek başına VM oluşturursanız, tek başına VM ve VM ölçek kümesinin VM’leri, Sanal Ağ/Alt Ağ tarafından tanımlanan iç IP adreslerini kullanarak birbirine bağlanabilir. Genel bir IP adresi oluşturup tek başına VM’ye atarsanız, tek başına VM’ye RDP veya SSH uygulayabilir ve ardından bu makineden VM ölçek kümesi örneklerine bağlanabilirsiniz. Bu noktada, basit VM ölçek kümesinin, varsayılan yapılandırmasında genel IP adresine sahip basit bir tek başına VM’den doğal olarak daha güvenli olduğunu fark edebilirsiniz.
  
   Bu yaklaşımın bir örneği olarak bu şablon, basit bir Mesos kümesi oluşturur. Bu küme, sanal makine ölçek kümesi tabanlı VM kümesi yöneten bir tek başına Ana VM’den oluşur: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **VM ölçek kümesi örneklerine yük devretmek için hepsini bir kez deneme seçeneğini kullanma** - "Hepsini bir kez deneme" yaklaşımı kullanarak bir VM işlem kümesine çalışma iletmek istiyorsanız, bir Azure yük dengeleyiciyi uygun yük dengeleme kurallarıyla yapılandırabilirsiniz. Belirtilen bir protokol, aralık ve istek yolu ile bağlantı noktalarına ping göndererek, uygulamanızın çalıştığını doğrulamak için araştırmalar da tanımlayabilirsiniz.
  
   IIS web sunucusu çalıştıran bir VM ölçek kümesinin oluşturulduğu ve her VM’nin aldığı yükü dengelemek üzere bir yük dengeleyicinin kullanıldığı örnek aşağıda verilmiştir. Ayrıca, her VM üzerindeki belirli bir URL’ye ping göndermek için HTTP protokolü kullanılır: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - Microsoft.Network/loadBalancers kaynak türüne ve virtualMachineScaleSet içindeki networkProfile ile extensionProfile öğesine bakın.
* **Bir VM ölçek kümesini PaaS küme yöneticisi içinde işlem kümesi olarak dağıtma** - VM ölçek kümeleri bazı durumlarda yeni nesil çalışan rolü olarak tanımlanır. Bu geçerli bir açıklamadır, ancak aynı zamanda PaaS v1 Çalışan rolü özellikleriyle ölçek kümesi özelliklerini karıştırma riskini taşır. VM ölçek kümeleri, platformdan/çalışma zamanından bağımsız, özelleştirilebilir ve Azure Resource Manager IaaS hizmeti ile tümleştirilen genelleştirilmiş bir işlem kaynağı sağlamaları bakımından gerçek bir "çalışan rolü" veya çalışan kaynağı sağlar.
  
   PaaS v1 çalışan rolü, platform/çalışma zamanı desteği bakımından sınırlı olmasına karşın (yalnızca Windows platform görüntüleri), ayrıca VIP takas gibi hizmetler, yapılandırılabilir yükseltme ayarları, VM ölçek kümelerinde *henüz* kullanılamayan veya Service Fabric gibi diğer yüksek PaaS hizmetleri tarafından sunulacak olan çalışma zamanı/uygulama dağıtımına özgü ayarlar içerir. Bunu aklınızda bulundurarak, VM ölçek kümelerine PaaS destekleyen bir altyapı olarak bakabilirsiniz. Ör. Service Fabric gibi PaaS çözümleri veya Mesos gibi küme yöneticileri, VM ölçek kümelerinin üzerine ölçeklenebilir bir işlem katmanı olarak oluşturulabilir.
  
   Tek başına VM ile aynı sanal ağ içindeki bir VM Ölçek Kümesine dağıtım yapan Mesos kümesinin örneği aşağıda verilmiştir. Tek başına, VM bir Mesos ana öğesidir. VM ölçek kümesi ise bağımlı düğümlerden oluşan bir kümeyi temsil eder: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/)’in gelecekteki sürümleri, bu senaryonun VM ölçek kümelerini temel alan daha karmaşık/zorlaştırılmış sürümlerini dağıtacaktır.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM ölçek kümesi performansı ve ölçek Kılavuzu
* Genel önizleme sırasında, birden fazla VM Ölçek Kümesinde aynı anda en fazla 500 VM oluşturabilirsiniz.
* Her depolama hesabı için en fazla 40 VM planlayın.
* Depolama hesabı adlarının ilk harflerini mümkün olduğunca yaymaya dikkat edin.  [Azure Hızlı Başlangıç şablonlarındaki](https://github.com/Azure/azure-quickstart-templates/) VMSS şablonları bunun nasıl yapılacağına ilişkin örnekler vermektedir.
* Özel VM’ler kullanıyorsanız bir VM ölçek kümesi için tek bir depolama hesabında en fazla 40 VM planlayabilirsiniz.  VM ölçek kümesi dağıtımına başlayabilmeniz için görüntüyü depolama hesabına önceden kopyalamanız gerekir. Daha fazla bilgi için SSS bölümüne bakın.
* Sanal ağ başına en fazla 2048 VM planlayabilirsiniz.  Bu sınır gelecekte yükseltilecektir.
* Oluşturabileceğiniz VM sayısı, herhangi bir bölgedeki İşlem çekirdek kotanızla sınırlıdır. Şu anda bulut hizmetleri veya IaaS v1 ile kullanmak üzere yüksek çekirdek sınırına sahip olsanız bile, İşlem kota sınırınızı yükseltmek için Müşteri Desteği ile iletişim kurmanız gerekebilir. Kotanızı sorgulamak için şu Azure CLI komutunu çalıştırabilirsiniz: *azure vm list-usage* ve şu PowerShell komutu: *Get-AzureRmVMUsage* (PowerShell 1.0’dan önceki bir sürümü kullanıyorsanız *Get-AzureVMUsage* kullanın).

## <a name="vm-scale-set-frequently-asked-questions"></a>VM ölçek kümeleri hakkında sık sorulan sorular
**S.** Bir VM ölçek kümesinde kaç tane VM’niz olabilir?

**C.** birden fazla depolama hesabı arasında dağıtılabilir platform görüntüleri kullanıyorsanız, 100. Özel görüntüler önizleme sırasında tek bir depolama hesabı ile sınırlı olduğu için, özel görüntüler kullanıyorsanız bu sayı en fazla 40’tır.

**S** VM ölçek kümeleri için başka hangi kaynak limitleri mevcuttur?

**C.** Önizleme süresi boyunca, her bölge için birden fazla ölçek kümesinde en fazla 500 VM oluşturabilirsiniz. Var olan [Azure Abonelik Hizmeti Limitleri/](../articles/azure-subscription-service-limits.md) geçerlidir.

**S.** VM ölçek kümelerinde Veri Diskleri destekleniyor mu?

**C.** İlk sürümde desteklenmez. Veri depolama seçenekleriniz şunlardır:

* Azure dosyaları (paylaşılan SMB sürücüleri)
* İşletim sistemi sürücüsü
* Geçici sürücü (yerel, Azure depolama tarafından yedeklenmez)
* Dış veri hizmeti (örn. uzak veritabanı, Azure tabloları, Azure blobları)

**S.** Hangi Azure bölgeleri VM ölçek kümelerini destekler?

**C.** Azure Resource Manager’ı destekleyen tüm bölgeler VM Ölçek Kümelerini de destekler.

**S.** Özel bir görüntü kullanarak nasıl VM ölçek kümesi oluşturulur?

**C.** vhdContainers özelliğini boş bırakın, örneğin:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**S.** VM ölçek kümemin kapasitesini 20’den 15’e düşürürsem hangi VM’ler kaldırılacak?

**C.** Sanal makineler, ölçek kümesinden yükseltme etki alanları ve hata etki alanları arasında eşit olacak şekilde kaldırılır.

**S.** O halde kapasiteyi 15’ten 18’e yükseltirsem ne olur?

**C.** Sayıyı 18’e çıkarırsanız dizini 15, 16, 17 olan VM’ler oluşturulur. Her iki durumda da VM’ler, FD’ler ile UD’ler arasında dengelenir.

**S.** Bir VM ölçek kümesinde birden fazla uzantı kullanırken bir yürütme sırası uygulamayı zorunlu kılabilir miyim?

**C.** Doğrudan olmasa da, customScript uzantısı için betiğiniz başka bir uzantının tamamlanmasını bekleyebilir (örneğin, uzantı günlüğünü izleyerek - bkz. [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**S.** VM ölçek kümeleri Azure kullanılabilirlik kümeleri ile birlikte çalışır mı?

**C.** Evet. Bir VM ölçek kümesi, 3 FD ve 5 UD’ye sahip örtülü bir kullanılabilirlik kümesidir. VirtualMachineProfile altında herhangi bir yapılandırma yapılması gerekmez. Gelecekteki sürümlerde, VM ölçek kümeleri birden fazla kiracıya yayılabilir, ancak şimdilik bir ölçek kümesi tek bir kullanılabilirlik kümesidir.

