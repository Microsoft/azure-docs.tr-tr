Bu makalede ölçeklenebilirlik, kullanılabilirlik, yönetilebilirlik ve güvenliğe dikkat edilerek Azure’da Linux sanal makinesi (VM) çalıştırmak için işe yaradığı kanıtlanmış bir dizi uygulama açıklanır. Azure’da CentOS, Debian, Red Hat Enterprise, Ubuntu ve FreeBSD dahil olmak üzere çeşitli popüler Linux dağıtımlarını çalıştırma desteklenir. Daha fazla bilgi edinmek için bkz. [Azure ve Linux][azure-linux].

> [!NOTE]
> Azure’da iki farklı dağıtım modeli vardır: [Resource Manager][resource-manager-overview] ve klasik. Bu makalede, Microsoft’un yeni dağıtımlar için önerdiği Resource Manager modeli kullanılır.
> 
> 

Tek sanal makine kullanıldığında tek bir hata noktası oluştuğundan, görev açısından kritik iş yükleri için tek makine kullanılmasını önermeyiz. Daha yüksek kullanılabilirlik için bir [kullanılabilirlik kümesinde][availability-set] birden çok sanal makine dağıtın. Daha fazla bilgi edinmek için bkz. [Azure’da birden çok sanal makine çalıştırma][multi-vm]. 

## <a name="architecture-diagram"></a>Mimari diyagramı
Azure'da sanal makine sağlama, yalnızca sanal makinenin kendisini taşımaktan çok parçaların taşınmasıyla ilgilidir. Göz önünde bulundurulması gereken bilgi işlem, ağ ve depolama unsurları vardır.

> [Microsoft İndirme Merkezi][visio-download]'nden bu mimari diyagramını içeren bir Visio belgesini indirebilirsiniz. Bu diyagram "Bilgi işlem - tek sanal makine" sayfasındadır.
> 
> 

![[0]][0]

* **Kaynak grubu.** [*Kaynak grubu*][resource-manager-overview], ilgili kaynakları barındıran bir kapsayıcıdır. Bu sanal makinenin kaynaklarını barındıracak bir kaynak grubu oluşturun.
* **Sanal makine**. Yayımlanmış görüntüler listesinden ya da Azure Blob depolamaya yüklediğiniz bir sanal sabit disk (VHD) dosyasından sanal makine sağlayabilirsiniz.
* **İşletim sistemi diski.** İşletim sistemi diski, [Azure Depolama][azure-storage]’da saklanan bir VHD’dir. Bu, konak makinenin çökmesi durumunda bile kalıcı olduğu anlamına gelir. İşletim sistemi diski şudur: `/dev/sda1`.
* **Geçici disk.** Sanal makine geçici bir diskle oluşturulur. Bu disk konak makinedeki fiziksel bir sürücüde depolanır. Azure Depolama’ya *kaydedilmez* ve yeniden başlatma işlemleri ya da diğer sanal makine yaşam döngüsü olayları sırasında silinebilir. Bu diski yalnızca sayfa veya takas dosyaları gibi geçici veriler için kullanın. Geçici disk `/dev/sdb1` olarak adlandırılır ve `/mnt/resource` ya da `/mnt` konumuna bağlanır.
* **Veri diskleri.** [Veri diski][data-disk], uygulama verileri için kullanılan kalıcı bir VHD’dir. Veri diskleri, işletim sistemi diski gibi Azure Depolama'da saklanır.
* **Sanal ağ (VNet) ve alt ağ.** Azure'daki her sanal makine, kendi içinde alt ağlara ayrılan bir sanal ağa dağıtılır.
* **Genel IP adresleri.** Sanal makine ile iletişim kurmak (örneğin, SSH üzerinden) için bir genel IP adresi gerekir.
* **Ağ arabirimi (NIC)**. NIC, sanal makinenin sanal ağla iletişim kurmasına imkan tanır.
* **Ağ güvenlik grubu (NSG)**. [NSG][nsg], alt ağa yönelik ağ trafiğine izin vermek veya trafiği reddetmek için kullanılır. NSG’yi tek başına bir NIC ya da alt ağ ile ilişkilendirebilirsiniz. Bir alt ağla ilişkilendirmeniz durumunda NSG kuralları alt ağdaki tüm sanal makinelere uygulanır.
* **Tanılama.** Sanal makinenin yönetilmesi ve sorunlarının giderilmesi için tanılama günlük kaydı çok önemlidir.

