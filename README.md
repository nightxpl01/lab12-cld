
# Lab12 — Stack LEMP z phpMyAdmin

## 🔥 Uruchomienie

```bash
docker compose --env-file .env up -d
```

## 💻 Porty

| Usługa       | Adres lokalny          |
|--------------|------------------------|
| NGINX + PHP  | http://localhost:4001  |
| phpMyAdmin   | http://localhost:6001  |

## 🔐 Dostępy

- Użytkownik DB: `testuser`
- Hasło: `testpass` (przez secret)
- Root: `rootpass` (przez secret)

## 📦 Sieci

- `frontend`: nginx, phpmyadmin
- `backend`: nginx, php, mysql, phpmyadmin

## ✅ Co działa?

- `index.php` pokazuje `phpinfo()`
- phpMyAdmin pozwala zarządzać bazą
- `testdb` jest utworzona automatycznie

## 💡 Bonusy

- Zmienne środowiskowe w `.env`
- Hasła przez `secrets`
- Sieć logicznie rozdzielona
