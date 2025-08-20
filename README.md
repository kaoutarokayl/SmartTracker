## 🚀 SmartTracker

SmartTracker est une application full‑stack visant à suivre, analyser et optimiser le temps d’utilisation des applications sur ordinateur. Elle combine un tracking en temps réel, des tableaux de bord interactifs, un module de pointage (attendance) et un moteur de recommandations. Le projet met l’accent sur la productivité, la sécurité, et l’analyse des données.

💡 Développé pour un usage interne (ex. ERTC Technologies).

## 📌 Table des matières

Fonctionnalités

Architecture & Flux de données

Technologies

Sécurité & Authentification (JWT)

Structure des fichiers

Base de données

Pré‑requis

Configuration (.env)

Installation & Lancement

Utilisation

API Endpoints

Modèle ML : catégorisation des applications

Tests & Débogage

Bonnes pratiques

Améliorations futures

Licence

## ✨ Fonctionnalités

Côté Utilisateur

Suivi en temps réel des applications actives (normalisation des noms).

Statistiques visuelles : temps total, top applications, répartition par catégorie (Chart.js/Recharts).

Recommandations personnalisées (dépassement de seuils, hygiène de travail, pauses).

Calendrier & Pomodoro : tâches (priorité, statut) + timer (25 min par défaut).

Pointage (attendance) : heures d’arrivée/départ, calcul d’heures effectives, export CSV.

Notifications : in‑app (bannières dynamiques) et email via smtplib.

Côté Administrateur

Gestion des utilisateurs (CRUD) et rôles.

Stats globales & tendances (périodes 24h/7j/30j).

Supervision du pointage + export.

Santé système (statut du tracker, volumétrie, etc.).

## 🧭 Architecture & Flux de données

Frontend (React)  →  API REST (Flask)  →  SQLite
         ↑                           ↓
       Auth (JWT)        Recommandations & ML (Joblib)
         ↑                           ↓
  Tracker.py (client)  ←  Scripts Python (analyse, emails)

## Flux simplifié

Tracking : tracker.py capte l’application active, normalise le nom et envoie/stocke la durée (API/DB).

Catégorisation : le nom d’app est vectorisé (TF‑IDF) puis classé (RandomForest) → catégorie (Travail, Social…).

Recommandations : script d’analyse (seuils & règles) + envoi d’emails via smtplib si dépassements.

Visualisation : le frontend (React) interroge l’API (JWT) et affiche stats, graphiques et alertes.

## 🧰 Technologies
Frontend

React 18+, React Router (routes protégées), Hooks/Context (AuthContext)

Tailwind CSS, Lucide (icônes), Framer Motion (animations)

Chart.js & Recharts (graphes), React Calendar (planification)

Axios (intercepteurs JWT), File‑Saver (export CSV), EmailJS (formulaires de contact, si utilisé)

Backend

Flask (API REST), Flask‑CORS

SQLite (stockage), Pandas (stats), PyJWT (JWT), Werkzeug (hashing)

PyGetWindow (capture fenêtre active), threading (processus de tracking)

smtplib (emails SMTP), logging (journalisation)

Machine Learning

scikit‑learn : RandomForestClassifier, TfidfVectorizer

Joblib : persistance category_model.joblib & vectorizer.joblib

Outils & Divers

app_categories.csv (mapping statique)

Scripts : db_init.py, normalize_data.py, recommandation.py, recommender.py, train_model.py, test_usage.py

## 🔐 Sécurité & Authentification (JWT)

Authentification par JWT (PyJWT).

Rôles : user et admin (contrôle d’accès côté API + garde de route côté frontend).

Flux :

Login → JWT signé avec SECRET_KEY.

Frontend stocke le token (mémoire/contexte) et l’envoie via l’en‑tête Authorization: Bearer <token>.

Intercepteur Axios : rafraîchit/redirige en cas de 401.

Bonnes pratiques : durée de vie limitée, renouvellement, hachage des mots de passe (Werkzeug), CORS restrictif.

## 📂 Structure des fichiers
smarttracker/

├── backend/                                   # Serveur & traitements

│   ├── __pycache__/                           # Cache Python (ignorer)

│   ├── uploads/                               # Fichiers uploadés

│   ├── venv/                                  # Environnement virtuel (ignorer)

│   ├── api.py                                 # API Flask (auth, usage, tasks, admin…)

│   ├── app_categories.csv                     # Mapping Application → Catégorie

│   ├── category_model.joblib                  # Modèle ML (catégorisation)

│   ├── config_tracker.json                    # Config tracker (fréquence, exclusions)

│   ├── db_init.py                             # Init DB (tables + données de test)

│   ├── email_utils.py                         # Envoi d’emails (SMTP/smtplib)

