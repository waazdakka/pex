# StarLord - Partage sécurisé de mots de passe

Application web sécurisée pour partager des mots de passe de manière temporaire et chiffrée.

## 🚀 Fonctionnalités

- **Chiffrement Sodium** : Les mots de passe sont chiffrés avec libsodium (cryptographie moderne)
- **Expiration temporelle** : Configurez la durée de validité (10 min à 1 mois)
- **Limite de consultations** : Définissez un nombre maximum d'affichages
- **Génération automatique** : Créez des mots de passe sécurisés (12, 18, 24 caractères)
- **Envoi par email** : Partagez automatiquement le lien par email
- **Interface responsive** : Design moderne avec Bootstrap 5
- **Suppression automatique** : Nettoyage des mots de passe expirés

## 📋 Prérequis

- PHP 8.2 ou supérieur
- MariaDB/MySQL
- Extension PHP Sodium (généralement incluse par défaut en PHP 8.2)
- Extension PHP PDO MySQL
- Serveur SMTP pour l'envoi d'emails

## 🔧 Installation

### 1. Cloner/déployer le projet

Placez tous les fichiers dans votre répertoire web.

### 2. Créer le fichier .env

```env
DB_HOST=localhost
DB_NAME=starlord
DB_USER=starlord
DB_PASS=votre_mot_de_passe
SODIUM_KEY=kElJB2SwmRls4oU0BxtiJl4e7MlDfAxqLxKWuBf9Cto=
MAIL_FROM=starlord@piedallu.me
MAIL_SMTP_HOST=smtp.piedallu.me
MAIL_SMTP_USER=raphael
MAIL_SMTP_PASS=votre_mot_de_passe_smtp
MAIL_SMTP_PORT=587
```

**Important** : Générez une nouvelle clé Sodium avec :
```php
<?php
echo base64_encode(sodium_crypto_secretbox_keygen());
```

### 3. Créer la base de données

```sql
CREATE DATABASE starlord CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'starlord'@'localhost' IDENTIFIED BY 'votre_mot_de_passe';
GRANT ALL PRIVILEGES ON starlord.* TO 'starlord'@'localhost';
FLUSH PRIVILEGES;
```

### 4. Installer les tables

Accédez à : `http://votre-domaine.com/install.php`

Suivez les instructions et supprimez ensuite le fichier `install.php`.

### 5. Configuration du nettoyage automatique

Ajoutez dans votre crontab :
```bash
0 */1 * * * php /chemin/vers/starlord/cleanup.php
```

## 📁 Structure du projet

```
starlord/
├── config/
│   ├── database.php       # Connexion BDD
│   └── env.php            # Chargement variables d'environnement
├── includes/
│   ├── functions.php      # Fonctions métier
│   └── mailer.php         # Envoi d'emails
├── assets/
│   ├── css/
│   │   └── style.css      # Styles personnalisés
│   ├── js/
│   │   └── main.js        # JavaScript principal
│   └── img/               # Images et logo
├── api/
│   ├── delete.php         # Suppression de mot de passe
│   └── send-mail.php      # Envoi d'email
├── logs/                  # Logs de nettoyage
├── .env                   # Configuration (à créer)
├── .htaccess             # Sécurité Apache
├── index.php             # Page principale
├── view.php              # Page de visualisation
├── install.php           # Installation initiale
├── cleanup.php           # Nettoyage automatique
└── README.md             # Cette documentation
```

## 🔒 Sécurité

### Mesures implémentées

1. **Chiffrement** : Utilisation de Sodium (ChaCha20-Poly1305)
2. **Tokens uniques** : Génération aléatoire cryptographiquement sûre
3. **Requêtes préparées** : Protection contre les injections SQL
4. **Sanitization** : Nettoyage des entrées utilisateur
5. **Headers HTTP** : X-XSS-Protection, X-Frame-Options, CSP
6. **Fichiers sensibles** : Protection via .htaccess
7. **HTTPS** : Recommandé en production (à activer dans .htaccess)

### Recommandations supplémentaires

1. **Certificat SSL** : Utilisez HTTPS en production
2. **Permissions** : 
   - Fichiers : 644
   - Dossiers : 755
   - .env : 600
3. **Firewall** : Limitez l'accès à la base de données
4. **Sauvegardes** : Sauvegardez régulièrement la base de données
5. **Logs** : Surveillez les logs d'erreurs
6. **Updates** : Maintenez PHP et les librairies à jour

## ⚙️ Configuration SMTP

### Pour Gmail

```env
MAIL_SMTP_HOST=smtp.gmail.com
MAIL_SMTP_USER=votre-email@gmail.com
MAIL_SMTP_PASS=mot_de_passe_application
MAIL_SMTP_PORT=587
```

**Note** : Activez l'authentification à deux facteurs et créez un mot de passe d'application.

### Pour un serveur personnalisé

Utilisez les paramètres fournis par votre hébergeur.

## 🎨 Personnalisation

### Logo

Placez votre logo dans `assets/img/logo.png`

### Textes

Modifiez les textes dans :
- `index.php` : Ligne avec la classe `.subtitle`
- `view.php` : Ligne avec la classe `.subtitle`

### Couleurs

Modifiez les variables CSS dans `assets/css/style.css` :
```css
:root {
    --primary-color: #007bff;
    --secondary-color: #6c757d;
    --danger-color: #dc3545;
    --success-color: #28a745;
}
```

## 📝 Utilisation

### Créer un mot de passe

1. Remplissez le formulaire sur la page d'accueil
2. Choisissez la durée de validité et les options
3. Cliquez sur "Générer le mot de passe"
4. Partagez l'URL générée

### Consulter un mot de passe

1. Accédez au lien partagé
2. Le mot de passe s'affiche (masqué par défaut)
3. Cliquez sur l'œil pour l'afficher
4. Utilisez les boutons de copie pour faciliter l'usage

### Supprimer un mot de passe

Cliquez sur "Supprimer le mot de passe" en bas de la page de visualisation.

## 🐛 Dépannage

### Les emails ne s'envoient pas

1. Vérifiez la configuration SMTP dans `.env`
2. Testez avec un outil comme `telnet smtp.example.com 587`
3. Vérifiez les logs PHP : `/var/log/php_errors.log`
4. Assurez-vous que PHP peut utiliser `mail()` ou configurez PHPMailer

### Erreurs de base de données

1. Vérifiez les identifiants dans `.env`
2. Assurez-vous que la base existe
3. Vérifiez les permissions de l'utilisateur MySQL
4. Testez la connexion : `mysql -u starlord -p starlord`

### Erreurs de chiffrement

Générez une nouvelle clé Sodium :
```php
<?php echo base64_encode(sodium_crypto_secretbox_keygen());
```

## 📄 Licence

Ce projet est sous licence MIT. Libre d'utilisation et de modification.

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

## 📞 Support

Pour toute question ou problème, créez une issue sur le dépôt GitHub.

---

**Développé avec ❤️ pour la sécurité de vos mots de passe**
