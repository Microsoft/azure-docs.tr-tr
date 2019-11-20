---
title: Hyper-V VM 'Leri için Azure geçişi sunucu değerlendirmesini/geçişini ayarlama | Microsoft Docs
description: Azure geçişi sunucu değerlendirmesini/geçişini kullanarak Hyper-V VM 'lerinin bulma, değerlendirme ve aracısız geçiş için bir gereç ayarlamayı açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: 00473519ad324d34d1fd595b2fb08408ad991f4c
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158620"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Hyper-V VM 'Leri için gereç ayarlama

Bu makalede, Hyper-V VM 'lerini Azure geçişi sunucu değerlendirme aracı ile değerlendiriyorsanız veya Azure geçişi sunucu geçiş aracını kullanarak VMware VM 'lerini Azure 'a geçirdiğinizde Azure geçişi gerecinin nasıl ayarlanacağı açıklanır.

Hyper-V VM gereci, aşağıdakileri yapmak için Azure geçişi sunucu değerlendirmesi/geçişi tarafından kullanılan hafif bir gereç:

- Şirket içi Hyper-V VM 'lerini bulun.
- Keşfedilen VM 'Ler için meta verileri ve performans verilerini Azure geçişi sunucu değerlendirmesini/geçişini gönderin.

Azure geçişi gereci hakkında [daha fazla bilgi edinin](migrate-appliance.md) .


## <a name="appliance-deployment-steps"></a>Gereç dağıtım adımları

Gereci kurmak için şunları yapın:
- Azure portal sıkıştırılmış bir Hyper-V VHD 'sini indirin.
- Gereci oluşturun ve Azure geçişi sunucu değerlendirmesi 'ne bağlanıp bağlanamadığından emin olun.
- Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.

## <a name="download-the-vhd"></a>VHD 'YI indirin

