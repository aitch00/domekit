# Regole di divergenza — il contratto load-bearing di dm:ideation

Reference statica caricata da `dm:ideation`. Sono le tre regole che, se mancate, fanno fallire
l'ideazione anche quando le idee sembrano buone. Vivono qui perché devono agire **a runtime**, in
qualunque repo la skill giri.

---

## 1 · Separazione netta divergenza / convergenza (anti-pattern principale)

`dm:ideation` **diverge**. Non valuta, non scarta, non ordina, non indica "la migliore". La
convergenza — pro/contro, criteri, selezione — è interamente di `dm:concept`.

**Perché è load-bearing.** Mischiare i due momenti produce **convergenza prematura**: il cervello
(umano o modello) appena vede due idee inizia a confrontarle, e il confronto uccide le idee deboli
*prima* che abbiano avuto modo di essere sviluppate. Le idee "strane" — spesso le più preziose —
muoiono per prime. Tenere i due momenti separati protegge lo spazio delle possibilità finché non è
abbastanza ampio.

**Cosa NON fare in ideation:**
- ❌ "Questa è l'opzione migliore / più realistica / quella che consiglio"
- ❌ Ordinare le idee per fattibilità o valore
- ❌ Scartare un'idea perché "non funzionerebbe"
- ❌ Aggiungere un punteggio o una matrice di valutazione

**Cosa fare invece:** registrare per ogni idea l'**assunzione chiave** e il **rischio principale** —
senza giudicarli. Sono il materiale grezzo che `dm:concept` userà *dopo* per convergere. Annotare un
rischio non è valutare: è descrivere.

---

## 2 · HMW come gate

Ogni area di opportunità si apre riformulando il pain point in un **How Might We**:
*"Come potremmo [aiutare persona X] a [raggiungere obiettivo] nonostante [ostacolo]?"*

Se il pain point **non** si lascia riformulare in un HMW sensato — manca il chi, l'obiettivo o
l'ostacolo — è il segnale che il problema non è abbastanza chiaro per ideare sopra. **Dichiaralo**:

```
⚠ L'area "[area]" non si riformula in un HMW sensato: manca [chi / obiettivo / ostacolo].
  Idee generate ora rischiano di risolvere un problema mal definito. Chiarisci o salta.
```

Non inventare un HMW plausibile per coprire il buco: idee su un problema vago sono idee sprecate. Un
HMW troppo ampio ("come potremmo migliorare l'esperienza?") è anch'esso un fail del gate — va
ristretto a un ostacolo concreto.

---

## 3 · Diversità > quantità

Il self-check di fine area verifica la **diversità dei pattern**, non il numero di idee.

- 3 idee su 3 pattern realmente distanti (es. preventivo · gamification · analogia-medicina) >
  8 variazioni dello stesso pattern self-service.
- Se le idee di un'area collassano su 1-2 pattern, la divergenza è debole: sostituiscine una con un
  pattern distante (l'analogia da altro dominio è la leva più affidabile).

La quantità è un mezzo, non il fine. `--n` fissa un *target* di idee per area, ma una quarta idea
che ripete il pattern delle prime tre non aggiunge valore: meglio fermarsi a tre diverse.

---

## In sintesi

| Regola | Domanda di self-check | Se fallisce |
|--------|-----------------------|-------------|
| Divergenza pura | "Sto valutando o ordinando le idee?" | Rimuovi il giudizio; rimanda a dm:concept |
| HMW gate | "Il pain point ha chi/obiettivo/ostacolo?" | Dichiara l'area come non-ideabile; chiarisci |
| Diversità | "Le idee toccano ≥3 pattern distanti?" | Sostituisci con un pattern distante |
