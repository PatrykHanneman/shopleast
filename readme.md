# 🛒 Lista Zakupów — aplikacja

Zbudowałem aplikację webową do wspólnego prowadzenia listy zakupów — coś w stylu narzędzia dla domowników albo współlokatorów, żeby nie trzeba było pytać "czy mleko już jest na liście?".

URL: https://incomparable-blini-616f96.netlify.app/

## Co robi?

Każdy zakłada własne konto z hasłem (bezpiecznie szyfrowanym, nigdy nie trzymanym jawnym tekstem). Po zalogowaniu tworzę listę i mogę zaprosić kogoś do niej jednorazowym kodem — druga osoba loguje się na swoje konto, wpisuje kod i od tej pory widzimy tę samą listę na żywo. Jak ja dodam mleko na telefonie, u niej pojawia się od razu, bez odświeżania.

Produkty mają kategorię (warzywa, nabiał, pieczywo itd.) i ilość, można je odznaczać jako kupione i filtrować. Jest też zakładka analityki — pokazuje co kupuję najczęściej, jak często robię zakupy, kto z domowników co dodaje.

A na koniec dodałem coś, z czego jestem najbardziej zadowolony: zakładkę **ML**, która trenuje mały model sieci neuronowej bezpośrednio w przeglądarce, na podstawie mojej historii zakupów, i sama podpowiada czego prawdopodobnie zabraknie — bez wysyłania żadnych danych na zewnątrz, model liczy się lokalnie.

## Funkcje

- ✅ Logowanie i rejestracja (hasła szyfrowane)
- ✅ Wiele list zakupów, współdzielonych przez kod zaproszenia
- ✅ Synchronizacja w czasie rzeczywistym (WebSockets)
- ✅ Kategorie i ilości produktów
- ✅ Filtrowanie (wszystkie / do kupienia / kupione)
- ✅ Analityka zakupów (częstotliwość, kategorie, aktywność domowników)
- ✅ Sugestie produktów oparte na ML (TensorFlow.js, lokalnie w przeglądarce)

## Stack technologiczny

**Frontend**
- Czysty `HTML` / `CSS` / `JavaScript` — bez frameworka, celowo lekkie
- Hostowane jako statyczna strona na **Netlify**

**Backend**
- **Supabase** (PostgreSQL) — logowanie, baza danych, realtime
- **Row Level Security** wymuszone bezpośrednio w bazie — nawet przy bezpośrednich zapytaniach do API użytkownik widzi tylko swoje dane

**Machine Learning**
- **TensorFlow.js** — trenowanie i inferencja sieci neuronowej lokalnie w przeglądarce, bez wysyłania danych na zewnątrz

## Architektura

```text
┌─────────────────────┐      ┌─────────────────────┐
│ Frontend            │ ───► │ Supabase Auth       │ logowanie / rejestracja
│ HTML/CSS/JS         │      ├─────────────────────┤
│ Netlify             │ ───► │ PostgreSQL + RLS    │ listy, produkty, zaproszenia
└─────────────────────┘ ◄──► ├─────────────────────┤
                             │ Realtime (WS)       │ synchronizacja na żywo
                             └─────────────────────┘
                                       │
                                       ▼
                              ┌─────────────────────┐
                              │ TensorFlow.js       │ trening i predykcja lokalnie
                              └─────────────────────┘
```
## Co było ciekawe w budowaniu tego

Najwięcej czasu zajęło dopracowanie reguł bezpieczeństwa w bazie — trzeba było rozpisać precyzyjnie, kto może co widzieć i edytować (właściciel listy, zaproszony współużytkownik, ktoś z zewnątrz), i upewnić się, że te reguły nie gryzą się ze sobą przy takich operacjach jak dołączanie do listy przez kod zaproszenia.