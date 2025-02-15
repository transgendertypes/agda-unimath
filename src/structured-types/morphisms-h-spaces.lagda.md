# Morphisms of H-spaces

```agda
module structured-types.morphisms-h-spaces where
```

<details><summary>Imports</summary>

```agda
open import foundation.action-on-identifications-binary-functions
open import foundation.action-on-identifications-functions
open import foundation.dependent-pair-types
open import foundation.function-types
open import foundation.homotopies
open import foundation.identity-types
open import foundation.path-algebra
open import foundation.universe-levels

open import group-theory.homomorphisms-semigroups

open import structured-types.h-spaces
open import structured-types.pointed-maps
```

</details>

## Idea

Morphisms of [H-spaces](structured-types.h-spaces.md) are
[pointed maps](structured-types.pointed-maps.md) that preserve the unital binary
operation, including its laws.

## Definition

### Morphisms of H-spaces

```agda
preserves-left-unit-law-mul :
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  (μ : A → A → A) {eA : A} → ((x : A) → Id (μ eA x) x) →
  (ν : B → B → B) {eB : B} → ((y : B) → Id (ν eB y) y) →
  (f : A → B) → Id (f eA) eB → preserves-mul μ ν f → UU (l1 ⊔ l2)
preserves-left-unit-law-mul {A = A} {B} μ {eA} lA ν {eB} lB f p μf =
  (x : A) → Id (ap f (lA x)) (μf ∙ (ap (λ t → ν t (f x)) p ∙ lB (f x)))

preserves-right-unit-law-mul :
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  (μ : A → A → A) {eA : A} → ((x : A) → Id (μ x eA) x) →
  (ν : B → B → B) {eB : B} → ((y : B) → Id (ν y eB) y) →
  (f : A → B) → Id (f eA) eB → preserves-mul μ ν f → UU (l1 ⊔ l2)
preserves-right-unit-law-mul {A = A} {B} μ {eA} rA ν {eB} rB f p μf =
  (x : A) → Id (ap f (rA x)) (μf ∙ (ap (ν (f x)) p ∙ rB (f x)))

preserves-coh-unit-laws-mul :
  {l1 l2 : Level} (M : H-Space l1) (N : H-Space l2) →
  ( f : pointed-type-H-Space M →∗ pointed-type-H-Space N) →
  ( μf :
    preserves-mul (mul-H-Space M) (mul-H-Space N) (pr1 f)) →
  preserves-left-unit-law-mul
    ( mul-H-Space M)
    ( left-unit-law-mul-H-Space M)
    ( mul-H-Space N)
    ( left-unit-law-mul-H-Space N)
    ( pr1 f)
    ( pr2 f)
    ( μf) →
  preserves-right-unit-law-mul
    ( mul-H-Space M)
    ( right-unit-law-mul-H-Space M)
    ( mul-H-Space N)
    ( right-unit-law-mul-H-Space N)
    ( pr1 f)
    ( pr2 f)
    ( μf) →
  UU l2
preserves-coh-unit-laws-mul M
  (pair (pair N ._) μ)
  (pair f refl) μf lf rf =
  Id (ap (ap f) cM ∙ rf eM) (lf eM ∙ ap (concat μf (f eM)) cN)
  where
  eM = unit-H-Space M
  cM = coh-unit-laws-mul-H-Space M
  cN = pr2 (pr2 (pr2 μ))
```

### Second description of preservation of the coherent unit laws