## <a name="recommendations"></a>Öneriler

Aşağıdaki öneriler çoğu senaryo için geçerlidir. Bu önerileri geçersiz kılan belirli bir gereksiniminiz olmadığı sürece izlemeniz önerilir. 

### <a name="vm-recommendations"></a>Sanal makine önerileri

Azure tarafından birçok farklı sanal makine boyutu sunulur, ancak [Premium Depolama][premium-storage]’yı desteklediği için DS ve GS serisi sanal makineleri öneririz. Yüksek performanslı bilgi işlem gibi özel bir iş yükünüz yoksa bu sanal makine boyutlarından birini seçin. Ayrıntılar için bkz. [sanal makine boyutları][virtual-machine-sizes].

Mevcut bir iş yükünü Azure’a taşıyorsanız şirket içi sunucularınıza en yakın olan sanal makine boyutunu seçerek başlayın. Daha sonra gerçek iş yükünüzün performansını CPU, bellek ve saniye başına disk giriş/çıkış işlemleri (IOPS) açısından ölçerek gerek duymanız durumunda boyutu değiştirin. Sanal makineniz için birden çok NIC gerekiyorsa, en fazla NIC sayısının [sanal makine boyutu][vm-size-tables] ile ilişkili olduğunu unutmayın.

Sanal makineyi ve diğer kaynakları sağlarken bir bölge belirtmeniz gerekir. Genel bir kural olarak dahili kullanıcılarınıza veya müşterilerinize en yakın bölgelerden birini seçin. Ancak, her bölgede tüm sanal makine boyutları kullanılamıyor olabilir. Ayrıntılar için bkz. [Bölgelere göre hizmetler][services-by-region]. Belirli bir bölgede kullanılabilen sanal makine boyutlarını listelemek için aşağıdaki Azure komut satırı arabirimi (CLI) komutunu çalıştırın:

```
azure vm sizes --location <location>
```

