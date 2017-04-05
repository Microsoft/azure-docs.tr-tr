1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Sol üstten başlayarak sırasıyla **Yeni > İşlem > Windows Server 2016 Datacenter**'a tıklayın.

    ![Portaldaki Azure sanal makine görüntülerine gitme](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Windows Server 2016 Datacenter’da Klasik dağıtım modelini seçin. Oluştur’a tıklayın.

    ![Portalda kullanılabilecek Azure VM görüntülerini gösteren ekran görüntüsü](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Temel Bilgiler dikey penceresi

Temel Bilgiler dikey penceresi, sanal makine için yönetim bilgileri ister.

1. Sanal makine için bir **Ad** girin. Bu örnekte, sanal makinenin adı _HeroVM_’dir. Ad, 1-15 karakter uzunluğunda olmalıdır ve özel karakterler içeremez.

2. Sanal makinede yerel hesap oluşturmak için kullanılan bir **Kullanıcı adı** ve güçlü bir **Parola** girin. Yerel hesap VM’de oturum açmak ve VM’yi yönetmek için kullanılır. Bu örnekte, kullanıcı adı _azureuser_’dır.

 Parola 8-123 karakter uzunluğunda olmalıdır ve en az şu dört karmaşıklık gereksinimini karşılamalıdır: bir küçük harf karakter, bir büyük harf karakter, bir sayı ve bir özel karakter. [Kullanıcı adı ve parola gereksinimleri](../articles/virtual-machines/windows/faq.md) hakkında daha fazla bilgi edinin.

3. **Abonelik** isteğe bağlıdır. Yaygın olarak kullanılan bir ayar "Kullandıkça Öde"dir.

4. Varolan **Kaynak grubunu** seçin veya yenisi için adı yazın. Bu örnekte, kaynak grubunun adı _HeroVMRG_’dir.

5. Sanal makinenin çalışmasını istediğiniz bir Azure veri merkezi **Konumu** seçin. Bu örnekte konum **Doğu ABD**’dir.

6. İşiniz bittiğinde, sonraki dikey pencereye geçmek için **İleri**’ye tıklayın.

    ![Bir Azure VM’yi yapılandırmak için Temel Bilgiler dikey penceresindeki ayarları gösteren ekran görüntüsü](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Boyut dikey penceresi

Boyut dikey penceresi sanal makinenin yapılandırma ayrıntılarını tanımlar ve işletim sistemi, işlemci sayısı, disk depolama türü ve tahmini aylık kullanım maliyeti gibi çeşitli seçenekleri listeler.  

Bir sanal makine boyutu seçin ve devam etmek için **Seç**’e tıklayın. Bu örnekte VM boyutu _DS1_\__V2 Standart_ olarak seçilmiştir.

  ![Seçebileceğiniz Azure VM boyutlarını gösteren Boyut dikey penceresi ekran görüntüsü](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Ayarlar dikey penceresi

Ayarlar dikey penceresi depolama ve ağ seçeneklerini ister. Varsayılan ayarları kabul edebilirsiniz. Azure, gerekli durumlarda uygun girişleri oluşturur.

Bunu destekleyen bir sanal makine boyutu seçtiyseniz, Disk türü altında Premium (SSD) seçeneğini belirleyerek Azure Premium Depolama’yı deneyebilirsiniz.

Değişiklikleriniz bittiğinde **Tamam**’a tıklayın.

## <a name="4-summary-blade"></a>4. Özet dikey penceresi

Özet dikey penceresinde, önceki dikey pencerelerde belirtilen ayarlar listelenir. Görüntüyü oluşturmaya hazır olduğunuzda **Tamam**’a tıklayın.

 ![Sanal makine için belirtilen ayarları gösteren Özet dikey penceresi raporu](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Sanal makine oluşturulduktan sonra portal tarafından **Tüm kaynaklar** altında listelenir ve panoda sanal makine için bir kutucuk görüntülenir. Ayrıca, sanal makineye karşılık gelen bulut hizmeti ve depolama hesabı da oluşturulur ve listelenir. Hem sanal makine hem de bulut hizmeti otomatik olarak başlatılır ve durumları **Çalışıyor** olarak listelenir.

 ![Sanal makinenin VM Aracısı’nı ve uç noktalarını yapılandırma](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
