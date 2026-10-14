# FOAMETRA · Gestionare stoc

Aplicație web responsive pentru evidența stocului de caractere din polistiren. Prețurile sunt individuale pentru fiecare caracter din fiecare dimensiune. Valorile per caracter, totalurile pe dimensiune și totalul general se calculează automat în MDL. Modificările Firestore se sincronizează în timp real.

## Configurare Firebase

1. Creează un proiect în [Firebase Console](https://console.firebase.google.com/) și înregistrează o aplicație Web.
2. Activează **Authentication → Sign-in method → Email/Password**.
3. Creează conturi cu **Authentication → Users → Add user** pentru personalul autorizat. În aplicație autentificarea este doar cu e-mail și parolă; nu există formular public de înregistrare.
4. Creează baza **Cloud Firestore** în modul Production.
5. În **Project settings → Your apps → SDK setup**, copiază configurația aplicației web în `firebase-config.js` (`apiKey`, `authDomain`, `projectId`, `appId`). Acestea sunt identificatori ai clientului Firebase, nu chei de cont de serviciu. Nu publica niciodată chei de service account.
6. Publică regulile din `firestore.rules` în **Firestore → Rules**. Pentru colaborarea mai multor utilizatori în același stoc, aplicația curentă izolează datele per UID; folosiți un cont de firmă comun sau extindeți schema și regulile pentru o listă explicită de UID-uri autorizate înainte de a folosi conturi separate.

Structura datelor: `users/{uid}/sizes/{sizeId}/characters/{characterId}`. Fiecare caracter aparține unei singure dimensiuni, astfel încât de exemplu A de 15 cm și A de 20 cm sunt înregistrări distincte. Documentele conțin `character`, `quantity`, `price`, timpi de creare/modificare. Elemente eliminate se păstrează în `users/{uid}/deletedCharacters` și pot fi restaurate în 90 de zile cât timp dimensiunea sursă există.

## Publicare pe GitHub Pages

1. Completează `firebase-config.js` și publică regulile Firestore.
2. Încarcă fișierele proiectului într-un repository GitHub.
3. Deschide **Settings → Pages**, alege **Deploy from a branch**, selectează `main` și folderul `/ (root)`, apoi salvează.
4. Așteaptă publicarea și accesează URL-ul oferit de GitHub Pages. Autentificarea, Firestore și instalarea PWA cer HTTPS, furnizat de GitHub Pages.
5. Pentru schimbări ulterioare, publică commit-ul în ramura aleasă. Nu activa CDN sau alte optimizări care scot scripturile ES modules din funcțiune.

## Funcții incluse

- Crearea, redenumirea și arhivarea logică a dimensiunilor.
- Adăugare, editare și eliminare confirmată a caracterelor; avertisment la eliminarea stocului nenul.
- Editare directă a cantității și prețului unitar pentru fiecare caracter.
- Calcul automat cantitate × preț, totaluri pe dimensiune și tablou general live.
- Căutare și filtre pentru caractere în stoc sau epuizate.
- Recuperarea caracterelor șterse din „Elemente șterse recent”.
- Interfață adaptată telefonului/desktopului și instalabilă ca PWA.

Aplicația este online-first: sincronizarea și funcțiile Firestore necesită conexiune. Service worker-ul păstrează interfața pentru încărcare mai rapidă, dar nu pune în coadă modificările offline. Înainte de operațiuni administrative, exportă periodic un backup din consola Firebase sau adaugă exportul CSV/backup ca funcție separată.
