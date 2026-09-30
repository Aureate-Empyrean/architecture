# Atlas

## Purpose

Atlas is Aureate Empyrean's **personal geographic layer**. It represents, and lets the user explore, the geographic meaning in their own data: places, areas, routes, saved locations, geographic references, and geographic projections of resources owned by other modules.

It is **not a Google Maps clone**, and general navigation is not its purpose. It should help answer: What places matter to me? What have I saved? Where did something personally meaningful happen? Where does someone or something belong geographically? What routes matter to me? What do I want to visit? What geographic information exists across my Empyrean data? And later: where have I been, and how did I move?

Atlas must be useful with **manually entered data alone**. Automatic location tracking is optional future functionality, not a prerequisite.

The long-term model below is the architecture. The [V1 boundary](#v1-boundary) is an implementation subset, not a redefinition; deferred items are deferred, not rejected.

## Owns

- Geographic resources: **Place**, **Area**, **Route**, and (later) **Visit**.
- Location history, stays/visits and movement between places (long-term; see [Later](#location-collection-and-visits-later)).
- Atlas organization of its resources: tags/categories, smart views, Collections, favorite/want-to-visit states.
- A short description/note on a geographic resource, when it describes that resource itself.
- The map and library experiences, and Atlas's own map presentation.
- Atlas's geographic *understanding* of resources owned elsewhere (their projection), not those resources.
- Manual correction of derived geographic data.

## Does not own

- People, organizations, life events, interactions and their temporal address/place associations ([Meridian](meridian.md)); it references and projects them.
- Photos and their metadata ([Argus](argus.md)); Atlas may understand and project their geographic metadata.
- Calendar events and reminders ([Chronos](chronos.md)).
- Long-form knowledge ([Mnemosyne](mnemosyne.md)); it is referenced, not duplicated.
- Device-side location collection ([collectors](../concepts/collectors.md)); collectors feed Atlas.
- The external providers' data (open geodata, POIs, routing): providers are sources, not authorities.
- Turn-by-turn navigation (not a current responsibility).

## Integrations

- **References and backlinks**: Atlas resources may be referenced by, and may reference, other modules' resources through the generic mechanism ([cross-module-references](../concepts/cross-module-references.md)). Illustrative: `argus://photo/<uuid> taken_at atlas://place/<uuid>`; a Meridian Interaction or Life Event referencing `atlas://area/…`. Exact types and syntax are not established.
- **Meridian**: Person/Organization/Group place associations (residence, workplace, registered office, funeral or resting place, etc.) reference Atlas resources; Meridian owns the association and its history, Atlas owns the resource ([meridian](meridian.md#places)).
- **Argus**: photos with geographic metadata may be projected onto the map.
- **Mnemosyne**: Notes may reference Atlas resources; notes referencing a resource may appear as context on it.
- **Chronos**: events may carry geographic references.
- **Collectors**: opt-in device collectors (e.g. an Android companion) supply location data via Nexus ([collectors](../concepts/collectors.md)). Later.
- **Design system**: Atlas uses the shared design language with its own map presentation ([design-system](../concepts/design-system.md)).

## Long-term model

Established at the conceptual level unless labeled otherwise. Exact schemas, geometry storage and internals are Open.

### Surfaces: Map and Library

Two equally legitimate ways to explore geographic data.

- **Map**: an interactive map is a primary experience. The user selectively enables or disables **layers**, illustratively: saved places, areas, routes, favorites, want-to-visit, friends' homes, organizations, historical/inactive locations, photo locations from Argus, geographically referenced Meridian and Mnemosyne resources, and later location history/visits/recorded movement. The built-in layer taxonomy is not fixed, and not every semantic category is hard-coded.
- **Library / list browsing**: browsing geographic resources without map interaction (interesting locations, friends' homes, favorite places, want to visit, movie locations, nature, custom collections, user-defined smart views). It is a place/geography library, not a database-admin CRUD table.

### First-class geographic resources

- **Place**: a discrete location/POI/user-defined point (home, someone's home, restaurant, gym, shop, bench, viewpoint, an arbitrary pin, an external OpenStreetMap POI saved into Atlas). A Place does not require a street address or external POI; the user can place and name a custom point anywhere.
- **Area**: a geographic region represented spatially, not one arbitrary point, and not merely an administrative boundary (a park, campus, neighborhood, forest area, custom region, the approximate area in which a personal event occurred). The user can eventually create a custom Area interactively on the map with a polygon/drawing tool.
- **Route**: a first-class resource for a meaningful historical route ("our first date"), a planned route for a future day/trip, and later a recorded route from actual movement. A Route may include geometry/path, ordered stops, transport mode (walking, cycling, driving, public transport, other; extensible, no enum frozen), a short description, tags/categories, references/backlinks and temporal/planning context. **Routing-engine-assisted creation is the primary intended workflow** (choose stops/endpoints and Atlas calculates the route); manual drawing/editing should also be possible where appropriate.
- **Visit** (later): presence at a Place/Area over time; may be entered manually, inferred from location data, or both. Manual entry stays possible even when collection exists. Atlas does not depend on continuous tracking.

**Geographic shape is semantic.** A geographic reference may meaningfully target a Place, Area or Route, and must not assume "where" is always a point. "We kissed here" may reference a Place; "we spent our first date around this part of the park" an Area; "this is the route we walked" a Route. The resource chosen reflects the actual geographic meaning.

### Cross-module geographic projection

A central capability: Atlas may visually project geographically relevant resources owned by other modules onto the map, e.g. a Meridian Person's current or historical home, a Meridian Organization's addresses, a Meridian Life Event or Interaction referencing an Atlas Place/Area/Route, an Argus photo with geographic metadata, a Mnemosyne Note referencing an Atlas resource, future Chronos events.

- **Projection does not transfer ownership.** Atlas does not take ownership of, or independently edit, another module's semantic content merely because it displays it. Example: a Meridian Interaction "First kiss with @Person" referencing `atlas://area/<uuid>` can be shown on that Area through references/backlinks; Atlas does not store its own authoritative copy of the text.
- **Derived projection data is allowed.** For practical scale (e.g. thousands of geotagged Argus photos), Atlas may keep a derived, non-authoritative, rebuildable cache of other modules' geographic data under the [derived-data rule](../concepts/interoperability.md#derived-data-and-projection). A module-declared geographic query capability is Planned ([module-contract](../concepts/module-contract.md#planned)).
- **There is no first-class Atlas "Moment" resource.** A moment on the map is a visual projection of a resource owned elsewhere. Argus owns the photo; Atlas owns or understands its geographic projection.
- Generic references/backlinks are used rather than hard-coded module coupling. Projected content is visibly understandable as projected, not presented as Atlas-owned.

### Cross-module resource creation (Planned direction)

While editing another module's resource, the user should eventually be able to reference a geographic resource: select an existing one, or, if none exists, create a Place, Area or Route and continue that workflow in Atlas, establishing the reference. The originating resource holds the forward reference; the backlink is exposed through Atlas/Nexus. This should rest on a **generic** resource reference/picker/open/create-target mechanism, not a Meridian-specific Atlas integration. The shared mechanism does not yet exist; see [cross-module-references](../concepts/cross-module-references.md) (Open).

### Descriptions and notes

Atlas resources may own a short description when it describes the geographic resource itself ("Good viewpoint after sunset", "Parking entrance is from the rear"). Long-form knowledge belongs in Mnemosyne and is referenced. Other domain information stays with its owning module.

### Organization

- **Tags/categories**: flexible semantic classification.
- **Smart views**: saved, named, rule-derived filtered views (e.g. Want to Visit + Nature, Movie Locations, Friends' Homes, Historical Locations). Not every smart view is hard-coded.
- **Collections**: manual organization with **explicit membership** (e.g. Trip to Austria, First Date, Bratislava Weekend); may eventually contain combinations of Places, Areas and Routes.
- Smart views derive membership from rules; Collections have explicit membership. They are different concepts.
- **Personal states** such as favorite and want to visit. No large fixed status taxonomy.

### Open geodata and external sources

Atlas strongly prefers open geographic data and does not architecturally depend on a proprietary map provider. OpenStreetMap is the natural source to investigate first. "OpenStreetMap" is **not one service** that solves everything: map rendering, tiles, geocoding, reverse geocoding, POI search and routing are separate capabilities that may involve different components/providers. Providers and architecture are Open.

**Search is local knowledge first** ([search-and-discovery](../concepts/search-and-discovery.md)): searching for a place first searches saved Places, Areas and Routes and permitted local projections. If nothing suitable exists, or the user explicitly asks, Atlas searches configured external/open geographic data, as a clearly distinguishable action, so the user can find an existing place without entering coordinates. This must remain consistent with the ecosystem's "no mandatory cloud" and privacy principles: no feature may require a hosted service operated by the project, and a query to an external provider can reveal what the user is interested in. Whether and how third-party providers are contacted (and self-hosted alternatives) is Open.

**External POI ownership and provenance.** Saving an external POI creates a **user-owned Atlas representation/snapshot** with useful external source identity/provenance. The provider remains a source, not the authority over the user's Atlas data. A later refresh must not silently overwrite the user's edits or personal metadata, and conflicts/updates are inspectable where relevant. Atlas's schema does not mirror an external provider's schema.

### Map presentation

External data does not dictate Atlas's visual design. Atlas has its own map presentation consistent with the Aureate Empyrean design system and its eventual module accent; the standard OpenStreetMap website look is not the intended UI. The style covers basemap, labels, roads, land/water, POIs, Atlas-owned resources, selected resources, layers and historical/inactive resources. Not neon, cyberpunk or gaming-map. The renderer/style technology is Open (it is not established that this is CSS).

### Historical and inactive geography

Historical geographic information is represented without silently replacing it. Example: a Meridian Organization has Address A valid 2019–2024 and Address B from 2024. Meridian owns that temporal information; Atlas may project both. The old location remains available as historical/inactive geography (visually muted and labeled previous/inactive, and hideable/showable) rather than disappearing. **Historical/inactive state is metadata/presentation, not part of the name**: no "[OLD] Company X". The same applies to historical addresses/locations of people and other resources.

### Route export and sharing (Planned)

Routes should be portable. Export/sharing via open geographic formats is a long-term direction; GPX, GeoJSON and KML are examples to investigate, not requirements. Sharing must not require the recipient to use Aureate Empyrean. Share model, privacy, hosted/static representation and formats are Open.

### Location collection and visits (Later)

Automatic collection is on the roadmap, not the initial priority: opt-in device collector → location samples → Atlas processing → Visits, movement and recorded routes. **The user explicitly controls collection** (established), and manual geographic data and Visits remain valid alongside it. The preference is to reconstruct meaningful visits and routes rather than display clouds of raw pings; raw samples are source/implementation data, and the useful representation emphasizes visits and routes. The collector implementation follows the shared [collectors](../concepts/collectors.md) architecture.

### Place statistics (optional)

A Place may eventually show contextual statistics (visit count, first/last visit, accumulated time) in the context of the Place itself. They are optional derived information, not the purpose of Atlas, and not dashboard filler.

### Route intelligence with Astra (Planned)

A long-term capability, not V1. [Astra](astra.md) may help plan and enrich routes using Atlas-provided tools and explicitly requested external information. Example request: "Plan this drive and show me interesting historical places no more than 15 minutes off the route." Potential capabilities: route calculation and alternatives, POIs near the route, interesting detours, viewpoints, historical sites, hiking-related stops, parking/fuel/charging, toll/vignette requirements, and current restrictions/closures when trustworthy live sources are available.

- LLM memory is **never authoritative** for safety-sensitive or time-sensitive route information. Tolls, closures, restrictions and similar information come from appropriate current sources.
- Proposed Places and Routes are drafts; Atlas performs the change after the user approves.

### Navigation

Turn-by-turn navigation is not a current responsibility and is not part of V1. "Open in external maps/navigation app" interoperability may come later. Native navigation remains a long-term direction that would use the same Atlas resources; for example, approaching a relevant POI on a route could optionally surface "To your right is [historic site]". Such commentary must be grounded in known, source-backed POI information, not invented dynamically. No navigation engine is designed.

### Interaction model

**Expose workflows, not schemas** ([principles](../principles.md)). Atlas behaves like a geographic application, not a CRUD database of Place/Area/Route rows: map interaction is spatial; creating an Area supports polygon drawing; creating a Route supports selecting stops and routing; selecting a pin, area or route exposes useful context and actions; library browsing is compact and useful; metadata editing uses progressive disclosure; projected cross-module content is visibly understandable without pretending Atlas owns it. The full data model is not exposed as giant property forms by default. Layouts and components are not specified.

## V1 boundary

**Planned** implementation scope centered on the manually useful product; revisable. Items in the second tier are Planned for V1 but may be reduced or slip; the goal is not to pretend everything ships at once.

**V1 core**

- Full-page Atlas module integrated with Nexus (see [architecture](../architecture.md#application-boundary)).
- Interactive Map with selectable layers.
- Places, including custom pins without an external POI or address.
- Areas with map-based drawing/editing.
- Routes as first-class resources, with routing-engine-assisted creation and transport mode.
- Short Atlas descriptions; tags/categories; favorite; want to visit; Collections.
- List/library browsing in addition to the Map.
- Generic cross-module references/backlinks, and geographic projection sufficient for other modules to appear without copying their content.
- Historical/inactive projection semantics.
- External POI provenance/snapshot semantics.

**V1 Planned (may be reduced)**

- Manual route drawing/editing where practical.
- Explicit external geographic/POI search after local search (depends on the provider decision).
- Atlas-specific map visual style (a basic first version).
- Smart views / saved filters at a useful basic level.

**Deferred (not rejected):** continuous/background phone tracking, automatic Visit detection, complete location history, recorded movement reconstruction, automatically recorded routes, advanced visit statistics, native turn-by-turn navigation, complex route-sharing infrastructure, exhaustive import/export formats, advanced geospatial analytics.

## Established decisions

- Atlas is the personal geographic layer; not a Google Maps clone; navigation is not its purpose. It must work with manually entered data alone.
- Map and Library are both primary ways to explore geographic data; the library is not a generic CRUD table.
- Place, Area and Route are distinct first-class geographic resources; Visit is part of the long-term domain (later). A geographic reference may target a Place, Area or Route, and shape carries meaning.
- Atlas resources may reference other modules' resources through cross-module references.
- **Projection does not transfer ownership**; Atlas does not own or independently edit other modules' content, derived caches are non-authoritative, and there is no first-class Atlas "Moment" resource.
- Search is local first; external geographic search is explicit.
- Atlas may own a short description of the geographic resource; long-form knowledge belongs in Mnemosyne.
- Tags/categories, smart views (rule-derived) and Collections (explicit membership) are distinct; favorite and want-to-visit are personal states without a large fixed taxonomy.
- Atlas prefers open geographic data and does not depend architecturally on a proprietary map provider; OpenStreetMap is not treated as one service that solves rendering, geocoding, search and routing.
- A saved external POI is a user-owned snapshot with provenance; refreshes never silently overwrite user data; Atlas's schema does not mirror a provider's.
- Atlas has its own map visual style and does not adopt a provider's default look.
- Historical/inactive geography is preserved and is metadata/presentation, not part of names.
- Location collection must be **explicitly enabled and locally controlled**; manual data and Visits remain valid alongside it. Raw samples are source data; the product emphasizes visits and routes.
- Manual correction is part of the direction (automatically derived data must be correctable).
- Turn-by-turn navigation is not a current Atlas responsibility.
- Atlas exposes workflows, not schemas.

## Planned direction

- The map experience with layers, Area drawing, routing-assisted Routes and Atlas-specific styling.
- Smart views, Collections and library browsing.
- Cross-module resource creation via a shared create-target/picker mechanism.
- Route export/sharing via open formats; "open in external maps" interoperability.
- Route intelligence with Astra; long-term native navigation using Atlas resources with source-grounded contextual commentary.
- Later: opt-in location collection, Visit inference, recorded routes and history, optional Place statistics.

## Open questions

- Exact model boundaries among Place, Area, Route and Visit; geometry representation, geospatial storage and indexing (PostgreSQL with PostGIS is the expected direction under the [database default](../architecture.md#databases); details Open).
- Map renderer; basemap/tile architecture; caching; offline map capability.
- Geocoding/reverse geocoding, POI search provider(s), and routing engine/provider(s), consistent with "no mandatory cloud" and with privacy of external queries (self-hosted or optional).
- Exact transport-mode model; route editing semantics.
- Import/export formats for geographic resources (GPX, GeoJSON, KML, existing location-history exports); route-sharing mechanism and privacy.
- Exact geographic projection/resolution rules, including how a module declares that a resource is geographically projectable, and complex historical projection cases.
- Cross-module create-target/resource-picker UX and the shared architecture it requires.
- Granularity of Atlas Places versus Meridian address/place associations. Ownership is established: Atlas owns the resource; Meridian owns a Person's/Organization's relationship to it ([meridian](meridian.md#places)).
- Automatic Visit inference; location collector retention and raw-data policy; data model for raw points vs. derived visits.
- Precision/privacy controls (retention, redaction, sensitive places).
- Atlas module accent and exact visual design.