│   ├── normalize_data.py                      # Normalisation noms d’apps

│   ├── recommandation.py                      # Génération de recommandations

│   ├── recommender.py                         # Moteur de catégorisation/ML

│   ├── requirements.txt                       # Dépendances backend

│   ├── test_usage.py                          # Tests/validation usage

│   ├── tracker.py                             # Tracking en temps réel

│   ├── train_model.py                         # Entraînement du modèle ML

│   ├── usage_data.db                          # Base SQLite

│   └── vectorizer.joblib                      # Vectoriseur TF‑IDF

│
├── frontend/                                  # Interface utilisateur

│   └── src/

│       ├── pages/                             # Pages principales

│       │   ├── Dashboard.jsx                  # Tableau de bord utilisateur

│       │   ├── Stats.jsx                      # Statistiques filtrées (7j/30j…)

│       │   ├── Calendar.jsx                   # Tâches + Pomodoro

│       │   ├── Attendance.jsx                 # Pointage (filtres + export)

│       │   ├── AdminDashboard.jsx             # Vue administrateur

│       │   └── ...

│       ├── components/                        # Composants UI réutilisables

│       │   ├── NotificationBanner.jsx         # Alertes in‑app

│       │   ├── TrackerStatus.jsx              # Statut/Toggle tracker

│       │   └── ...

│       ├── context/

│       │   └── AuthContext.js                 # Auth & rôles (JWT)

│       └── services/

│           ├── api.js                         # Axios (+ intercepteurs JWT)

│           ├── emailService.js                # Envoi (EmailJS) si utilisé

│           └── recommendations.js             # Logique client (rappels/suggestions)

🗄 Base de données

## Tables principales

users(id, username, email, password_hash, role, last_login)

usage(id, user_id, app_name, start_time, duration)

tasks(id, user_id, title, status, priority, time, date)

user_settings(user_id, app_name, max_duration)

attendance(username, arrival, departure, date)

## 🧱 Pré‑requis

Node.js ≥ 14

Python ≥ 3.8

Accès SMTP (ex. Gmail avec mot de passe d’application) pour smtplib

(Optionnel) EmailJS pour les formulaires de contact côté frontend

⚙️ Configuration (.env)

Créer un fichier .env (ou variables d’environnement équivalentes) :

# Backend
SECRET_KEY="change_me_super_secret"
JWT_ALGORITHM="HS256"
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_USER="votre.email@gmail.com"
SMTP_PASSWORD="mot_de_passe_application"


# Frontend
VITE_API_BASE_URL="http://127.0.0.1:5000"
EMAILJS_SERVICE_ID="xxxx"
EMAILJS_TEMPLATE_ID="xxxx"
EMAILJS_PUBLIC_KEY="xxxx"

Pensez à ne jamais committer vos secrets. Utilisez un fichier .env ignoré par Git.

## 🛠 Installation & Lancement
1) Cloner le projet
git clone <url-du-repo>
cd smarttracker
2) Backend
python -m venv venv
source venv/bin/activate        # Unix/Mac
venv\Scripts\activate           # Windows
pip install -r backend/requirements.txt
python backend/db_init.py       # crée la DB + compte admin (admin/admin123)
python backend/api.py

API : http://127.0.0.1:5000

3) Frontend
cd frontend
npm install
npm start

UI : http://localhost:3000

## ▶️ Utilisation

Inscription/Connexion (compte de test : admin / admin123).

Démarrer le tracker : automatique au login (ou via TrackerStatus).

Explorer le Dashboard : stats, catégories, recommandations.

Gérer les tâches : calendrier + Pomodoro.

Suivre le pointage : filtres (24h/7j/30j) + export CSV.

## 📡 API Endpoints
Authentification

POST /login                # { username, password } → { token, user }

POST /register             # { username, email, password }

POST /profile/update       # { username, email } (JWT)

POST /profile/change-password  # { currentPassword, newPassword } (JWT)

En‑tête : Authorization: Bearer <JWT>

Tracker / Usage

POST /tracker/start        # Démarrer tracking (JWT)

POST /tracker/stop         # Arrêter tracking (JWT)

GET  /tracker/status       # { running | stopped } (JWT)

GET  /usage/<user_id>      # Statistiques d’usage (JWT)

GET  /categorize/<app_name>    # Catégorie ML ou mapping

Admin

GET  /admin/users                  # Liste des utilisateurs (admin)

GET  /admin/stats?time_range=7|30|24

GET  /admin/activity?time_range=7|30

GET  /admin/users/stats?time_range=7|30

GET  /admin/system/health

GET  /admin/usage-trends?time_range=7|30

Tasks & Attendance

GET    /tasks?date=YYYY-MM-DD

