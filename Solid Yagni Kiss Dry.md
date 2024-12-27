# Zasady SOLID, YAGNI, KISS i DRY

Te zasady to podstawowe filary, które pomagają w tworzeniu czytelnego, skalowalnego i łatwego w utrzymaniu kodu. Oto ich omówienie:

---

## **1. SOLID**  
SOLID to akronim pięciu zasad programowania obiektowego zaproponowanych przez Roberta C. Martina (Uncle Bob). Są one kluczowe dla projektowania dobrze zorganizowanych systemów:

- **S** - *Single Responsibility Principle (SRP)*  
  Klasa powinna mieć tylko jedną odpowiedzialność. Oznacza to, że każda klasa powinna zajmować się jednym, określonym zadaniem.  
  **Przykład:** Klasa do zarządzania plikami powinna tylko otwierać, zapisywać lub czytać pliki, a nie formatować dane.

- **O** - *Open/Closed Principle (OCP)*  
  Klasy powinny być otwarte na rozszerzanie, ale zamknięte na modyfikacje. Nowe funkcje powinny być dodawane przez dziedziczenie lub kompozycję, a nie przez zmiany w istniejącym kodzie.

- **L** - *Liskov Substitution Principle (LSP)*  
  Obiekty klasy bazowej powinny być wymienialne na obiekty klasy pochodnej bez zmiany działania programu.

- **I** - *Interface Segregation Principle (ISP)*  
  Klient nie powinien być zmuszany do implementowania interfejsów, których nie używa. Lepsze są mniejsze, bardziej precyzyjne interfejsy.

- **D** - *Dependency Inversion Principle (DIP)*  
  Moduły wysokopoziomowe nie powinny zależeć od modułów niskopoziomowych. Oba powinny zależeć od abstrakcji.

---

## **2. YAGNI**  
**"You Aren’t Gonna Need It"**  
Zasada przypomina, by nie tworzyć funkcjonalności, które mogą nigdy nie być użyte. Programuj tylko to, co jest wymagane tu i teraz, unikając nadmiarowej złożoności.  
**Przykład:** Jeśli tworzysz aplikację, która obsługuje zamówienia, nie implementuj od razu funkcji obsługi płatności kryptowalutami, jeśli nie ma na to zapotrzebowania.

---

## **3. KISS**  
**"Keep It Simple, Stupid"**  
Kod powinien być możliwie prosty i zrozumiały. Jeśli istnieją dwa rozwiązania problemu, wybierz to prostsze.  
**Przykład:** Zamiast pisać złożony algorytm sortowania, jeśli wystarcza gotowa funkcja `sort()` z biblioteki standardowej, użyj jej.

---

## **4. DRY**  
**"Don’t Repeat Yourself"**  
Kod nie powinien się powtarzać. Jeśli widzisz powtarzające się fragmenty kodu, przenieś je do wspólnej metody, klasy lub modułu.  
**Przykład:** Jeśli kilka metod w programie przelicza wartość podatku w ten sam sposób, lepiej utworzyć jedną wspólną funkcję.

---

## **Podsumowanie**  
Te zasady można traktować jako drogowskaz w tworzeniu kodu, który jest:
- czytelny,
- łatwy do utrzymania,
- gotowy na przyszłe zmiany.

**Czysty kod (Clean Code)**, książka Roberta C. Martina, jest doskonałym punktem startowym do pogłębiania tej wiedzy, zwłaszcza jeśli chcesz zrozumieć kontekst zastosowania tych zasad w praktyce.
