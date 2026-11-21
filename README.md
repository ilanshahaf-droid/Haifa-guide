# מדריך חיפה · Haifa Guide

A mobile-first, trilingual (he / en / fr) recommendations guide for Haifa, the Carmel, the Krayot, Atlit and the Carmel Coast.

## Files
- `index.html` – the whole app (UI, i18n, map, filters). Content is not in the code.
- `data.json` – all places and events. Edit this file to add, update or remove content.
- `vendor/` – Leaflet 1.9.4 (map library), served locally.
- `manifest.json` – lets you install it as a PWA.

## Item format (`data.json` → `items[]`)
| field | notes |
|---|---|
| `id` | unique string |
| `category` | `attractions` · `restaurants` · `free` · `events` |
| `price` | `free`, `₪`, `₪₪`, `₪₪₪`. Anything with `free` also shows in the "Free" tab |
| `rating` | the guide's own score, 0–5 |
| `lat`, `lng` | coordinates |
| `added` | `YYYY-MM-DD`. Items added in the last 7 days get a "New" badge |
| `start`, `end` | events only (`YYYY-MM-DDTHH:MM`). Events whose end has passed are hidden automatically |
| `image` | optional photo URL. If empty or broken, a category illustration is shown |
| `link` | optional website / tickets |
| `name`, `desc`, `address`, `hours` | objects with `he`, `en` and `fr` |

## Switching to Supabase (optional)
Fill `CONFIG.supabase.url` and `anonKey` in `index.html`. If Supabase fails, the app falls back to `data.json`.

```sql
create table public.places (
  id text primary key,
  category text not null check (category in ('attractions','restaurants','free','events')),
  price text, rating numeric, lat double precision not null, lng double precision not null,
  added date default current_date, start timestamptz, "end" timestamptz,
  image text, link text,
  name jsonb not null, "desc" jsonb, address jsonb, hours jsonb,
  active boolean default true
);
alter table public.places enable row level security;
create policy "public read" on public.places for select using (active);
grant select on public.places to anon;   -- needed for new tables (Data API grant change)
```
