---
version: 1
slug: "web-src-app-tsx"
primary_target: "web/src/App.tsx"
related_targets: ["web/src/styles.css"]
---

# Siteline app (web/src/App.tsx)

Scope: the whole single-page app: intake, live run, approvals, map, results. Mode: Operate. Audience: business owners at a laptop (daily use) and hackathon judges on a projector (demo); both confirmed by the user on Nov 14. Every built feature must be findable on screen: intake (any business or healthcare), run sources (live, recorded on the server, offline), business profile, plan, parallel scouts, step log, factors (why, proxy, status, priority, direction, source, caveat), both approvals, map (density, fit, top zones, sites, hover detail, fly-to), results (customers won, rent per customer, facts, availability, stability, 3D tour, verified claims with sources, caveats, score breakdown), report caveats, sources, run meter (calls, tokens, cache hits, cost), LangSmith link. Weight sliders are deferred.

## Direction contract

THESIS: The agent's run is a transit line, and each result is a leasing flyer. Refuses the dark-sidebar GIS dashboard with identical result cards.

OWN-WORLD: White map paper (#F6F5F0) and white panels, ink #15171C, Public Sans with tabular figures. Route colors do jobs: the run line is red #D62839, scout branches are purple, blue and orange, and the fit ramp is green #0E7C66. Broker yellow #F2B705 means two things only: what you selected, and your turn (an approval waiting on you). Status chips are ink, shown by pip shape; a scouted factor wears its scout's color. Buttons are ink pills, never route colors. Ringed station dots; double rings are approvals; scout branches leave the trunk on a spur to their own stop.

STORY: Owners see the agent reason station by station, approve at the transfer points, then compare real spaces. The ranked list leads to a flyer per site with every number sourced.

FIRST VIEWPORT: A 64px top bar (mark, business, run source, status, meter). Left 400px: the run line, or the intake before a run. Center: the map. Right 400px: profile and factors. When done, ranked stops run along the bottom of the map and the right column becomes the flyer for the selected site.

FORM: Fusion of grounded candidate 5 (METRO system map, assigned) with the model pick (leasing flyer) at the user's request; seed c3b3d22d.

FINISH: unreviewed and undocumented is unfinished; this build ends with the finish review, the verdict, DESIGN.md, and every shipping raster carrying its provenance
