🚀 SmartTracker

SmartTracker est une application full‑stack visant à suivre, analyser et optimiser le temps d’utilisation des applications sur ordinateur. Elle combine un tracking en temps réel, des tableaux de bord interactifs, un module de pointage (attendance) et un moteur de recommandations. Le projet met l’accent sur la productivité, la sécurité, et l’analyse des données.

💡 Développé pour un usage interne (ex. ERTC Technologies).

📌 Table des matières

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

✨ Fonctionnalités
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

🧭 Architecture & Flux de données
Frontend (React)  →  API REST (Flask)  →  SQLite
         ↑                           ↓
       Auth (JWT)        Recommandations & ML (Joblib)
         ↑                           ↓
  Tracker.py (client)  ←  Scripts Python (analyse, emails)

Flux simplifié

Tracking : tracker.py capte l’application active, normalise le nom et envoie/stocke la durée (API/DB).

Catégorisation : le nom d’app est vectorisé (TF‑IDF) puis classé (RandomForest) → catégorie (Travail, Social…).

Recommandations : script d’analyse (seuils & règles) + envoi d’emails via smtplib si dépassements.

Visualisation : le frontend (React) interroge l’API (JWT) et affiche stats, graphiques et alertes.

🧰 Technologies
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

🔐 Sécurité & Authentification (JWT)

Authentification par JWT (PyJWT).

Rôles : user et admin (contrôle d’accès côté API + garde de route côté frontend).

Flux :

Login → JWT signé avec SECRET_KEY.

Frontend stocke le token (mémoire/contexte) et l’envoie via l’en‑tête Authorization: Bearer <token>.

Intercepteur Axios : rafraîchit/redirige en cas de 401.

Bonnes pratiques : durée de vie limitée, renouvellement, hachage des mots de passe (Werkzeug), CORS restrictif.

📂 Structure des fichiers
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

Tables principales

users(id, username, email, password_hash, role, last_login)

usage(id, user_id, app_name, start_time, duration)

tasks(id, user_id, title, status, priority, time, date)

user_settings(user_id, app_name, max_duration)

attendance(username, arrival, departure, date)

🧱 Pré‑requis

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

🛠 Installation & Lancement
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

▶️ Utilisation

Inscription/Connexion (compte de test : admin / admin123).

Démarrer le tracker : automatique au login (ou via TrackerStatus).

Explorer le Dashboard : stats, catégories, recommandations.

Gérer les tâches : calendrier + Pomodoro.

Suivre le pointage : filtres (24h/7j/30j) + export CSV.

📡 API Endpoints
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
🤖 Modèle ML : catégorisation des applications

Dataset : app_categories.csv (colonnes : app_name, category).

Vectorisation : TF‑IDF (TfidfVectorizer).

Classifieur : RandomForest (RandomForestClassifier).

Persistance : category_model.joblib, vectorizer.joblib (Joblib).

**Entraînement (ex
