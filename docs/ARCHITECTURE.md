# Architecture Proposal

## Overview
This project is a client-only React application built with Vite. It uses React Router for navigation, the PokeAPI as the data source, and browser localStorage for persisting favorites. The app is intentionally simple: users can browse Pokémon, search by name, view detail pages, and save favorites without authentication or a backend.

## Goals
- Keep the app easy to understand and maintain
- Separate UI, routing, data access, and persistence clearly
- Support a lightweight but scalable structure for future enhancements
- Avoid unnecessary complexity for a small, single-page-style application

## Proposed Folder Structure

```text
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

## Component Tree

### App shell
- App
  - Layout
    - Header
    - AppRoutes

### Pages
- HomePage
  - SearchBar
  - PokemonList
    - PokemonCard
      - FavoriteButton

- PokemonDetailPage
  - PokemonDetail

- FavoritesPage
  - PokemonList
    - PokemonCard
      - FavoriteButton

- NotFoundPage

This structure keeps page-level composition clear while allowing shared UI pieces to be reused across views.

## Routes
The application will use a minimal route set:

- `/` → Home page for browsing and searching Pokémon
- `/pokemon/:name` → Detailed view for a specific Pokémon
- `/favorites` → Page showing the user’s saved favorites
- `*` → Not found page

This route structure is simple and matches the core product requirements without introducing unnecessary complexity.

## State Management

### Local state
Local state should be used for UI concerns that only affect a single component or page.

Examples:
- Current search input value
- Loading and error state for a specific fetch
- Temporary UI state such as hover or selection state

### Shared state
Shared state should be used for data that needs to be used across multiple pages and components.

Examples:
- Favorites list
- Favorite toggle behavior
- Any app-wide status that should be reflected consistently throughout the interface

A React context is a good fit for favorites because the same data needs to be available from the home page, detail page, and favorites page.

## State Placement

### Favorites
Favorites are shared state and should live in a context provider.

Why:
- Favorite status must be visible from multiple views
- The same toggle action should update the app consistently
- The data should be available without prop drilling

### Search and page data
Search input and current results are best kept in the page or component that uses them.

Why:
- They are closely tied to the current view
- They do not need to be shared globally
- Keeping them local keeps the component logic easier to follow

### Pokémon detail data
Detail data should be loaded and managed by the detail page, or by a small data hook used by that page.

Why:
- The detail view is focused on one resource
- It keeps detail-specific loading logic close to the UI that needs it

## Data Flow
1. The user opens the app.
2. The home page requests Pokémon data from the PokeAPI.
3. The list is rendered with search and selection behavior.
4. If the user opens a detail page, the app requests the selected Pokémon’s data.
5. If the user marks a Pokémon as a favorite, the app updates shared favorites state and persists it in localStorage.
6. On reload, the app restores favorites from storage and rehydrates the UI state.

## API Organization
The PokeAPI integration should be isolated from UI components so that the app remains maintainable.

### Proposed responsibilities
- api/pokemonApi.js
  - Contains low-level fetch requests to the PokeAPI
  - Handles endpoint calls such as listing Pokémon and fetching a single Pokémon

- api/pokemonService.js
  - Converts raw API responses into the shape needed by the UI
  - Centralizes any data mapping or normalization logic

This separation keeps components focused on rendering and user interaction rather than direct API details.

## Persistence Strategy
Favorites should be stored in browser localStorage so they survive page reloads.

Recommended approach:
- A small storage helper in the utils layer reads and writes favorites
- The favorites context uses this helper to initialize state and save updates

This keeps persistence logic centralized and avoids spreading storage logic across components.

## Design Principles
- Keep components focused and small
- Prefer simple, readable state flow over over-engineering
- Make data access reusable and predictable
- Separate product-facing UI from technical data concerns

## Review Notes
This proposal is intended to be a practical starting point for implementation. It prioritizes clarity and maintainability while staying aligned with the product scope.
