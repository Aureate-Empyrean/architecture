# Localization

Localization (l10n) and internationalization (i18n) are an ecosystem-wide concern. They are defined once here so that modules do not each design incompatible schemes.

Core rule: **Nexus owns the installation/user locale preference and localization interoperability. Modules own their translatable messages.**

## Concept

```
Nexus locale: sk-SK
      │
      ├─► Meridian → sk-SK
      ├─► Hermes   → sk-SK
      ├─► Atlas    → sk-SK
      └─► Janus    → sk-SK   (standalone clients: locale chosen locally)
```

- Nexus holds the default locale preference. Connected modules normally inherit it.
- Each module owns its own translatable messages, kept in structured localization resources separate from application/business logic.
- Official and community translations plug into the same mechanism, without forking a module's source.

Locale controls **presentation**. Domain data is neither duplicated nor rewritten when the UI language changes: switching Nexus from English to Slovak does not create a second copy of any Meridian record.

## Established decisions

- Localization is an ecosystem-wide capability.
- Nexus owns the ecosystem/default locale preference when present; modules connected to Nexus normally inherit it.
- Modules own their translatable messages.
- Standalone/local-first clients (e.g. [Janus](../modules/janus.md)) remain localizable without Nexus. Nexus provides the ecosystem preference when connected; it is not required to select or use a language.
- Translatable strings live in localization resources, separate from business/domain logic.
- Official translations and community translations are both supported. A community translation must not require forking and maintaining a module.
- Missing translations use fallback and must not make software unusable.
- Localization includes locale-aware formatting, not only string translation: dates, times (12h/24h), numbers and decimal separators, currencies, plural rules, weekday/month names, first day of week, and sorting/collation where relevant. Modules must not hard-code US/English formatting assumptions.
- UI localization does not translate or duplicate domain/user data. Notes, messages, names, quotes and imported content stay in their original form. Example: "Date of birth" → "Dátum narodenia" is localization; an English note does not become a Slovak copy. Automatic translation of user content is not part of this decision.
- Third-party/community modules can participate in the same localization mechanisms.
- Do not invent a proprietary translation language where established i18n standards and libraries solve the problem.

## Planned direction

- Modules declare which locales they support.
- Nexus communicates the preferred locale to connected modules.
- Deterministic fallback, conceptually `sk-SK → sk → ecosystem fallback language`.
- Portable, independently maintainable community translation packs, with loading/discovery.
- Compatibility/version metadata between a translation pack and the module version it targets, where required.
- Preference for mature standards (ICU / CLDR / MessageFormat-style capabilities) over naive string substitution, because plurals, placeholders, locale-aware formatting and, where supported, grammatical variation are required.
- Possible future path for a high-quality community translation to become official without changing domain data.

Illustrative only (not a mandated structure): a module may hold resources such as `locales/en-US`, `locales/sk-SK`, `locales/cs-CZ`, `locales/de-DE`.

## Module protocol

The Empyrean module protocol will eventually need localization-related metadata/capabilities, potentially: locales supported, default/fallback locale, translation resource/version compatibility, and a way for Nexus to communicate the preferred locale. Concrete fields are Planned/Open; no manifest schema is defined here. See [interoperability](interoperability.md).

## Open questions

- Translation resource format and the specific ICU/CLDR/MessageFormat implementation or library.
- Exact fallback chain and the default ecosystem fallback language.
- Whether individual modules may override the Nexus locale. Not established: modules are not required to expose their own language selector.
- Translation pack packaging, distribution, discovery and update.
- Signing, trust and security model for third-party language packs (a pack supplies text shown inside privileged UI, so this needs consideration; see the Official/Verified/Community categories in [architecture](../architecture.md)).
- Review process for official translations, and promotion of community translations to official.
- Handling of partially translated locales.
- Right-to-left language requirements.
- Collation/sorting details.
- Module-protocol manifest fields.
- How local-first clients synchronize the locale preference, if at all.
- Language of generated documents (e.g. Meridian's selective PDF export) versus UI language; and translation of Nexus's own UI, which is not covered by "modules own their messages."
