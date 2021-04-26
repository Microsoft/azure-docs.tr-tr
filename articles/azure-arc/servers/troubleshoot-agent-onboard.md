---
title: Azure Arc etkin sunucu Aracısı bağlantı sorunlarını giderme
description: Bu makalede, hizmete bağlanmaya çalışırken Azure Arc etkin sunucularıyla oluşan bağlı makine aracısında sorunları gidermeye ve gidermeye nasıl çözüm yapılacağı açıklanır.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498008"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Azure Arc etkin sunucu Aracısı bağlantı sorunlarını giderme

Bu makalede, Windows veya Linux için bağlı olan Azure Arc özellikli sunucular bağlı makine Aracısı 'nı yapılandırmaya çalışırken oluşabilecek sorunları giderme ve çözme hakkında bilgi verilmektedir. Hizmetle bağlantı yapılandırılırken hem etkileşimli hem de ölçekli yükleme yöntemleri dahildir. Genel bilgiler için bkz. [Arc etkin sunucularına genel bakış](./overview.md).

## <a name="agent-error-codes"></a>Aracı hata kodları

Azure Arc etkin sunucular Aracısı 'nı yapılandırırken bir hata alırsanız aşağıdaki tablo, olası nedeni belirlemenize yardımcı olabilir ve sorunu gidermek için önerilen adımları uygulayın. `AZCM0000`Devam etmek için konsola veya betik çıktısına yazdırılmış ("0000" herhangi bir 4 basamaklı sayı olabilir) hata kodu gerekir.