```agda
preserves-coh-unit-laws-mul' :
  {l1 l2 : Level} (M : H-Space l1) (N : H-Space l2) →
  ( f : pointed-type-H-Space M →∗ pointed-type-H-Space N) →
  ( μf :
    preserves-mul (mul-H-Space M) (mul-H-Space N) (pr1 f)) →
  preserves-left-unit-law-mul
    ( mul-H-Space M)
    ( left-unit-law-mul-H-Space M)
    ( mul-H-Space N)
    ( left-unit-law-mul-H-Space N)
    ( pr1 f)
    ( pr2 f)
    ( μf) →
  preserves-right-unit-law-mul
    ( mul-H-Space M)
    ( right-unit-law-mul-H-Space M)
    ( mul-H-Space N)
    ( right-unit-law-mul-H-Space N)
    ( pr1 f)
    ( pr2 f)
    ( μf) →
  UU l2
preserves-coh-unit-laws-mul' M N f μf lf rf =
  Id
    { A =
      Id (ap (pr1 f) (lM eM) ∙ ef) ((μf ∙ ap-binary μN ef ef) ∙ rN eN)}
    ( ( horizontal-concat-Id² (lf eM) (inv (ap-id ef))) ∙
      ( ( ap
          ( _∙ (ap id ef))
          ( inv
            ( assoc
              ( μf)
              ( ap (mul-H-Space' N (pr1 f eM)) ef)
              ( lN (pr1 f eM))))) ∙
        ( ( assoc
            ( μf ∙ ap (mul-H-Space' N (pr1 f eM)) ef)
            ( lN (pr1 f eM))
            ( ap id ef)) ∙
          ( ( ap
              ( ( μf ∙ ap (mul-H-Space' N (pr1 f eM)) ef) ∙_)
              ( nat-htpy lN ef)) ∙
            ( ( inv
                ( assoc
                  ( μf ∙ ap (mul-H-Space' N (pr1 f eM)) ef)
                  ( ap (μN eN) ef)
                  ( lN eN))) ∙
              ( ( ap
                  ( λ t → t ∙ lN eN)
                  ( assoc
                    ( μf)
                    ( ap (mul-H-Space' N (pr1 f eM)) ef)
                    ( ap (μN eN) ef))) ∙
                ( horizontal-concat-Id²
                  ( ap
                    ( μf ∙_)
                    ( inv (triangle-ap-binary μN ef ef)))
                  ( cN))))))))
    ( ( ap (_∙ ef) (ap (ap (pr1 f)) cM)) ∙
      ( ( horizontal-concat-Id² (rf eM) (inv (ap-id ef))) ∙
        ( ( ap
            ( _∙ ap id ef)
            ( inv
              ( assoc
                ( μf) (ap (μN (pr1 f eM)) ef) (rN (pr1 f eM))))) ∙
          ( ( assoc
              ( μf ∙ ap (μN (pr1 f eM)) ef)
              ( rN (pr1 f eM))
              ( ap id ef)) ∙
            ( ( ap
                ( ( μf ∙ ap (μN (pr1 f eM)) ef) ∙_)
                ( nat-htpy rN ef)) ∙
              ( ( inv
                  ( assoc
                    ( μf ∙ ap (μN (pr1 f eM)) ef)
                    ( ap (mul-H-Space' N eN) ef)
                    ( rN eN))) ∙
                ( ap
                  ( λ t → t ∙ rN eN)
                  ( ( assoc
                      ( μf)
                      ( ap (μN (pr1 f eM)) ef)
                      ( ap (mul-H-Space' N eN) ef)) ∙
                    ( ap
                      ( μf ∙_)
                      ( inv (triangle-ap-binary' μN ef ef)))))))))))
  where
  eM = unit-H-Space M
  μM = mul-H-Space M
  lM = left-unit-law-mul-H-Space M
  rM = right-unit-law-mul-H-Space M
  cM = coh-unit-laws-mul-H-Space M
  eN = unit-H-Space N
  μN = mul-H-Space N
  lN = left-unit-law-mul-H-Space N
  rN = right-unit-law-mul-H-Space N
  cN = coh-unit-laws-mul-H-Space N
  ef = pr2 f

preserves-unital-mul :
  {l1 l2 : Level} (M : H-Space l1) (N : H-Space l2) →
  (f : pointed-type-H-Space M →∗ pointed-type-H-Space N) →
  UU (l1 ⊔ l2)
preserves-unital-mul M N f =
  Σ ( preserves-mul μM μN (pr1 f))
    ( λ μ11 →
      Σ ( preserves-left-unit-law-mul μM lM μN lN (pr1 f) (pr2 f) μ11)
        ( λ μ01 →
          Σ ( preserves-right-unit-law-mul μM rM μN rN (pr1 f) (pr2 f) μ11)
            ( λ μ10 → preserves-coh-unit-laws-mul M N f μ11 μ01 μ10)))
  where
  μM = mul-H-Space M
  lM = left-unit-law-mul-H-Space M
  rM = right-unit-law-mul-H-Space M
  μN = mul-H-Space N
  lN = left-unit-law-mul-H-Space N
  rN = right-unit-law-mul-H-Space N

hom-H-Space :
  {l1 l2 : Level} (M : H-Space l1) (N : H-Space l2) →
  UU (l1 ⊔ l2)
hom-H-Space M N =
  Σ ( pointed-type-H-Space M →∗ pointed-type-H-Space N)
    ( preserves-unital-mul M N)
```

### Homotopies of morphisms of H-spaces

```agda
preserves-mul-htpy :
  {l1 l2 : Level} {A : UU l1} {B : UU l2} (μA : A → A → A) (μB : B → B → B) →
  {f g : A → B} (μf : preserves-mul μA μB f) (μg : preserves-mul μA μB g) →
  (f ~ g) → UU (l1 ⊔ l2)
preserves-mul-htpy {A = A} μA μB μf μg H =
  (a b : A) → Id (μf ∙ ap-binary μB (H a) (H b)) (H (μA a b) ∙ μg)
```
