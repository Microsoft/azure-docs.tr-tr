---
title: Azure 'da OpenShift kapsayıcı platformu 3,11 otomatik yönetilen Market teklifi dağıtma
description: Azure 'da OpenShift kapsayıcı platformu 3,11 kendinden yönetilen Market Teklifini dağıtın.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 20a17e52064c5beb09ce4db5815ddd6faf7cbcba
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035514"
---
# <a name="configure-prerequisites"></a>Önkoşulları yapılandırma

Azure 'da otomatik olarak yönetilen bir OpenShift kapsayıcı platformu 3,11 kümesi dağıtmak için Market Teklifini kullanmadan önce birkaç koşulun yapılandırılması gerekir.  Bir SSH anahtarı (parola olmadan), Azure Anahtar Kasası, Anahtar Kasası gizli anahtarı ve hizmet sorumlusu oluşturma yönergeleri için [OpenShift önkoşulları](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) makalesini okuyun.

 
## <a name="deploy-using-the-marketplace-offer"></a>Market teklifini kullanarak dağıtma

Azure 'a otomatik olarak yönetilen bir OpenShift kapsayıcı platformu 3,11 kümesi dağıtmanın en kolay yolu [Azure Market Teklifini](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)kullanmaktır.

Bu seçenek en basit seçenektir, ancak aynı zamanda sınırlı özelleştirme yeteneklerine sahiptir. Market teklifi, OpenShift kapsayıcı platformu 3.11.82 dağıtır ve aşağıdaki yapılandırma seçeneklerini içerir:

- **Ana düğümler**: yapılandırılabilir örnek türüne sahip üç (3) ana düğüm.
- **Infra düğümleri**: yapılandırılabilir örnek türüne sahip üç (3) infra düğümleri.
- **Düğümler**: düğüm sayısı (1 ile 9 arasında) ve örnek türü yapılandırılabilir.
- **Disk türü**: yönetilen diskler kullanılır.
- **Ağ iletişimi**: yeni veya mevcut ağ ve özel CIDR aralığı için destek.
- **CNS**: CNS etkinleştirilebilir.
- **Ölçümler**: hawksel ölçümler etkinleştirilebilir.
- **Günlüğe kaydetme**: EFK günlüğü etkinleştirilebilir.
- **Azure bulut sağlayıcısı**: varsayılan olarak etkindir, devre dışı bırakılabilir.

