# Expense Tracker: Bot RPA do Weryfikacji Wyciągów Bankowych

> Moduł automatyzujący sprawdzanie wydatków, stworzony w UiPath jako rozszerzenie aplikacji `expense-tracker`.

## 🎯 Cel Projektu
Ręczne porównywanie wyciągów bankowych z bazą danych jest uciążliwe i podatne na błędy. Ten bot RPA automatyzuje ten proces - pobiera na żywo dane z API REST (tego samego, z którym komunikuje się frontend webowy), porównuje je z wyciągami w formacie Excel i generuje końcowy raport. 

Co kluczowe, bot potrafi wyłapać **błędy ludzkie** (np. złe przypisanie kategorii w systemie) dzięki mechanizmowi Fuzzy Matching. Robi to w pamięci operacyjnej za pomocą zapytań LINQ, by nie obciążać API.

## ⚙️ Architektura i Technologie
Skrypt RPA pełni rolę klienta dla backendu opartego na Node.js, wykorzystując dynamiczne logowanie za pomocą tokenów.

* **Platforma RPA:** UiPath Studio
* **Logika Główna:** VB.NET, LINQ
* **Integracja:** REST API, parsowanie JSON
* **Bezpieczeństwo:** Dynamiczne tokeny JWT i autoryzacja Bearer
* **Przetwarzanie Danych:** In-memory DataTables

## 🎥 Demo Wideo
https://github.com/user-attachments/assets/699a286b-f7f5-4c33-ae8a-3aecf1de725e

## 📸 Logika Procesu
Poniżej znajduje się zapytanie LINQ odpowiedzialne za szybką walidację danych w pamięci:

```vb.net
json_ExpensesArray.AsEnumerable().Any(Function(exp) Val(exp("totalAmount").ToString) = Val(CurrentRow("Amount").ToString.Replace(",",".")) AndAlso exp.SelectToken("category.name") IsNot Nothing AndAlso CurrentRow("Description").ToString.ToLower().Contains(exp.SelectToken("category.name").ToString.ToLower()))
```

## 🚀 Jak to działa
1. **Logowanie:** Bot uderza do backendu (POST), logując się na konto użytkownika, aby pobrać jego indywidualny token JWT.
2. **Pobieranie Danych:** Wykonuje autoryzowane żądanie GET, pobiera listę wydatków użytkownika i parsuje odpowiedź JSON.
3. **Odczyt Wyciągu:** Bot wczytuje plik `Statement.xlsx` z eksportem z banku.
4. **Walidacja (LINQ):** Przeszukuje wiersze Excela i łączy je z danymi z JSON na podstawie:
   * Kwoty transakcji.
   * Częściowego dopasowania nazwy kategorii do opisu z banku.
5. **Raportowanie:** Bot generuje końcowy plik `Report.xlsx` z dodaną kolumną "Status", wskazując transakcje poprawne i te wymagające ręcznej weryfikacji.

## 🔗 Linki do Projektu
Ten bot jest częścią większej aplikacji fullstackowej:
* **Repozytorium:** [github.com/K-Piask/expense-tracker](https://github.com/K-Piask/expense-tracker)
* **Wersja Live (Vercel):** [expense-tracker-web-delta-seven.vercel.app](https://expense-tracker-web-delta-seven.vercel.app)
