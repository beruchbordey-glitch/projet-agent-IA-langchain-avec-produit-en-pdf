# Assistant IA - Le Tropical (RAG sur documents PDF)

Deuxième version de l'assistant de service client de Le Tropical. Ici, l'assistant ne s'appuie plus sur un catalogue codé en dur, mais sur les documents PDF réels de la marque, dont on démontre qu'il extrait la bonne information même au milieu de contenus hors-sujet.

# Contexte

Une vraie entreprise n'a pas ses données produits dans une liste Python : elle a des fichiers (catalogues, fiches, PDF) qui contiennent souvent un mélange d'informations. L'enjeu de cette version : brancher l'assistant sur ces documents réels, et prouver que la recherche reste fiable malgré le désordre.

# Ce que cette version apporte

- L'assistant se met à jour en déposant un fichier dans le dossier data, sans toucher au code.
- Il retrouve la bonne information produit même quand les documents mélangent des sujets sans rapport (sport, météo, actualité).
- Le reste des capacités du projet 1 est conservé : mémoire, conseil produit, création de ticket, vérification de commande et de stock, choix autonome de l'action.

# Nouveautés techniques par rapport au projet 1

1. Chargement de documents réels. Les PDF du dossier data sont lus avec 'PyPDFDirectoryLoader', qui renvoie un document par page avec ses métadonnées (fichier source, page).

2. Chunking. Les PDF mélangent produits et articles hors-sujet dans de longs blocs. On les découpe en petits morceaux avec RecursiveCharacterTextSplitter (chunk_size et chunk_overlap), pour qu'un morceau porte sur une seule chose et donne un vecteur net. C'est ce qui rend la recherche précise. Au projet 1, cette étape n'était pas nécessaire car le catalogue était déjà découpé (un produit = une entrée courte).

3. Robustesse au bruit. Les documents contiennent volontairement des passages sans rapport avec les cosmétiques. La recherche sémantique les écarte, car ils sont éloignés, par le sens, des questions produits.

# Démonstration

Fichiers de test : trois PDF mêlant produits Le Tropical et articles hors-sujet (résultats sportifs, météo, économie, horoscope), dans des proportions au moins équivalentes.

Résultat observé sur la requête « je cherche un soin pour mes cheveux secs » :
- les résultats retournés sont bien des produits capillaires (shampoing, après-shampoing), tirés des PDF ;
- aucun passage de sport, météo ou actualité ne remonte, alors qu'ils figurent dans les mêmes fichiers ;
- la réponse finale de l'assistant cite les produits avec noms, ingrédients et prix exacts, et ignore les fragments hors-sujet.

## Compétences mises en œuvre

Chargement de documents (PyPDFDirectoryLoader), découpage de texte (RecursiveCharacterTextSplitter, chunk_size, chunk_overlap), embeddings multilingues et recherche sémantique, RAG sur fichiers réels, robustesse au bruit, RAG emballé en outil, agent multi-outils avec mémoire et routage.

## Limites

- Le chunking n'est pas parfait : des fragments hors-sujet peuvent s'accrocher aux résultats de recherche. Ils sont neutralisés par une consigne système, mais restent à surveiller.
- La recherche fonctionne sur le sens : très efficace pour une demande large, moins fiable sur une référence exacte, ou entre deux produits sémantiquement proches aux caractéristiques différentes. Un RAG hybride (mots-clés exacts combinés au sens) corrigerait cette faiblesse.
- La qualité des réponses dépend directement de la qualité des documents fournis.
- Les actions (ticket, vérifications) sont simulées : à brancher sur les vrais systèmes en production.

