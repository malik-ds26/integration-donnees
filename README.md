# Système d’intégration de données pour l’optimisation de la recherche d’emploi

Ce projet agrège et relie plusieurs sources (offres d’emploi, entreprises, événements de networking, réseau de transport) afin d’aider un chercheur d’emploi à filtrer et évaluer des opportunités selon des critères concrets (accessibilité, secteur, évènements associés, etc.). :contentReference[oaicite:1]{index=1}

## ✨ Objectifs
- Centraliser des **offres d’emploi** (Indeed, France Travail) et des **entreprises**.
- Relier les offres à des **événements de networking** pertinents (Eventbrite).
- Évaluer l’**accessibilité** via le **réseau de transport (RATP)**.
- Mettre à disposition des **vues SQL** pour l’analyse et des **triggers** pour garder les vues à jour. :contentReference[oaicite:2]{index=2}

## 🗃️ Sources de données
- **APIs** : Indeed, France Travail → JSON (traité en Python). :contentReference[oaicite:3]{index=3}  
- **Réseau de transport** : jeux de données RATP → CSV. :contentReference[oaicite:4]{index=4}  
- **Événements** : scraping **Eventbrite** (cat. Business, Paris) → HAR → CSV (traités avec `pandas`). :contentReference[oaicite:5]{index=5}

## 🧱 Modèle de données (tables principales)
- `offres(id, intitule, description, lieu_travail_libelle, entreprise_nom, type_contrat, experience_exige, salaire_libelle, secteur)`
- `entreprise(id, nom, adresse, codepostale, commune, section, lat, long)`
- `networking(id, nom, timezone, url, date_debut, categorie)`
- `transports(station, traffic, correspondance1..5, ville)` :contentReference[oaicite:6]{index=6}

## 👓 Vues SQL
- `offres_view` : enrichit les offres avec les **URLs des entreprises**.  
- `offres_networking_view` : relie offres ↔ événements du **même secteur**.  
- `offres_transport_view` : associe les offres aux **stations** selon la localisation.  
- `recrutement_networking_view` : entreprises recrutant lors d’événements.  
- `sector_networking_view` : secteurs qui recrutent + événements associés.  
- `recent_networking_offres_view` : offres liées à des événements **< 30 jours**.  
- `company_sector_city_view` : nb de **villes** et **secteurs** par entreprise.  
- `formation_events_view` : offres ↔ événements de **formation**. :contentReference[oaicite:7]{index=7}

## 🔔 Triggers & rafraîchissement
- Une table `trigger_log` enregistre les actions sur `offres`.
- Un script/fonction Python surveille ce journal et **rafraîchit automatiquement** les vues après **INSERT/UPDATE/DELETE**. :contentReference[oaicite:8]{index=8}

## 🏗️ Architecture / Tech
- **Python** (extraction, nettoyage, normalisation)
- **pandas**, **json**, éventuellement **requests/beautifulsoup** pour le scraping
- **SQL** (création des tables, vues, triggers)
- **Databricks / PySpark** pour les notebooks (fichier `.dbc`)  
- Fichiers **JSON** (offres/entreprises), **CSV** (RATP, Eventbrite) :contentReference[oaicite:9]{index=9}