POST   /tasks                      # { user_id, title, status, priority, time, date }

PATCH  /tasks/<id>                 # { status }

DELETE /tasks/<id>


GET    /attendance/<user_id>?time_range=24|7|30

GET    /admin/attendance?time_range=24|7|30

Exemple cURL (login)

curl -X POST http://127.0.0.1:5000/login \

  -H "Content-Type: application/json" \
  
  -d '{"username":"admin","password":"admin123"}'
  
## 🤖 Modèle ML : Catégorisation des Applications

La catégorisation des applications est un composant clé de SmartTracker, permettant d'analyser et de regrouper les usages par type (ex. : Travail, Social, Divertissement). Cela facilite les statistiques avancées, les recommandations personnalisées et la détection de comportements non productifs. Le système combine un mapping statique (basé sur un fichier CSV) avec un modèle d'apprentissage automatique pour gérer les cas inconnus ou variés.
Principes de Fonctionnement

Normalisation des Noms d'Applications :

Avant toute catégorisation, les noms d'applications capturés par le tracker (via pygetwindow) sont normalisés pour gérer les variations (ex. : "Microsoft Word" → "ms word", "VS Code" → "visual studio code").
Cela se fait via une fonction dédiée dans tracker.py (méthode normalize_app_name), qui applique des règles de minuscule, suppression d'espaces inutiles et matching fuzzy si nécessaire.
Un script dédié (normalize_data.py) peut être exécuté pour normaliser rétroactivement les données en base SQLite :
pythonimport sqlite3
from tracker import UsageTracker  # Importe la classe avec normalize_app_name

conn = sqlite3.connect("usage_data.db")

cursor = conn.cursor()

cursor.execute("SELECT id, app_name FROM usage")

rows = cursor.fetchall()

for row_id, app_name in rows:
    normalized_name = UsageTracker(1).normalize_app_name(app_name)
    if normalized_name and normalized_name != app_name:
        cursor.execute("UPDATE usage SET app_name = ? WHERE id = ?", (normalized_name, row_id))

conn.commit()
conn.close()
print("Normalisation des noms dans la base terminée.")

Avantages : Réduit les doublons et améliore la précision du modèle (ex. : "snipping tool" et "microsoft snipping tool" deviennent identiques).


Mapping Statique :

Basé sur le fichier app_categories.csv, qui liste des applications courantes et leurs catégories prédéfinies.
Exemple de contenu (extrait) :






















app_namecategoryVS CodeTravailYouTubeDivertissementFacebookSocialChromeNavigateursWhatsAppSocialsnipping toolOutils système

Utilisation : Lors de l'insertion d'une entrée dans la table usage, l'API vérifie d'abord si l'app existe dans ce CSV. Si oui, la catégorie est directement assignée.


Modèle d'Apprentissage Automatique :

Dataset : Le fichier app_categories.csv sert de base d'entraînement (colonnes : app_name pour les features, category pour les labels). Il contient des exemples variés pour couvrir les cas courants.
Pré-traitement des Données :

Chargement via Pandas : data = pd.read_csv("app_categories.csv").
Vectorisation des noms d'applications avec TF-IDF (TfidfVectorizer de scikit-learn), qui convertit les textes en vecteurs numériques pondérés (ex. : importance des termes rares comme "snipping" pour "Outils système").
Code extrait de train_model.py :
pythonfrom sklearn.feature_extraction.text import TfidfVectorizer
import pandas as pd

data = pd.read_csv("app_categories.csv")
X = data["app_name"]
y = data["category"]

vectorizer = TfidfVectorizer()
X_vectorized = vectorizer.fit_transform(X)



Modèle : RandomForestClassifier (ensemble d'arbres de décision) pour sa robustesse aux données textuelles et sa capacité à gérer les catégories multi-classes.

Paramètres : n_estimators=100 (100 arbres), random_state=42 pour la reproductibilité.
Entraînement :
pythonfrom sklearn.ensemble import RandomForestClassifier
import joblib

model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_vectorized, y)

# Sauvegarde
joblib.dump(model, "category_model.joblib")
joblib.dump(vectorizer, "vectorizer.joblib")

Précision : Sur un dataset de test (split 80/20), le modèle atteint typiquement >95% d'accuracy grâce à la simplicité des features (noms d'apps courts et distinctifs).


Inférence (Prédiction) :

Chargement du modèle et du vectoriseur via Joblib dans api.py.
Endpoint dédié : POST /predict_category (ex. : body { "app_name": "Discord" } → { "app_name": "Discord", "category": "Social" }).
Flux : Si pas de match dans le CSV statique, vectoriser l'entrée et prédire avec le modèle.
Code extrait :
pythonmodel = joblib.load("category_model.joblib")
vectorizer = joblib.load("vectorizer.joblib")

