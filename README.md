# Datathon-Wolf_987
Repository for Trilemma Datathon 
# RentAudit YVR — Student Rental Engine

**Build Session 1: Foundations & Repo Setup**

---

## 1. Problem Evidence

International, out-of-province, and first-time post-secondary students moving to Metro Vancouver face high-risk, high-friction housing decisions because they lack local context. Specifically:

- **Market Opacity & Scam Exposure** — Students don't know baseline rents for their area/unit type, making it hard to tell a fair listing from an inflated or fraudulent one.
- **Undisclosed Property Defects** — Landlords aren't required to disclose active by-law violations (Standards of Maintenance, electrical, plumbing, structural) at the time of listing.
- **Construction & Renovation Disruption** — Nearby permitted construction/renovation work can cause major livability issues that never show up in a listing.
- **Student-Specific Blind Spots** — Generic rental portals (Zillow, Craigslist, etc.) don't account for what matters to students: commute time to campus, budget groceries, study-friendly cafés, and affordable social spaces.

**Why this matters:** these gaps combine to put new students at financial and safety risk *before* they've signed a lease — and no existing tool synthesizes public data to flag this at decision time.

---

## 2. Data Evidence

RentAudit YVR is built entirely on verifiable open/public data sources. Confirmed datasets and access methods:

| Category | Dataset / Source | Access Method | Key Fields |
|---|---|---|---|
| Rental Standards | CoV Rental Standards – Current Issues | `opendata.vancouver.ca` REST API | `address`, `by_law_issue`, `issue_date`, `business_licence_number` |
| Building Permits | CoV Issued Building Permits (2017–) | `opendata.vancouver.ca` REST API | `address`, `typeofwork`, `descriptionofwork`, `issuedate` |
| Address Registry | CoV Property Addresses | `opendata.vancouver.ca` REST API | `civic_number_street`, `local_area`, `geo_point_2d` |
| Public Safety | VPD GeoDASH Incident Data | VPD Open Data Portal (annual CSV) | `TYPE`, `NEIGHBOURHOOD`, `HUNDRED_BLOCK` |
| Market Rent | CMHC Rental Market Survey | Static baseline CSV / Open Canada Portal | Zone, Bedroom Type, Median Rent, YoY Rent Change (%) |
| Transit | TransLink GTFS Feeds | `api.translink.ca/rtti/v1/` | Route No., Stop No., schedule/realtime feeds |
| Commercial & Amenities | OpenStreetMap Overpass API / CoV Licences | `overpass-api.de/api/interpreter` | `shop=supermarket`, `amenity=restaurant/cafe/cinema` |

This matrix confirms every sub-score in the scoring engine (Price Fairness, Safety Risk, Campus Commute, Student Livability) can be populated from a real, queryable public source — no dataset is assumed or synthetic.

---

## 3. Carrying Into Build Session 2

With problem evidence and data sources locked in, Build Session 1 closes with:

- ✅ Finalized dataset matrix (above) — all endpoints verified as accessible
- ✅ Initial ingestion script committed: `vancouver_rental_audit.py`
- ✅ Composite scoring formula defined:

  ```
  Composite Score = w1·S_Price + w2·S_Risk + w3·S_Commute + w4·S_Livability
  w1 = 0.30 (Price Fairness)
  w2 = 0.30 (Safety & Municipal Risk)
  w3 = 0.20 (Campus Commute)
  w4 = 0.20 (Student Livability & Commercial Amenities)
  ```

- ✅ Commercial/Livability sub-formula defined, using exponential distance decay `f(d) = exp(-λ·d)`:

  ```
  S_Commercial = min(100, 0.35·S_Grocery + 0.30·S_Dining + 0.20·S_Cafes + 0.15·S_Entertainment)
  ```

**Build Session 2 scope:**
- Build the FastAPI backend pipeline to call the ingestion script and compute live composite scores
- Implement the React / Tailwind frontend to render color-coded risk cards from the API output
- Wire up the four data sources into the scoring engine end-to-end (not just the commercial sub-score)