| Hata kodu | Olası neden | Önerilen düzeltme |
|------------|----------------|-----------------------|
| AZCM0000 | Eylem başarılı oldu | Yok |
| AZCM0001 | Bilinmeyen bir hata oluştu | Daha fazla yardım için Microsoft Desteği başvurun |
| AZCM0011 | Kullanıcı eylemi iptal etti (CTRL + C) | Önceki komutu yeniden dene |
| AZCM0012 | Belirtilen erişim belirteci geçersiz | Yeni bir erişim belirteci edinin ve yeniden deneyin |
| AZCM0013 | Girilen Etiketler geçersiz | Etiketlerin virgülle ayırarak ve boşluklar içeren adların veya değerlerin tek tırnak içine alınmış olduğunu kontrol edin: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Bulut geçersiz | Desteklenen bir bulut belirtin: `AzureCloud` veya `AzureUSGovernment` |
| AZCM0015 | Belirtilen bağıntı KIMLIĞI geçerli bir GUID değil | İçin geçerli bir GUID belirtin `--correlation-id` |
| AZCM0016 | Zorunlu bir parametre eksik | Hangi parametrelerin eksik olduğunu belirlemek için çıktıyı gözden geçirin |
| AZCM0017 | Kaynak adı geçersiz | Yalnızca alfasayısal karakter, kısa çizgi ve/veya alt çizgi kullanan bir ad belirtin. Ad, kısa çizgi veya alt çizgi ile bitemez. |
| AZCM0018 | Komut yönetici ayrıcalıkları olmadan yürütüldü | Yükseltilmiş bir komut isteminde veya konsol oturumunda komutu yönetici veya kök ayrıcalıklarıyla yeniden deneyin. |
| AZCM0041 | Sağlanan kimlik bilgileri geçersiz | Cihaz oturum açmaları için, belirtilen kullanıcı hesabının, sunucu kaynağının oluşturulacağı kiracıya ve aboneliğe erişimi olduğunu doğrulayın. Hizmet sorumlusu oturum açmaları için, istemci KIMLIĞINI ve gizli anahtarı doğruluğunu, gizli dizinin sona erme tarihini ve hizmet sorumlunun sunucu kaynağının oluşturulacağı kiracıdan olduğunu denetleyin. |
| AZCM0042 | Yay etkin sunucu kaynağı oluşturulamadı | Belirtilen kullanıcı/hizmet sorumlusunun belirtilen kaynak grubunda yay özellikli sunucu kaynakları oluşturma erişimi olduğunu doğrulayın. |
| AZCM0043 | Yay etkin sunucu kaynağını silme başarısız | Belirtilen kullanıcı/hizmet sorumlusunun, belirtilen kaynak grubundaki yay etkin sunucu kaynaklarını silme erişimi olduğunu doğrulayın. Kaynak artık Azure 'da yoksa, `--force-local-only` devam etmek için bayrağını kullanın. |
| AZCM0044 | Aynı ada sahip bir kaynak zaten var | Parametre için farklı bir ad belirtin `--resource-name` veya Azure 'da var olan yay etkin sunucuyu silip yeniden deneyin. |
| AZCM0061 | Aracı hizmetine ulaşılamıyor | Komutunu yükseltilmiş bir kullanıcı bağlamında (yönetici/kök) kullandığınızı ve HıMDS hizmetinin sunucunuzda çalıştığını doğrulayın. |
| AZCM0062 | Sunucu bağlanırken bir hata oluştu | Daha ayrıntılı bilgi için çıkışdaki diğer hata kodlarını gözden geçirin. Azure kaynağı oluşturulduktan sonra hata oluştuysa, yeniden denemeden önce yay sunucusunu kaynak grupınızdan silmeniz gerekir. |
| AZCM0063 | Sunucunun bağlantısı kesilirken bir hata oluştu | Daha ayrıntılı bilgi için çıkışdaki diğer hata kodlarını gözden geçirin. Bu hatayla karşılaşmaya devam ederseniz, kaynağı Azure 'da silebilir ve sonra `azcmagent disconnect --force-local-only` aracının bağlantısını kesmek için sunucuda çalıştırabilirsiniz. |
| AZCM0064 | Aracı hizmeti yanıt vermiyor | `himds`Çalıştığından emin olmak için hizmetin durumunu denetleyin. Çalışmıyorsa hizmeti başlatın. Çalışıyorsa bir dakika bekleyip yeniden deneyin. |
| AZCM0065 | Bir iç aracı iletişim hatası oluştu | Yardım için Microsoft Desteği başvurun |
| AZCM0066 | Aracı Web hizmeti yanıt vermiyor veya kullanılamıyor | Yardım için Microsoft Desteği başvurun |
| AZCM0067 | Aracı zaten Azure 'a bağlı | [Aracının bağlantısını kesme](manage-agent.md#unregister-machine) bölümündeki adımları izleyin ve sonra yeniden deneyin. |
| AZCM0068 | Sunucunun Azure bağlantısı kesilirken bir iç hata oluştu | Yardım için Microsoft Desteği başvurun |
| AZCM0081 | Azure Active Directory yönetilen kimlik sertifikası indirilirken bir hata oluştu | Sunucuyu Azure 'a bağlamaya çalışırken bu iletiyle karşılaşılırsa, aracı Azure Arc hizmeti ile iletişim kuramaz. Kaynağı Azure 'da silip yeniden bağlanmayı deneyin. |
| AZCM0101 | Komut başarıyla ayrıştırılamadı | `azcmagent <command> --help`Doğru komut sözdizimini gözden geçirmek için komutunu çalıştırın |
| AZCM0102 | Bilgisayar ana bilgisayar adı alınamıyor | `hostname`Sistem düzeyindeki hata iletilerini denetlemek için öğesini çalıştırın, sonra Microsoft desteği başvurun. |
| AZCM0103 | RSA anahtarları üretilirken bir hata oluştu | Yardım için Microsoft Desteği başvurun |
| AZCM0104 | Sistem bilgileri okunamadı | Çalıştırmak için kullanılan kimliğin `azcmagent` sistemde yönetici/kök ayrıcalıklarına sahip olduğundan emin olun ve yeniden deneyin. |

## <a name="agent-verbose-log"></a>Aracı ayrıntılı günlüğü

