# Installation et Configuration de ModSecurity sur Apache (Ubuntu)

## 📋 Description

Ce laboratoire présente un guide complet pour installer et configurer **ModSecurity** (Web Application Firewall) sur un serveur Apache sous Ubuntu. Le projet inclut également l'intégration des règles **OWASP Core Rule Set (CRS)** et des simulations d'attaques pour tester l'efficacité de la protection.

**Auteur :** Wafae Bouajaja  
**Programme :** Master Intelligence Artificielle & CyberSécurité

---

## 🎯 Objectifs

- Installer et configurer un serveur Apache
- Déployer ModSecurity comme WAF (Web Application Firewall)
- Intégrer les règles OWASP CRS pour la détection d'attaques
- Tester la sécurité via des simulations d'attaques (SQL Injection, XSS, etc.)

---

## 🛠️ Prérequis

- **OS :** Ubuntu 20.04 LTS ou supérieur
- **Serveur Web :** Apache2
- **Outils :** Git, Webmin (optionnel)
- **Accès :** Privilèges sudo/root

---

## 📦 Installation

### Étape 1 : Installation de Webmin 

Webmin facilite la gestion graphique du serveur.

```bash
# Télécharger et installer Webmin
wget http://www.webmin.com/download/deb/webmin-current.deb
sudo dpkg -i webmin-current.deb
sudo apt-get install -f
```

Accès : `https://<votre-ip>:10000`

### Étape 2 : Installation d'Apache

```bash
# Mettre à jour les paquets
sudo apt update

# Installer Apache
sudo apt install apache2 -y

# Vérifier le statut
sudo systemctl status apache2

# Activer Apache au démarrage
sudo systemctl enable apache2
```

### Étape 3 : Configuration d'un serveur virtuel

```bash
# Créer le répertoire du site
sudo mkdir -p /var/www/monsite

# Créer une page index.html
sudo nano /var/www/monsite/index.html
```

Exemple de contenu :
```html
<!DOCTYPE html>
<html>
<head>
    <title>Mon Site Protégé</title>
</head>
<body>
    <h1>Bienvenue sur mon serveur Apache avec ModSecurity</h1>
</body>
</html>
```

Configurer le VirtualHost :
```bash
sudo nano /etc/apache2/sites-available/monsite.conf
```

```apache
<VirtualHost *:80>
    ServerName monsite.local
    DocumentRoot /var/www/monsite
    
    <Directory /var/www/monsite>
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/monsite_error.log
    CustomLog ${APACHE_LOG_DIR}/monsite_access.log combined
</VirtualHost>
```

Activer le site :
```bash
sudo a2ensite monsite.conf
sudo systemctl reload apache2
```

### Étape 4 : Installation de ModSecurity

```bash
# Ajouter le PPA 
sudo add-apt-repository ppa:ondrej/apache2
sudo apt update

# Installer ModSecurity
sudo apt install libapache2-mod-security2 -y

# Vérifier l'installation
sudo apachectl -M | grep security

# Activer le module
sudo a2enmod security2

# Redémarrer Apache
sudo systemctl restart apache2
```

### Étape 5 : Configuration de ModSecurity

```bash
# Copier le fichier de configuration recommandé
sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

# Éditer la configuration
sudo nano /etc/modsecurity/modsecurity.conf
```

Modifier la ligne suivante pour activer ModSecurity :
```
SecRuleEngine On
```

### Étape 6 : Installation des règles OWASP CRS

```bash
# Installer Git si nécessaire
sudo apt install git -y

# Télécharger OWASP CRS
cd /etc/modsecurity
sudo git clone https://github.com/coreruleset/coreruleset.git

# Copier le fichier de configuration
cd coreruleset
sudo cp crs-setup.conf.example crs-setup.conf

# Créer un lien symbolique vers les règles
sudo ln -s /etc/modsecurity/coreruleset/rules /etc/modsecurity/rules
```

Configurer Apache pour charger les règles :
```bash
sudo nano /etc/apache2/mods-enabled/security2.conf
```

Ajouter :
```apache
IncludeOptional /etc/modsecurity/coreruleset/crs-setup.conf
IncludeOptional /etc/modsecurity/coreruleset/rules/*.conf
```

Redémarrer Apache :
```bash
sudo systemctl restart apache2
```

---

## 🧪 Tests de Sécurité

### Simulation 1 : Injection SQL classique

Tester avec une requête malveillante :
```
http://votre-serveur/?id=1' OR '1'='1
```

**Résultat attendu :** HTTP 403 Forbidden (Requête bloquée par ModSecurity)

### Simulation 2 : Cross-Site Scripting (XSS)

```
http://votre-serveur/?search=<script>alert('XSS')</script>
```

### Simulation 3 : Path Traversal

```
http://votre-serveur/?file=../../../etc/passwd
```

### Vérification des logs

```bash
# Consulter les logs ModSecurity
sudo tail -f /var/log/apache2/modsec_audit.log

# Logs Apache généraux
sudo tail -f /var/log/apache2/error.log
```

---

## 📊 Structure du Projet

```
modsecurity-apache-lab/
├── README.md
├── docs/
│   └── Lab2.pdf (Documentation complète)
├── configs/
│   ├── modsecurity.conf (Configuration ModSecurity)
│   ├── virtualhost.conf (Exemple VirtualHost)
│   └── custom-rules/ (Règles personnalisées)
├── tests/
│   └── test-attacks.sh (Script de test d'attaques)
└── screenshots/
    └── (Captures d'écran des configurations)
```

---

## 🔒 Règles OWASP CRS Activées

Les règles OWASP Core Rule Set protègent contre :

- **SQL Injection (SQLi)**
- **Cross-Site Scripting (XSS)**
- **Local File Inclusion (LFI)**
- **Remote File Inclusion (RFI)**
- **Remote Code Execution (RCE)**
- **HTTP Protocol Violations**
- **Session Fixation**


---

## ✉️ Contact

**Wafae Bouajaja**  
Master Intelligence Artificielle & CyberSécurité



