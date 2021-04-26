---
title: Azure 'da sorun giderme | Microsoft Docs
description: Bu makalede, Azure savunma sorunlarını giderme hakkında bilgi edinin.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 23b7a66afcc91cf1cf4a5dd9f720aad24ad40071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133999"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion sorunlarını giderme

Bu makalede, Azure savunma sorunlarını giderme işlemi gösterilmektedir.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>AzureBastionSubnet üzerinde NSG oluşturulamıyor

**S:** Azure savunma alt ağında bir NSG oluşturmayı denediğimde şu hatayı alıyorum: *' ağ güvenlik grubu, <NSG name> Azure savunma alt ağı için gerekli kurallara sahip değil AzureBastionSubnet "*.

Y **:** *AzureBastionSubnet*'e BIR NSG oluşturup uygularsanız, NSG 'ye gerekli kuralları eklediğinizden emin olun. Gerekli kuralların listesi için bkz. [NSG erişimiyle çalışma ve Azure](./bastion-nsg.md)savunma. Bu kuralları eklemedikçe NSG oluşturma/güncelleştirme başarısız olur.

NSG kurallarına bir örnek [hızlı başlangıç şablonunda](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)başvuru için kullanılabilir.
Daha fazla bilgi için bkz. [Azure Için NSG Kılavuzu](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Azure ile SSH anahtarımı kullanılamıyor

**S:** SSH anahtar dosyasına gözatmaya çalıştığımda, şu hatayı alıyorum: *' SSH özel anahtarı-----başlangıç RSA özel anahtar-----ile başlamalı ve-----End RSA özel anahtar-----' ile sona ermelidir*.

Y **:** Azure savunma yalnızca RSA SSH anahtarlarını destekler, bu noktada zaman içinde. Hedef VM 'de sağlanan ortak anahtarla SSH için RSA özel anahtarı olan bir anahtar dosyasına gözattığınızdan emin olun. 

Örnek olarak, yeni bir RSA SSH anahtarı oluşturmak için aşağıdaki komutu kullanabilirsiniz:

**SSH-keygen-t RSA-b 4096-C " email@domain.com "**

Çıkış:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Windows etki alanına katılmış sanal makinmda oturum açılamıyor

**S:** Etki alanına katılmış Windows sanal makinmem ile bağlantı kuramıyor.

Y **:** Azure savunma, etki alanına katılmış VM 'nin Kullanıcı adı için oturum açmasını destekler-yalnızca parola tabanlı etki alanı oturum açma. Azure portal etki alanı kimlik bilgilerini belirtirken, username@domain oturum açmak için *etkialanı \ KullanıcıAdı* biçiminde değil UPN () biçimini kullanın. Bu, etki alanına katılmış veya hibrit katılmış (hem etki alanına katılmış hem de Azure AD 'ye katılmış) sanal makineler için desteklenir. Yalnızca Azure AD 'ye katılmış sanal makinelerde desteklenmez.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Dosya aktarımı sorunları

**S:** Dosya aktarımı Azure ile destekleniyor mu?

Y **:** Dosya aktarımı Şu anda desteklenmiyor. Destek eklemek için çalışıyoruz.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure portal siyah ekran

**S:** Azure savunma 'yı kullanarak bağlanmayı denediğimde, hedef VM 'ye bağlanamıyorum ve Azure portal bir siyah ekran alıyorum.

Y **:** Bu durum, Web tarayıcınız ile Azure savunma arasında bir ağ bağlantısı sorunu olduğunda (istemci Internet güvenlik duvarınız WebSockets trafiği engelliyor veya benzer şekilde) ya da Azure savunma ile hedef sanal makinenizin arasında bir ağ bağlantısı sorunu olduğunda meydana gelir. Çoğu durumda, AzureBastionSubnet 'e veya sanal ağınızdaki RDP/SSH trafiğini engelleyen hedef VM alt ağına uygulanan bir NSG bulunur. İstemci internet güvenlik duvarınız üzerinde WebSockets trafiğe izin verin ve hedef VM alt ağınızdaki NSG 'leri kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. savunma [hakkında SSS](bastion-faq.md).
