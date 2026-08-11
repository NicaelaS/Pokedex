# COPILOT

## Stack and Versions
- Node: >=16 (development)
- Vite: ^8.2.0
- React: ^19.2.8
- React DOM: ^19.2.8
- @vitejs/plugin-react: ^6.0.4

(See `package.json` for exact dependency entries.)

## Project Folder Structure
Based on the architecture proposal in `docs/ARCHITECTURE.md`:

```
src/
  api/
    pokemonApi.js
    pokemonService.js

  components/
    common/
      Layout.jsx
      Header.jsx
      SearchBar.jsx
      EmptyState.jsx
    pokemon/
      PokemonCard.jsx
      PokemonList.jsx
      PokemonDetail.jsx
      FavoriteButton.jsx

  pages/
    HomePage.jsx
    PokemonDetailPage.jsx
    FavoritesPage.jsx
    NotFoundPage.jsx

  hooks/
    useFavorites.js
    usePokemonSearch.js

  context/
    FavoritesContext.jsx

  utils/
    storage.js
    formatters.js

  routes/
    AppRoutes.jsx

  App.jsx
  main.jsx
  styles/
    global.css
```

## How to run the app locally
1. Install dependencies

```bash
npm install
```

2. Run the development server

```bash
npm run dev
```

3. Build for production

```bash
npm run build
```

4. Preview a production build

```bash
npm run preview
```

Notes:
- Vite exposes the app on `http://localhost:5173` by default unless configured otherwise.

## LocalStorage Convention
All direct interactions with browser `localStorage` MUST be centralized in a single place:

- Only `src/hooks/useFavorites.js` may read from or write to `localStorage` directly.
- Other modules/components should use the `useFavorites` hook (or the `FavoritesContext`) to access and update favorites.
- If a utility function in `utils/storage.js` is used, it should only be called from `useFavorites.js` and should not be imported directly across the app.

Reasoning:
- Centralizing persistence avoids scattered side effects and makes it easier to change storage strategy later (for example, moving to IndexedDB or syncing with a backend).
- It ensures consistent serialization and error handling for stored favorites.

## Where to document exceptions
If you need to persist other data outside of favorites, add a short note in `docs/REVIEW.md` describing the change and why direct storage access was required.

---

Created for the Pokedex React app. Keep this file updated when stack versions or conventions change.
