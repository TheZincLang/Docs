# Mixins (header-style reuse)
Status: **partial** — the `mixin` clause parses (targets stored as interned identifiers);
none of the stamping, collision, or provenance semantics below are implemented yet.

A **mixin** is pure code/definition theft: it stamps a source class's fields **and**
method bodies into the target. It is expressed by the `mixin` clause on a class. Interface
conformance is a separate concern, handled by `implements` (see `lang/interfaces.md`).

## Syntax
```
"mixin" <Ident> {"," <Ident>}
```
A `mixin` clause on a class header names one or more source classes whose members are
stamped in. See `lang/classes.md` for where it sits in the class-header clause order.

There is **no is-a relationship** and no shared identity: the target is emphatically *not*
an instance of the mixed-in class. Mixins are monomorphized in — optimizer-friendly, no
vtable, lightweight, good Brass output. This is the **systems-language default for reuse**.

For how mixins sit alongside interface conformance and `extends`, see the three
orthogonal relationships in `lang/coop.md`.

## Mixins are atomic
You take the **whole** mixin (all fields + all method bodies) or nothing — no
cherry-picking parts. Fields come along because the stolen method bodies reference them.

## Access: `::` not `.`
A mixin contributes **no runtime sub-entity** — its members are stamped directly into the
target with no separate storage or identity. Access therefore uses `::` (compile-time name
resolution), never `.`:

```zn
ClassName::field
ClassName::method()
```

`ref self.ClassName::...` is **malformed by construction** — there is nothing to point at.
See `lang/coop.md` for the full `::` vs `.` distinction (mixins/modules vs owns/extends).

## Implicit self
`self` is elided at use sites (`ClassName::field`) but is real — it can be passed as
`this` to functions, which can then access those members.

## Namespaces and implicit usage
A mixin's name acts as a **namespace** (`Rectangle::`) over everything it brought in.

- **Unqualified usage is allowed when there is no clash.** You only need
  `Rectangle::field` to disambiguate.
- A mixed-in class's own internal `::` references keep their **original provenance** in the
  new home. This is what makes same-origin dedup (below) correct.

## Collision rules
| Situation                                                   | Result                                  |
|-------------------------------------------------------------|-----------------------------------------|
| Same origin (one name reached via two paths from one upstream source) | literally one thing → **dedup silently** (requires provenance tracking) |
| Different origin, same name                                 | **hard error** — disambiguate with `::` |

There is **no diamond problem**: mixins share no base identity, so nothing is
diamond-shaped. This is strictly simpler than multiple-inheritance virtual-base machinery.

## Clash warning (corporate-scale safety)
Beyond the hard error above, mixing in something under a different namespace that
introduces the *potential* for a name clash — i.e. two namespaces both carry a same-named
field — makes bare (unqualified) usage **warn**. The warning fires on the **potential for
ambiguity between namespaces**, not only on realized collisions.

| Aspect          | Detail                                                                          |
|-----------------|--------------------------------------------------------------------------------|
| Purpose         | provenance / blast-radius signal for large, multi-team codebases — not solo devs |
| Why             | a field added by another team's class can silently create action-at-a-distance breakage; the warning surfaces latent cross-boundary dependencies at write-time rather than at integration |
| Correctness     | already guaranteed by the hard clash-**error**; the warning is advisory         |
| Severity        | tunable per build profile — **off / warn / error** (solo work stays quiet; corporate CI can gate hard) |

**Philosophy:** give corporate users first-class tools to express organizational
boundaries in the language itself, rather than forcing the convention-and-hack
improvisation that Java's lack of such tools produced.
