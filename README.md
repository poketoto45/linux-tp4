## 1 Installation de la VirtualBox

### 1.1 Configuration réseau

* **Mode :** Bridged Adapter (Accès par pont).
* **Vérification :** `ip a` dans la VM et `ping <IP_VM>` depuis l'hôte.

---

## 2. Installation et configuration du serveur SSH

### 2.1 Installation d’OpenSSH

```bash
sudo apt update && sudo apt install openssh-server
```


### 2.2 Vérification du service

```bash
sudo systemctl status ssh
sudo ss -tlnp | grep ssh

```

---

## 3. Première connexion SSH

Connexion classique par mot de passe :

```bash
ssh etudiant@<IP_VM>

```

---

## 4. Authentification par clé

### 4.1 Génération de clé

```bash
ssh-keygen -t ed25519

```

* **Clé privée :** Reste sur le client.
* **Clé publique :** Copiée sur le serveur.

### 4.2 Copier la clé vers le serveur

```bash
ssh-copy-id etudiant@<IP_VM>

```

### 4.3 Test

Connexion instantanée sans mot de passe.

---

## 5. Sécurisation du serveur

Édition du fichier `/etc/ssh/sshd_config` :

* `PasswordAuthentication no`
* `PermitRootLogin no`
* `Port 2222`

Redémarrage : `sudo systemctl restart ssh`

![test port 2222](https://github.com/poketoto45/linux-tp4/blob/main/image/connection%20ssh%202222.png)

---

## 6. Configuration d’un alias SSH

Simplifier la connexion via `~/.ssh/config` :

```text
Host serveur-tp
    HostName <IP_VM>
    User etudiant
    Port 2222

``

![alias](https://github.com/poketoto45/linux-tp4/blob/main/image/definition%20alias.png)

Connexion : `ssh serveur-tp`

![test alias](https://github.com/poketoto45/linux-tp4/blob/main/image/alias%20.png)

---

## 7. Transfert de fichiers

* **SCP :** `scp fichier.txt serveur-tp:/home/etudiant/`
* **SFTP :** `sftp serveur-tp` (Commandes `put`, `get`, `ls`)
* **RSYNC :** `rsync -avz dossier/ serveur-tp:/home/etudiant/`

---

## 8. Analyse des logs

Suivi en temps réel des tentatives de connexion :

```bash
sudo tail -f /var/log/auth.log

```
![log1](https://github.com/poketoto45/linux-tp4/blob/main/image/HTTPS%20avec%20certificat%20auto-signé.png)

![log2](https://github.com/poketoto45/linux-tp4/blob/main/image/tail%20encore%20%2B.png)
---

## 9. Installation et analyse de Fail2Ban

Protection contre les attaques par force brute :

```bash
sudo apt install fail2ban
sudo fail2ban-client status sshd

```
![fail2ban](https://github.com/poketoto45/linux-tp4/blob/main/image/fail2ban.png)
---

## 10. Tunnel SSH

### 10.1 Tunnel local

Rediriger le port 80 du serveur vers le 8080 du client :

```bash
ssh -L 8080:localhost:80 serveur-tp

```

### 10.2 Tunnel distant

```bash
ssh -R 9090:localhost:22 serveur-tp

```

---

## 11. Simulation d’incidents et diagnostic

Tests de pannes : service arrêté, mauvais port, permissions `chmod` incorrectes sur `.ssh`.

---

## 12. Résultat attendu (Checklist)

* [x] SSH sur port 2222 uniquement.
* [x] Accès Root interdit.
* [x] Mot de passe désactivé.
* [x] Fail2Ban opérationnel.

---

## 13. Installation et configuration Nginx

### 13.1 Installation

```bash
sudo apt install nginx

```
![Nginx](https://github.com/poketoto45/linux-tp4/blob/main/image/install%20status%20Nginx%20.png)


![conf Nginx](https://github.com/poketoto45/linux-tp4/blob/main/image/configuration%20Nginx%20.png)
### 13.2 Page test

```bash
sudo mkdir -p /var/www/site-tp
sudo vim /var/www/site-tp/index.html

```
![testt nginx](https://github.com/poketoto45/linux-tp4/blob/main/image/diffution%20par%20Nginx%20.png)

### 13.3 Configuration Vhost

Activation du site dans `/etc/nginx/sites-available/site-tp` et création du lien symbolique.

---

## 14. HTTPS avec certificat auto-signé

### 14.1 Génération du certificat

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout site-tp.key -out site-tp.crt

```

### 14.2 Configuration Nginx HTTPS

Configuration du port 443 et redirection automatique du port 80 vers 443.

![Nginx](https://github.com/poketoto45/linux-tp4/blob/main/image/HTTPS%20avec%20certificat%20auto-signé.png)

---

## 15. Firewall et sécurité

Ouverture des ports Web :

```bash
sudo ufw allow 'Nginx Full'
sudo chown -R www-data:www-data /var/www/site-tp

```
![FireWall](https://github.com/poketoto45/linux-tp4/blob/main/image/Firewall%20ufw.png)
---

## 16. Résultat attendu final

* Nginx accessible en HTTPS.
* Redirection HTTP vers HTTPS fonctionnelle.
* Firewall actif et configuré.

---
