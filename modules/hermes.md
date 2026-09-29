# Hermes

## Purpose

The communications module. Its scope is broader than message backup: a unified local communication history and, eventually and where technically feasible, a live unified inbox.

## Owns

Candidate concepts:

- accounts and communication identities
- conversations: DMs, group chats, channels, threads
- messages, replies, reactions, edits/deletions where known
- attachments and voice messages
- calls
- participants
- source/import metadata and raw source data where practical
- normalized data for search and presentation

Possible sources: Discord, Instagram, Facebook Messenger, WhatsApp, Signal, Telegram, SMS, RCS, email, phone calls, generic imports, other future connectors.

## Does not own

- People or their identity beyond communication identities ([Meridian](meridian.md)). A Hermes identity *references* a Meridian person.
- Physical blob storage of attachments ([storage-and-files](../concepts/storage-and-files.md)); attachments are blob references.
- Calendar/event data ([Chronos](chronos.md)).
- Device-side collection logic ([collectors](../concepts/collectors.md)).

## Integrations

- **Meridian**: several communication identities (Instagram, Discord, WhatsApp, Messenger…) may reference one Meridian person. Hermes owns communication identities and the source-observed metadata (e.g. platform account ID, observed handles) needed to preserve/import communication correctly; Meridian owns the knowledge that an online account belongs to a person. Neither is authoritative over the other; Hermes observations (e.g. a handle change) may serve as provenance for Meridian's account history. Calls, messages and conversations stay in Hermes; a Meridian Interaction only adds person/relationship context and references them. See [Meridian](meridian.md#cross-module-observations).
- **Collectors**: SMS, call history and (where permitted) RCS from an Android companion arrive through Nexus.
- **Storage**: attachments share blobs with other modules.
- **Argus/Chronos/Atlas/Mnemosyne/Documents**: via references (e.g. `hermes://conversation/91` embedded in a document).

## Established decisions

- Scope includes historical import as an important use case, and potentially a live inbox.
- Ingestion is via **connectors**; browser-based or otherwise unofficial connectors must not define core Hermes architecture, since they can break when services change.
- Official APIs and user-provided exports remain valid alternative ingestion methods.
- Preserve provenance/raw source information where practical, alongside normalized data.
- Multiple communication identities may reference one Meridian person.
- Do not assume every service supports sending/receiving.

## Planned direction

- Import framework for user-provided exports and generic formats.
- Connector interface so sources are pluggable.
- Possible browser integration that locally syncs supported web services from an authenticated browser session, continuing from the latest known message/date. Treated strictly as an optional connector.
- Possible live inbox with send/receive through connectors where reliable and permitted by the service.

## Open questions

- Normalized message/conversation model that fits very different services (email vs. chat vs. calls).
- Raw-source retention format and how it relates to the normalized model.
- Deduplication when the same conversation is ingested via multiple paths (export + connector + collector).
- Connector contract: credentials handling, sync cursors, error/breakage reporting.
- Identity resolution: how identities are proposed/confirmed as one Meridian person (must stay user-controlled).
- Live inbox feasibility per service, and its security/credential implications.
- Handling of edits, deletions and disappearing messages when the source no longer has them.
- Email: whether it belongs in Hermes fully or is a separate concern.
