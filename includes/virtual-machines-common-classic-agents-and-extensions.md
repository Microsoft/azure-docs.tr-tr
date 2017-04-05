

VM uzantıları aşağıdakilerde size yardımcı olur:

* Hesap değerlerini sıfırlama ve kötü amaçlı yazılımdan koruma yazılımı kullanma gibi güvenlik ve kimlik özelliklerini değiştirmek
* İzleme ve tanılama başlatmak, durdurmak veya yapılandırmak
* RDP ve SSH gibi bağlantı özelliklerini sıfırlamak veya yüklemek
* VM’lerinizi tanılamak, izlemek ve yönetmek

Başka birçok özellik de vardır. Yeni VM Uzantısı özellikleri düzenli olarak kullanıma sunulur. Bu makalede, Windows ve Linux için Azure Sanal Makine Aracıları ve bunların VM Uzantısı işlevselliğini nasıl destekledikleri açıklanmaktadır. Özellik kategorisine göre VM Uzantılarının bir listesi için, bkz. [Azure VM Uzantıları ve Özellikleri](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows ve Linux için Azure VM Aracıları
Azure Aracısı (VM Aracısı), Azure Sanal Makine örneklerinde VM uzantılarını yükleyen, yapılandıran ve kaldıran, güvenlikli ve hafif bir işlemdir. VM Aracısı, Azure VM’niz için güvenli yerel denetim hizmeti işlevi görür. Aracının yüklediği uzantılar, örneğini kullanarak verimliliğinizi artırmak için belirli özellikler sağlar.

Biri Windows VM’leri için ve biri Linux VM’leri için olmak üzere iki adet Azure VM Aracısı vardır.

Bir sanal makine örneğinin bir veya daha fazla VM uzantısı kullanmasını istiyorsanız, örnekte yüklü bir VM Aracısı her zaman olmalıdır. Azure portalı kullanılarak oluşturulan bir sanal makine görüntüsü ve **Marketten** bir görüntü, oluşturma işleminde bir VM Aracısını otomatik olarak yükler. Bir sanal makine örneğinde bir VM Aracısı yoksa, sanal makine örneği oluşturulduktan sonra VM Aracısı'nı yükleyebilirsiniz. Veya aracıyı özel bir VM görüntüsüne yükleyip ardından bu görüntüyü karşıya yükleyebilirsiniz.

> [!IMPORTANT]
> Bu VM Aracıları, sanal makine örneklerinin güvenlikli yönetimini sağlayan çok hafif hizmetlerdir. VM Aracısı istemediğiniz durumlar da olabilir. Böyle bir durumda, Azure CLI veya PowerShell kullanarak, VM Aracısı yüklü olmayan VM’ler oluşturduğunuzdan emin olun. VM Aracısı fiziksel olarak kaldırılabilir olsa da, örnekteki VM Uzantılarının davranışı tanımlanmamıştır. Sonuç olarak, yüklü bir VM Aracısının kaldırılması desteklenmez.
>

VM Aracısı aşağıdaki durumlarda etkinleştirilir:

* Azure portalını kullanarak ve **Marketten** bir görüntü seçerek bir VM örneği oluşturduğunuzda,
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) veya [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) cmdlet'ini kullanarak bir VM örneği oluşturduğunuzda. **– DisableGuestAgent** parametresini [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) cmdlet'ine ekleyerek, VM Aracısı olmayan bir VM oluşturabilirsiniz,

* VM Aracısını mevcut bir VM örneğine el ile indirip yüklediğinizde ve **ProvisionGuestAgent** değerini **true** şeklinde ayarladığınızda. Bir PowerShell komutu veya bir REST çağrısı kullanarak Windows ve Linux aracıları için bu tekniği kullanabilirsiniz. (VM Aracısını el ile yükledikten sonra **ProvisionGuestAgent** değerini ayarlamazsanız, VM Aracısının eklenmesi düzgün şekilde algılanmaz.) Aşağıdaki kod örneğinde, bunun, `$svc` ve `$name` bağımsız değişkenlerinin belirlenmiş olduğu PowerShell kullanılarak nasıl yapılacağı gösterilmektedir:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Yüklü bir VM Aracısını içeren bir VM görüntüsü oluşturduğunuzda. VM Aracısıyla görüntü mevcut olduğunda, o görüntüyü Azure’a yükleyebilirsiniz. Bir Windows VM için, [Windows VM Agent .msi dosyasını](http://go.microsoft.com/fwlink/?LinkID=394789) indirin ve VM Aracısını yükleyin. Bir Linux VM için, <https://github.com/Azure/WALinuxAgent> adresinde bulunan GitHub deposundan VM Aracısını yükleyin. Linux’ta VM Aracısını yükleme hakkında daha fazla bilgi için, bkz: [Azure Linux VM Aracısı Kullanıcı Kılavuzu](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> PaaS’ta, VM Aracısına **WindowsAzureGuestAgent** adı verilir ve her zaman Web’de ve Çalışan Rolü VM’lerinde kullanılabilir. (Daha fazla bilgi için bkz. [Azure Rol Mimarisi](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) Rol VM’leri için VM Aracısı şimdi uzantıları bulut hizmeti VM’lerine, kalıcı Sanal Makineler için yapacağı gibi ekleyebilir. Rol VM’lerindeki ve kalıcı VM’lerdeki VM Uzantıları arasındaki en büyük fark, VM uzantılarının eklendiği zamandır. Rol VM’leriyle, uzantılar önce bulut hizmetine, ardından o bulut hizmeti içerisindeki dağıtımlara eklenir.
>
> [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet'ini, tüm kullanılabilir rol VM uzantılarını listelemek için kullanın.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>VM Uzantıları Bulma, Ekleme, Güncelleştirme ve Kaldırma
Bu görevler hakkında daha fazla bilgi için, bkz: [Azure VM Uzantıları Ekleme, Bulma, Güncelleştirme ve Kaldırma](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
