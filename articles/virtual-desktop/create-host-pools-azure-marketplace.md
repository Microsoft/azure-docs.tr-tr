---
title: Windows sanal masaüstü konak havuzu Azure portal-Azure
description: Azure portal kullanarak Windows sanal masaüstü konak havuzu oluşturma.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1003ab20f839469235d6bd7949ec3b2df4dd4f25
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447990"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Öğretici: Azure portal bir konak havuzu oluşturma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)bakın. Windows sanal masaüstü (klasik) ile oluşturduğunuz tüm nesneler Azure portal ile yönetilemez.

Konak havuzları, Windows sanal masaüstü ortamlarında bir veya daha fazla özdeş sanal makine (VM) koleksiyonudur. Her konak havuzu, kullanıcıların fiziksel bir masaüstünde yaptıkları gibi etkileşime girebilecekleri bir uygulama grubu içerebilir.

Bu makale, Azure portal aracılığıyla bir Windows sanal masaüstü ortamı için bir konak havuzu oluşturmaya yönelik kurulum sürecinde size yol gösterecektir. Bu yöntem, Windows sanal masaüstünde bir konak havuzu oluşturmak, bir Azure aboneliğinde VM 'Ler içeren bir kaynak grubu oluşturmak, bu VM 'Leri Azure Active Directory (AD) etki alanına eklemek ve VM 'Leri Windows sanal masaüstü ile kaydettirmek için tarayıcı tabanlı bir kullanıcı arabirimi sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bir konak havuzu oluşturmak için aşağıdaki parametreleri girmeniz gerekir:

- VM görüntü adı
- VM yapılandırması
- Etki alanı ve ağ özellikleri
- Windows sanal masaüstü konak Havuzu Özellikleri

Ayrıca aşağıdaki işlemleri de bilmeniz gerekir:

- Kullanmak istediğiniz görüntünün kaynağı. Azure galerisinden mi veya özel bir görüntü mi?
- Etki alanınıza katılarak kimlik bilgileri.

Ayrıca, Microsoft. DesktopVirtualization kaynak sağlayıcısını kaydettiğinizden emin olun. Henüz yapmadıysanız, **abonelikler**' e gidin, aboneliğinizin adını seçin ve ardından **kaynak sağlayıcıları**' nı seçin. DesktopVirtualization için arama yapın, Microsoft. DesktopVirtualization ' ı seçin ve ardından Kaydet ' i seçin.

