# Prognozowanie kwartalnych wyników finansowych z wykorzystaniem ML i LLM

Repozytorium zawiera kod źródłowy wykorzystany w badaniu empirycznym dotyczącym prognozowania kwartalnych wyników finansowych spółek notowanych na Giełdzie Papierów Wartościowych w Warszawie.

## Cel badania

Celem eksperymentu jest porównanie jakości prognoz kwartalnych:

- przychodów ze sprzedaży,
- wyniku operacyjnego EBIT,
- zysku netto.

W badaniu porównuje się pięć podejść prognostycznych:

1. model sezonowo naiwny (Seasonal Naive),
2. regresję liniową,
3. XGBoost,
4. LLM-1 - duży model językowy wykorzystujący wyłącznie historię danych liczbowych,
5. LLM-2 - ten sam model językowy wykorzystujący historię danych liczbowych oraz oficjalny kontekst tekstowy publikowany przez przedsiębiorstwa.

## Badana próba

Analiza obejmuje 10 spółek:

- ORLEN,
- KGHM Polska Miedź,
- Orange Polska,
- Asseco Poland,
- Budimex,
- LPP,
- CD Projekt,
- Cyfrowy Polsat,
- PGE,
- Grupa Kęty.

Dane kwartalne obejmują lata 2015-2024. Okres testowy obejmuje lata 2023-2024, czyli 8 kwartałów dla każdej spółki i łącznie 80 obserwacji testowych dla każdej prognozowanej zmiennej.

## Zawartość repozytorium

```text
.
├── analiza_prognoz_finansowych.ipynb  # pełny kod badania
├── README.md                           # opis repozytorium i instrukcja uruchomienia
├── requirements.txt                   # biblioteki wykorzystywane w notebooku
└── .gitignore                         # pliki robocze, wyniki i sekrety wyłączone z Git
```

Notebook zawiera cały pipeline badawczy: pobranie i przygotowanie danych, eksploracyjną analizę danych, konstrukcję cech, modele oparte na danych liczbowych, przygotowanie i wykonanie eksperymentów LLM-1 i LLM-2, obliczenie mierników jakości, analizę wyników na poziomie spółek, test Wilcoxona, bootstrap klastrowy oraz generowanie wykresów.

## Źródła danych

Dane liczbowe są pobierane przez kod z serwisu BiznesRadar.pl. Materiały tekstowe dla wariantu LLM-2 są pozyskiwane z oficjalnych stron relacji inwestorskich analizowanych przedsiębiorstw. Notebook zawiera adresy i logikę pobierania materiałów wykorzystywanych w eksperymencie.

Ponieważ strony internetowe i adresy dokumentów mogą zmieniać się w czasie, ponowne uruchomienie części odpowiedzialnej za pobieranie danych lub dokumentów może wymagać aktualizacji adresów źródłowych.

## Wymagania

Do uruchomienia potrzebne są:

- Python 3,
- Jupyter Notebook lub JupyterLab,
- dostęp do Internetu,
- klucz OpenAI API dla części wykorzystującej modele językowe.

Biblioteki wymagane przez notebook są wymienione w pliku `requirements.txt`. Pierwsza komórka notebooka również instaluje wymagane pakiety.

Instalacja zależności z terminala:

```bash
pip install -r requirements.txt
```

## Klucz OpenAI API

Klucza API **nie należy umieszczać w kodzie ani przesyłać do repozytorium**.

Notebook najpierw próbuje odczytać zmienną środowiskową:

```text
OPENAI_API_KEY
```

Jeżeli zmienna nie jest ustawiona, notebook prosi o podanie klucza w sposób niewidoczny w interfejsie Jupyter. Klucz jest wtedy wykorzystywany wyłącznie w bieżącej sesji.

Przykład ustawienia zmiennej środowiskowej w systemie macOS/Linux:

```bash
export OPENAI_API_KEY="TWÓJ_KLUCZ"
```

W PowerShell:

```powershell
$env:OPENAI_API_KEY="TWÓJ_KLUCZ"
```

Nie należy zapisywać rzeczywistego klucza w plikach `README.md`, notebooku ani innych plikach przesyłanych do GitHub.

## Uruchomienie analizy

