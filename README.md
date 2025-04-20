# specs_dl_wordpress

Exemple de specs pour un site Wordpress. Si jamais vous tombez un peu par hasard sur ce repo, n'hésitez pas à lire [cet article](https://www.mightandmetrics.io/blog/ecrire_specs) qui explique le pourquoi du comment de la démarche.

## Snippet GTM et règles CSP - Web

### CSP

Les directives [Content Security Policy suivantes](https://developers.google.com/tag-manager/web/csp) doivent être mises en place afin de prermettre la bonne exécution des tags depuis GTM : 

### Snippet GTM

Insérer ce code aussi haut que possible dans le ```<head>``` de la page : 

```javascript
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-PF7MN30');</script>
```

Insérer ce code aussi haut que possible dans le ```<body>``` :

```javascript
<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-PF7MN30"
height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
```

Pour valider la bonne installation de GTM, suivre les étapes suivantes : 

- S'assurer que l'on a bien supprimé / désactivé tout plugin qui pourrait bloquer le déclenchement de Google Tag Manager (ex : uBlock) 
- Vérifier l'appel au fichier gtm.js dans le navigateur, répond en 200 : 

Alimentation du data layer

Pour vérifier que le data layer est alimenté correctement sur les spécifications ci-dessus, il suffit d'ouvrir la console de débug du navigateur, et de vérifier que le push est bien fait sur l'objet JS "dataLayer" :

## Alimentation data layer lors du consentement au bandeau de cookies

Lorsque l'utilisateur clique sur un des boutons du bandeau de cookie consent et que le cookie "cst-cookie-consent" est posé :

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'interactionCookieConsent', //String : constant
  'interactionCookieConsent' : {
      'choix' : 'Je refuse' // Call to action cliqué par l'utilisateur : "Je refuse", "J'accepte"
    }
  });
```

## Alimentation data layer au chargement page

Idéalement, le code suivant doit être envoyé **avant** l'appel au snippet GTM, afin que les variables soient disponibles dés l'envoi du tag de page :

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'dlLoad', //String : constant
  'dlLoad' : {
    //Pour toutes les pages
    'template' : 'Article', //String : template Wordpress
    'editorialisation' : '', //String : mode d'éditorialisation de la page, si éligible : "Aucune", "Courte", "Longue"
    'typeLogin' : 'Non loggué', //String : statut du login : "non Loggué", "Loggué sans abo", "Loggué abo"
    
    //Sur les pages de tags et les articles
    'tag' : ['Retraites','Gouvernement','Bordeaux'], //Array de strings : Tags de la page 
    'tagPrincipal' : 'Retraites', //String : Tag mis en avant
    
    //Pour les articles uniquement
    'auteur' : 'Jean Dupont', //String : Auteur de l'article
    'nbSignes' : 3210, //Integer : nombre de signes de l'article
    'statut' : 'Payant', //String : Statut article : "Gratuit" / "Payant" / "Offert" / "Token expiré"
    'datePub' : '21/02/2023' //String : Date de publication de l'article 
    }
  });
```

## Actions front end

### Interactions widget "Article réservé aux abonnés"

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'widgetAbonnes', //String : constant
  'widgetAbonnes' : {
      'cta' : 'Déjà abonné' // Call to action : "Déjà abonné?" / "Connectez-vous" / "En savoir plus"
    }
  });
```

### Steps intermédiaires souscription abonnement

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'stepsAbo', //String : constant
  'stepsAbo' : {
    'nom' : 'Paiement', //String : Nom du step chargé
    'id' : 2, //Integer : Numéro du step chargé
    'formule' : 'Découverte', //String : Formule choisie : "Découverte" / "Annuel" / "Soutien"
    'frequence' : 'Mensuel', //String : fréquence de l'abonnement "Mensuel" / "Annuel"
    'montant' : 80 //Float : Montant de la transaction en euros
    }
  });
```

### Validation abonnement 

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'validAbonnement', //String : constant
  'validAbonnement' : {
    'formule' : 'Découverte', //String : Formule choisie : "Découverte" / "Annuel" / "Soutien"
    'frequence' : 'Mensuel', //String : fréquence de l'abonnement "Mensuel" / "Annuel"
    'montant' : 80 //Float : Montant de la transaction en euros
    }
  });
```

### Validation abonnement cadeau

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'aboCadeau', //String : constant
  'aboCadeau' : {
    'formule' : '3 mois' //String : Nom de la formulme choisie : "3 mois", "6 mois", "12 mois"
    }
  });
```

### Validation abonnement cadeau abonné

(Abonnement gratuit, d'1 mois)

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'aboGratuit', //String : constant
    });
