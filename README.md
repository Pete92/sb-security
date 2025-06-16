# Spring Boot JWT Authentication Example (JdbcUserDetailsManager)

Tämä projekti on esimerkki siitä, miten toteuttaa JWT-autentikointi Spring Securityllä ilman omia `User`- tai `Role`-modeleita. Käyttäjät luodaan automaattisesti sovelluksen käynnistyessä `JdbcUserDetailsManager`in kautta, ja kaikki autentikointi tapahtuu JWT:n avulla.

## 🔐 Ominaisuudet

- JWT-pohjainen autentikointi
- Spring Security + JwtTokenFilter + ExceptionHandler
- Käyttäjät tallennetaan JDBC:n kautta H2-tietokantaan
- Ei omia JPA-modeleita – käytössä Spring Securityn omat `users`- ja `authorities`-taulut
- API-endpointit autentikointiin ja roolipohjaisiin testeihin

## 📁 Projektin rakenne
src/main/java/com/example/securitydemo/
````
├── config/
│   └── SecurityConfig.java          # Spring Security -konfiguraatio
│
├── security/
│   ├── AuthEntryPointJwt.java       # 401 Unauthorized -poikkeusten käsittely
│   ├── AuthTokenFilter.java         # JWT-suodatin, joka tarkistaa tokenin jokaisessa pyynnössä
│   └── JwtUtils.java                # Apuluokka JWT:n generointiin ja validointiin
│
└── controller/
    └── Controller.java              # API-päätepiste kirjautumista ja testikutsuja varten
````


## 🧑‍💻 Testikäyttäjät

Käyttäjät luodaan automaattisesti sovelluksen käynnistyessä `CommandLineRunner`-metodin kautta (tyypillisesti `SecurityConfig`-luokassa):

| Käyttäjätunnus | Salasana | Rooli       |
|----------------|----------|-------------|
| `user`         | `user`   | `ROLE_USER` |
| `admin`        | `admin`  | `ROLE_ADMIN` |

## ✅ API-päätepisteet

| Metodi | Polku              | Kuvaus                         | Tarvitsee tokenin |
|--------|--------------------|--------------------------------|--------------------|
| POST   | `/api/auth/signin` | Kirjautuminen, palauttaa JWT  | ❌                |
| GET    | `/api/test/user`   | Käyttäjäpäätepiste             | ✅ (ROLE_USER)    |
| GET    | `/api/test/admin`  | Admin-päätepiste               | ✅ (ROLE_ADMIN)   |

## 🔐 Kirjautuminen esimerkki (curl)

```bash
curl -X POST http://localhost:8080/api/auth/signin \
  -H "Content-Type: application/json" \
  -d '{"username":"user","password":"user"}'
