# Whiskering homotopies

```agda
module foundation.whiskering-homotopies where

open import foundation-core.whiskering-homotopies public
```

<details><summary>Imports</summary>

```agda
open import foundation.action-on-identifications-functions
open import foundation.commuting-squares-of-homotopies
open import foundation.commuting-squares-of-identifications
open import foundation.homotopy-induction
open import foundation.path-algebra
open import foundation.universe-levels

open import foundation-core.equivalences
open import foundation-core.function-extensionality
open import foundation-core.function-types
open import foundation-core.functoriality-dependent-function-types
open import foundation-core.homotopies
open import foundation-core.identity-types
open import foundation-core.postcomposition-functions
open import foundation-core.precomposition-functions
```

</details>

## Idea

There are two **whiskering operations** on
[homotopies](foundation-core.homotopies.md). The **left whiskering** operation
assumes a diagram of the form

```text
      f
    ----->     h
  A -----> B -----> C
      g
```

and is defined to be a function `h ·l_ : (f ~ g) → (h ∘ f ~ h ∘ g)`. The **right
whiskering** operation assumes a diagram of the form

```text
               g
      f      ----->
  A -----> B -----> C
               h
```

and is defined to be a function `_·r f : (g ~ h) → (g ∘ f ~ h ∘ f)`.

**Note**: The infix whiskering operators `_·l_` and `_·r_` use the
[middle dot](https://codepoints.net/U+00B7) `·` (agda-input: `\cdot`
`\centerdot`), as opposed to the infix homotopy concatenation operator `_∙h_`
which uses the [bullet operator](https://codepoints.net/U+2219) `∙` (agda-input:
`\.`). If these look the same in your editor, we suggest that you change your
font. For more details, see [How to install](HOWTO-INSTALL.md).

## Properties

### Computation of function extensionality on whiskerings

```agda
module _
  { l1 l2 l3 : Level} {A : UU l1} {B : UU l2} {C : UU l3}
  { f g : B → C} (h : A → B)
  where

  compute-eq-htpy-htpy-eq-right-whisk :
    ( p : f ＝ g) →
    eq-htpy ((htpy-eq p) ·r h) ＝ ap (precomp h C) p
  compute-eq-htpy-htpy-eq-right-whisk refl =
    eq-htpy-refl-htpy (f ∘ h)

  compute-eq-htpy-right-whisk :
    ( H : f ~ g) →
    eq-htpy (H ·r h) ＝ ap (precomp h C) (eq-htpy H)
  compute-eq-htpy-right-whisk H =
    ( ap
      ( λ K → eq-htpy (K ·r h))
      ( inv (is-section-eq-htpy H))) ∙
    ( compute-eq-htpy-htpy-eq-right-whisk (eq-htpy H))
```

```agda
module _
  { l1 l2 l3 : Level} {A : UU l1} {B : UU l2} {C : UU l3}
  { f g : A → B} (h : B → C)
  where

  compute-eq-htpy-htpy-eq-left-whisk :
    ( p : f ＝ g) → eq-htpy (h ·l (htpy-eq p)) ＝ ap (postcomp A h) p
  compute-eq-htpy-htpy-eq-left-whisk refl =
    eq-htpy-refl-htpy (h ∘ f)

  compute-eq-htpy-left-whisk :
    (H : f ~ g) →
    eq-htpy (h ·l H) ＝ ap (postcomp A h) (eq-htpy H)
  compute-eq-htpy-left-whisk H =
    ( ap
      ( λ K → eq-htpy (h ·l K))
      ( inv (is-section-eq-htpy H))) ∙
    ( compute-eq-htpy-htpy-eq-left-whisk (eq-htpy H))
```

### Whiskering a square of homotopies by a homotopy is an equivalence

A
[commuting square of homotopies](foundation.commuting-squares-of-homotopies.md)
may be whiskered by a homotopy `L` on the left or right, which results in a
commuting square of homotopies with `L` appended or prepended to the two ways of
going around the square.

Diagrammatically, we may turn the pasting diagram

```text
        H
    f ~~~~~ g
    ︴      ︴
 H' ︴  ⇗   ︴ K
    ︴      ︴
   g' ~~~~~ h ~~~~~ k
       K'       L
```

into a commmuting square

```text
        H
    f ~~~~~ g
    ︴      ︴
 H' ︴  ⇗   ︴K ∙h L
    ︴      ︴
   g' ~~~~~ k
     K' ∙h L   ,
```

and similarly for the other side.