Bu makalenin ilerleyen kısımlarında açıklanan sorun giderme adımlarını takip etmeden önce, ihtiyacınız olan en düşük bilgiler ayrıntılı günlüğdeğer olacaktır. Verbose (-v) bağımsız değişkeni kullanıldığında **azcmagent** aracı komutlarının çıktısını içerir. Günlük dosyaları `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` Windows ve Linux için üzerine yazılır `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Aşağıda, etkileşimli bir yükleme gerçekleştirirken Windows için bağlı makine Aracısı ile ayrıntılı günlüğe yazmayı etkinleştirme komutuna bir örnek verilmiştir.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Aşağıda, bir hizmet sorumlusu kullanarak ölçekli bir yükleme gerçekleştirirken Windows için bağlı makine Aracısı ile ayrıntılı günlüğe yazmayı etkinleştirme komutuna bir örnek verilmiştir.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Aşağıda, etkileşimli bir yükleme gerçekleştirirken Linux için bağlı makine Aracısı ile ayrıntılı günlüğe yazmayı etkinleştirme komutuna bir örnek verilmiştir.

>[!NOTE]
>**Azcmagent** çalıştırmak için Linux makinelerde *kök* erişim izinlerine sahip olmanız gerekir.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Aşağıda, bir hizmet sorumlusu kullanarak ölçekli bir yükleme gerçekleştirirken Linux için bağlı makine Aracısı ile ayrıntılı günlüğe yazmayı etkinleştirme komutuna bir örnek verilmiştir.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Hizmete yönelik aracı bağlantısı sorunları

Aşağıdaki tabloda, bilinen hatalardan bazıları ve bunların nasıl giderileceği ve çözüleceği ile ilgili öneriler listelenmektedir.

|İleti |Hata |Olası neden |Çözüm |
|--------|------|---------------|---------|
|Yetkilendirme belirteci cihaz akışı alınamadı |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |`login.windows.net`Uç noktaya ulaşılamıyor | Uç nokta bağlantısını doğrulayın. |
|Yetkilendirme belirteci cihaz akışı alınamadı |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy veya güvenlik duvarı `login.windows.net` uç noktaya erişimi engelliyor. | Uç nokta bağlantısını doğrulayın ve bir güvenlik duvarı veya proxy sunucusu tarafından engellenmiyor. |
|Yetkilendirme belirteci cihaz akışı alınamadı  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Grup ilkesi nesne *Bilgisayar Yapılandırması \ Yönetim Şablonları \ sistem \ Kullanıcı profilleri \ sistem yeniden başlatıldığında belirtilen gün sayısından daha eski Kullanıcı profillerini Sil* etkin. | GPO 'nun etkinleştirildiğini ve etkilenen makinenin hedeflendiğini doğrulayın. Daha fazla ayrıntı için bkz. dipnot <sup>[1](#footnote1)</sup> . |
|SPN 'den yetkilendirme belirteci alınamadı |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy veya güvenlik duvarı `login.windows.net` uç noktaya erişimi engelliyor. |Uç nokta bağlantısını doğrulayın ve bir güvenlik duvarı veya proxy sunucusu tarafından engellenmiyor. |
|SPN 'den yetkilendirme belirteci alınamadı |`Invalid client secret is provided` |Yanlış veya geçersiz hizmet sorumlusu gizli anahtarı. |Hizmet sorumlusu gizliliğini doğrulayın. |
| SPN 'den yetkilendirme belirteci alınamadı |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Yanlış hizmet sorumlusu ve/veya kiracı KIMLIĞI. |Hizmet sorumlusu ve/veya kiracı KIMLIĞINI doğrulayın.|
|ARM kaynak yanıtını al |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Yanlış kimlik bilgileri ve/veya izinler |**Azure bağlı makine ekleme** rolünün bir üyesi olduğunu veya hizmet sorumlunun olduğunu doğrulayın. |
|ARM kaynağı AzcmagentConnect başarısız oldu |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure kaynak sağlayıcıları kayıtlı değil. |[Kaynak sağlayıcılarını](./agent-overview.md#register-azure-resource-providers)kaydedin. |
|ARM kaynağı AzcmagentConnect başarısız oldu |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxy sunucusu veya güvenlik duvarı `management.azure.com` uç noktaya erişimi engelliyor. |Uç nokta bağlantısını doğrulayın ve bir güvenlik duvarı veya proxy sunucusu tarafından engellenmiyor. |

<a name="footnote1"></a><sup>1</sup> Bu GPO etkin ve bağlı makine aracısına sahip makineler için geçerliyse, *hımds* hizmeti için belirtilen yerleşik hesapla ilişkili kullanıcı profilini siler. Sonuç olarak, yerel sertifika deposunda önbelleğe alınan hizmetle iletişim kurmak için kullanılan kimlik doğrulama sertifikasını 30 gün boyunca de siler. 30 günlük sınırından önce, sertifikayı yenilemek için bir girişimde bulunuldu. Bu sorunu çözmek için, [makinenin kaydını silme](manage-agent.md#unregister-machine) adımlarını izleyin ve ardından çalıştıran hizmete yeniden kaydedin `azcmagent connect` .

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* [Microsoft Q&A](/answers/topics/azure-arc.html)aracılığıyla Azure uzmanlarından yanıtlar alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.

* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
