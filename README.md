# Unidos — Gastos en pareja

App para que ambos novios registren y vean juntos los gastos de la boda, la luna de miel, la casa y otros objetivos (terreno, ahorro, etc.).

## 1. Publicar en GitHub Pages

```bash
cd unidos-app
git init
git add .
git commit -m "Unidos: app de gastos para la pareja"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/unidos-app.git
git push -u origin main
```

Luego en GitHub: **Settings → Pages → Source: Deploy from a branch → Branch: main / (root)**.
En un par de minutos el enlace queda activo en:
`https://TU-USUARIO.github.io/unidos-app/`

Ese es el enlace que comparten los dos: cada uno lo abre desde su celular y en
Chrome (Android) o Safari (iOS) usa "Añadir a pantalla de inicio" para que
quede instalada como app, con ícono propio.

## 2. Estado actual del guardado de datos

Ahora mismo la app guarda los gastos y objetivos en el almacenamiento local
del navegador (`localStorage`) **de cada dispositivo por separado**. Es decir:
funciona perfecto para probarla, pero el celular de la novia y el del novio
todavía no se sincronizan entre sí.

## 3. Activar la sincronización real (recomendado: Supabase)

Como ya usas Supabase en RegCampo, es el camino más directo:

1. En tu proyecto de Supabase (puede ser el mismo de RegCampo o uno nuevo),
   abre el SQL Editor y ejecuta:

   ```sql
   create table unidos_data (
     id text primary key,
     payload jsonb not null default '{}'::jsonb,
     updated_at timestamptz default now()
   );

   alter table unidos_data enable row level security;

   create policy "acceso publico anon"
     on unidos_data for all
     using (true)
     with check (true);
   ```

   (La policy es abierta a propósito porque solo ustedes dos van a conocer el
   enlace de la app; si prefieres más seguridad luego podemos añadir un PIN
   simple o autenticación con Supabase Auth.)

2. En `index.html`, busca estas dos líneas cerca del final del `<script>`:

   ```js
   const SUPABASE_URL = "";
   const SUPABASE_ANON_KEY = "";
   ```

   y complétalas con los datos de **Project Settings → API** de tu proyecto
   Supabase (`Project URL` y `anon public key`).

3. Sube el cambio (`git add . && git commit -m "conectar supabase" && git push`)
   y listo: desde ese momento, cualquier gasto que agregue uno lo verá el otro
   la próxima vez que abra la app (se refresca solo cada 12 segundos mientras
   está abierta).

## 4. Íconos y PWA

`manifest.json` y `sw.js` ya están listos para que la app se pueda instalar
como aplicación en Android e iOS, con su propio ícono (`icon-192.png`,
`icon-512.png`, `apple-touch-icon.png`).
