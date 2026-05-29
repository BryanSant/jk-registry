# jk-registry

The curated short-name → Maven coordinate index for [`jk`](https://github.com/BryanSant/jk).

A short name lets a jk user write:

```toml
[dependencies.main]
picocli = "4.7.7"
```

instead of the full `info.picocli:picocli`. `jk` resolves the name
through this file (the **downloaded** layer in jk's layered registry).

## How it gets to users

```
~/.jk/aliases.toml             # user-global overrides         (hand-edited)
~/.jk/registry/aliases.toml    # this file, downloaded         (`jk registry update`)
<bundled-in-jk-binary>         # shipped with each jk release  (floor)
```

`jk registry update` fetches the latest `main` of this repo and writes
it to the user's cache. There is no client-side pinning — every team
runs against the same revision, by design (jk values global
consistency over per-project drift).

## Curation policy

- **Java / Kotlin ecosystem only.** jk's v1 scope is JVM; aliases for
  other ecosystems will land when (and if) jk grows to them.
- **One canonical short name per artifact.** If a project already uses
  a different short name internally, override it locally with
  `[aliases]` in `jk.toml` or `~/.jk/aliases.toml`.
- **First-PR wins on collisions.** If `foo` is the natural short name
  for both `com.acme:foo` and `org.example:foo`, whoever PRs first
  claims the name. The other artifact must use an explicit `group =`
  in jk.toml.
- **No version field.** The registry is a name-to-coordinate index,
  not a curated catalog. Version pinning is the user's call. The
  validator rejects entries with three colons.
- **Prefer the artifactId when unambiguous.** `picocli`, `guava`,
  `tomlj` — short, known, unambiguous artifacts get their natural
  name. Don't invent abbreviations that no one types.
- **Spring Boot starters keep the prefix.** `spring-boot-starter-web`,
  not `sbsw`. The prefix is load-bearing for grep and muscle memory.

## Contributing

1. Fork this repo.
2. Edit `aliases.toml`. Add your entry to the right thematic block
   (or add a new commented block if none fits).
3. Open a PR. The CI workflow validates that the TOML parses,
   contains an `[aliases]` table, has no version-bearing entries,
   and has no duplicate short names.
4. BryanSant reviews and merges. Other maintainers will be added via
   `CODEOWNERS` as the project grows.

After merge, the change is live for everyone who runs
`jk registry update`. No release process — `main` HEAD is the
distribution.

## License

[Apache-2.0](LICENSE) — matches `jk`.
