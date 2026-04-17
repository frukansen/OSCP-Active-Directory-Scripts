## 🛠️ Core .NET Classes

To perform searches within AD, two main classes under `System.DirectoryServices` are used:

- **DirectoryEntry:** Determines where the search will begin (Search Root). It encapsulates the LDAP path.
    
- **DirectorySearcher:** Executes queries on the specified root using LDAP filters. It returns the results via the `FindAll()` method.
    

## 🔍 Filtering and samAccountType

The most effective method for narrowing down searches is using the `samAccountType` attribute.

|**Account Type**|**Code (Decimal)**|**Description**|
|---|---|---|
|**User**|`805306368`|Standard user accounts|
|**Group**|`268435456`|Group objects|
|**Computer**|`805306369`|Computer accounts|

---

## 🚀 Flexible Search Function (LDAPSearch)

Once you define [this function](https://github.com/frukansen/OSCP-Active-Directory-Scripts/blob/main/enumeration-LDAPquery), you can use it to quickly run queries throughout your session.

### Function Usage and Operational Commands

**1. Loading the Function into Memory:**

PowerShell

```
Import-Module .\enumeration.ps1
```

**2. Listing All Users:**

PowerShell

```
LDAPSearch -LDAPQuery "(samAccountType=805306368)"
```

**3. Finding a Specific Group and Its Members (Including Nested):** While the `net group` command only shows direct members, this method allows you to discover nested groups as well.

PowerShell

```
$targetGroup = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"
$targetGroup.properties.member
```

**4. Printing All Groups and Their Members:**

PowerShell

```
foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
    $group.properties | select {$_.cn}, {$_.member}
}
```

---

## ⚠️ Critical Info: Nested Groups

- Tools like `net.exe` only list users directly assigned to that group.
    
- If **Group A** is a member of **Group B**, the users in Group A inherit the permissions of Group B.
    
- By checking the `member` attribute with this script, you can detect if there are groups within groups and identify potential privilege escalation paths.
    

🔗 **For the Full Script:** [OSCP-Active-Directory-Scripts/enumeration-LDAPquery.ps1](https://github.com/frukansen/OSCP-Active-Directory-Scripts/blob/main/enumeration-LDAPquery)