Yayımlanmış sanal makine görüntülerinden birini seçme hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile Linux sanal makine görüntüleri seçme][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Disk ve depolama önerileri

En iyi disk G/Ç performansı için verileri katı hal sürücülerinde (SSD) depolayan [Premium Depolama][premium-storage]’yı öneririz. Maliyet, sağlanan disk boyutuna bağlıdır. IOPS ve aktarım hızı (diğer bir deyişle, veri aktarım hızı) da disk boyutuna bağlı olduğundan, disk sağlarken üç faktörün tamamını (kapasite, IOPS ve aktarım hızı) göz önünde bulundurun. 

Depolama hesaplarının IOPS limitlerine ulaşmaktan kaçınmak amacıyla, sanal sabit diskleri (VHD) olacak her sanal makine için ayrı ayrı Azure depolama hesapları oluşturun. 

Bir veya daha fazla veri diski ekleyin. Bir VHD oluşturduğunuzda, disk biçimlendirilmemiş olur. Diski biçimlendirmek için sanal makinede oturum açın. Linux kabuğunda veri diskleri `/dev/sdc`, `/dev/sdd` vb. şeklinde görüntülenir. Diskler dahil olmak üzre blok cihazları listelemek için `lsblk` komutunu çalıştırabilirsiniz. Veri diski kullanmak için bir bölüm ve dosya sistemi oluşturun ve diski bağlayın. Örneğin:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Çok sayıda veri diskiniz varsa depolama hesabının G/Ç limitlerine dikkat edin. Daha fazla bilgi edinmek için bkz. [sanal makine disk limitleri][vm-disk-limits].

Bir veri diski eklediğinizde, diske bir mantıksal birim numarası (LUN) kimliği atanır. İsteğe bağlı olarak, (örneğin, bir diski değiştiriyor ve aynı LUN kimliğini korumak istiyorsanız ya da belirli bir LUN kimliğini arayan bir uygulamanız varsa) LUN kimliğini belirtebilirsiniz.&mdash; Bununla birlikte, her diskin LUN kimliğinin benzersiz olması gerektiğini unutmayın.

Premium depolama hesaplarına sahip sanal makinelere yönelik diskler SSD olduğundan, SSD’lerde performansı iyileştirmek için G/Ç zamanlayıcısını değiştirmeniz önerilir. Genel olarak SSD’ler için NOOP zamanlayıcısının kullanılması önerilse de [iostat] gibi bir araçla kendi iş yükünüzün disk G/Ç performansını izlemeniz gerekir.

En iyi performansı elde etmek için tanılama günlüklerini barındıracak ayrı bir depolama hesabı oluşturun. Tanılama günlükleri için standart bir yerel olarak yedekli depolama (LRS) yeterlidir.

### <a name="network-recommendations"></a>Ağ önerileri

Genel IP adresi dinamik veya statik olabilir. Varsayılan seçenek dinamiktir.

* Değişmeyen bir IP adresine ihtiyacınız varsa (örneğin, DNS’de bir A kaydı oluşturmanız ya da IP adresinin güvenli bir listeye eklenmesi gerekiyorsa) [statik IP adresi][static-ip] ayırın.&mdash;
* IP adresi için bir tam etki alanı adı da (FQDN) oluşturabilirsiniz. Daha sonra DNS’te FQDN’yi gösteren bir [CNAME kaydı][cname-record] oluşturabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure portalında tam etki alanı adı oluşturma][fqdn].

Tüm NSG’ler, tüm gelen İnternet trafiğini engelleyen bir kuralın da dahil olduğu bir [varsayılan kurallar][nsg-default-rules] kümesi içerir. Varsayılan kurallar silinemez, ancak diğer kurallar tarafından geçersiz kılınabilir. İnternet trafiğini etkinleştirmek için belirli bağlantı noktalarına (örneğin, HTTP için 80) gelen trafiğe izin veren kurallar oluşturun.&mdash;  

SSH’yi etkinleştirmek için NSG’ye TCP bağlantı noktası 22’ye gelen trafiğe izin veren bir kural ekleyin.

## <a name="scalability-considerations"></a>Ölçeklenebilirlik konusunda dikkat edilmesi gerekenler

Ölçeği artırmak veya azaltmak için [VM boyutunu değiştirin][vm-resize]. 

Ölçeği yatay olarak genişletmek için bir yük dengeleyici arkasındaki kullanılabilirlik kümesine iki veya daha fazla sanal makine ekleyin. Ayrıntılar için bkz. [Azure’da birden çok sanal makine çalıştırma][multi-vm].

## <a name="availability-considerations"></a>Kullanılabilirlik konusunda dikkat edilmesi gerekenler

Daha yüksek kullanılabilirlik için bir kullanılabilirlik kümesinde birden çok sanal makine dağıtın. Bunun için sağlanan [hizmet düzeyi sözleşmesi][vm-sla] (SLA) de daha yüksektir. 

Sanal makineniz [planlı bakım][planned-maintenance] veya [plansız bakımdan][manage-vm-availability] etkilenebilir. [Sanal makine yeniden başlatma günlükleri][reboot-logs] ile bir sanal makine yeniden başlatma işleminin planlı bakımdan kaynaklanıp kaynaklanmadığını belirleyebilirsiniz.

VHD’ler [Azure depolama alanında][azure-storage] saklanır ve Azure depolama alanı dayanıklılık ve kullanılabilirlik sağlamak amacıyla çoğaltılır.