Gereç için daraltılmış VHD şablonunu indirin.

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makineleriniz > ** makinelerde **sanallaştırılmış mı?** , **Hyper-V ile Evet**' e tıklayın.
3. VHD dosyasını indirmek için **İndir** ' e tıklayın.

    ![VM 'yi indir](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.
2. VHD için karma oluşturmak üzere aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Gereç sürümü 2.19.11.12 için, oluşturulan karma bu ayarlarla eşleşmelidir.

  **Algoritma** | **Karma değeri**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Gereç VM 'sini oluşturma

İndirilen dosyayı içeri aktarın ve VM 'yi oluşturun.

1. Sıkıştırılmış VHD dosyasını Hyper-V konağında gereç sanal makinesini barındıracak bir klasöre ayıklayın. Üç klasör ayıklanır.
2. Hyper-V Yöneticisi 'Ni açın. **Eylemler**' de **sanal makineyi içeri aktar**' a tıklayın.

    ![VHD dağıtma](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Sanal makineyi Içeri aktarma sihirbazında > **başlamadan önce** **İleri**' ye tıklayın.
3. **Klasörü bul**' da ayıklanan VHD 'yi içeren klasörü belirtin. Ardından **İleri**'ye tıklayın.
1. **Sanal makine seç**bölümünde **İleri**' ye tıklayın.
2. **Içeri aktarma türünü seçin**bölümünde **sanal makineyi Kopyala (yenı bir benzersiz kimlik oluştur)** seçeneğine tıklayın. Ardından **İleri**'ye tıklayın.
3. **Hedef Seç**bölümünde varsayılan ayarı bırakın. **İleri**’ye tıklayın.
4. **Depolama klasörlerinde**varsayılan ayarı bırakın. **İleri**’ye tıklayın.
5. **Ağ seçin**bölümünde, VM 'nin kullanacağı sanal anahtarı belirtin. Anahtar, verileri Azure 'a göndermek için internet bağlantısı gerektirir.
6. **Özet**bölümünde ayarları gözden geçirin. Ardından **son**' a tıklayın.
7. Hyper-V Yöneticisi 'nde **sanal makineler**>, VM 'yi başlatın.


### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç VM 'sinin [Azure URL 'lerine](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)bağlanabildiğinizden emin olun.

## <a name="configure-the-appliance"></a>Gereci yapılandırma

Gereci ilk kez ayarlayın.

1. Hyper-V Yöneticisi > **sanal makineler**' de, **bağlan**' a > VM ' ye sağ tıklayın.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. VM 'ye bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı gereç masaüstünden açabilirsiniz.
1. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Lisans**: lisans koşullarını kabul edin ve üçüncü taraf bilgilerini okuyun.
    - **Bağlantı**: uygulama, sanal makinenin internet erişimi olup olmadığını denetler. VM bir proxy kullanıyorsa:
        - **Ara sunucu ayarları**' na tıklayın ve proxy adresini ve dinleme bağlantı noktasını, http://ProxyIPAddress veya http://ProxyFQDNbiçiminde belirtin.
        - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
        - Yalnızca HTTP proxy’si desteklenir.
    - **Zaman eşitleme**: Saat doğrulandı. VM bulmanın düzgün çalışması için gereç süresi internet saatine eşit olmalıdır.
    - **Güncelleştirmeleri yükleme**: Azure geçişi sunucu değerlendirmesi, gerecin en son güncelleştirmelerin yüklü olduğunu denetler.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. **Oturum aç**' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
2. Yeni sekmede, Azure kimlik bilgilerinizi kullanarak oturum açın.
    - Kullanıcı adınızla ve parolanızla oturum açın.
    - PIN ile oturum açma desteklenmez.
3. Başarıyla oturum açtıktan sonra Web uygulamasına geri dönün.
4. Azure geçişi projesinin oluşturulduğu aboneliği seçin. Ardından projeyi seçin.
5. Gereç için bir ad belirtin. Ad 14 karakter veya daha az olmalıdır.
6. **Kaydol**' a tıklayın.


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD 'leri için temsilci kimlik bilgileri

SMB 'lerde VHD 'ler çalıştırıyorsanız, Gereç üzerinden Hyper-V konaklarına kimlik bilgileri temsilcisini etkinleştirmeniz gerekir. Bunu gereç içinden yapmak için:

1. Gereç sanal makinesinde bu komutu çalıştırın. HyperVHost1/HyperVHost2, örnek ana bilgisayar adlarıdır.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Alternatif olarak, bunu gereç üzerindeki Yerel Grup İlkesi Düzenleyicisi yapın:
    - **Bilgisayar yapılandırması** > **yerel bilgisayar Ilkesinde** **Yönetim Şablonları** > **sistem** > **kimlik bilgileri temsili**' ne tıklayın.
    - **Yeni kimlik bilgileri aktarımına Izin ver**' e çift tıklayın ve **etkin**' i seçin.
    - **Seçenekler**' de **göster**' e tıklayın ve bulmayı Istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.
    - **Kimlik bilgileri temsilcisi**içinde, **yalnızca NTLM sunucu kimlik doğrulamasıyla yeni kimlik bilgileri aktarmaya izin ver**' e çift tıklayın. Daha sonra, bulmayı istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gerecden Hyper-V konaklarına veya kümelerine bağlanın ve VM bulmayı başlatın.

1. **Kullanıcı adı** ve **parola**' da, gerecin VM 'leri bulması için kullanacağı hesap kimlik bilgilerini belirtin. Kimlik bilgileri için kolay bir ad belirtin ve **ayrıntıları kaydet**' e tıklayın.
2. **Konak Ekle**' ye tıklayın ve Hyper-V konağı/küme ayrıntılarını belirtin.
3. **Doğrula**' ya tıklayın. Doğrulamadan sonra, her bir konakta/kümede keşfedilebilir VM sayısı gösterilir.
    - Bir konakta doğrulama başarısız olursa, **durum** sütunundaki simgenin üzerine gelerek hatayı gözden geçirin. Sorunları giderin ve yeniden doğrulayın.
    - Konakları veya kümeleri kaldırmak için > **Sil**' i seçin.
    - Belirli bir konağı kümeden kaldıramazsınız. Tüm kümeyi yalnızca çıkarabilirsiniz.
    - Kümedeki belirli konaklarla ilgili sorunlar olsa bile bir küme ekleyebilirsiniz.
4. Doğrulamadan sonra, bulma işlemini başlatmak için **Kaydet ve bulmayı Başlat** ' a tıklayın.

Bu, bulmayı başlatır. Bulunan VM 'lerin meta verilerinde Azure portal görünmesi 15 dakika sürer.

## <a name="verify-vms-in-the-portal"></a>VM’lerin portalda olup olmadığını doğrulama

Bulma işlemi tamamlandıktan sonra, VM 'Lerin portalda göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi sunucu değerlendirmesi ile [Hyper-V değerlendirmesi](tutorial-assess-hyper-v.md) yapmayı deneyin.
