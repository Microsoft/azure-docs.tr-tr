---
title: Mevcut Windows sanal masaüstü (klasik) konak havuzunu yeni oturum Konakları ile genişletin-Azure
description: Mevcut bir konak havuzunu Windows sanal masaüstündeki (klasik) yeni oturum Konakları ile genişletme.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f82b831d887f0ebdd659167935f2134583b3bb87
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551960"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Windows sanal masaüstündeki yeni oturum Konakları ile mevcut bir konak havuzunu genişletme (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../expand-existing-host-pool.md)bakın.

Konak havuzunuzdaki kullanımı artırın, yeni yükü işlemek için mevcut konak havuzunuzu yeni oturum konaklarıyla genişletmeniz gerekebilir.

Bu makalede, mevcut bir konak havuzunu yeni oturum Konakları ile nasıl genişletebileceğiniz açıklanır.

## <a name="what-you-need-to-expand-the-host-pool"></a>Konak havuzunu genişletmeniz için gerekenler

Başlamadan önce, aşağıdaki yöntemlerden birini kullanarak bir konak havuzu ve oturum ana bilgisayarı sanal makineleri (VM 'Ler) oluşturduğunuzdan emin olun:

- [Azure Market teklifi](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager şablonu](create-host-pools-arm-template.md)
- [PowerShell ile ana bilgisayar havuzu oluşturma](create-host-pools-powershell-2019.md)

Konak havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda aşağıdaki bilgilere de ihtiyacınız olacaktır:

- VM boyutu, resim ve ad ön eki
- Etki alanına ekleme ve Windows sanal masaüstü kiracı yöneticisi kimlik bilgileri
- Sanal ağ adı ve alt ağ adı

Sonraki üç bölüm, ana bilgisayar havuzunu genişletmek için kullanabileceğiniz üç yöntemden oluşur. Rahatımız bir dağıtım aracı ile yapabilirsiniz.

>[!NOTE]
>Dağıtım aşamasında, önceki oturum ana bilgisayar VM kaynakları için şu anda kapatıldıklarında hata iletileri görürsünüz. Bu hatalar, Azure 'ın, oturum ana bilgisayar VM 'lerinin mevcut konak havuzunuza doğru şekilde kaydedildiğini doğrulamak için PowerShell DSC uzantısını çalıştıramadığı için oluşur. Adı "-0" ile biten oturum ana bilgisayarı çalışıyor olmalıdır ancak diğer oturum konakları için bu hataları güvenle yoksayabilirsiniz veya dağıtım işlemini başlatmadan önce mevcut konak havuzundaki tüm oturum ana bilgisayarları ' nı başlatarak hatalardan kaçınabilirsiniz.

## <a name="redeploy-from-azure"></a>Azure 'dan yeniden dağıtın

[Azure Marketi](create-host-pools-azure-marketplace-2019.md) teklifini veya [GitHub Azure Resource Manager şablonunu](create-host-pools-arm-template.md)kullanarak zaten bir konak havuzu ve oturum ana bilgisayar vm 'leri oluşturduysanız, Azure Portal aynı şablonu yeniden dağıtabilirsiniz. Şablonu yeniden dağıtmak, parola hariç özgün şablona girdiğiniz tüm bilgileri otomatik olarak yeniden girer.

Bir konak havuzunu genişletmek için Azure Resource Manager şablonunu yeniden dağıtma:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure portal en üstündeki arama çubuğundan **kaynak grupları** ' nı arayın ve **Hizmetler** altında öğeyi seçin.
3. Konak havuzunu oluştururken oluşturduğunuz kaynak grubunu bulun ve seçin.
4. Tarayıcının sol tarafındaki panelde **dağıtımlar**' ı seçin.
5. Konak havuzu oluşturma işleminiz için uygun dağıtımı seçin:
     - Özgün ana bilgisayar havuzunu Azure Marketi teklifiyle oluşturduysanız, **RDS. WVD-provision-Host-Pool** ile başlayan dağıtımı seçin.
     - GitHub Azure Resource Manager şablonuyla orijinal ana bilgisayar havuzunu oluşturduysanız, **Microsoft. Template** adlı dağıtımı seçin.
6. Yeniden **Dağıt**' ı seçin.

     >[!NOTE]
     >Yeniden **Dağıt** seçeneğini belirlediğinizde şablon otomatik olarak yeniden **dağıtmazsa**, tarayıcınızın sol tarafındaki panelde **şablon** ' u seçin ve ardından Dağıt ' ı seçin.

7. Mevcut konak havuzundaki geçerli oturum ana bilgisayar sanal makinelerini içeren kaynak grubunu seçin.

     >[!NOTE]
     >Girdiğiniz bir kaynak grubunu seçmenizi bildiren bir hata görürseniz, başka bir kaynak grubu seçin ve ardından orijinal kaynak grubunu seçin.

8. *_ArtifactsLocation* için aşağıdaki URL 'yi girin:`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Toplam oturum ana bilgisayar sayısını ( *RDSH örnek sayısı*) istediğiniz yeni girin. Örneğin, ana bilgisayar havuzunuzu beş oturum ana bilgisayardan sekiz ' a genişleyorsanız **8** girin.
10. Mevcut etki alanı UPN 'si için kullandığınız etki alanı parolasının aynısını girin. Şablonu çalıştırdığınızda bir hataya neden olacağı için Kullanıcı adını değiştirmeyin.
11. *Kiracı Yöneticisi UPN 'Si veya uygulama kimliği* için girdiğiniz kullanıcı veya uygulama kimliği için kullandığınız kiracı yönetici parolasını girin. Bir kez daha, Kullanıcı adını değiştirmeyin.
12. Ana bilgisayar havuzunuzu genişletmek için gönderimi doldurun.

## <a name="run-the-azure-marketplace-offering"></a>Azure Marketi teklifini çalıştırma

[Yeni bir konak havuzu sağlamak Için Azure](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)marketi teklifini çalıştırmaya ulaşıncaya kadar [Azure Marketi 'ni kullanarak bir konak havuzu oluşturma](create-host-pools-azure-marketplace-2019.md) bölümündeki yönergeleri izleyin. Bu noktaya geldiğinizde, her sekme için aşağıdaki bilgileri girmeniz gerekir:

### <a name="basics"></a>Temel Bilgiler

Bu bölümdeki tüm değerler, *varsayılan masaüstü kullanıcıları* dışında, ana bilgisayar havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda sağladıklarınızla eşleşmelidir:

1.    *Abonelik* için, ilk olarak konak havuzunu oluşturduğunuz aboneliği seçin.
2.    *Kaynak grubu* için, mevcut konak havuzu oturum ana bilgisayar VM 'lerinin bulunduğu kaynak grubunu seçin.
3.    *Bölge* için, mevcut konak havuzu oturum ana bilgisayar VM 'lerinin bulunduğu bölgeyi seçin.
4.    *Hostpool adı* için, mevcut konak havuzunun adını girin.
5.    *Masaüstü türü* için, mevcut konak havuzuyla eşleşen masaüstü türünü seçin.
6.    *Varsayılan masaüstü kullanıcıları* Için, Windows sanal masaüstü istemcilerinde oturum açmak istediğiniz ek kullanıcıların virgülle ayrılmış bir listesini girin ve Azure Marketi teklifi bittikten sonra bir masaüstüne erişin. Örneğin, atamak user3@contoso.com ve erişmek istiyorsanız, user4@contoso.com girin user3@contoso.com user4@contoso.com .
7.    **İleri ' yi seçin: sanal makineyi yapılandırın**.

>[!NOTE]
>*Varsayılan masaüstü kullanıcıları* hariç, tüm alanların mevcut konak havuzunda tam olarak yapılandırıldığı şekilde eşleşmesi gerekir. Bir uyumsuzluk varsa, bu, yeni bir konak havuzu ile sonuçlanır.

### <a name="configure-virtual-machines"></a>Sanal makineleri yapılandırma

Toplam VM sayısı dışında, bu bölümdeki tüm parametre değerleri, ana bilgisayar havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda sağladıklarınız ile aynı olmalıdır. Girdiğiniz VM sayısı, genişletilmiş konak havuzunuzdaki sanal makinelerin sayısı olacaktır:

1. Mevcut oturum ana bilgisayarı VM 'leriyle eşleşen VM boyutunu seçin.

    >[!NOTE]
    >Aradığınız belirli VM boyutu VM boyut Seçicisi 'nde görünmezse, bu, henüz Azure Market aracına eklendi.

2. Konak havuzunuzdaki olmasını istediğiniz oturum ana bilgisayarlarının toplam sayısını seçmek için *kullanım profilini*, *Toplam kullanıcıları* ve *sanal makine sayısı* parametrelerini özelleştirin. Örneğin, ana bilgisayar havuzunuzu beş oturum ana bilgisayardan sekiz ' a genişleyorsanız, 8 sanal makineye ulaşmak için bu seçenekleri yapılandırın.
3. Sanal makinelerin adları için bir ön ek girin. Örneğin, "önek" adını girerseniz, sanal makineler "ön ek-0", "ön ek-1" olarak adlandırılır ve bu şekilde devam eder.
4. **İleri ' yi seçin: sanal makine ayarları**.

### <a name="virtual-machine-settings"></a>Sanal makine ayarları

Bu bölümdeki tüm parametre değerleri, ana bilgisayar havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda sağladıklarınız ile eşleşmelidir:

1. *Görüntü kaynağı* ve *görüntü işletim sistemi sürümü* için, ana bilgisayar havuzunu ilk oluşturduğunuzda sağladınız aynı bilgileri girin.
2. *Ad etki alanına KATıLMASı UPN* ve ilişkili parolalar Için, vm 'leri Active Directory etki alanına katmak üzere konak havuzunu ilk oluşturduğunuzda verdiğiniz aynı bilgileri girin. Bu kimlik bilgileri, sanal makinelerinizde yerel bir hesap oluşturmak için kullanılacaktır. Bu yerel hesapları, kimlik bilgilerini daha sonra değiştirmek üzere sıfırlayabilirsiniz.
3. Sanal ağ bilgileri için, mevcut konak havuzu oturum ana bilgisayar sanal makinelerinizin bulunduğu sanal ağı ve alt ağı seçin.
4. **İleri ' yi seçin: Windows sanal masaüstü bilgilerini yapılandırın**.

### <a name="windows-virtual-desktop-information"></a>Windows sanal masaüstü bilgileri

Bu bölümdeki tüm parametre değerleri, ana bilgisayar havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda sağladıklarınız ile eşleşmelidir:

1. *Windows sanal masaüstü kiracı grubu adı* için kiracınızı içeren kiracı grubunun adını girin. Belirli bir kiracı grubu adı sağlanmadığınız sürece varsayılan olarak bırakın.
2. *Windows sanal masaüstü kiracı adı* için, bu konak havuzunu oluşturacağınız kiracının adını girin.
3. Ana bilgisayar havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda kullandığınız kimlik bilgilerini belirtin. Hizmet sorumlusu kullanıyorsanız, hizmet sorumlunun bulunduğu Azure Active Directory örneğinin KIMLIĞINI girin.
4. **İleri ' yi seçin: gözden geçir + oluştur**.

## <a name="run-the-github-azure-resource-manager-template"></a>GitHub Azure Resource Manager şablonunu çalıştırma

[Yeni bir konak havuzu sağlamak](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) ve *RDSH örnek sayısı* hariç tüm aynı parametre değerlerini sağlamak için Azure Resource Manager şablonunu çalıştırma bölümündeki yönergeleri izleyin. Şablonu çalıştırdıktan sonra konak havuzunda istediğiniz oturum ana bilgisayar VM 'lerinin sayısını girin. Örneğin, ana bilgisayar havuzunuzu beş oturum ana bilgisayardan sekiz ' a genişleyorsanız **8** girin.

## <a name="next-steps"></a>Sonraki adımlar

Mevcut konak havuzunuzu genişlettiyseniz, bir kullanıcı oturumunun parçası olarak test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Aşağıdaki istemcilerden herhangi biriyle bir oturuma bağlanabilirsiniz:

- [Windows Masaüstü istemcisine bağlanma](connect-windows-7-10-2019.md)
- [Web istemcisiyle bağlanma](connect-web-2019.md)
- [Android istemcisiyle bağlanma](connect-android-2019.md)
- [macOS istemcisiyle bağlanma](connect-macos-2019.md)
- [iOS istemcisiyle bağlanma](connect-ios-2019.md)