```agda
module _
  { l1 l2 : Level} {A : UU l1} {B : UU l2}
  { f g g' h k : A → B}
  where

  module _
    ( H : f ~ g) (H' : f ~ g') {K : g ~ h} {K' : g' ~ h} (L : h ~ k)
    where

    equiv-right-whisk-square-htpy :
      ( coherence-square-homotopies H H' K K') ≃
      ( coherence-square-homotopies H H' (K ∙h L) (K' ∙h L))
    equiv-right-whisk-square-htpy =
      equiv-Π-equiv-family
        ( λ a → equiv-right-whisk-square-identification (H a) (H' a) (L a))

    right-whisk-square-htpy :
      coherence-square-homotopies H H' K K' →
      coherence-square-homotopies H H' (K ∙h L) (K' ∙h L)
    right-whisk-square-htpy = map-equiv equiv-right-whisk-square-htpy

    right-unwhisk-square-htpy :
      coherence-square-homotopies H H' (K ∙h L) (K' ∙h L) →
      coherence-square-homotopies H H' K K'
    right-unwhisk-square-htpy = map-inv-equiv equiv-right-whisk-square-htpy

  module _
    ( L : k ~ f) {H : f ~ g} {H' : f ~ g'} {K : g ~ h} {K' : g' ~ h}
    where

    equiv-left-whisk-square-htpy :
      ( coherence-square-homotopies H H' K K') ≃
      ( coherence-square-homotopies (L ∙h H) (L ∙h H') K K')
    equiv-left-whisk-square-htpy =
      equiv-Π-equiv-family
        ( λ a → equiv-left-whisk-square-identification (L a))

    left-whisk-square-htpy :
      coherence-square-homotopies H H' K K' →
      coherence-square-homotopies (L ∙h H) (L ∙h H') K K'
    left-whisk-square-htpy = map-equiv equiv-left-whisk-square-htpy

    left-unwhisk-square-htpy :
      coherence-square-homotopies (L ∙h H) (L ∙h H') K K' →
      coherence-square-homotopies H H' K K'
    left-unwhisk-square-htpy = map-inv-equiv equiv-left-whisk-square-htpy

module _
  { l1 l2 : Level} {A : UU l1} {B : UU l2}
  { f g h h' k m : A → B}
  ( H : f ~ g) {K : g ~ h} {K' : g ~ h'} {L : h ~ k} {L' : h' ~ k} (M : k ~ m)
  where

  equiv-both-whisk-square-htpy :
    ( coherence-square-homotopies K K' L L') ≃
    ( coherence-square-homotopies (H ∙h K) (H ∙h K') (L ∙h M) (L' ∙h M))
  equiv-both-whisk-square-htpy =
    equiv-Π-equiv-family
      ( λ a → equiv-both-whisk-square-identifications (H a) (M a))

  both-whisk-square-htpy :
    ( coherence-square-homotopies K K' L L') →
    ( coherence-square-homotopies (H ∙h K) (H ∙h K') (L ∙h M) (L' ∙h M))
  both-whisk-square-htpy = map-equiv equiv-both-whisk-square-htpy

  both-unwhisk-square-htpy :
    ( coherence-square-homotopies (H ∙h K) (H ∙h K') (L ∙h M) (L' ∙h M)) →
    ( coherence-square-homotopies K K' L L')
  both-unwhisk-square-htpy = map-inv-equiv equiv-both-whisk-square-htpy
```

### Whiskering a square of homotopies by a map

Given a square of homotopies

```text
        H
    g ~~~~~ h
    ︴      ︴
 H' ︴  ⇗   ︴ K
    ︴      ︴
   h' ~~~~~ k
       K'
```

and a map `f`, we may whisker it by a map on the left into a square of
homotopies

```text
           f ·l H
         fg ~~~~~ fh
         ︴        ︴
 f ·l H' ︴   ⇗    ︴f ·l K
         ︴        ︴
        fh' ~~~~~ fk
           f ·l K' ,
```

and similarly we may whisker it on the right.

```agda
module _
  { l1 l2 l3 : Level} {A : UU l1} {B : UU l2} {C : UU l3}
  ( f : B → C) {g h h' k : A → B}
  ( H : g ~ h) (H' : g ~ h') {K : h ~ k} {K' : h' ~ k}
  where

  ap-left-whisk-coherence-square-homotopies :
    coherence-square-homotopies H H' K K' →
    coherence-square-homotopies (f ·l H) (f ·l H') (f ·l K) (f ·l K')
  ap-left-whisk-coherence-square-homotopies α a =
    coherence-square-identifications-ap f (H a) (H' a) (K a) (K' a) (α a)

module _
  { l1 l2 l3 : Level} {A : UU l1} {B : UU l2} {C : UU l3}
  { g h h' k : B → C} (H : g ~ h) (H' : g ~ h') {K : h ~ k} {K' : h' ~ k}
  ( f : A → B)
  where

  ap-right-whisk-coherence-square-homotopies :
    coherence-square-homotopies H H' K K' →
    coherence-square-homotopies (H ·r f) (H' ·r f) (K ·r f) (K' ·r f)
  ap-right-whisk-coherence-square-homotopies α = α ·r f
```

### The two definitions of horizontal concatenation of homotopies agree

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} {B : A → UU l2} {C : A → UU l3}
  where

  coh-left-unit-htpy-comp-horizontal :
    {f : (x : A) → B x} {g g' : {x : A} → B x → C x}
    (G : {x : A} → g {x} ~ g' {x}) →
    htpy-comp-horizontal (refl-htpy' f) G ~
    htpy-comp-horizontal' (refl-htpy' f) G
  coh-left-unit-htpy-comp-horizontal G = inv-htpy-right-unit-htpy

  coh-right-unit-htpy-comp-horizontal :
    {f f' : (x : A) → B x} {g : {x : A} → B x → C x}
    (F : f ~ f') →
    htpy-comp-horizontal F (refl-htpy' g) ~
    htpy-comp-horizontal' F (refl-htpy' g)
  coh-right-unit-htpy-comp-horizontal F = right-unit-htpy

  coh-htpy-comp-horizontal :
    {f f' : (x : A) → B x} {g g' : {x : A} → B x → C x}
    (F : f ~ f') (G : {x : A} → g {x} ~ g' {x}) →
    htpy-comp-horizontal F G ~ htpy-comp-horizontal' F G
  coh-htpy-comp-horizontal {f} F G =
    ind-htpy f
      ( λ f' F → htpy-comp-horizontal F G ~ htpy-comp-horizontal' F G)
      ( coh-left-unit-htpy-comp-horizontal G)
      ( F)
```

## See also

- For interactions between whiskering and exponentiation, see
  [`foundation.composition-algebra`](foundation.composition-algebra.md).
