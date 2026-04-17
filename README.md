## 🛠️ Temel .NET Sınıfları

AD içerisinde arama yapmak için `System.DirectoryServices` altındaki iki ana sınıf kullanılır:

- **DirectoryEntry:** Aramanın nereden başlayacağını (Search Root) belirler. LDAP yolunu encapsulate eder.
    
- **DirectorySearcher:** Belirlenen root üzerinde LDAP filtrelerini kullanarak sorgu çalıştırır. `FindAll()` metodu ile sonuçları döner.
    

## 🔍 Filtreleme ve samAccountType

Aramaları daraltmak için en etkili yöntem `samAccountType` özniteliğini kullanmaktır.

|**Hesap Türü**|**Kod (Decimal)**|**Açıklama**|
|---|---|---|
|**User**|`805306368`|Normal kullanıcı hesapları|
|**Group**|`268435456`|Grup nesneleri|
|**Computer**|`805306369`|Bilgisayar hesapları|

---

## 🚀 Esnek Arama Fonksiyonu (LDAPSearch)

[Bu fonksiyonu](https://github.com/frukansen/OSCP-Active-Directory-Scripts/blob/main/enumeration-LDAPquery) bir kez tanımladıktan sonra oturum boyunca hızlıca sorgu atmak için kullanabilirsin.
### Fonksiyonun Kullanımı ve Operasyonel Komutlar

**1. Fonksiyonu Hafızaya Alma:**

PowerShell

```
Import-Module .\enumeration.ps1
```

**2. Tüm Kullanıcıları Listeleme:**

PowerShell

```
LDAPSearch -LDAPQuery "(samAccountType=805306368)"
```

**3. Belirli Bir Grubu ve Üyelerini (Nested Dahil) Bulma:** `net group` komutu sadece doğrudan üyeleri gösterirken, bu yöntemle iç içe geçmiş (nested) grupları da görebilirsin.

PowerShell

```
$targetGroup = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"
$targetGroup.properties.member
```

**4. Tüm Grupları ve Üyelerini Ekrana Basma:**

PowerShell

```
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
    $group.properties | select {$_.cn}, {$_.member}
}
```

---

## ⚠️ Kritik Bilgi: Nested Groups (İç İçe Gruplar)

- `net.exe` gibi araçlar sadece o gruba doğrudan atanmış kullanıcıları listeler.
    
- Eğer **Grup A**, **Grup B**'nin üyesiyse; Grup A'daki kullanıcılar Grup B'nin izinlerini devralır.
    
- Bu script ile `member` özniteliğini kontrol ederek, grup içinde grup olup olmadığını tespit edebilir ve privilege escalation yolları bulabilirsin.
    

🔗 **Tam Script İçin:** [OSCP-Active-Directory-Scripts/enumeration-LDAPquery.ps1](https://github.com/frukansen/OSCP-Active-Directory-Scripts/blob/main/enumeration-LDAPquery)