@app.route("/predict_category", methods=["POST"])
def predict_category():
    app_name = request.json.get("app_name")
    X_vectorized = vectorizer.transform([app_name])
    prediction = model.predict(X_vectorized)[0]
    return jsonify({"app_name": app_name, "category": prediction})



Gestion des Cas Inconnus : Si le modèle a une faible confiance (proba < 0.7), fallback sur une catégorie "Autre" et logging pour enrichir le dataset ultérieurement.


Intégration dans l'Application :

Dans le Tracker : Lors de la capture, la catégorie est prédite et stockée dans la table usage (ajout d'une colonne category si extension future).
Dans les Stats : Agrégation par catégorie pour les graphiques (ex. : pie chart dans Dashboard.jsx via Recharts).
Recommandations : Utilisation des catégories pour détecter les abus (ex. : >3h en "Social" → alerte via recommandation.py et email).
Mise à Jour du Modèle : Ré-entraîner périodiquement avec de nouvelles apps ajoutées au CSV via python train_model.py.



Avantages et Limites

Avantages : Automatisation (pas besoin de mapping manuel pour toutes les apps), scalabilité (ajout facile de nouvelles catégories via CSV), précision élevée sur des noms descriptifs.
Limites : Sensible aux noms très courts ou ambigus (ex. : "App1" → pourrait nécessiter du fine-tuning). Pas de contextualisation (ex. : Chrome pour travail vs. loisir).
Améliorations Possibles : Intégrer du NLP avancé (BERT pour embeddings sémantiques) ou un feedback loop utilisateur pour corriger les prédictions.

Pour entraîner ou tester : Exécutez python backend/train_model.py après avoir mis à jour app_categories.csv. Le modèle est prêt à l'emploi et intégré à l'API pour une inférence rapide (<1ms par prédiction).

Analyse locale des usages reçus depuis l’API.

Génération de recommandations dynamiques affichées directement dans l’UI (bannières/alertes).

Exemples :

🚨 Plus de 3h sur WhatsApp → suggérer une pause.

⚠️ Temps de concentration inférieur à hier → alerte sur la baisse de productivité.

Backend (recommandation.py)

Extraction des données réelles depuis la base SQLite (usage, user_settings).

Vérification des seuils d’utilisation par application (valeurs par défaut : 3h, personnalisables par utilisateur).

Génération de recommandations personnalisées :

Dépassements détectés (navigateur, apps sociales, etc.).

Suggestions d’hygiène numérique si utilisation excessive.

Envoi automatique d’emails via smtplib :

⚡ Alerte immédiate en cas de dépassement.

📩 Résumé quotidien de l’activité (heures totales).

Persistance et Paramétrage

Seuils enregistrés par utilisateur (user_settings).

Possibilité de définir des règles métier (par ex. seuil réduit pour réseaux sociaux).

Notifications stockées dans la DB pour traçabilité.

## 🧪 Tests & Débogage

Tests unitaires

Vérification de l’authentification (/login, /register).

Vérification du tracking (insertion dans usage).

Vérification du moteur ML (/categorize/<app_name>).

Exécution :


pytest backend/tests/

## Débogage
Activer le mode debug dans Flask :

export FLASK_ENV=development
Logs stockés dans backend/logs/app.log.

Vérifier le statut du tracker avec :


GET /tracker/status

## ✅ Bonnes pratiques
Sécurité : ne jamais exposer SECRET_KEY (utiliser .env + .gitignore).

JWT : durée de vie limitée (ex. 15 min) + refresh token.

DB : sauvegardes régulières (SQLite → dump ou migration vers PostgreSQL).

Emails : utiliser mot de passe d’application, pas le vrai mot de passe Gmail.

Frontend : éviter le stockage du JWT dans localStorage (privilégier mémoire ou cookies HttpOnly).

Code : linting avec flake8 (Python) et eslint (JS).

## 🚧 Améliorations futures
 Support multi-plateforme (Linux, macOS).

 Migration DB de SQLite → PostgreSQL pour production.

 Ajout d’un système de notifications push (WebSockets).

 Implémentation d’un moteur de recommandations avancé (apprentissage non supervisé).

 Ajout d’un tableau Kanban pour la gestion des tâches.

 Intégration d’un dashboard admin en temps réel (WebSocket/Socket.IO).

 Déploiement Docker (frontend + backend + DB).

## 📜 Licence
Ce projet est distribué sous licence MIT.
Vous êtes libre de l’utiliser, le modifier et le distribuer, sous réserve de conserver la mention de copyright.

## 👨‍💻 Auteur
Développé par Kaoutar OKAYL dans le cadre d’un stage chez ERTC Technologies.
