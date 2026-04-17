Fonksiyonun Kullanımı ve Operasyonel Komutlar
1. Fonksiyonu Hafızaya Alma:

PowerShell
Import-Module .\enumeration-LDAPquery.ps1
2. Tüm Kullanıcıları Listeleme:

PowerShell
LDAPSearch -LDAPQuery "(samAccountType=805306368)"
3. Belirli Bir Grubu ve Üyelerini (Nested Dahil) Bulma:
net group komutu sadece doğrudan üyeleri gösterirken, bu yöntemle iç içe geçmiş (nested) grupları da görebilirsin.

PowerShell
$targetGroup = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"
$targetGroup.properties.member
4. Tüm Grupları ve Üyelerini Ekrana Basma:

PowerShell
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
    $group.properties | select {$_.cn}, {$_.member}
}