Azure portal sol üst tarafında, **kaynak oluştur ' a**tıklayın, arama kutusuna ' openshıft kapsayıcı platformu ' yazın ve ENTER tuşuna basın.

   ![Yeni kaynak arama](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Sonuçlar sayfası, listede **Red Hat OpenShift kapsayıcı platformu 3,11** içinde açılır. 

   ![Yeni kaynak arama sonucu](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Teklifin ayrıntılarını görüntülemek için teklifine tıklayın. Bu teklifi dağıtmak için **Oluştur**' a tıklayın. Gerekli parametreleri girmek için Kullanıcı arabirimi görüntülenir. İlk ekran **temel bilgiler** dikey penceresdir.

   ![Teklif başlığı sayfası](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Temel Bilgiler**

Giriş parametrelerinden herhangi biri hakkında yardım almak için, parametre adının yanında ***g*** 'nin üzerine gelin.

Giriş parametrelerinin değerlerini girin ve **Tamam**' a tıklayın.

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| VM Yöneticisi Kullanıcı adı | Tüm sanal makine örneklerinde oluşturulacak Yönetici Kullanıcı |
| Yönetici Kullanıcı için SSH ortak anahtarı | VM 'de oturum açmak için kullanılan SSH ortak anahtarı-bir parola içermemelidir |
| Abonelik | Kümenin dağıtılacağı Azure aboneliği |
| Kaynak Grubu | Küme kaynakları için yeni bir kaynak grubu oluşturun veya var olan boş bir kaynak grubunu seçin |
| Konum | Kümenin dağıtılacağı Azure bölgesi |

   ![Teklif temelleri dikey penceresi](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Altyapı ayarları**

Giriş parametrelerinin değerlerini girin ve **Tamam**' a tıklayın.

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| OCP kümesi adı ön eki | Tüm düğümlerde konak adlarını yapılandırmak için kullanılan küme öneki. 1 ila 20 karakter arasında |
| Ana düğüm boyutu | Varsayılan VM boyutunu kabul edin veya farklı bir VM boyutu seçmek için **boyutu Değiştir** ' e tıklayın.  İş yüklerinizin uygun VM boyutunu seçin |
| Altyapı düğümü boyutu | Varsayılan VM boyutunu kabul edin veya farklı bir VM boyutu seçmek için **boyutu Değiştir** ' e tıklayın.  İş yüklerinizin uygun VM boyutunu seçin |
| Uygulama düğümlerinin sayısı | Varsayılan VM boyutunu kabul edin veya farklı bir VM boyutu seçmek için **boyutu Değiştir** ' e tıklayın.  İş yüklerinizin uygun VM boyutunu seçin |
| Uygulama düğümü boyutu | Varsayılan VM boyutunu kabul edin veya farklı bir VM boyutu seçmek için **boyutu Değiştir** ' e tıklayın.  İş yüklerinizin uygun VM boyutunu seçin |
| Savunma ana bilgisayar boyutu | Varsayılan VM boyutunu kabul edin veya farklı bir VM boyutu seçmek için **boyutu Değiştir** ' e tıklayın.  İş yüklerinizin uygun VM boyutunu seçin |
| Yeni veya var olan sanal ağ | Yeni vNet oluştur (varsayılan) veya var olan bir sanal ağı kullan |
| Varsayılan CıDR ayarlarını seçin veya IP aralığını (CıDR) özelleştirin | Varsayılan CıDR aralıklarını kabul edin veya **özel IP aralığı** ' nı seçin ve özel CIDR bilgilerini girin.  Varsayılan ayarlar, 10.0.0.0/14 CıDR, 10.1.0.0/16 ile ana alt ağ, 10.2.0.0/16 ve bilgi işlem ve CNS subnet with 10.3.0.0/16 olan sanal ağ oluşturur |
| Key Vault kaynak grubu adı | Key Vault içeren kaynak grubunun adı |
| Key Vault Adı | SSH özel anahtarıyla gizli dizi içeren Key Vault adı.  Yalnızca alfasayısal karakterlere ve tirelere izin verilir ve 3 ila 24 karakter arasında olmalıdır |
| Gizli dizi adı | SSH özel anahtarını içeren gizli dizinin adı.  Yalnızca alfasayısal karakterlere ve tirelere izin verilir |

   ![Teklif altyapısı dikey penceresi](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Değişiklik boyutu**

Farklı bir VM boyutu seçmek için ***boyutu Değiştir***' e tıklayın.  VM seçim penceresi açılır.  İstediğiniz VM boyutunu seçin ve **Seç**' e tıklayın.

   ![VM boyutunu seçin](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Var olan sanal ağ**

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| Var olan sanal ağ adı | Mevcut vNet 'in adı |
| Ana düğümlerin alt ağ adı | Ana düğümlerin mevcut alt ağının adı.  En az 16 IP adresi içermesi ve RFC 1918 ' i izlemeniz gerekir |
| Infra düğümleri için alt ağ adı | Infra düğümleri için mevcut alt ağın adı.  En az 32 IP adresi içermesi ve RFC 1918 ' i izlemeniz gerekir |
| İşlem ve CNS Nodes için alt ağ adı | İşlem ve CNS düğümleri için mevcut alt ağın adı.  En az 32 IP adresi içermesi ve RFC 1918 ' i izlemeniz gerekir |
| Mevcut sanal ağın kaynak grubu | Mevcut vNet 'i içeren kaynak grubunun adı |

   ![Altyapı mevcut VNET 'i sunma](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Özel IP aralığı**

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| Sanal ağın adres aralığı | VNet için özel CıDR |
| Ana düğümleri içeren alt ağın adres aralığı | Ana alt ağ için özel CıDR |
| Altyapı düğümlerini içeren alt ağın adres aralığı | Altyapı alt ağı için özel CıDR |
| İşlem ve CNS Nodes içeren alt ağın adres aralığı | İşlem ve CNS düğümleri için özel CıDR |

   ![Teklif altyapısı özel IP aralığı](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift kapsayıcı platformu 3,11**

Giriş parametrelerinin değerlerini girin ve **Tamam 'a** tıklayın

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| OpenShift Yönetici Kullanıcı parolası | İlk OpenShift kullanıcısının parolası.  Bu Kullanıcı da Küme Yöneticisi olacak |
| OpenShift Yönetici Kullanıcı parolasını onayla | OpenShift Yönetici Kullanıcı parolasını yeniden yazın |
| Red Hat abonelik Yöneticisi Kullanıcı adı | Red Hat aboneliğinize veya kuruluş KIMLIĞINIZE erişmek için Kullanıcı adı.  Bu kimlik bilgisi, RHEL örneğini aboneliğinize kaydetmek ve Microsoft veya Red Hat tarafından depolanmayacak şekilde kullanılır |
| Red Hat abonelik Yöneticisi Kullanıcı parolası | Red Hat aboneliğinize veya etkinleştirme anahtarına erişmek için parola.  Bu kimlik bilgisi, RHEL örneğini aboneliğinize kaydetmek ve Microsoft veya Red Hat tarafından depolanmayacak şekilde kullanılır |
| Red Hat abonelik Yöneticisi OpenShift havuz KIMLIĞI | OpenShift kapsayıcı platformu Yetkilendiricisi içeren havuz KIMLIĞI. Kümenin yüklenmesi için OpenShift kapsayıcı platformu için yeterli yetkilendirmediğinizden emin olun |
| Red Hat abonelik Yöneticisi aracı/ana düğümler için OpenShift havuz KIMLIĞI | Aracı/ana düğümler için OpenShift kapsayıcı platformu yetkilendirmelerini içeren havuz KIMLIĞI. Kümenin yüklenmesi için OpenShift kapsayıcı platformunun yeterli yetkilendirmelerinizi bulundurtığınızdan emin olun. Aracı/ana havuz KIMLIĞI kullanmıyorsanız, uygulama düğümleri için havuz KIMLIĞINI girin |
| Azure bulut sağlayıcısını yapılandırma | Azure bulut sağlayıcısını kullanmak için OpenShift 'i yapılandırın. Kalıcı birimler için Azure disk iliştirme kullanılıyorsa gereklidir.  Varsayılan değer Evet ' tir |
| Azure AD hizmet sorumlusu Istemci KIMLIĞI GUID | Azure AD hizmet sorumlusu Istemci KIMLIĞI GUID 'SI-AppID olarak da bilinir. Yalnızca Azure Cloud Provider 'ı **Evet** olarak ayarlandıysa gereklidir |
| Azure AD hizmet sorumlusu Istemci KIMLIĞI gizli anahtarı | Azure AD hizmet sorumlusu Istemci KIMLIĞI gizli anahtarı. Yalnızca Azure Cloud Provider 'ı **Evet** olarak ayarlandıysa gereklidir |
 
   ![OpenShift dikey penceresini teklif edin](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Ek ayarlar**

Ek ayarlar dikey penceresi, GlusterFS depolama, günlüğe kaydetme, ölçümler ve yönlendirici alt etki alanı için CNS yapılandırmasına izin verir.  Varsayılan değer bu seçeneklerden hiçbirini yüklemez ve test amacıyla yönlendirici alt etki alanı olarak nip.io kullanır. CNS 'in etkinleştirilmesi, GlusterFS pods 'yi barındıracak üç ek bağlı disk ile üç ek işlem düğümü yükler.  

Giriş parametrelerinin değerlerini girin ve **Tamam 'a** tıklayın

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| Kapsayıcı yerel depolamayı yapılandırma (CNS) | OpenShift kümesine CNS yükleme yapar ve depolama olarak etkinleştirir. Azure sağlayıcısı devre dışıysa varsayılan olacak |
| Küme günlüğünü yapılandırma | , Kümeye EFK günlüğü işlevini kurar.  EFK Pod 'leri barındırmak için Infra düğümlerini uygun şekilde boyutlandır |
| Küme için ölçümleri yapılandırma | OpenShift kümesine Hawksel ölçümler yükleme.  Cara düğümlerini, Hawksel ölçüm yığınlarını barındıracak şekilde boyutlandırın |
| Varsayılan yönlendirici alt etki alanı | Test için nipio veya üretim için kendi alt etki alanınızı girmek üzere özel ' i seçin |
 
   ![Ek dikey pencere sunun](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Ek ayarlar-ek parametreler**

| Giriş parametresi | Parametre açıklaması |
|-----------------------|-----------------|
| CNS Düğüm boyutu | Varsayılan düğüm boyutunu kabul edin veya yeni bir VM boyutu seçmek için **boyutu Değiştir** ' i seçin |
| Özel alt etki alanınızı girin | OpenShift kümesindeki yönlendirici aracılığıyla uygulamaları ortaya çıkarmak için kullanılacak özel yönlendirme etki alanı.  Uygun joker karakter DNS girişini oluşturmayı unutmayın] |
 
   ![Ek CNS Install sunun](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Özet**

Çekirdek kotasının, küme için seçilen toplam VM sayısını dağıtmak için yeterli olup olmadığını denetlemek için bu aşamada doğrulama gerçekleşir.  Girilen tüm parametreleri gözden geçirin.  Girişler kabul edilebilir ise devam etmek için **Tamam** ' ı tıklatın.

   ![Teklif Özeti dikey penceresi](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Satın Al**

Satın alma sayfasında iletişim bilgilerini onaylayın ve OpenShift kapsayıcı platformu kümesinin kullanım koşullarını ve dağıtım başlangıcını kabul etmek için **satın al** ' a tıklayın.

   ![Teklif satın alma dikey penceresi](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift kümesine bağlanma

Dağıtım tamamlandığında, dağıtımın çıkış bölümünden bağlantıyı alın. **OpenShift konsol URL**'sini kullanarak, tarayıcınızla OpenShift konsoluna bağlanın. Ayrıca, savunma ana bilgisayarına SSH de ekleyebilirsiniz. Aşağıda Yönetici Kullanıcı adının kümeyöneticisi olduğu ve savunma genel IP DNS FQDN 'sinin bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com olduğu bir örnek verilmiştir:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu, OpenShift kümesini ve artık gerekli olmadığında tüm ilgili kaynakları kaldırmak için [az Group Delete](/cli/azure/group) komutunu kullanın.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dağıtım sonrası görevler](./openshift-container-platform-3x-post-deployment.md)
- [Azure 'da OpenShift dağıtımında sorun giderme](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift kapsayıcı platformu ile çalışmaya başlama](https://docs.openshift.com)
- 