```

### Validation inscription Newsletter

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'inscriptionNL', //String : constant
  'inscriptionNL' : {
    'nom' : 'La Matinale' //String : Nom de la newsletter souscrite : "La Matinale", "Écologie"... Liste à compléter selon évolution
    'emplacement' : 'Espace membre' //String : emplacement du bloc ayant généré l'inscription... Liste à compléter selon évolution
    }
  });
```

### Validation création de compte

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'validCompte', //String : constant
  });
```

### Validation formulaire de contact

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'validContact', //String : constant
  });
```

### Actions "Mon compte"

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'monCompteAction', //String : constant
  'monCompteAction' : 'MAJ moyen de paiement' //Liste exhaustive ci-dessous
  });
```
Liste exhaustive : 
  - MAJ moyen de paiement
  - Résiliation abonnement
  - Changement offre abonnement
  - Téléchargement facture
  - Modif notification (Navigateur / Mail / SMS)
  - Ajout mot clé suivi
  - Suppression mot clé suivi
  - Modification mot de passe
  - Modification email
  - Ajouter un profil
  - Modifier un profil
  - Supprimer un profil
  - Déconnecter un appareil
  - Modification informations personnelles
  - Téléchargement données personnelles
  - Suppression compte
  - Suppression d'un commentaire
  - Offrir un abonnement

### Validation d'ajout d'un commentaire

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'ajoutCommentaire', //String : constant
  'ajoutCommentaire' : {
    'article' : 'heures-supplementaires-non-compensees-hopital-268435', //String : URI de l'article sur lequel est fait le commentaire (peut être vide dans le cadre de l'Agora)
    'userID' : '321654987',//String : ID utilisateur (optionnel)
    'type' : 'Loggué',//String Type commentaire : "Loggué", "Pseudo", "Anonyme"
    'reponse' : 'Initial'//String Distinguo entre commentaire "Initial" et "Réponse à un commentaire"
    }
  });
```

### Vote

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'vote', //String : constant
  'vote' : {
    'type' : '+' //String : type de vote ("+" ou "-" ou "0")
    }
  });
```

### Clics widgets interactions articles

Lors des clics sur les différents widgets d'interaction sur les articles :
  - ✔️ Lire plus tard
  - ✔️ Écouter (player)
  - 🕰️ Offrir cet article
  - ✔️ Partage social Facebook
  - ✔️ Partage social Twitter
  - ✔️ Partage social Mail
  - ✔️ Toolbar

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'clicWidgetArticles', //String : constant
  'clicWidgetArticles' : {
    'nom' : 'Toolbar' //String : Nom du widget cliqué (liste ci-dessus)
    }
  });
```

### Clic nav top "CRM" (niveau 1)

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'navTopNiveau1', //String : constant
  'navTopNiveau1' : {
    'label' : 'Connexion', //String : libellé du picto cliqué : 'Alerter la rédaction', 'Logo', 'Alerte'...
    }
  });
```

### Clic nav top "édito" (niveau 2)

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'navTopNiveau2', //String : constant
  'navTopNiveau2' : {
    'label' : 'Camp de l\'étoile', //String : libellé du lien cliqué, tel qu'il est indiqué sur le site
    'position' : 3 //Integer : position du lien cliqué
    }
  });
```

### Interactions recherche

Les 2 interactions suivantes doivent être trackées :
  - Chargement résultats
  - Clic sur un résultat

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'interactionsRecherche', //String : constant
  'interactionsRecherche' : {
      'action' : 'Chargement résultats' //String : Nom de l'action effectuée par l'utilisateur : "Chargement résultats" / "Clic sur un résultat"
    }
  });
```

### Abonnement à un tag 


```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'aboTag', //String : constant
  'aboTag' : {
    'nom' : 'Psychologie' //Sting : nom du tag tel qu'il est indiqué sur la page
    }
  });
```

### Erreurs formulaires souscription abo

```javascript
(window.dataLayer = window.dataLayer || []).push({
  'event' : 'erreurAbo', //String : constant
  'erreurAbo' : {
    message : 'Carte bleue non valide' //String : message d'erreur remonté par Stripe (exemples à préciser)
    }
  });
```

## Flags HTML

### Visibilité pied d'article

Afin de mieux mesurer le scroll sur les pages de contenu, le pied d'article doit être flaggué avec un attribut "data-gtm-contenu"

```html
<div data-gtm-contenu="Pied d'article">
```

Note : l'attribut doit être ajouté sur une div qui est visible sur la page (pas une div "placeholder")

## Dépendances techniques

### Validation souscription abonnement

L'URL de validation de la souscription d'abonnement sera utilisée pour un objectif GA4. Elle devra être fournie par Geoffrey et tout changement devra être documenté.
