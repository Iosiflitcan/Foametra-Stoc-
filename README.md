# FOAMETRA · Stoc și magazin online

Aplicația include două zone: administrarea stocului la `index.html` și magazinul pentru clienți la `store.html`. Catalogul se bazează pe aceleași documente Firestore ca stocul. Cumpărătorii se conectează cu e-mail și parolă; numărul de telefon este solicitat pentru contact și livrare. Plata este în numerar la livrare.

## Configurarea Firebase

1. În **Authentication → Sign-in method**, activează **Email/Password**. Nu este necesară autentificarea prin SMS sau activarea facturării pentru SMS.
2. Creează contul administratorului în **Authentication → Users → Add user**. Păstrează ID-ul UID afișat pentru acel cont.
3. Creează baza **Cloud Firestore** în modul Production și publică regulile din `firestore.rules`.
4. În **Firestore → Data**, creează colecția `admins`, documentul cu ID-ul UID al administratorului, și câmpul boolean `active` cu valoarea `true`. Numai administratorul proiectului o poate configura; aplicația nu permite utilizatorilor să-și acorde rolul.
5. Verifică în **Authentication → Settings → Authorized domains** că domeniul paginii găzduite este permis (de exemplu `iosiflitcan.github.io`).
6. Configurează `firebase-config.js` cu valorile aplicației Web Firebase dacă proiectul se schimbă.
7. Autentifică-te la `index.html` cu administratorul. Aplicația creează automat `storeSettings/main` cu UID-ul contului care a conectat prima dată catalogul.

Regulile separă rolurile: clienții conectați pot citi catalogul și pot crea comenzi în așteptare; fiecare client poate vedea numai propriile comenzi și profil. Doar administratorul poate modifica stocul, accepta/refuza comenzile sau schimba setările magazinului. Comenzile sunt acceptate prin tranzacție Firestore: prețurile și cantitățile se verifică din nou, iar stocul se scade numai dacă există suficient.

## Fonturi și stoc

- În administrare, selectează o dimensiune și fontul din selectorul **Font**.
- **＋ Font** adaugă un font pentru toate dimensiunile existente și copiază lista caracterelor cu cantitate și preț inițial `0`. Completează stocul separat pentru fiecare caracter/font/dimensiune.
- Numele familiei CSS este folosit pentru previzualizarea literelor în magazin. Pentru ca aspectul să corespundă exact literelor tăiate fizic, selectează o familie disponibilă în browser sau adaugă fișierul real de font în proiect și configurează-l.
- La crearea unei dimensiuni, caracterele standard se adaugă pentru fiecare font existent.

## Comenzi

Clienții creează un cont din **Magazin**, aleg dimensiunea, fontul și caracterele disponibile, apoi completează numele, telefonul și adresa de livrare. Metoda de plată este afișată clar ca **numerar la livrare**. Comanda apare în **Comenzi primite** din administrarea stocului. Acceptarea recalculează totalul din prețurile curente și scade cantitățile într-o tranzacție; refuzul nu schimbă stocul. Clientul vede actualizarea stării în „Comenzile mele”.

## Publicare pe Firebase Hosting

Firebase Hosting este configurat în `firebase.json` și `.firebaserc`, pentru proiectul `foametra-stoc`. Pentru publicare, deschide terminalul în folderul proiectului și rulează:

```sh
npm install -g firebase-tools
firebase login
firebase deploy --only hosting
```

Aceste comenzi instalează uneltele de publicare, nu Firebase SDK-ul aplicației. Aplicația însăși încarcă SDK-ul din CDN. Firebase Hosting are cote fără cost pe planul Spark; nu activa Blaze doar pentru această aplicație. După publicare, deschide domeniul `foametra-stoc.web.app`; magazinul este la `/store.html`. Adaugă domeniul Hosting în **Authentication → Settings → Authorized domains** dacă nu apare automat.

GitHub Pages rămâne o alternativă dacă repository-ul este public sau planul GitHub permite Pages pentru repository privat.

Înainte de utilizarea cu clienți reali, încarcă și verifică regulile Firestore, adaugă documentul de administrator și testează o comandă completă cu un caracter de test. Firestore și autentificarea necesită conexiune la internet.
