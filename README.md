# Application Spring Boot Hello World

Ce projet est une application Spring Boot simple qui affiche "Hello World!" via un endpoint REST. L'application est configurée pour être déployée sur un serveur Tomcat via GitHub Actions.

## Prérequis

- Java 17 ou supérieur
- Maven 3.6 ou supérieur
- Serveur Tomcat 9 ou supérieur (pour le déploiement)

## Structure du projet

```
src/
├── main/
│   └── java/
│       └── com/
│           └── example/
│               └── demo/
│                   ├── DemoApplication.java
│                   └── controller/
│                       └── HelloController.java
└── pom.xml
```

## Comment exécuter l'application

### Exécution locale

1. Clonez le projet
2. Ouvrez un terminal dans le répertoire du projet
3. Exécutez la commande suivante :
   ```bash
   mvn spring-boot:run
   ```
4. L'application sera accessible à l'adresse : http://localhost:8080

### Génération du WAR pour déploiement sur Tomcat

1. Assurez-vous que le fichier `pom.xml` contient la configuration suivante :
   ```xml
   <packaging>war</packaging>

   <dependencies>
       <!-- Autres dépendances -->

       <!-- Dépendance pour le déploiement sur Tomcat -->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-tomcat</artifactId>
           <scope>provided</scope>
       </dependency>
   </dependencies>
   ```

2. Modifiez la classe principale pour étendre `SpringBootServletInitializer` :
   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
   import org.springframework.boot.builder.SpringApplicationBuilder;

   @SpringBootApplication
   public class DemoApplication extends SpringBootServletInitializer {

       @Override
       protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
           return application.sources(DemoApplication.class);
       }

       public static void main(String[] args) {
           SpringApplication.run(DemoApplication.class, args);
       }
   }
   ```

3. Générez le fichier WAR avec la commande :
   ```bash
   mvn clean package
   ```

4. Le fichier WAR sera généré dans le répertoire `target/`

## Endpoints disponibles

- GET `/` : Retourne "Hello World!"

## Technologies utilisées

- Spring Boot 3.2.3
- Java 17
- Maven
- Tomcat (pour le déploiement)

## Déploiement

L'application est configurée pour être déployée automatiquement sur un serveur Tomcat via GitHub Actions.

### Workflow de déploiement

Le workflow de déploiement est défini dans le fichier `.github/workflows/deploy.yml` et comprend les étapes suivantes :

1. **Build** : Compilation de l'application avec Maven pour générer un fichier WAR
2. **Upload** : Téléversement du fichier WAR vers un bucket S3
3. **Déploiement** : Téléchargement du fichier WAR depuis S3 vers l'instance EC2 et déploiement sur Tomcat

### Configuration requise

Pour que le déploiement fonctionne, les secrets suivants doivent être configurés dans les secrets du repository GitHub :

- `AWS_ACCESS_KEY_ID` : Clé d'accès AWS
- `AWS_SECRET_ACCESS_KEY` : Clé secrète AWS
- `AWS_REGION` : Région AWS (ex: eu-west-3)
- `S3_BUCKET_NAME` : Nom du bucket S3
- `S3_KEY_PREFIX` : Préfixe (dossier) dans le bucket S3
- `EC2_HOST` : Adresse IP ou nom DNS de l'instance EC2
- `EC2_USER` : Nom d'utilisateur pour se connecter à l'instance EC2
- `EC2_SSH_PRIVATE_KEY` : Clé SSH privée pour accéder à l'instance EC2
- `TARGET_DIR` : Répertoire temporaire sur l'instance EC2
- `WAR_NAME` : Nom du fichier WAR
- `TOMCAT_WEBAPPS` : Chemin vers le répertoire webapps de Tomcat

### Accès à l'application déployée

Après le déploiement, l'application est accessible à l'adresse :

```
http://[EC2_HOST]:8080/[nom-de-l'application]
```

Où `[nom-de-l'application]` est le nom du fichier WAR sans l'extension `.war`.