1. Sklonuj lub pobierz repozytorium.
2. Otwórz `analiza_prognoz_finansowych.ipynb` w Jupyter Notebook/JupyterLab.
3. Upewnij się, że komputer ma dostęp do Internetu.
4. Ustaw `OPENAI_API_KEY` przed uruchomieniem części LLM lub podaj klucz po wyświetleniu bezpiecznego monitu.
5. Uruchamiaj komórki notebooka kolejno od początku do końca.

Notebook zapisuje pliki pośrednie i wyniki w bieżącym katalogu roboczym. Są to między innymi arkusze Excel, pobrane dokumenty PDF oraz wykresy. Pliki te są wyłączone z repozytorium przez `.gitignore`, ponieważ mogą zostać odtworzone podczas wykonywania kodu.

## Główne etapy pipeline'u

1. Pobranie kwartalnych danych finansowych dla lat 2015–2024.
2. Kontrola i przygotowanie zbioru danych.
3. Eksploracyjna analiza danych (EDA).
4. Utworzenie cech opóźnionych i podział chronologiczny train/test.
5. Prognozy Seasonal Naive, regresji liniowej i XGBoost.
6. Rozszerzenie oceny o MAE, RMSE, sMAPE i WAPE.
7. Przygotowanie i wykonanie eksperymentu LLM-1.
8. Pobranie i ekstrakcja oficjalnych materiałów tekstowych dla LLM-2.
9. Wykonanie prognoz LLM-2.
10. Porównanie wszystkich pięciu podejść.
11. Analiza wyników według spółek.
12. Test rangowanych znaków Wilcoxona z korektą Holma oraz bootstrap klastrowy.
13. Generowanie wykresów wykorzystanych w analizie wyników.

## Najważniejsze ustawienia eksperymentu

- długość historii przekazywanej do LLM-1 i LLM-2: 8 kwartałów,
- okres testowy: 2023–2024,
- XGBoost: `n_estimators=300`, `max_depth=3`, `learning_rate=0.03`, `subsample=0.8`, `colsample_bytree=0.8`, `random_state=42`,
- bootstrap klastrowy: 10 000 replikacji,
- ziarno losowe bootstrapu: `140343`.

## Pliki wynikowe

Podczas wykonywania notebooka powstają między innymi:

- `dane_finansowe_gpw_2015_2024.xlsx`,
- `dane_finansowe_gpw_2015_2024_dataset_ML.xlsx`,
- `EDA_podsumowanie.xlsx`,
- `wyniki_modeli_ML.xlsx`,
- `wyniki_modeli_ML_rozszerzone.xlsx`,
- `dane_wejsciowe_LLM_liczby_v3.xlsx`,
- `wyniki_LLM_liczby_v3.xlsx`,
- pliki z materiałami i wynikami LLM-2,
- `FINALNE_wyniki_badania.xlsx`,
- `ANALIZA_spolki_i_zwyciestwa.xlsx`,
- `ANALIZA_odpornosci_LLM.xlsx`,
- wykresy `.png` i `.pdf`.

## Reprodukowalność

Kod zachowuje chronologię danych wykorzystywanych w eksperymencie. Należy jednak pamiętać, że część analizy korzysta z zewnętrznych serwisów internetowych oraz API. Zmiany treści stron, adresów dokumentów, dostępności usług lub odpowiedzi modelu mogą wpływać na możliwość uzyskania identycznych rezultatów przy ponownym uruchomieniu w innym czasie.

Każda prognoza LLM w badaniu jest generowana pojedynczo. Wyniki generatywnego modelu językowego nie muszą być deterministycznie identyczne pomiędzy kolejnymi uruchomieniami.

## Bezpieczeństwo

Repozytorium nie powinno zawierać:

- kluczy API,
- plików `.env`,
- innych danych uwierzytelniających.

Przed każdym `git push` należy sprawdzić, czy żaden sekret nie został przypadkowo zapisany w notebooku lub innym pliku.

## Kontekst akademicki

Kod został przygotowany jako część badania empirycznego pracy magisterskiej dotyczącej zastosowania metod uczenia maszynowego i dużych modeli językowych w prognozowaniu kwartalnych wyników finansowych spółek giełdowych.