Normal işlemler sırasında yanlışlıkla veri kaybı gerçekleşmesini (örneğin, kullanıcı hatası nedeniyle) engellemek için [blob anlık görüntülerini][blob-snapshot] ya da başka bir aracı kullanarak belirli noktaya yedeklemeler de uygulamanız gerekir.

## <a name="manageability-considerations"></a>Yönetilebilirlik konusunda dikkat edilmesi gerekenler

**Kaynak grupları.** Aynı yaşam döngüsünü paylaşan sıkı şekilde bağlı kaynakları aynı [kaynak grubuna][resource-manager-overview] ekleyin. Kaynak grupları, kaynakları bir grup halinde dağıtıp izlemenize ve faturalandırma maliyetlerini kaynak grubuna göre toplamanıza olanak tanır. Kaynakları bir küme halinde silme imkanınız da olur ve test dağıtımları için bu özellik çok kullanışlıdır. Kaynaklara anlamlı adlar verin. Bu sayede belirli bir kaynağı bulmak ve kaynağın rolünü anlamak daha kolay olur. Bkz. [Azure Kaynakları için Önerilen Adlandırma Kuralları][naming conventions].

**SSH**. Bir Linux sanal makinesi oluşturmadan önce 2048 bit RSA ortak-özel anahtar çifti oluşturun. Sanal makineyi oluştururken ortak anahtar dosyasını kullanın. Daha fazla bilgi edinmek için bkz. [Azure’da Linux ve Mac ile SSH Kullanma][ssh-linux].

**Sanal makine tanılama.** Temel sistem durumu ölçümleri, tanılama altyapısı günlükleri ve [önyükleme tanılaması][boot-diagnostics] gibi izleme ve tanılama özelliklerini etkinleştirin. Önyükleme tanılaması, sanal makinenizin önyükleme gerçekleştiremeyecek bir duruma girmesi durumunda önyükleme hatasını tanılamanıza yardımcı olabilir. Daha fazla bilgi edinmek için bkz. [İzleme ve tanılamayı etkinleştirme][enable-monitoring].  

Aşağıdaki CLI komutları tanılamayı etkinleştirir:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Sanal makineyi durdurma.** Azure’da “durduruldu” ile “serbest bırakıldı” durumları birbirinden farklıdır. Sanal makine durdurulmuş durumdayken ücret ödersiniz, ancak serbest bırakılmış durumdayken ödemezsiniz.

Sanal makineyi serbest bırakmak için aşağıdaki CLI komutunu kullanın:

```
azure vm deallocate <resource-group> <vm-name>
```

Azure portalındaki **Durdur** düğmesi sanal makineyi serbest bırakır. Bununla birlikte, sanal makineyi oturumunuz açıkken işletim sistemi aracılığıyla kapatırsanız sanal makine durdurulmasına rağmen *serbest bırakılmaz* ve bu nedenle ücretlendirilmeye devam edersiniz.

**Sanal makineyi silme.** Bir sanal makineyi silerseniz VHD’ler silinmez. Bu, sanal makineyi verileri kaybetmeden güvenli bir şekilde silebileceğiniz anlamına gelir. Ancak, depolama ücretlendirilmeye devam edersiniz. VHD’yi silmek için dosyayı [Blob depolamadan][blob-storage] silin.

VHD’nin yanlışlıkla silinmesini engellemek için bir [kaynak kilidi][resource-lock] ile kaynak grubunun tamamını ya da tek tek kaynakları (sanal makine gibi) kilitleyin. 

## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

[OSPatching] sanal makine uzantısını kullanarak işletim sistemi güncelleştirmelerini otomatikleştirin. Sanal makineyi sağlarken bu uzantıyı yükleyin. Düzeltme eklerinin ne sıklıkta yükleneceğini ve düzeltme eki uygulandıktan sonra yeniden başlatılıp başlatılmayacağını belirtebilirsiniz.