Azure Resource Manager şablonuyla bir Windows sanal masaüstü konak havuzu oluşturduğunuzda, Azure Galerisi 'nden, yönetilen bir görüntüden veya yönetilmeyen görüntüden bir sanal makine oluşturabilirsiniz. VM görüntülerini oluşturma hakkında daha fazla bilgi edinmek için bkz. Azure ['a yüklemek için bir WINDOWS VHD veya vhdx hazırlama](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ve [Azure 'DA genelleştirilmiş bir VM 'Nin yönetilen görüntüsünü oluşturma](../virtual-machines/windows/capture-image-resource.md).

Henüz bir Azure aboneliğiniz yoksa, bu yönergeleri izleyerek önce [bir hesap oluşturmayı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) unutmayın.

## <a name="begin-the-host-pool-setup-process"></a>Konak havuzu kurulum işlemini Başlat

Yeni konak havuzunuzu oluşturmaya başlamak için:

1. [https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.
   
   >[!NOTE]
   > US Gov portalında oturum açıyorsanız, [https://portal.azure.us/](https://portal.azure.us/) bunun yerine öğesine gidin.

2. Arama çubuğuna **Windows sanal masaüstü 'nü** girin, ardından hizmetler altında **Windows sanal masaüstü** ' nü bulun ve seçin.

3. **Windows sanal masaüstüne** Genel Bakış sayfasında, **konak havuzu oluştur**' u seçin.

4. **Temel bilgiler** sekmesinde, proje ayrıntıları altında doğru aboneliği seçin.

5. Yeni bir kaynak grubu **oluşturmak için yeni oluştur** ' u seçin ya da açılan menüden var olan bir kaynak grubunu seçin.

6. Konak havuzunuz için benzersiz bir ad girin.

7. Konum alanında, açılan menüden konak havuzunu oluşturmak istediğiniz bölgeyi seçin.

   Seçtiğiniz bölgelerle ilişkili Azure coğrafya, bu konak havuzunun ve ilgili nesnenin meta verilerinin depolanacağı yerdir. Hizmet meta verilerinin depolanmasını istediğiniz coğrafya içindeki bölgeleri seçtiğinizden emin olun.

     > [!div class="mx-imgBorder"]
     > ![Konum alanını seçili Doğu ABD konumu ile gösteren Azure portal ekran görüntüsü. Alanın yanında, "meta veriler Doğu ABD depolanır." ifadesini görürsünüz.](media/portal-location-field.png)
  
   >[!NOTE]
   > Ana bilgisayar havuzunuzu ABD dışındaki [desteklenen bir bölgede](data-locations.md) oluşturmak isterseniz, kaynak sağlayıcısını yeniden kaydetmeniz gerekir. Yeniden kaydolduktan sonra, konumu seçmek için açılan listeden diğer bölgeleri görmeniz gerekir. [Ana havuz oluşturma](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) sorun giderme makalemize nasıl yeniden kaydolacağınızı öğrenin.

8. Konak havuzu türü altında, ana bilgisayar havuzunuzun **Kişisel** mi yoksa **havuza alınmış** mı olacağını seçin.

    - **Kişisel**' i seçerseniz, atama türü alanında **Otomatik** veya **doğrudan** ' yi seçin.

      > [!div class="mx-imgBorder"]
      > ![Atama türü alanı açılan menüsünün ekran görüntüsü. Kullanıcı otomatik olarak seçildi.](media/assignment-type-field.png)

9.  **Havuza alınmış**' ı seçerseniz, aşağıdaki bilgileri girin:

     - **En fazla oturum sınırı** için, yük dengelenmesi istediğiniz en fazla kullanıcı sayısını tek bir oturum konağına girin.
     - **Yük dengeleme algoritması** için, kullanım düzeninizi temel alarak, öncelikle, birinci veya derinlik seçeneklerinden birini belirleyin.

       > [!div class="mx-imgBorder"]
       > ![Atama türü alanının "havuza alınmış" seçiliyken bir ekran görüntüsü. Kullanıcı, Yük Dengeleme açılan menüsünde imlecini yukarı doğru üzerine alır.](media/pooled-assignment-type.png)

10. **İleri ' yi seçin: sanal makineler >**.

11. Zaten sanal makineler oluşturduysanız ve bunları yeni konak havuzuyla kullanmak istiyorsanız **Hayır**' ı seçin, ileri ' yi seçin **: çalışma alanı >** ve [çalışma alanı bilgileri](#workspace-information) bölümüne atlayın. Yeni sanal makineler oluşturmak ve bunları yeni konak havuzuna kaydetmek istiyorsanız **Evet**' i seçin.

İlk parçayı tamamladığınıza göre, VM 'yi oluşturduğumuz kurulum sürecinin bir sonraki bölümüne geçeceğiz.

## <a name="virtual-machine-details"></a>Sanal makine ayrıntıları

İlk parçayı öğrendiğimiz için VM 'nizi ayarlamanız gerekir.

Sanal makinenizi konak Havuzu Kurulum işlemi içinde ayarlamak için:

1. **Kaynak grubu** altında, sanal makineleri oluşturmak istediğiniz kaynak grubunu seçin. Bu, konak havuzu için kullanılandan farklı bir kaynak grubu olabilir.

2. Bundan sonra, kurulum işleminin oluşturduğu sanal makineleri adlandırmak için bir **ad ön eki** sağlayın. Sonek `-` 0 ' dan başlayan sayılarla birlikte olacaktır.

3. Sanal makineleri oluşturmak istediğiniz **sanal makine konumunu** seçin. Konak havuzu için seçtiğiniz bölgeden aynı veya farklı olabilir.
   
4. Ardından, gereksinimlerinize en uygun kullanılabilirlik seçeneğini belirleyin. Sizin için uygun olan seçenek hakkında daha fazla bilgi edinmek için bkz. [Azure 'da sanal makineler Için kullanılabilirlik seçenekleri](../virtual-machines/availability.md) ve [SSS](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [Kullanılabilirlik bölgesi açılan menüsünün ekran görüntüsü. "Kullanılabilirlik alanı" seçeneği vurgulanır.](media/availability-zone.png)

5. Sonra, sanal makineyi oluşturmak için kullanılması gereken görüntüyü seçin. **Galeri** veya **Depolama Blobu** seçeneklerinden birini belirleyebilirsiniz.

    - **Galeri**' yi seçerseniz, açılan menüden önerilen görüntülerden birini seçin:

      - Windows 10 Enterprise multi-session, sürüm 1909
      - Windows 10 Enterprise çoklu oturum, sürüm 1909 + Microsoft 365 uygulamalar
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multi-session, sürüm 2004
      - Windows 10 Enterprise çoklu oturum, sürüm 2004 + Microsoft 365 uygulamalar

      İstediğiniz görüntüyü görmüyorsanız Galerinizdeki başka bir görüntüyü veya Microsoft ve diğer yayımcılar tarafından sunulan bir görüntüyü seçmenizi sağlayan **tüm görüntüleri gör**' ü seçin. Seçtiğiniz görüntünün [desteklenen işletim sistemi görüntülerinden](overview.md#supported-virtual-machine-os-images)biri olduğundan emin olun.

      > [!div class="mx-imgBorder"]
      > ![Microsoft 'un gösterildiği resimlerin listesini içeren Market 'in ekran görüntüsü.](media/marketplace-images.png)

      **Öğelerime** giderek, zaten karşıya yüklediğiniz özel bir görüntüyü seçebilirsiniz.

      > [!div class="mx-imgBorder"]
      > ![Öğelerimin sekmesinin ekran görüntüsü.](media/my-items.png)

    - **Depolama Blobu**' nı seçerseniz, Hyper-V veya BIR Azure VM 'de kendi görüntü yapınızı kullanabilirsiniz. Tüm yapmanız gerekirse, bir URI olarak Depolama Blobu içindeki görüntünün konumunu girin.
   
   Görüntünün konumu kullanılabilirlik seçeneğinden bağımsızdır, ancak görüntünün bölge dayanıklılığı, bu görüntünün kullanılabilirlik bölgesi ile kullanılıp kullanılamayacağını belirler. Görüntünüzü oluştururken bir kullanılabilirlik bölgesi seçerseniz, galerideki bölge dayanıklılığı etkin olan bir görüntü kullandığınızdan emin olun. Kullanmanız gereken bölge dayanıklılığı seçeneği hakkında daha fazla bilgi edinmek için bkz. [SSS](faq.md#which-availability-option-is-best-for-me).

6. Bundan sonra, kullanmak istediğiniz **sanal makine boyutunu** seçin. Varsayılan boyutu olduğu gibi tutabilirsiniz ya da boyutu değiştirmek için **boyutu Değiştir** ' i seçin. **Boyutu Değiştir**' i seçerseniz, görüntülenen pencerede, iş yükünüz için uygun olan sanal makine boyutunu seçin.

7. **VM sayısı**' nın altında, konak havuzunuz için oluşturmak istediğiniz VM sayısını sağlayın.

    >[!NOTE]
    >Kurulum işlemi, konak havuzunuzu ayarlarken en fazla 400 VM oluşturabilir ve her bir VM kurulum işlemi kaynak grubunuzda dört nesne oluşturur. Oluşturma işlemi abonelik kotayı denetlemediğinden, girdiğiniz sanal makine sayısının Azure VM 'de ve kaynak grubunuz ve aboneliğiniz için API sınırları içinde olduğundan emin olun. Konak havuzunuzu oluşturmayı bitirdikten sonra daha fazla VM ekleyebilirsiniz.

8. Sanal makinelerinizin ne tür işletim sistemi diskleri kullanmasını istediğinizi seçin: Standart SSD, Premium SSD veya Standart HDD.

9. Ağ ve güvenlik altında, oluşturduğunuz sanal makineleri yerleştirmek istediğiniz **sanal ağı** ve **alt ağı** seçin. Sanal ağ içindeki sanal makineleri etki alanına katdığınızdan emin olmanız gerektiğinden, sanal ağın etki alanı denetleyicisine bağlanabildiğinden emin olun. Seçtiğiniz sanal ağın DNS sunucuları, etki alanı denetleyicisinin IP 'sini kullanacak şekilde yapılandırılmalıdır.

10. İstediğiniz güvenlik grubu türünü seçin: **temel**, **Gelişmiş** veya **yok**.

    **Temel** seçeneğini belirlerseniz, herhangi bir gelen bağlantı noktasının açılmasını isteyip istemediğinizi seçmeniz gerekir. **Evet**' i seçerseniz, gelen bağlantılara izin vermek için standart bağlantı noktaları listesinden seçim yapın.

    >[!NOTE]
    >Daha fazla güvenlik için genel gelen bağlantı noktalarını açmemenizi öneririz.

    > [!div class="mx-imgBorder"]
    > ![Bir açılan menüdeki kullanılabilir bağlantı noktalarının listesini gösteren güvenlik grubu sayfasının ekran görüntüsü.](media/available-ports.png)

    **Gelişmiş**' i seçerseniz, önceden yapılandırdığınız mevcut bir ağ güvenlik grubunu seçin.

11. Bundan sonra, sanal makinelerin belirli bir etki alanına ve kuruluş birimine katılmasını isteyip istemediğinizi seçin. **Evet**' i seçerseniz, katılacak etki alanını belirtin. İsteğe bağlı olarak, sanal makinelerin içinde olmasını istediğiniz belirli bir kuruluş birimini ekleyebilirsiniz. **Hayır**' ı seçerseniz, VM 'ler **ad etkı alanına katılma UPN**'si sonekiyle eşleşen etki alanına birleştirilir.

    - Bir OU belirttiğinizde, tam yolu (ayırt edici ad) ve tırnak işaretleri olmadan kullandığınızdan emin olun.

12. Etki alanı yöneticisi hesabı ' nın altında, seçtiğiniz sanal ağın Active Directory Etki Alanı yöneticisinin kimlik bilgilerini girin. Bu hesabın birden çok facklu kimlik doğrulaması (MFA) etkin olamaz. Azure Active Directory Domain Services (Azure AD DS) etki alanına katılırken, hesap Azure AD DC yöneticileri grubunun bir parçası olmalıdır ve hesap parolasının Azure AD DS 'de çalışması gerekir.

13. **İleri ' yi seçin: çalışma alanı >**.

Bununla birlikte, konak havuzunuzu ayarlamanın bir sonraki aşamasına başlamaya hazırız: uygulama grubunuzu bir çalışma alanına kaydetme.

## <a name="workspace-information"></a>Çalışma alanı bilgileri

Konak Havuzu Kurulum işlemi varsayılan olarak bir masaüstü uygulama grubu oluşturur. Konak havuzunun istendiği gibi çalışması için, bu uygulama grubunu kullanıcılara veya Kullanıcı gruplarına yayımlamanız gerekir ve uygulama grubunu bir çalışma alanına kaydetmeniz gerekir.

Masaüstü uygulama grubunu bir çalışma alanına kaydetmek için:

1. **Evet**’i seçin.

   **Hayır**' ı seçerseniz, uygulama grubunu daha sonra kaydedebilirsiniz, ancak konak havuzunuzun düzgün şekilde çalışması için çalışma alanı kaydını hemen yapmanız önerilir.

2. Sonra, yeni bir çalışma alanı oluşturmak mı yoksa mevcut çalışma alanlarından seçmek mi istediğinizi seçin. Yalnızca konak havuzuyla aynı konumda oluşturulan çalışma alanları, uygulama grubunu ' a kaydetmeye izin verilir.

3. İsteğe bağlı olarak, Ileri ' yi seçebilirsiniz **: etiketler >**.

    Burada, yöneticilerinize daha kolay hale getirmek için nesneleri meta verilerle gruplandırabilmeniz için Etiketler ekleyebilirsiniz.

4. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.

     >[!NOTE]
     >İnceleme + doğrulama oluştur işlemi, parolanızın güvenlik standartlarını karşılayıp karşılamadığını veya mimarinizin doğru olup olmadığını kontrol etmez. bu nedenle, bunlardan herhangi biriyle ilgili sorunları denetlemeniz gerekir.

5. Her şeyin doğru göründüğünden emin olmak için dağıtımınız hakkındaki bilgileri gözden geçirin. İşiniz bittiğinde **Oluştur**'u seçin. Bu, aşağıdaki nesneleri oluşturan dağıtım işlemini başlatır:

     - Yeni konak havuzunuz.
     - Bir masaüstü uygulama grubu.
     - Oluşturmayı seçerseniz çalışma alanı.
     - Masaüstü uygulama grubunu kaydetmeyi seçerseniz, kayıt tamamlanır.
     - Sanal makineler, etki alanına katılmış ve yeni konak havuzuna kayıtlı olan bunları oluşturmayı seçerseniz.
     - Yapılandırmanızı temel alan bir Azure Kaynak Yönetimi şablonu için indirme bağlantısı.

Bundan sonra işiniz tamamlandı!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Yeni bir konak havuzu sağlamak için Azure Resource Manager şablonunu çalıştırma

Otomatik bir işlem kullanmak istiyorsanız, bunun yerine yeni konak havuzunuzu sağlamak için [Azure Resource Manager şablonumuzu indirin](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) .

>[!NOTE]
>Ortamınızı derlemek için otomatikleştirilmiş bir işlem kullanıyorsanız, yapılandırma JSON dosyasının en son sürümü gerekir. JSON dosyasını [buradan](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık konak havuzunuzu oluşturduğunuza göre, bunu RemoteApp programları ile doldurabilirsiniz. Windows sanal masaüstündeki uygulamaları yönetme hakkında daha fazla bilgi edinmek için sonraki öğreticimize gidin:

> [!div class="nextstepaction"]
> [Uygulama gruplarını yönetme öğreticisi](./manage-app-groups.md)
