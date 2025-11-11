
public class Client {
    private final int idClient;
    private final String nom;
    private final String adresse; // Ville
    private final double chiffreAffaire;

    // Constructeur, Getters, toString(), equals(), hashCode()
    // ... (voir fichier Client.java)
}

List<Client> clients = ClientData.getClients();
// Exemple de données :
// Client{id=101, nom='Dupont', adresse='Paris', CA=150000.00}
// Client{id=104, nom='Dubois', adresse='Marseille', CA=1200000.75}

clients.stream()
    .filter(c -> c.getChiffreAffaire() > 500000)
 
List<String> noms = clients.stream()
    .map(Client::getNom) // Équivalent à c -> c.getNom()
    .collect(Collectors.toList());

clients.stream()
    .sorted(Comparator.comparing(Client::getNom))
    .forEach(System.out::println);

clients.stream()
    .sorted(Comparator.comparing(Client::getChiffreAffaire).reversed())
    .limit(3)
    .forEach(System.out::println);

clients.stream()
    .map(Client::getAdresse)
    .distinct()
    .forEach(System.out::println);

Map<String, List<Client>> clientsParVille = clients.stream()
    .collect(Collectors.groupingBy(Client::getAdresse));

double caTotal = clients.stream()
    .map(Client::getChiffreAffaire)
    .reduce(0.0, Double::sum); // 0.0 est l'identité

boolean grosClientExiste = clients.stream()
    .anyMatch(c -> c.getChiffreAffaire() > 1000000); // true

Optional<Client> clientMaxCA = clients.stream()

double caMoyen = clients.stream()
    .mapToDouble(Client::getChiffreAffaire) // Conversion en DoubleStream
    .average() // Opération spécifique au DoubleStream
    .orElse(0.0);
```

---

## VI. Exercices Pratiques (Basés sur la classe `Client`)

Les exercices suivants utilisent la liste de clients fournie par `ClientData.getClients()`.

### Exercice 1 : Filtrage et Tri

**Objectif :** Trouver tous les clients résidant à **"Lyon"** et les trier par **chiffre d'affaires décroissant**.

### Exercice 2 : Transformation et Collecte

**Objectif :** Créer une `Map<Integer, String>` où la clé est l'**ID du client** et la valeur est son **nom**.

### Exercice 3 : Agrégation

**Objectif :** Calculer le **chiffre d'affaires total** des clients de **"Paris"**.

### Exercice 4 : Opérations Complexes (Regroupement)

**Objectif :** Regrouper les clients par **ville** et, pour chaque ville, calculer le **chiffre d'affaires moyen** de ses clients. Le résultat doit être une `Map<String, Double>`.

### Exercice 5 : Vérification et Transformation

**Objectif :** Vérifier si **tous** les clients ont un CA supérieur à 50 000. Si oui, afficher la liste des noms des clients en **majuscules**.

---

## VII. Solutions des Exercices

*(Les solutions seront fournies dans la section suivante.)*

---

## VIII. Conclusion

L'API Stream de Java est un outil puissant qui permet d'écrire du code plus **fonctionnel**, **lisible** et **maintenable**. En maîtrisant les opérations intermédiaires et terminales, vous pouvez transformer des tâches complexes de manipulation de collections en pipelines de données élégants.

**Ressources supplémentaires :**
*   [1] Documentation officielle Oracle sur les Streams Java.
*   [2] Tutoriel Baeldung sur l'API Stream Java 8.

## VII. Solutions des Exercices

Pour exécuter ces solutions, vous aurez besoin des classes `Client` et `ClientData` ainsi que des imports suivants :

```java
import com.manus.streams.Client;
import com.manus.streams.ClientData;
import java.util.Comparator;
import java.util.List;
import java.util.Map;
import java.util.OptionalDouble;
import java.util.stream.Collectors;
```

### Solution Exercice 1 : Filtrage et Tri

**Objectif :** Trouver tous les clients résidant à **"Lyon"** et les trier par **chiffre d'affaires décroissant**.


List<Client> lyonClients = ClientData.getClients().stream()
    .filter(c -> "Lyon".equals(c.getAdresse()))
    .sorted(Comparator.comparing(Client::getChiffreAffaire).reversed())
    .collect(Collectors.toList());

System.out.println("Clients de Lyon (CA décroissant) :");
lyonClients.forEach(System.out::println);


### Solution Exercice 2 : Transformation et Collecte

**Objectif :** Créer une `Map<Integer, String>` où la clé est l'**ID du client** et la valeur est son **nom**.

Map<Integer, String> idToNameMap = ClientData.getClients().stream()
    .collect(Collectors.toMap(
        Client::getIdClient, // Clé : idClient
        Client::getNom       // Valeur : nom
    ));

System.out.println("\nMap ID -> Nom :");
idToNameMap.forEach((id, nom) -> System.out.println("ID " + id + " : " + nom));


### Solution Exercice 3 : Agrégation

**Objectif :** Calculer le **chiffre d'affaires total** des clients de **"Paris"**.


double caTotalParis = ClientData.getClients().stream()
    .filter(c -> "Paris".equals(c.getAdresse()))
    .mapToDouble(Client::getChiffreAffaire) // Utilisation d'un DoubleStream pour la performance
    .sum(); // Opération terminale sum()

System.out.printf("\nChiffre d'affaires total des clients de Paris : %.2f\n", caTotalParis);


### Solution Exercice 4 : Opérations Complexes (Regroupement)

**Objectif :** Regrouper les clients par **ville** et, pour chaque ville, calculer le **chiffre d'affaires moyen** de ses clients. Le résultat doit être une `Map<String, Double>`.


Map<String, Double> caMoyenParVille = ClientData.getClients().stream()
    .collect(Collectors.groupingBy(
        Client::getAdresse, // Clé de regroupement : adresse (ville)
        Collectors.averagingDouble(Client::getChiffreAffaire) // Downstream Collector : calcul de la moyenne
    ));

System.out.println("\nCA Moyen par Ville :");
caMoyenParVille.forEach((ville, moyenne) -> System.out.printf("%s : %.2f\n", ville, moyenne));


### Solution Exercice 5 : Vérification et Transformation

**Objectif :** Vérifier si **tous** les clients ont un CA supérieur à 50 000. Si oui, afficher la liste des noms des clients en **majuscules**.


boolean caSup50k = ClientData.getClients().stream()
    .allMatch(c -> c.getChiffreAffaire() > 50000);

System.out.println("\nTous les clients ont-ils un CA > 50 000 ? " + caSup50k);

if (caSup50k) {
    List<String> nomsMajuscules = ClientData.getClients().stream()
        .map(Client::getNom)
        .map(String::toUpperCase)
        .collect(Collectors.toList());

    System.out.println("Noms en majuscules : " + nomsMajuscules);
}