Dağıttığınız Azure kaynaklarına erişimi denetlemek için [rol tabanlı erişim denetimi][rbac] (RBAC) kullanın. RBAC, DevOps takımınızın üyelerine yetkilendirme rolleri atamanıza imkan tanır. Örneğin, Okuyucu rolü Azure kaynaklarını görüntüleyebilir ancak oluşturamaz, yönetemez ve silemez. Bazı roller belirli Azure kaynak türlerine özeldir. Örneğin, Sanal Makine Katılımcısı rolü bir sanal makineyi yeniden başlatıp serbest bırakabilir, yönetici parolasını sıfırlayabilir, sanal makine oluşturabilir, vs. Bu başvuru mimarisi için kullanışlı olabilecek diğer [yerleşik RBAC rolleri][rbac-roles] [DevTest Labs Kullanıcısı][rbac-devtest] ve [Ağ Katılımcısı][rbac-network]’dır. 

Bir kullanıcıya birden çok rol atanabilir ve daha da ayrıntılı izinler verebilmek için özel roller oluşturabilirsiniz.

d> [!NOTE]
> RBAC, bir sanal makinede oturum açmış kullanıcıların gerçekleştirebileceği eylemleri kısıtlamaz. Bu izinler konuk işletim sistemindeki hesap türüne göre belirlenir.   
> 
> 

Sağlama eylemlerini ve diğer sanal makine olaylarını görmek için [denetim günlüklerini][audit-logs] kullanın.

İşletim sistemini ve veri disklerini şifrelemeniz gerekiyorsa [Azure Disk Şifrelemesi][disk-encryption]’ni öneririz. 

## <a name="solution-deployment"></a>Çözüm dağıtımı
[GitHub][github-folder]’dan bu başvuru mimarisine yönelik bir dağıtım edinebilirsiniz. Bir sanal ağ, NSG ve tek bir sanal makine içerir. Mimariyi dağıtmak için aşağıdaki adımları izleyin: 

1. Aşağıdaki düğmeye sağ tıklayın ve "Bağlantıyı yeni sekmede aç" ya da "Bağlantıyı yeni pencerede aç"ı seçin.
   [![Azure’a dağıtma](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Bağlantı Azure portalında açıldığında bazı ayarlar için değerler girmeniz gerekir: 
   
   * **Kaynak grubu** adı dosyada zaten tanımlanmış olduğundan, **Yeni Oluştur**’u seçip metin kutusuna `ra-single-vm-rg` yazın.
   * **Konum** açılan kutusundan bölgeyi seçin.
   * **Şablon Kök Uri’si** veya **Parametre Kök Uri’si** metin kutularını düzenlemeyin.
.   * **İşletim Sistemi Türü** açılan kutusundan **linux**’u seçin.
   * Hüküm ve koşulları gözden geçirin ve ardından **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusuna tıklayın.
   * **Satın al** düğmesine tıklayın.
3. Dağıtımın tamamlanmasını bekleyin.
4. Parametre dosyaları sabit olarak kodlanmış bir yönetici kullanıcı adı ve parolası içerir. İkisini de hemen değiştirmeniz önemle tavsiye edilir. Azure portalında `ra-single-vm0 ` adlı sanal makineye tıklayın. Daha sonra **Destek ve sorun giderme** bölümünden **Parolayı sıfırla**’ya tıklayın. **Mod** açılan kutusundan **Parolayı sıfırla**’yı seçin ve sonra bir **Kullanıcı adı** ve **Parola** belirleyin. Yeni kullanıcı adı ve parolayı kalıcı hale getirmek için **Güncelleştir** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
Daha yüksek kullanılabilirlik elde etmek için bir yük dengeleyicisinin arkasında iki veya daha fazla sanal makine dağıtın. Daha fazla bilgi edinmek için bkz. [Azure’da birden çok sanal makine çalıştırma][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure'da tek Linux sanal makineli mimari"

